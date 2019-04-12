### POSIX
POSIX是一个标准,android默认支持POSIX
所有的
Centos posix函数(例如open)、pthread函数没有man手册的问题
- yum -y install man-pages
Ubuntu :
- sudo apt-get install manpages-de manpages-de-dev  manpages-dev glibc-doc manpages-posix-dev manpages-posix

* Linux下的多线程,多进程
- pthread   pthread.h 一个头文件里面,有他的所有方法.
查看pthread.h 的所有方法和单个方法:
所有方法查看: man -k pthread
单个方法查看: man pthread_join(pthread_create,pthread_exit,pthread_cancel), man 跟方法名,就能看到该方法的说明
- 容易出错 的地方,
调试的时候加上这个 gcc -o test  -lpthread test.c
参数传递注意需要的是指针还是 
```c

#include <stdlib.h>
#include <stdio.h>
#include <unistd.h>
#include <pthread.h>


void* thr_fun(void* arg){
        char* no = (char*)arg;
        int i = 0;
        for(; i < 10; i++){
                printf("打印处理事件%d",i);
        }
        printf("该线程已经启动,相当于java中run方法%s",no);
        return 1;
}

void main(){
        printf("main pthread\n");
        pthread_t tid;
        //
        pthread_create(&tid,NULL,&thr_fun,"1");

        int* rval;
        //等待tid线程结束
        pthread_join(tid,&rval);
        printf("rval:%d",rval);
}



#include <stdlib.h>                                                         
#include <stdio.h>
#include <unistd.h>
#include <pthread.h>

int i = 0;
//互斥锁
pthread_mutex_t mutex;

void* thr_fun(void* arg){
	//加锁
	pthread_mutex_lock(&mutex);
	char* no = (char*)arg;
	for(;i < 5; i++){
		printf("%s thread, i:%d\n",no,i);
		sleep(1);
	}
	i=0;
	//解锁
	pthread_mutex_unlock(&mutex);
}

void main(){
	pthread_t tid1, tid2;
	//初始化互斥锁
	pthread_mutex_init(&mutex,NULL);

	pthread_create(&tid1,NULL,thr_fun,"No1");
	pthread_create(&tid2,NULL,thr_fun,"No2");

	pthread_join(tid1,NULL);
	pthread_join(tid2,NULL);

	//销毁互斥锁
	pthread_mutex_destroy(&mutex);
}




#include <stdlib.h>                                                      
#include <stdio.h>
#include <unistd.h>
#include <pthread.h>

//消费者数量
#define CONSUMER_NUM 2
//生产者数量
#define PRODUCER_NUM 1

pthread_t pids[CONSUMER_NUM+PRODUCER_NUM];

//产品队列
int ready = 0;

//互斥锁
pthread_mutex_t mutex;
//条件变量
pthread_cond_t has_product;

//生产
void* producer(void* arg){
	int no = (int)arg;
	//条件变量
	for(;;){
		pthread_mutex_lock(&mutex);
		//往队列中添加产品
		ready++;
		printf("producer %d, produce product\n",no);
		//fflush(NULL);
		//通知消费者，有新的产品可以消费了
		//会阻塞输出
		pthread_cond_signal(&has_product);
		printf("producer %d, singal\n",no);
		pthread_mutex_unlock(&mutex);
		sleep(1);
	}
}

//消费者
void* consumer(void* arg){
	int num = (int)arg;
	for(;;){
		pthread_mutex_lock(&mutex);
		//while?
		//superious wake ‘惊群效应’
		while(ready==0){
			//没有产品，继续等待
			//1.阻塞等待has_product被唤醒
			//2.释放互斥锁，pthread_mutex_unlock
			//3.被唤醒时，解除阻塞，重新申请获得互斥锁pthread_mutex_lock
			printf("%d consumer wait\n",num);
			pthread_cond_wait(&has_product,&mutex);
		}
		//有产品，消费产品
		ready--;
		printf("%d consume product\n",num);
		pthread_mutex_unlock(&mutex);
		sleep(1);
	}
}


void main(){
	//初始化互斥锁和条件变量                                                
 	pthread_mutex_init(&mutex,NULL);
  	pthread_cond_init(&has_product,NULL);
	printf("init\n");

	int i;
	for(i=0; i<PRODUCER_NUM;i++){
		//生产者线程
		printf("%d\n",i);
		pthread_create(&pids[i],NULL,producer,(void*)i);
	}
	
	for(i=0; i<CONSUMER_NUM;i++){
		//消费者线程
		pthread_create(&pids[PRODUCER_NUM+i],NULL,consumer,(void*)i);
	}
	
	//等待
	for(i=0; i<PRODUCER_NUM+CONSUMER_NUM;i++){
		pthread_join(pids[i],NULL);
	}
	
	//销毁互斥锁和条件变量
	pthread_mutex_destroy(&mutex);
	pthread_cond_destroy(&has_product);
	
}



```
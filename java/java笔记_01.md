
## Junit 测试
### 测试
- 黑盒测试   不用关心具体的代码逻辑,   一个input  一个output, 看看结果是否达到期望.
- 白盒测试  一般需要我们写代码, 一个input  Junit单元测试就是白盒测试的一种.
* Junit使用: 白盒测试
	* 步骤:
		1. 定义一个测试类(测试用例)  `建议在xxx包下.test包,创建ClassNmaeTest`
		2. 定义测试方法, 可以独立运行 `建议 以方法名前加tsetXXX方法,返回值voide,参数列表:空参`
		3. 给方法加 @Test 注解
		4. 增加依赖 alt + enter
	 * 判定结果:
	 	* 红色: 失败
	 	* 绿色:成功
	 	* 一般会使用断言操作比较数据结果 Assert.assertEquals
	 * 资源 @Before 注解的方法初始化数据, @After 注解的方法释放数据
### 反射

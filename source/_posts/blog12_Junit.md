---
title: Junit单元测试
---

### 使用方法
1. 引入Junit依赖
	* maven引入
	```
	<dependency>
	  <groupId>junit</groupId>
	  <artifactId>junit</artifactId>
	  <version>4.8</version>
      <scope>test</scope>
	</dependency>
	```
2. 定义测试类
	* 包名: xxx.xxx.xxx.test(xxx部分与主程序部分一致)
	* 类名: xxxTest
3. 定义测试方法
	* 方法名: 要测试的方法名
	* 参数: 空参
	* 返回值: void
	* 注解: @Test
4. 断言
	* 即使代码成功运行，执行结果也未必是期望的结果
	* Assert类的相关方法，如: Assert.assertEquals(期望的结果，代码实际执行的结果)
5. 示例
	```
	public class ExampleTest{
	    @Test
	    public void doSomethingTest(){
           TestObj testObj = new TestObj(); 
           String result = testObj.doTest();
           Assert.assertEquals("abc"，result);
       }
	}
	```
	
### 结果判定
 * 红色: 执行代码失败，出现了错误或异常
 * 绿色: 代码执行成功，没有出现错误或异常
 
### 扩展
 * @Before注解标记的方法将在测试方法前执行，一般用于加载资源
 * @After注解标记的方法将在测试方法执行完毕后执行，一般用于释放资源
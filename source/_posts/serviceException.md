---
title: 业务流程异常处理
date: 2017-08-24 21:43:07
tags: [业务，异常处理]
categories: 业务
top: 99
---

## 一、异常分类
在web系统开发中，异常可大致分为:
* <font color="#FF0000">系统异常:</font>软件的缺陷，客户端对此类异常是无能为力的,系统内部未知异常。
* <font color="#FF0000">业务异常</font>用户未按正常流程操作导致的异常。
<!--more-->

## 二、处理方式
在实际开发中，常见的异常处理方式有:
1.系统异常采用`try{}catch{}`处理,在`service`抛出异常，由`controller`层得到异常信息在返回,最好定义一个异常信息枚举类，集中记录异常信息：
``` java
`service`层：
try {
		//service代码

	} catch (Exception e) {
		e.printStackTrace();
		throw new ServiceException(ErrorCode.SYSTEM_ERROR);
	}

枚举类`ErrorCode`：
/**
 * @author 
 * errorCode 错误代码  
 * sysMsg 系统显示信息（一般用于日志输出）
 * showMsg 页面显示信息（一般用于页面提示用户）
 *
 */
public enum ErrorCode {
	
	SYSTEM_ERROR(10086,"","系统异常！"),
	CODE_EXIST(101111101,"","code码已存在已存在！");

	int errorCode;
	String sysMsg;
	String showMsg;
	ErrorCode(int errorCode, String sysMsg, String showMsg){
		this.errorCode = errorCode;
		this.sysMsg = sysMsg;
		this.showMsg = showMsg;
	}
	public int getErrorCode(){
		return errorCode;
	}
	public String getSysMsg(){
		return sysMsg;
	}
	public String getShowMsg(){
		return showMsg;
	}
	public void setShowMsg(String showMsg){
		this.showMsg = showMsg;
	}
	public void setSysMsg(String sysMsg){
		this.sysMsg = sysMsg;
	}
}

`controller`层：
try {
		
		//controller代码
		
	} catch (ModuleException e) {
		//通过枚举类返回错误码和错误信息
		return ResponseError.create(e.getErrorCode().getErrorCode(),e.getErrorCode().getShowMsg());
	}

```
2.业务异常可以采用`try{}catch{}`处理，或者采用`if`加错误码判断处理
如果采用`try{}catch{}`方式，和上面方式一致，下面介绍采用`if`加错误码处理,定义一个对象，每次处理加上返回码和错误信息提示。
``` java
ReturnResultObj obj = new ReturnResultObj();
obj.setCode("101112101");
obj.setMessage("name不能为空!");
obj.setData("");
return obj;
```
## 三、两种方式优缺点：
* 用`if`加错误码控制业务流程,需要对每个接口的返回都要做一个错误码的校验,判断的代码会遍布在你的业务代码里面。优点就是对调用方,不必对你的接口进行异常校验,因为你的接口只可能返回「正确」或者「错误」,代码可读性高，但是随之带来的代码显得很臃肿，错误码集中在代码里，后期维护困难，但在效率上面也会更加高一点。对某些人来说,用错误码来控制业务流程更能符合「异常」的语义。
* 用`if`加错误码控制业务流程,因为没有抛出RuntimeException，如果在写操作之后加入业务判断，会导致事务无法回滚，因此写操作之前进行所有业务判断。
* 用异常来控制业务流程,可以把错误处理集中在一处,对客户端的代码编写更加友好,代码清晰简洁，在业务代码里面不会有很多错误码的判断。缺点就是创建异常堆栈是需要时间和空间的,异常处理性能开销在于
-是一个synchronized方法(主因)
-需要填充线程运行堆栈信息
但是可以通过复写业务异常基类的方法`Throwable.fillInStackTrace()`来提示性能。
``` java
public Throwable fillInStackTrace() {
    return null;
}
```



参考文章: 
https://segmentfault.com/q/1010000004308144?_ea=564305
http://blog.csdn.net/liujun13579/article/details/7742380
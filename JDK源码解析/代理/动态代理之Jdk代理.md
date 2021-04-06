## JDK动态代理
```
    public static void main(String[] args) {
        //设置系统属性，把代理文件保存下来，保存到com.sun.proxy包下
        System.getProperties().put("sun.misc.ProxyGenerator.saveGeneratedFiles", "true"); //设置系统属性
        // 新建一个代理类，需要三个参数
        // 1.ClassLoader 类加载器
        // 2.需要代理接口
        // 3.代理处理器
        Helloworld h = (Helloworld) Proxy.newProxyInstance(
                HelloworldImpl.class.getClassLoader(),
                new Class[]{Helloworld.class},
                new MyInvocationHandler(new HelloworldImpl()));
        // 调用代理对象的sayHello方法。
        h.sayHello();
        
    }
```
```
public class MyInvocationHandler implements InvocationHandler {
    private Object target;

    public MyInvocationHandler(Object target) {
        this.target = target;
    }

    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("method :" + method.getName() + " is invoked!");
        return method.invoke(this.target, args);
    }
}
```
生成的代理对象反编译代码
```
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package com.sun.proxy;

import aop.test.Helloworld;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.lang.reflect.UndeclaredThrowableException;

public final class $Proxy0 extends Proxy implements Helloworld {
    //被代理的方法.
    private static Method m1;
    private static Method m3;
    private static Method m2;
    private static Method m0;

    public $Proxy0(InvocationHandler var1) throws  {
        super(var1);
    }

    public final boolean equals(Object var1) throws  {
        try {
            return (Boolean)super.h.invoke(this, m1, new Object[]{var1});
        } catch (RuntimeException | Error var3) {
            throw var3;
        } catch (Throwable var4) {
            throw new UndeclaredThrowableException(var4);
        }
    }
    // 被代理接口的方法
    public final void sayHello() throws  {
        try {
            //调用超类的h的invoke方法，
            //这里的h就是传入的第三个参数 XXXInvokeHandler
            //传的三个参数是 第一个是当前代理类，方法，和参数。
            super.h.invoke(this, m3, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    public final String toString() throws  {
        try {
            return (String)super.h.invoke(this, m2, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    public final int hashCode() throws  {
        try {
            return (Integer)super.h.invoke(this, m0, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    static {
        try {
            // 从Class中找出上面代理的第二个参数 代理接口的方法集合，除了代理接口里的方法 ，还有Object里的 equals toString 和hashCode也被代理。
            m1 = Class.forName("java.lang.Object").getMethod("equals", Class.forName("java.lang.Object"));
            m3 = Class.forName("aop.test.Helloworld").getMethod("sayHello");
            m2 = Class.forName("java.lang.Object").getMethod("toString");
            m0 = Class.forName("java.lang.Object").getMethod("hashCode");
        } catch (NoSuchMethodException var2) {
            throw new NoSuchMethodError(var2.getMessage());
        } catch (ClassNotFoundException var3) {
            throw new NoClassDefFoundError(var3.getMessage());
        }
    }
}


```
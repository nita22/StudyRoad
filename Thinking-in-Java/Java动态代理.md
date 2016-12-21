## Java动态代理

代理是一种常用的程序设计模式，如同网络代理一样，代理是介于调用者和真正调用目标对象之间的中间对象，代理在调用真正目标对象时提供一些额外或者不同的操作，真正的对目标对象的操作还是通过代理调用目标对象来完成。

简单的代理例子如下：

``` java
//接口  
interface Interface{  
    void doSomething();  
    void somethingElse(String arg);  
}  
//目标对象  
class RealObject implement Interface{  
    public void doSomething(){  
        System.out.println(“RealObject doSomething”);  
	}  
    public void somethingElse(String arg){  
        System.out.println(“RealObject somethingElse ” + arg);  
    }  
}  
//简单代理对象  
class SimpleProxy implements Interface{  
    private Interface proxied;  
    public SimpleProxy(Interface proxied){  
        this.proxied = proxied;  
	}  
    public void doSomething(){  
        System.out.println(“SimpleProxy doSomething”);  
        proxied.doSomething();  
    }  
    public void somethingElse(String arg){  
        System.out.println(“SimpleProxy somethingElse ” + arg);  
        proxied.somethingElse(arg);  
    }  
} 
  
Class SimpleProxyDemo{  
    public static void consumer(Interface iface){  
        iface.doSomething();  
        iface.somethingElse(“TestProxy”);  
	}  
    public static void main(String[] args){  
    	//不是用代理  
        cosumer(new RealObject());  
        //使用代理  
        cosumer(new SimpleProxy(new RealObject()));  
    }  
}  
```

输出结果为：

RealObject doSomething

RealObjectsomethingElse TestProxy

SimpleProxy doSomething

RealObject doSomething

SimpleProxy somethingElse TestProxy

RealObject somethingElse TestProxy

上面例子可以看出代理SimpleProxy在调用目标对象目标方法之前做了一些额外的操作。



JDK动态代理的要素：

1. 实现了InvocationHandler的代理处理类，实现其invoke方法，该方法是代理调用目标对象方法以及提供额外操作的方法。
2. 使用Proxy.newProxyInstance(类加载器, 代理接口列表,InvocationHandler对象);方法创建实现了指定接口的动态代理。

JDK的代理例子如下：

``` java
//接口  
interface Interface{  
    void doSomething();  
    void somethingElse(String arg);  
}  
//目标对象  
class RealObject implement Interface{  
    public void doSomething(){  
        System.out.println(“RealObject doSomething”);  
    }  
    public void somethingElse(String arg){  
        System.out.println(“RealObject somethingElse ” + arg);  
    }  
}  
//代理处理类  
class DynamicProxyHandler implements InvocationHandler{  
    private Object proxied;  
    public DynamicProxyHandler(Object proxied){  
        this.proxied = proxied;  
	}  
    //动态代理调用目标对象的方法  
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable{  
        System.out.println(“Dynamic proxy invoke”);  
        return method.invoke(proxied, args);  
    }  
}  
class SimpleDynamicProxy{  
    public static void consumer(Interface iface){  
        iface.doSomething();  
        iface.somethingElse(“DynamicProxy”);  
    }  
    public static void main(String[] args){  
        RealObject real = new RealObject();  
        //不是用代理  
        consumer(real);  
        //创建动态代理  
        Interface proxy = (Interface) Proxy.newProxyInstance(  
                        Interface.class.getClassLoader(),  
                        new Class[]{Interface.class},  
                        new DynamicProxyHandler(real));  
       cosumer(proxy);  
	}   
}  
```

输出结果为：

RealObject doSomething

RealObject somethingElse DynamicProxy

Dynamic proxy invoke

RealObject doSomething

Dynamic proxyinvoke       

RealObject somethingElse DynamicProxy
####   ClassLoader

双亲委派类加载实际上是通过parent字段递归，找到根ClassLoader。实际上根ClassLoader = Bootstrap ClassLoader

```java
public abstract class ClassLoader {
    
	protected Class<?> loadClass
        (String name, boolean resolve)
        throws ClassNotFoundException
    {
        synchronized (getClassLoadingLock(name)) {
          ...
          if (parent != null) {
              c = parent.loadClass(name, false);
          } else {
              c = findBootstrapClassOrNull(name);
          }
          ...
          return c;
        }
    }
    
    private Class<?> findBootstrapClassOrNull
        (String name)
    {
        if (!checkName(name)) return null;
        return findBootstrapClass(name);
    }
}
```



#####   系统ClassLoader

通过java.lang.ClassLoader#getSystemClassLoader 方法获取

#####   应用ClassLoader

通过当前Thread上下文ClassLoader获取（不完全一定）：java.lang.Thread#getContextClassLoader



####  Class与ClassLoader之间的关系

Class对象是通过ClassLoader的loadClass方法加载并获取。

ClassLoader可以通过Class的getClassLoader()方法获取。

.class文件或者网络I/O都是字节码流（Stream）,ClassLoader能够把二进制流解析为Class对象，通过复用ClassLoader#defineClass方法实现（重）定义Class类，如Groovy动态加载类。

```java
package com.yjy.classload;

import org.apache.commons.io.FileUtils;
import java.io.File;
import java.io.IOException;

public class ClassLoadingDemo {

    public static void main(String[] args) throws IOException {

        String className = "com.yjy.classload.v1.User";
        String classFileName = className.replace('.', '/').concat(".class");
        String classPath = System.getProperty("user.dir")+"/target/classes";

        File classFile = new File(classPath,classFileName);
        System.out.println(classFile.getAbsolutePath());
        MyClassLoader myClassLoader = new MyClassLoader();
        //.class 文件变为字节流 ，再定义class对象
        Class<?> userClass  = myClassLoader.defineClass(className,classFile);
        //输出 当前类对象class com.yjy.classload.v1.User
        System.out.println("当前类对象" + userClass);
    }

    static class MyClassLoader extends ClassLoader{
        public MyClassLoader(){
            //当前上下文的Class作为Parent
            super(Thread.currentThread().getContextClassLoader());
        }

        // 文件 -》 定义某个class
        public Class<?> defineClass(String name , File classFile) throws IOException {
            byte[] bytes = loadBytes(classFile);

            //利用 ClassLoader defineClass 来定义class
            return super.defineClass(name,bytes,0,bytes.length);
        }

        private byte[] loadBytes(File classFile) throws IOException {
            byte[] bytes = FileUtils.readFileToByteArray(classFile);
            return bytes;
        }
    }
}

```






















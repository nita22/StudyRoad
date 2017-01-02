## Java I/O

Java中使用流来处理程序的输入和输出操作，流是一个抽象的概念，封装了程序数据于输入输出设备交换的底层细节。JavaIO中又将流分为字节流和字符流，字节流主要用于处理诸如图像，音频视频等二进制格式数据，而字符流主要用于处理文本字符等类型的输入输出。

### 字节流

#### 字节输入流InputStream

输入流InputStream负责从各种数据/文件源产生输入，输入源包括：数组，字符串，文件，管道，一系列其他类型的流，以及网络连接产生的流等等。

常用字节输入流的主要类型：

* ByteArrayInputStream字节数组输入流：

  主要功能：允许内存缓存作为输入流。

  ByteArrayInputStream包含一个内部缓冲区，该缓冲区包含从流中读取的字节。内部计数器跟踪read()方法要提供的下一个字节。

  注意：关闭ByteArrayInputStream无效，该类中的方法在关闭此流之后仍可被调用，而不会产生任何的IOException。

* FileInputStream文件输入流：

  主要功能：从文件系统中的某个文件获得输入字节，用于读取诸如图像数据子类的原始字节流。若要读取字符流，请使用FileReader。

* PipedInputStream管道输入流：

  主要功能：和管道输出流一起构成一个输入输出的管道，是管道的数据输入端。

  管道输入流应该连接到管道输出流，管道输入流提供要写入管道输出流的所有数据字节。通常，这些数据有某个线程从PipedInputStream对象中读取，并有其他线程将其写入到相应的PipedOutputStream对象中。

  注意：不建议PipedInputStream和PipedOutputStream对象使用单线程，因为这样可能死锁线程。管道输入流包含一个缓冲区，可以在缓冲区限定范围内将读操作和写操作分离开，如果先连接管道输出流提供数据字节的线程不再存在，则认为该管道已损坏。

* SequenceInputStream顺序输入流：

  重要功能：将两个或多个输入流对象转换为一个单个输入流对象。

  SequenceInputStream表示其他输入流的逻辑串联关系，它从输入流的有序集合开始，并从第一个输入流开始读取，直到到达文件末尾，接着从第二个输入流读取，以此类推，直到到达包含的最后一个输入流的文件末尾为止。

* FilterInputStream过滤输入流：

  主要功能：包含其他一些输入流，将这些被包含的输入流用作其基本数据源，它可以直接传输数据或者提供一些额外的功能。

  常用的FilterInputStream是DataInputStream数据输入流，主要用于允许程序以与机器无关的方式从底层输入流中读取java基本数据类型。其常用的方法有readInt()，readBoolean()，readChar()等等。

#### 字节输出流OutputStream：

和字节输入流相对应，字节输出流负责字节类型数据想目标文件或设备的输出。常见的字节输出流如下：

* ByteArrayOutputStream字节数组输出流：

  主要功能：在内存中创建一个缓冲区，将接收到的数据放入该内存缓冲区中。

  ByteArrayOutputStream实现了一个输出流，其中的数据被写入一个byte数组中。缓冲区会随着数据的不断写入而自动增长，可使用toByteArray()和toString()方法获取数据。

  注意：和ByteArrayInputStream类似，关闭ByteArrayOutputStream也是无效的，此类中的方法在关闭此流后仍可被调用，而不会产生任何IOException。

* FileOutputStream文件输出流：

  主要功能：将数据写入到指定文件中。

  文件输出流是用于将数据写入File或FIleDescriptor的输出流，用于写入诸如图像数据之类的原始字节流，若要写入字符流，请使用FileWriter。

* PipedOutputStream管道输出流：

  主要功能：连接管道输入流用来创建通信管道，管道输出流是管道数据输出端。

* FilterOutputStream过滤输出流：

  主要功能：用于将已存在的输出流作为其基本数据接收器，可以直接传输数据或提供一些额外的处理。

  常用的FilterOutputStream是DataOutputStream数据输出流，它允许程序以适当的方式将java基本数据类型写入输出流中。其常用方法有writeInt(intV)，writeChar(int v)，writeByte(String s)等等。

### 字符流：

Java中的字节流只能针对字节类型数据，即支持处理8位的数据类型，由于java中的是Unicode码，即两个字节代表一个字符，于是在JDK1.1之后提供了字符流Reader和Writer。

字符流相关常用类如下：

* Reader：

用于读取字符串流的抽象类，子类必须实现的方法只有reader(char[],int, int)和close()。

* InputStreamReader：

是将字节输入流转换为字符输入流的转换器，它使用指定的字符集读取字节并将其解码为字符。即：字节——>字符。

它使用的字符集可以有名称指定或显式给定，也可以使用平台默认的字符集。

* Writer：

用于写入字符流的抽象类，子类必须实现的方法只有write(char[],int, int)和close()。

* OutputStreamWriter：

是将字符输出流转换为字节输出流的转换器，它使用指定的字符集将要写入流的字符编码成字节。即：字符——>字节。

#### 综合使用java IO各种流：

Java IO中的各种流，很少单独使用，经常结合起来综合使用，既可以满足特定需求，又高效。例子如下：

* 使用缓冲流读取文件：

``` java
import java.io.*;  
  
public class BufferedInputFile{  
    public static String read(String filename) throws IOException{  
            //缓冲字符输入流  
            BufferedReader in = new BufferedReader(new FileReader(filename));  
            String s;  
            StringBuilder sb = new StringBuilder();  
            //每次读取文件中的一行  
            While((s = in.readLine()) != null){  
            sb.append(s + “\n”);  
        }  
        in.close();  	
        return sb.toString();  
    }  
    public static void main(String[] args) throws IOException{  
        System.out.println(read(“BufferedInputFile.java”));  
    }  
}  
```

* 读取内存中的字符串：

``` java
import java.io.*;  
  
public class MemoryInput{  
    public static void main(String[] args) throws IOException{  
        //将字符串包装为字符输入流  
        StringReader in = new StringReader(  
  			BufferedInputFile.read(“BufferedInputFile.java”));  
        int c;  
        //读取字符输入流中的字符  
        while((c == in.read()) != -1){  
            System.out.println((char)c);  
        }  
	}  
}  
```

* 数据输入/输出流：

``` java
import java.io.*;  
  
public class DataInputOutput{  
    public static void main(String[] args) thows IOException{  
        DataOutputStream out = new DataOutputStream(new BufferedOutputStream(  
			new FileOutputStream(“Data.txt”)));  
        out.writeDouble(3.14159);  
        out.writeUTF(“That was pi”);  
        out.writeDouble(1.41413)；  
        out.writeUTF(“Square root of 2”);  
        out.close();  
        DataInputStream in = new DataInputStream(new BufferedInputStream(  
			new FileOutputStream(“Data.txt”)));  
        System.out.println(in.readDouble());  
        System.out.println(in.readUTF());  
		System.out.println(in.readDouble());  
        System.out.println(in.readUTF());  
	}  
}  
```

输出结果：

3.14159

That was pi

1.41413

Square root of 2

* 文本文件输出流：

``` java
import java.io.*;  
  
public class TextFileOutput{  
    //输出文件名  
    static String file = “BasicFileOutput.out”;  
    public static void main(String[] args) throws IOException{  
            //将字符串先包装成字符串输入流，然后将字符串输入流再包装成缓冲字符输入流  
        BufferedReader in = new BufferedReader(new StringReader  
   			 (BufferedInputFile.read(“TextFileOutput.java”)));  
            //字符输出流  
        PrintWriter out = new PrintWriter(new BufferedWriter(new 		          FileWriter(file)));  
        int lineCount = 1;  
        String s;  
        While((s = in.readLine()) != null){  
            out.println(lineCount++ + “: ” + s);  
        }  
        out.close();  
    }  
}  
```

* 二进制文件读写：

``` java
import java.io.*;  
  
public class BinaryFileOutput{  
    //输出文件名  
    static String file = “BinaryFileOutput.out”;  
    public static void main(String[] args) throws IOException{  
        //将字符串先包装成字符串输入流，然后将字符串输入流再包装成缓冲字符输入流  
        BufferedInputStream in = new BufferedInputStream(  
    new FileInputStream(“TestFile.png”)));  
        //字符输出流  
        BufferedOutputStream out = new BufferedOutputStream (  
new FileOutputStream(file));  
        byte[] buf = new byte[1024];  
        int n;  
        While((n = in.read(buf)) > 0){  
            out.write(buf, 0, n);  
        }  
        out.close();  
        }  
}  
```


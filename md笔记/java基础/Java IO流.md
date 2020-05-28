# Java IO流

## 1、文件系统

- 文件系统是由OS（操作系统）管理的；
- 文件系统和进程是平行的，是两套系统；
- 文件系统是由文件夹和文件递归组合而成；
- 文件包括文件里面的内容和文件基本属性。

**java文件类File**

- File类与OS无关，但会受到OS的权限控制；
- File不涉及到文件内容，只涉及属性。

**基本创建：**

```java
import java.io.File;
import java.io.IOException;

public class MyFileTest {
    public static void main(String[] args) {
        String path="c:/temp";
        String path2="c:/temp/abc.txt";
        File file=new File("path");
        if(!file.exists()){
            file.mkdir();//mkdir创建单级目录，mkdirs创建多级目录
        }
        File file1=new File(path2);
        try {
            file1.createNewFile();//如果已存在不会覆盖,返回false,如果存在则创建文件，返回true
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```

**常用方法：**

```java
//创建：
createNewFile()//在指定位置创建一个空文件，成功就返回true，如果已存在就不创建，然后返回false。
mkdir()  //在指定位置创建一个单级文件夹。
mkdirs()  //在指定位置创建一个多级文件夹。
renameTo(File dest)//如果目标文件与源文件是在同一个路径下，那么renameTo的作用是重命名， 如果目标文件与源文件不是在同一个路径下，那么renameTo的作用就是剪切，而且还不能操作文件夹。 
//删除：
delete()  //删除文件或者一个空文件夹，不能删除非空文件夹，马上删除文件，返回一个布尔值。
deleteOnExit()//jvm退出时删除文件或者文件夹，用于删除临时文件，无返回值。
    
//判断：
exists()  //文件或文件夹是否存在。
isFile()  //是否是一个文件，如果不存在，则始终为false。
isDirectory()  //是否是一个目录，如果不存在，则始终为false。
isHidden()  //是否是一个隐藏的文件或是否是隐藏的目录。
isAbsolute()  //测试此抽象路径名是否为绝对路径名。
 获取：
getName()  //获取文件或文件夹的名称，不包含上级路径。
getAbsolutePath//()获取文件的绝对路径，与文件是否存在没关系
length()  //获取文件的大小（字节数），如果文件不存在则返回0L，如果是文件夹也返回0L。
getParent()  //返回此抽象路径名父目录的路径名字符串；如果此路径名没有指定父目录，则返回null。
lastModified()//获取最后一次被修改的时间。

//文件夹相关：
static File[] listRoots()//列出所有的根目录（Window中就是所有系统的盘符）
list()  //返回目录下的文件或者目录名，包含隐藏文件。对于文件这样操作会返回null。
listFiles()  //返回目录下的文件或者目录对象（File类实例），包含隐藏文件。对于文件这样操作会返回null。
list(FilenameFilter filter)//返回指定当前目录中符合过滤条件的子文件或子目录。对于文件这样操作会返回null。
listFiles(FilenameFilter filter)//返回指定当前目录中符合过滤条件的子文件或子目录。对于文件这样操作会返回null。
```

## 2、io包概述

**位、字符和字节**

| 单位        | 说明                                                         |
| :---------- | ------------------------------------------------------------ |
| 位（bit)    | 数据存储的最小单位。每个二进制数字0或者1就是1个位；          |
| 字节（byte) | 是计算机中数据处理的基本单位，习惯上用大写 B 来表示,1B（byte,字节）= 8bit（位） |
| 字符        | 是指计算机中使用的字母、数字、字和符号。                     |
| 数据类型    | int、double、String等                                        |

**io包**

- Java读写文件，只能以（数据）流的形式进行读写；
- 节点类：直接对文件进行读写，集直接操作文件类：
  - InputStream、OutputStream（字节）
    - FileInputStream、FileOutputStream
  - Reader、Writer（字符）
    - FileReader、FileWriter
- 包装类：
  - 转换类：字符到字节之间的转化
    - InputStreamReader:文件读取时字节，转化为Java能理解的字符；
    - OutputStreamWriter： Java将字符转化为字节输入到文件中。
  - 装饰类：装饰节点类。
    - DataInputStream、DataOutputStream:封装数据流
    - BufferedInputStream、BufferedOutputStream:缓存字节流
    - BufferedReader、BufferedWriter:缓存字符流

## 3、文本文件读写

- 从Java角度：
  - 输出：数据从Java到文件中，写操作；
  - 输入：数据从文件到Java中，读操作。
- 文本文件读写：
  - 输出文本文件到文件中；
  - 从文件中读取文本字符串。
- 写文件：
  - 创建文件，写入数据，关闭文件；
  - 使用类：
    - FileOutputStream：往文件写字节；
    - OutputStreamWriter：字节和字符串转化；
    - BufferedWriter：使用缓冲区类，加速写操作。
  - try-resource语句，自动关闭资源。
  - 关闭最外层数据流，将会把其他所有的数据流关闭。

```java
import java.io.*;

public class OutputFile {
    public static void main(String[] args) {
        FileOutputStream fileOutputStream = null;
        OutputStreamWriter outputStreamWriter = null;
        BufferedWriter bufferedWriter = null;
        try {
            fileOutputStream = new FileOutputStream("C:/temp/abc.txt");//字节类
            outputStreamWriter = new OutputStreamWriter(fileOutputStream, "utf-8");//转化类
            bufferedWriter = new BufferedWriter(outputStreamWriter);//装饰类
            bufferedWriter.write("张三，hello world!");//写入数据
            bufferedWriter.newLine();//换行
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                bufferedWriter.close();//包装的流会自动调用被包装的流的关闭方法，无需自己调用,也就是关闭流只需要关闭最外层的包装流，其他流会自动调用关闭
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
    }

}
//注意;注意： bufferedWriter.write(int c)方法虽然存在，但在io包中是将int强转为char输入文件，容易乱码。
```

使用try-resources自动关闭资源：

```java
import java.io.*;

public class OutputFile {
    public static void main(String[] args) {
        //try-resource语句，自动关闭资源
        try (BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(new FileOutputStream("C:/temp/abc.txt"), "utf-8"))) {
            bufferedWriter.write("张三，hello world!");//写入数据
            bufferedWriter.newLine();//换行
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```

- 读文件：
  - 先写入文件，**逐行**读入数据，关闭文件
  - BufferReader
    - readLine

```java
import java.io.*;

public class ReadFile {
    public static void main(String[] args) {
        try(BufferedReader bufferedReader=new BufferedReader(new InputStreamReader(new FileInputStream("c:/temp/abc.txt"),"utf-8"))) {
            String line;
            while((line=bufferedReader.readLine())!=null){
                System.out.println(line);
            }
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```


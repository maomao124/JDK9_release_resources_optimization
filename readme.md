

## 释放资源代码优化

### 概述

JDK8以前释放资源代码非常累赘， 如果释放资源较多的时候，很容易就会出现释放资源代码超过了正常业务的代码. 对此随着jdk版本的不断更新迭代，也对释放资源代码做了很大幅度的优化

JDK8开始已经不需要我们再手动关闭资源，只需要把要关闭资源的代码放入try语句中即可，但是要求初始化资源的语句必须位于try语句中

JDK9不要求初始化资源的语句必须位于try语句中





### 使用

jdk8以前：

```java
package mao;

import java.io.BufferedWriter;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStreamWriter;
import java.nio.charset.StandardCharsets;

/**
 * Project name(项目名称)：JDK9_release_resources_optimization
 * Package(包名): mao
 * Class(类名): Test1
 * Author(作者）: mao
 * Author QQ：1296193245
 * GitHub：https://github.com/maomao124/
 * Date(创建日期)： 2023/10/31
 * Time(创建时间)： 16:14
 * Version(版本): 1.0
 * Description(描述)： 无
 */

public class Test1
{
    public static void main(String[] args)
    {
        FileOutputStream fileOutputStream = null;
        OutputStreamWriter outputStreamWriter = null;
        BufferedWriter bufferedWriter = null;
        try
        {
            fileOutputStream = new FileOutputStream("a.txt");
            outputStreamWriter = new OutputStreamWriter(fileOutputStream, StandardCharsets.UTF_8);
            bufferedWriter = new BufferedWriter(outputStreamWriter);
            bufferedWriter.write("hello");
            bufferedWriter.write(" world");
            bufferedWriter.flush();
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
        finally
        {
            try
            {
                if (bufferedWriter != null)
                {
                    bufferedWriter.close();
                }
            }
            catch (IOException e)
            {
                e.printStackTrace();
            }
            try
            {
                if (outputStreamWriter != null)
                {
                    outputStreamWriter.close();
                }
            }
            catch (IOException e)
            {
                e.printStackTrace();
            }
            try
            {
                if (fileOutputStream != null)
                {
                    fileOutputStream.close();
                }
            }
            catch (IOException e)
            {
                e.printStackTrace();
            }
        }

    }
}
```





jdk8：

```java
package mao;

import java.io.BufferedWriter;
import java.io.FileOutputStream;
import java.io.OutputStreamWriter;
import java.nio.charset.StandardCharsets;

/**
 * Project name(项目名称)：JDK9_release_resources_optimization
 * Package(包名): mao
 * Class(类名): Test2
 * Author(作者）: mao
 * Author QQ：1296193245
 * GitHub：https://github.com/maomao124/
 * Date(创建日期)： 2023/10/31
 * Time(创建时间)： 16:15
 * Version(版本): 1.0
 * Description(描述)： 无
 */

public class Test2
{
    public static void main(String[] args)
    {
        try (FileOutputStream fileOutputStream = new FileOutputStream("b.txt");
             OutputStreamWriter outputStreamWriter = new OutputStreamWriter(fileOutputStream, StandardCharsets.UTF_8);
             BufferedWriter bufferedWriter = new BufferedWriter(outputStreamWriter))
        {
            bufferedWriter.write("hello");
            bufferedWriter.write(" world");
            bufferedWriter.flush();
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
    }
}
```





jdk9：

```java
package mao;

import java.io.BufferedWriter;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.OutputStreamWriter;
import java.nio.charset.StandardCharsets;

/**
 * Project name(项目名称)：JDK9_release_resources_optimization
 * Package(包名): mao
 * Class(类名): Test3
 * Author(作者）: mao
 * Author QQ：1296193245
 * GitHub：https://github.com/maomao124/
 * Date(创建日期)： 2023/10/31
 * Time(创建时间)： 16:15
 * Version(版本): 1.0
 * Description(描述)： 无
 */

public class Test3
{
    public static void main(String[] args) throws FileNotFoundException
    {
        FileOutputStream fileOutputStream = new FileOutputStream("c.txt");
        OutputStreamWriter outputStreamWriter = new OutputStreamWriter(fileOutputStream, StandardCharsets.UTF_8);
        BufferedWriter bufferedWriter = new BufferedWriter(outputStreamWriter);
        try (fileOutputStream; outputStreamWriter; bufferedWriter)
        {
            bufferedWriter.write("hello");
            bufferedWriter.write(" world");
            bufferedWriter.flush();
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
    }
}
```




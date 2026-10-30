---
date: 2026-10-30
authors:
  - lee
categories:
  - Java
  - 编程
tages:
  - Java
---
#Java Runtime.exec取stderr阻塞的解决办法

原因：

Because some native platforms only provide limited buffer size for standard input and output streams, failure to promptly write the input stream or read the output stream of the subprocess may cause the subprocess to block, and even deadlock.\
翻译：\
一些平台只为标准输入输出提供有限的缓存。错误的写子进程的输入流或者错误的都子进程的输出流都有可能造成子进程的阻塞，甚至是死锁。

解决方法：

把读取stderr及stdout的操用放到单独的线程中，示例代码：

```java

Process process = Runtime.getRuntime().exec(this.command, envs);
 //System.out.println("-------------------------------------");
 BufferedReader ebr = new BufferedReader(new InputStreamReader(process.getErrorStream()));
 Thread eThread = new Thread(() -> 
 try 
 String eline;
 while (null != (eline = ebr.readLine())) 
 System.out.println("[Error]" + eline);
 
 ebr.close();
 catch (Exception e) 
 e.printStackTrace();
 
 );
 eThread.start();
 //System.out.println("-------------------------------------");
 BufferedReader br = new BufferedReader(new InputStreamReader(process.getInputStream()));
 Thread oThread = new Thread(() -> 
 try 
 String line;
 while (null != (line = br.readLine())) 
 System.out.println(line);
 
 br.close();
 br.close();
 catch (Exception e) 
 e.printStackTrace();
 
 );
 oThread.start();
 process.waitFor();

 ```
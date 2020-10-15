![微信截图_20200828215718](Socket编程.assets/微信截图_20200828215718.png)

IP层的ip地址可以唯一标示主机，而TCP层协议和端口号可以唯一标示主机的一个进程，这样我们可以利用ip地址＋协议＋端口号唯一标示网络中的一个进程。

能够唯一标示网络中的进程后，它们就可以利用socket进行通信了

**socket是在应用层和传输层之间的一个抽象层，它把TCP/IP层复杂的操作抽象为几个简单的接口供应用层调用已实现进程在网络中通信**如下图所示

![微信截图_20200908093519](Socket编程.assets/微信截图_20200908093519.png)

以使用TCP协议通讯的socket为例，其交互过程大致是：

![微信截图_20200908093850](Socket编程.assets/微信截图_20200908093850.png)

javasocket编程：

**BIO**

客户端：

````java
    public static void main(String[] args) {
        try {
            Socket socket = new Socket("localhost",8080);
            OutputStream outputStream = socket.getOutputStream();
            Scanner s = new Scanner(System.in);
            System.out.println("请输入");
            String s1 = s.nextLine();
            outputStream.write(s1.getBytes());
            s.close();
            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
````

服务端

````java
    public static void main(String[] args) {
        Socket request = null;
        try {
            ServerSocket serverSocket = new ServerSocket(8080);
            System.out.println("服務器啟動完成");
            while (!serverSocket.isClosed()){
                //阻塞
                 request = serverSocket.accept();
                System.out.println("收到新的连接："+request.toString());
                InputStream inputStream = request.getInputStream();
                BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream, StandardCharsets.UTF_8));
                String msg;
                while ((msg = reader.readLine()) != null){
                    System.out.println(msg);
                }
                System.out.println("收到数据，来自:" + request.toString());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if(null != request){
                try {
                    request.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
````

*accept是阻塞的只有当处理完当前的请求才能迎接下一个*

````java
    // 创建一个 核心线程数量为5，最大数量为10,等待队列最大是3 的线程池，也就是最大容纳13个任务。
    private static ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(5, 10, 5, TimeUnit.SECONDS, new LinkedBlockingQueue<>(3), new RejectedExecutionHandler() {
        @Override
        public void rejectedExecution(Runnable r, java.util.concurrent.ThreadPoolExecutor executor) {
            System.err.println("有任务被拒绝了");
        }
    });

    public static void main(String[] args) {
        try {
            ServerSocket serverSocket = new ServerSocket(8080);
            System.out.println("服务器启动成功");
            while (!serverSocket.isClosed()) {
                //阻塞
                Socket request = serverSocket.accept();
                System.out.println("收到新连接 : " + request.toString());
                Future<?> submit = threadPoolExecutor.submit(() -> {
                    try {
                        InputStream inputStream = request.getInputStream();
                        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream, StandardCharsets.UTF_8));
                        String msg = "";
                        while ((msg = bufferedReader.readLine()) != null) {
                            if (msg.length() == 0) {
                                break;
                            }
                            System.out.println(msg);
                        }
                        System.out.println("收到数据,来自：" + request.toString());
                    } catch (IOException e) {
                        e.printStackTrace();
                    } finally {
                        try {
                            request.close();
                        } catch (IOException e) {
                            e.printStackTrace();
                        }
                    }
                });
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
````

*可以开多个线程来执行*

````java
 // 创建一个 核心线程数量为5，最大数量为10,等待队列最大是3 的线程池，也就是最大容纳13个任务。
    private static ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(5, 10, 5, TimeUnit.SECONDS, new LinkedBlockingQueue<>(3), new RejectedExecutionHandler() {
        @Override
        public void rejectedExecution(Runnable r, java.util.concurrent.ThreadPoolExecutor executor) {
            System.err.println("有任务被拒绝了");
        }
    });

    public static void main(String[] args) {
        try {
            ServerSocket serverSocket = new ServerSocket(8080);
            System.out.println("服务器启动成功");
            while (!serverSocket.isClosed()) {
                //阻塞
                Socket request = serverSocket.accept();
                System.out.println("收到新连接 : " + request.toString());
                Future<?> submit = threadPoolExecutor.submit(() -> {
                    try {
                        InputStream inputStream = request.getInputStream();
                        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream, StandardCharsets.UTF_8));
                        String msg = "";
                        while ((msg = bufferedReader.readLine()) != null) {
                            if (msg.length() == 0) {
                                break;
                            }
                            System.out.println(msg);
                        }
                        System.out.println("收到数据,来自：" + request.toString());
                        // 响应结果 200
                        OutputStream outputStream = request.getOutputStream();
                        outputStream.write("HTTP/1.1 200 OK\r\n".getBytes());
                        outputStream.write("Content-Length: 11\r\n\r\n".getBytes());
                        outputStream.write("Hello World".getBytes());
                        outputStream.flush();
                    } catch (IOException e) {
                        e.printStackTrace();
                    } finally {
                        try {
                            request.close();
                        } catch (IOException e) {
                            e.printStackTrace();
                        }
                    }
                });
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
````

*http协议是基于TCP协议的超文本传输协议，HTTP连接最显著的特点是客户端发送的每次请求都需要服务器回送响应，在请求结束后，会主动释放连接。从建立连接到关闭连接的过程称为“一次连接”*

````java
 // 创建一个 核心线程数量为5，最大数量为10,等待队列最大是3 的线程池，也就是最大容纳13个任务。
    private static ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(5, 10, 5, TimeUnit.SECONDS, new LinkedBlockingQueue<>(3), new RejectedExecutionHandler() {
        @Override
        public void rejectedExecution(Runnable r, java.util.concurrent.ThreadPoolExecutor executor) {
            System.err.println("有任务被拒绝了");
        }
    });

    public static void main(String[] args) {
        try {
            ServerSocket serverSocket = new ServerSocket(8080);
            System.out.println("服务器启动成功");
            while (!serverSocket.isClosed()) {
                //阻塞
                Socket request = serverSocket.accept();
                System.out.println("收到新连接 : " + request.toString());
                Future<?> submit = threadPoolExecutor.submit(() -> {
                    try {
                        InputStream inputStream = request.getInputStream();
                        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream, StandardCharsets.UTF_8));
                        String msg = "";
                        while ((msg = bufferedReader.readLine()) != null) {
                            if (msg.length() == 0) {
                                break;
                            }
                            System.out.println(msg);
                        }
                        System.out.println("收到数据,来自：" + request.toString());
                        // 响应结果 200
                        OutputStream outputStream = request.getOutputStream();
                        outputStream.write("HTTP/1.1 200 OK\r\n".getBytes());
                        outputStream.write("Content-Length: 11\r\n\r\n".getBytes());
                        outputStream.write("Hello World".getBytes());
                        outputStream.flush();
                    } catch (IOException e) {
                        e.printStackTrace();
                    } finally {
                        try {
                            request.close();
                        } catch (IOException e) {
                            e.printStackTrace();
                        }
                    }
                });
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
````



**NIO**

ByteBuffer :缓冲区，本质是一个可以写入数据的内存块，然后可以再次读取。

- 使用buffer进行数据写入与读取，需要如下四个步骤：
  1. 将数据写入缓冲区
  2. 调用buffer.flip(),转换为读取模式
  3. 缓冲区读取数据
  4. 调用buffer.clear(）或buffer.compact清除缓冲区
- 三个重要属性：
  1. capacity容量
  2. position位置
  3. limit限制：写入模式，限制等于Buffer的容量。读取模式下，limit等于写入的数据量

- 为性能关键性代码提供了**直接内存（direct堆外）和非直接内存（heap堆）**两种实现。

  - 堆外内存的获取方式：

    ````java
    ByteBuffer directByteBuffer = ByteBuffer.allocateDirect(noBytes);
    ````

  - 好处：

    1. 进行网络io或者文件io时比heapBuffer少一次拷贝。原因：(file/socket --- OS memory --- java heap) GC会移动对象内存，在写fule或者socket的过程中，JVM的实现中，会先把数据复制到堆外，再进行写入。

       - 为什么会复制到堆外：

         在jvm进行io写入时，会调用操作系统的api，然后传给操作系统要写入数据的内存地址，如果不复制，**GC会移动对象的内存，会导致jvm的地址改变**，操作系统直接读取jvm的heap，会导致读取不到。所以要先复制一份到堆外内存，然后告诉操作系统复制后的地址，再进行写入
       
    2. GC范围之外,降低GC压力，但实现了自动化管理。DirectByteBuffer中有一个Cleaner对象（PhantomReference）,Cleaner被GC前会执行clean方法，触发DirectByteBuffer中定义的Deallocator
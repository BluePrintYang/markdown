与网络调试助手通信

问题1：网络调试助手作为客户端，发送消息后，客户端未收到

原因：readLine（）这个方法本身有一个注意点，它读取的是一整行，遇见‘\n’或 ‘ \r’ 代表读取结束。因为用的是网络助手作为客户端 ，每次发送消息时并不会添加‘\n’，而用控制台作为客户端进行发送时，按下回车的时候就已经添加了‘\n’，所以服务器端进行接收的时候就是实时显示的。



互相发送信息，互相接收，每次一条



服务器端代码

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.ServerSocket;
import java.net.Socket;

public class SocketServer {
    //搭建服务器端
    public static void main(String[] args) {
        //1、a)创建一个服务器端Socket，即SocketService
        SocketServer socketService = new SocketServer();
        socketService.oneServer();
    }
    private void oneServer(){
        try{
            ServerSocket server=null;
            try{
                //下面是端口，端口可以和客户端代码里面的端口一样
                server=new ServerSocket(5209);
                //b)指定绑定的端口，并监听此端口。
                System.out.println("服务器启动成功");
                //创建一个ServerSocket在端口5209监听客户请求
            }catch(Exception e) {
                System.out.println("没有启动监听："+e);
                //出错，打印出错信息
            }
            Socket socket=null;
            try{
                socket=server.accept();
                //2、调用accept()方法开始监听，等待客户端的连接 
                //使用accept()阻塞等待客户请求，有客户
                //请求到来则产生一个Socket对象，并继续执行
            }catch(Exception e) {
                System.out.println("Error."+e);
                //出错，打印出错信息
            }
            //3、获取输入流，并读取客户端信息 
            String line;
            BufferedReader in=new BufferedReader(new InputStreamReader(socket.getInputStream()));
            //由Socket对象得到输入流，并构造相应的BufferedReader对象
            PrintWriter writer=new PrintWriter(socket.getOutputStream());
            //由Socket对象得到输出流，并构造PrintWriter对象
            BufferedReader br=new BufferedReader(new InputStreamReader(System.in));
            //由系统标准输入设备构造BufferedReader对象
            System.out.println("Client:"+in.readLine());
            //在标准输出上打印从客户端读入的字符串
            line=br.readLine();
            //从标准输入读入一字符串
            //4、获取输出流，响应客户端的请求 
            while(!line.equals("end")){
                //如果该字符串为 "bye"，则停止循环
                writer.println(line);
                //向客户端输出该字符串
                writer.flush();
                //刷新输出流，使Client马上收到该字符串
                System.out.println("服务:"+line);
                //在系统标准输出上打印读入的字符串
                System.out.println("客户:"+in.readLine());
                //从Client读入一字符串，并打印到标准输出上
                line=br.readLine();
                //从系统标准输入读入一字符串
            } //继续循环

            //5、关闭资源 
            writer.close(); //关闭Socket输出流
            in.close(); //关闭Socket输入流
            socket.close(); //关闭Socket
            server.close(); //关闭ServerSocket
        }catch(Exception e) {//出错，打印出错信息
            System.out.println("Error."+e);
        }
    }
}
```



客户端代码

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;

public class SocketClient {
    // 搭建客户端
    public static void main(String[] args) {
        try {
            // 1、创建客户端Socket，指定服务器地址和端口

            //下面是你要传输到另一台电脑的IP地址和端口
            Socket socket = new Socket("127.0.0.1", 5209);
            System.out.println("客户端启动成功");
            // 2、获取输出流，向服务器端发送信息
            // 向本机的52000端口发出客户请求
            BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
            // 由系统标准输入设备构造BufferedReader对象
            PrintWriter write = new PrintWriter(socket.getOutputStream());
            // 由Socket对象得到输出流，并构造PrintWriter对象
            //3、获取输入流，并读取服务器端的响应信息 
            BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            // 由Socket对象得到输入流，并构造相应的BufferedReader对象
            String readline;
            readline = br.readLine(); // 从系统标准输入读入一字符串
            while (!readline.equals("end")) {
                // 若从标准输入读入的字符串为 "end"则停止循环
                write.println(readline);
                // 将从系统标准输入读入的字符串输出到Server
                write.flush();
                // 刷新输出流，使Server马上收到该字符串
                System.out.println("客户:" + readline);
                // 在系统标准输出上打印读入的字符串
                System.out.println("服务:" + in.readLine());
                // 从Server读入一字符串，并打印到标准输出上
                readline = br.readLine(); // 从系统标准输入读入一字符串
            } // 继续循环
            //4、关闭资源 
            write.close(); // 关闭Socket输出流
            in.close(); // 关闭Socket输入流
            socket.close(); // 关闭Socket
        } catch (Exception e) {
            System.out.println("can not listen to:" + e);// 出错，打印出错信息
        }
    }

}
```





服务器端持续接收信息，并返回客户端以及写好的信息

```Java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.Date;

public class SocketServerContinue {

    private BufferedWriter writer;

    private BufferedReader reader;

    public static void main(String[] args) {
        SocketServerContinue socketServer = new SocketServerContinue();
        socketServer.startServer();
    }

    private void startServer() {
        ServerSocket serverSocket = null;
        Socket socket = null;
        try {
            // 端口号只要不冲突就行
            serverSocket = new ServerSocket(9898);
            System.out.println("server started..");
            while (true) {
                // 进入阻塞状态，等待客户端接入
                socket = serverSocket.accept();
                manageConnection(socket);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                assert socket != null;
                socket.close();
                serverSocket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    private void manageConnection(final Socket socket) {
        new Thread(() -> {
            try {
                System.out.println("client " + socket.hashCode() + " connected");
                // 从socket中读取数据
                reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
                writer = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
                String receiveMsg;
                while ((receiveMsg = reader.readLine()) != null) { // 以"\n"结束
                    System.out.println("client " + socket.hashCode() + ":" + receiveMsg);
                    Person person = new Person();
                    person.setBirthday(new Date());
                    person.setClassId("B170404");
                    person.setId("B17040417");
                    person.setName("yll");
                    writer.write("server reply:" + person.toString() + "\n");
                    writer.flush();
                }
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                try {
                    reader.close();
                    writer.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }

}
```


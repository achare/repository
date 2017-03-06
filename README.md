public class LoginClient {
	public LoginClient() {
		// TODO Auto-generated constructor stub
	}

	public static void main(String[] args) throws IOException, IOException {
		Socket socket=new Socket(InetAddress.getLocalHost(), 9090);// TODO Auto-generated method stub
        
		OutputStream outputstream=socket.getOutputStream();
        OutputStreamWriter oreader=new OutputStreamWriter(outputstream);
        BufferedWriter socketout=new BufferedWriter(oreader);//输出流至服务器
        
        InputStream inputstream=socket.getInputStream();
        InputStreamReader inputstreamreader=new InputStreamReader(inputstream);
        BufferedReader socketreader=new BufferedReader(inputstreamreader);//接受服务器的输入流
        
        InputStreamReader inboard=new InputStreamReader(System.in);//键盘输入的是字节流，reader把它转化为字符流。
        BufferedReader keyreader=new BufferedReader(inboard);
        System.out.println("请选择 a（登录）  b（注册）");
        String option=keyreader.readLine();
        
        if("a".equalsIgnoreCase(option)){
        	getInfo(socketout, socketreader, keyreader, option);
        }else if("b".equalsIgnoreCase(option)){
        	getInfo(socketout, socketreader, keyreader, option);
        }    
	}
	private static void getInfo(BufferedWriter socketout, BufferedReader socketreader, BufferedReader keyreader,
			String option) throws IOException {
		System.out.println("请输入姓名:");
		String users=keyreader.readLine();
		System.out.println("请输入密码:");
		String password=keyreader.readLine();
		String info=option+" "+users+" "+password;
		socketout.write(info);
		socketout.flush();
		String line=socketreader.readLine();
		System.out.println(line);
	}
}
---------
package login;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.io.OutputStreamWriter;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.Properties;

public class LoginServer {

	public LoginServer() {
		// TODO Auto-generated constructor stub
	}

	public static void main(String[] args) throws IOException {
		ServerSocket serverSocket=new ServerSocket(9090);// TODO Auto-generated method stub
        Socket socket=serverSocket.accept();               
		
        OutputStream os=socket.getOutputStream();
        OutputStreamWriter osw=new OutputStreamWriter(os);
        BufferedWriter  bfw=new BufferedWriter(osw);
        
        InputStream  is=socket.getInputStream();
		InputStreamReader isr=new InputStreamReader(is);
        BufferedReader br=new BufferedReader(isr);
        String info=br.readLine();
        String[] datas=info.split(" ");
        
        String option=datas[0];
        String users=datas[1];
        String password=datas[2];
        File file=new File("F:\\users.properties");
        if(!file.exists()){
        	file.createNewFile();
        }
        FileWriter ots=new FileWriter(file);
    	
        Properties pro=new Properties();//生成配置对象以便调用方法处理数据，存储时再生成配置文件
    	
    	
       while(true){
    	   if("a".equalsIgnoreCase(option)){
       
        	if(pro.containsKey(users)){
        		pro.load(new FileReader("F:\\users.properties"));
        		if(password.equals(pro.getProperty(users))){
        			bfw.write("登录成功");
        		}else{
        			bfw.write("密码错误");
        		}
        		
        	}else if(!pro.containsKey(users)){
        		bfw.write("用户名不存在");
        	}
        	
        	
        	
        }else if("b".equalsIgnoreCase(option)){
            
        	
            pro.load(new FileReader("F:\\users.properties"));
            if(pro.containsKey(users)){
            	bfw.write("用户名已存在");
            }else if(!pro.containsKey(users)){
            	pro.setProperty(users, password);
                pro.store(ots,"userinfo");
                bfw.write("注册成功");
                bfw.flush();
            }
           
        	
        }
        
        
        
	}
       }

}


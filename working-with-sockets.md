# Working with Sockets

#### What is a Socket?

A socket is another word for a connection. Sockets are used to link different computers together, through a network. This allows for the flow of information; being sent and received on both ends of the connection.

#### Hello World!

Lets get started on a simple client-server program. This program will have a server, which sends a string and a client that receives and outputs the string to the console.

Lets start off with creating a server class.

```java
import java.io.*;
import java.net.*;

public class Server {

    public static Socket clientSocket = null;
    public static ServerSocket serverSocket = null;

    private static ObjectOutputStream output;

    public static void main(String[] args) {

    }
}
```

Here we need 2 sockets :

**ServerSocket - Waits and accepts requests from the network**

**Socket - The accepted connection from the client**

The ObjectOutStream will be used to send information to the client.

Next lets create the methods used in each stage of the server loop.

```java
import java.io.*;
import java.net.*;

public class Server {
    public static Socket clientSocket = null;
    public static ServerSocket serverSocket = null;

    private static ObjectOutputStream output;

    public static void setup() throws IOException {
        serverSocket = new ServerSocket(1234);
        System.out.println("SERVER: Ready to roll!");
    }

    public static void waitForClient() throws IOException {
        System.out.println("SERVER: Waiting for client to accept connection...");
        clientSocket = serverSocket.accept();
        output = new ObjectOutputStream(clientSocket.getOutputStream());
        System.out.println("SERVER: A client has accepted");
    }

    public static void sendString(String string) throws IOException {
        System.out.println("SERVER: Sending...");
        output.writeObject(string);
        output.flush();
    }

    public static void end() throws IOException {
        System.out.println("SERVER: Time to close up shop :) ");
        serverSocket.close();
        clientSocket.close();
        output.close();
    }

    public static void main(String[] args) {

    }
}
```

1. **setup\(\) **- This method is called initially and is used to setup the ServerSocket
2. **waitForClient\(\)** - This method waits for a client connection through the ServerSocket and sets the clientSocket to that connection
3. **sendString\(\) **- This method sends a given string through the clientSocket
4. **end\(\) **- This method closes up all the sockets and streams

Now that basic functionality has been created, lets put everything in order in the **main\(\)** method

```java
import java.io.*;
import java.net.*;

public class Server {
    public static Socket clientSocket = null;
    public static ServerSocket serverSocket = null;

    private static ObjectOutputStream output;

    public static void setup() throws IOException {
        serverSocket = new ServerSocket(1234);
        System.out.println("SERVER: Ready to roll!");
    }

    public static void waitForClient() throws IOException {
        System.out.println("SERVER: Waiting for client to accept connection...");
        clientSocket = serverSocket.accept();
        output = new ObjectOutputStream(clientSocket.getOutputStream());
        System.out.println("SERVER: A client has accepted");
    }

    public static void sendString(String string) throws IOException {
        System.out.println("SERVER: Sending...");
        output.writeObject(string);
        output.flush();
    }

    public static void end() throws IOException {
        System.out.println("SERVER: Time to close up shop :) ");
        serverSocket.close();
        clientSocket.close();
        output.close();
    }

    public static void main(String[] args) {
        try {
            setup();
            waitForClient();
            for(int i = 0; i < 100; i++) {
            sendString("Hello World! " + String.valueOf(i));
            }    
            end();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

The main method is what gets called when a Server object is instantiated. The order of the methods is preserved and "Hello World!" is sent through the socket 100 times.

Now lets work on the client class.

```java
import java.io.*;
import java.net.*;

public class Client {
    public static Socket serverSocket = null;

    private static ObjectInputStream input;

    public Client(String ip, int port) {
        try {
            serverSocket = new Socket(ip, port);
        } catch (UnknownHostException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {

    }
}
```

The client class is relatively similar to the server class, except instead of a ServerSocket it has a socket and instead of an ObjectOutputStream it contains an ObjectInputStream. The constructor of the Client class takes a given ipaddress\_ \(of the server the client connects to\) \_and a port number that is used to connect to the server.

Next lets create the methods used in the **main\(\) **method.

```java
import java.io.*;
import java.net.*;

public class Client {
    public static Socket serverSocket = null;

    private static ObjectInputStream input;

    public Client(String ip, int port) {
        try {
            serverSocket = new Socket(ip, port);
        } catch (UnknownHostException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }

    public static void setup() throws IOException {
        input = new ObjectInputStream(serverSocket.getInputStream());
        System.out.println("CLIENT: All setup!");
    }


    public static String receive() throws IOException, ClassNotFoundException {
        return (String) input.readObject();
    }

    public static void end() throws IOException {
        System.out.println("CLIENT: Time to go fishing :)");
        serverSocket.close();
        input.close();
    }

    public static void main(String[] args) {

    }
}
```

1. **setup\(\) **- creates a new InputObjectStream from the server connection
2. **receive\(\) **- returns a received string from the server connection
3. **end\(\) **- closes off the server connection and the input stream 

Now lets put everything together.

```java
import java.io.*;
import java.net.*;

public class Client {
    public static Socket serverSocket = null;

    private static ObjectInputStream input;

    public Client(String ip, int port) {
        try {
            serverSocket = new Socket(ip, port);
        } catch (UnknownHostException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }

    public static void setup() throws IOException {
        input = new ObjectInputStream(serverSocket.getInputStream());
        System.out.println("CLIENT: All setup!");
    }


    public static String receive() throws IOException, ClassNotFoundException {
        return (String) input.readObject();
    }

    public static void end() throws IOException {
        System.out.println("CLIENT: Time to go fishing :)");
        serverSocket.close();
        input.close();
    }

    public static void main(String[] args) {
        Client client = new Client("localhost", 1234);
        try {
            client.setup();
            for(int i = 0; i < 100; i++) {
            System.out.println("CLIENT: RECIEVED MESSAGE: " + client.receive());
            }    
            client.end();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

The main method creates a new client - for our testing purposes the ipaddress will be "localhost" and the port will be 1234, so that we can test both programs on 1 computer. If you have multiple computers, change the ipaddress to the address of the host computer. The client will receive 100 messages and output them into the console.


## HOW I DID IT: a Java server with TCP connection

---

## Motivation: 

During my Object-Oriented Programming classes at college, which were taught in Java, I thought about developing a project that would remain within the subject's theme, but at the same time allow me to explore other areas.

The idea of creating a TCP server in Java came from this: in addition to applying OOP concepts, I could integrate the project with a database and keep the interface decoupled, allowing it to be developed in another language.

Since the entire environment would initially be local, direct communication via TCP made more sense than creating a complete HTTP API, reducing complexity and allowing me to focus on the server's operation and data flow.

[github link](https://github.com/CupOfCakes/card-game)

---

## Configuring the server and determining the listening port

In the project, I created the `LoginServer.java` file to be, as the name implies, where you log in to the server. It is a very simple and short file, having only 3 parts and being the only file with a `main`, meaning it's the only file that needs to run in the entire project.

```java
public static void main(String[] args) {
    try(ServerSocket server = new ServerSocket(5000)){

        while(true){
            Socket clientSocket = server.accept();
            new Thread(new playerHandler(clientSocket)).start();
        }

    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

The `new ServerSocket(5000)` means that the server will listen on port **5000** of the computer and any message sent there will be read. 

In `while(true)`, the server keeps reading connections, and `server.accept()` is run when a connection is made, returning a `Socket`. Then, a new `Thread` is generated and started for that client, allowing connections to act in parallel. 

> I might be mistaken, but I believe that without the `try/catch`, Java wouldn't even compile this code.

---

## Handles Management

Now moving to the already shown file, `playerHandler`, it is our handles manager. Basically, its function is to organize which code will run based on the message sent by the client. 

### 3 EXTREMELY important things:

- 1st Implement `Runnable` 

The first extremely important thing is that this file needs to be implemented with `Runnable` because it will give us the `run` function, and we will have to rewrite it with `@Override`. This allows us to deal with multiple clients in parallel.

```java
public class playerHandler implements Runnable
```

- 2nd Receive the Socket 

The second extremely important thing: it needs a constructor that receives the socket.

```java
public playerHandler(Socket socket) {
    this.clientSocket = socket;
}
```

- 3rd `try` inside `run()`

The third important thing: inside `run`, you need to have this `try`:

```java
    try (BufferedReader in = new BufferedReader(new         InputStreamReader(clientSocket.getInputStream()));
         PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true)
    ) 
```

In short, what this `try` does is create the variable `in`, which is the message sent by the client but in text form (UTF-8) because the connections are made through bytes and we need to make this conversion so that we can understand it. In addition, `out` is also created, which does the opposite, transforming text into bytes, and when the code finishes, it will return to the client. 

These 3 things are **indispensable** in the code. 

Having the client's message and a way to send messages back, you can decide how to handle it. As I said, this file is a manager. I used it just to send to other internal functions using `if` and `else if` and a `while` to skip if the input is empty, but you can do it however you want, such as using `switch case` or whatever you prefer.

---

## Final Details

An important detail: `out` needs to be passed as a parameter to the function that will deal with the internal logic, because it is through it that you will send the message back to the client. 

From this point on, there is nothing more to cover about the server creation itself. At this stage, you already have:

- a configured port
- support for multiple connections via **multithreading**
- reading the message sent by the client
- and a means to return responses

Any additional implementation becomes exclusively business logic. One thing I did during my project that you can also use is passing client data to the server via the `input`, using messages like `message:data`, because this way I can, for example, get the ID passed by the client and perform a database search.

**END**

![END.png](../../imagens/end/ShoukoKomi-Java.jpg)
# COMO EU FIZ: um server java com conexão TCP

---

## Motivação:

Durante as aulas de Programação Orientada a Objetos na faculdade, que estavam sendo lecionadas em Java, pensei em desenvolver um projeto que permanecesse dentro do tema da disciplina, mas que ao mesmo tempo me permitisse explorar outras áreas.

A ideia de criar um servidor TCP em Java surgiu justamente disso: além de aplicar os conceitos de POO, eu poderia integrar o projeto com um banco de dados e manter a interface desacoplada, permitindo que ela fosse desenvolvida em outra linguagem.

Como todo o ambiente inicialmente seria local, a comunicação direta via TCP fazia mais sentido do que a criação de uma API HTTP completa, reduzindo a complexidade e permitindo focar no funcionamento do servidor e no fluxo de dados.

[link do projeto no github](https://github.com/CupOfCakes/card-game)

---

## Configurando o servidor e determinando a porta de escuta

No projeto criei arquivo LoginServer.java para ser como o nome diz aonde se faz login no server, ele é um arquivo bem simples e curto possuindo apenas 3 partes e sendo o unico arquivo com uma `main`, ou seja, o unico arquivo q precisa rodar de todo o projeto


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
O `new ServerSocket(5000)` significa que o server escutara a porta 5000 do computador e qualquer mensagem enviada para la sera lida.

No `while(true)` o server fica lendo as conexões e o `server.accept` é rodado quando a conexão é feita retornando um `Socket`, então uma nova Thread é gerada e iniciada para esse cliente, fazendo com q as conexões possam agir de forma paralela.

> Posso estar enganado, mas acredito que sem o `try/catch`o java nem compila esse codigo

---

## Gerenciamento de Handles

Agora indo pro arquivo já mostrada anteriormente `playerHandler`, ele é nosso gerenciador de handles, basicamente a sua função é organizar qual codigo rodara com base na mensagem enviada pelo cliente.

### 3 coisas EXTREMAMENTE importante:

- 1ª Implementar `Runnable`

Primeira coisa extremamente importante, esse arquivp precisa ser implementado com `Runnable`pois ele nos dara a função `run`e teremos de reescrevela com o `@Override`, isso nos permite lidar com varios clientes de forma paralela.

`public class playerHandler implements Runnable`

- 2ª Receber o `Socket`

Segunda coisa extremamente importante, precisa de um construtor q receba o socket.


```java
public playerHandler(Socket socket) {
    this.clientSocket = socket;
}
```

- 3ª `try`dentro do `run()`

terceira coisa importante,  dentro do run vc precisa ter esse `try`


```java
    try (BufferedReader in = new BufferedReader(new 		InputStreamReader(clientSocket.getInputStream()));
         PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true)
    ) 
```
resumidamente o que esse try faz é criar a variavel in q é a mensagem enviada pelo cliente so que em forma de texto(UTF-8) por que entre as conexões são feitas atraves de bytes e precisamos fazer essa conversão para q nos possamos entender, além disso, também é feita o out que faz o inverso, transformando texto em bytes e quando o codigo acabar retornará para o cliente

Essas 3 coisas são indispensaveis no codigo

Tendo a mensagem do cliente e uma forma de enviar mensagens de volta a você pode decidir como tratar, como eu disse esse arquivo é um gerenciador, eu usei ele so pra enviar pra outras funções internas usando `if` e `else if` e um `while`pra pular caso o `input` estiver vazio, mas você pode fazer da forma que quiser, como por exemplo usando `switch case`ou o que preferir.

---

## Detalhes Finais

Um detalhe importante, o `out` precisa ser passado como parametro pra função q vai lidar com a logica interna por que é por ele que vc vai enviar a mensagem de volta pro cliente.

A partir deste ponto, não há mais o que abordar sobre a criação do servidor em si.
Neste estágio, já existe:
- uma porta configurada
- suporte a múltiplas conexões via multithreading
- leitura da mensagem enviada pelo cliente
- e um meio de retornar respostas

Qualquer implementação adicional passa a ser exclusivamente lógica de negócio.

Uma coisa que eu fiz durante o meu projeto que você também pode usar, eu passava dados do cliente para o server atraves do `input`, usando mensagens como `mensagem:dados` por que assim posso, por exemplo, pegar o id passado pelo cliente e fazer uma pesquisa no banco de dados.

---

# FIM

![FIM.png](../../imagens/end/ShoukoKomi-Java.jpg)
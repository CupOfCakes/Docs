# Docker: Guia Inicial

[![github](https://img.shields.io/badge/github-writer-green?logo=github)](https://github.com/CupOfCakes) [![linkedin](https://img.shields.io/badge/LinkedIn-0A66C2?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/heitor--carvalho/)

---

## O que é docker?

**Docker** cria **ambientes isolados chamados containers**, que permitem que programas e seus códigos rodem **de forma idêntica em qualquer máquina**.

No **Windows**, o Docker usa o **WSL (Windows Subsystem for Linux)**, que é um subsistema Linux que permite rodar o **kernel do Linux** dentro do Windows. Ele é usado porque o Linux **lida melhor com processos e isolamento**.

Sem o Docker, para ter esse mesmo nível de isolamento, seria necessário usar uma **VM (Máquina Virtual)**, que roda **um sistema completo** dentro do seu computador. Isso consome muito mais memória e processamento, tornando o processo **mais pesado e menos eficiente**.

> Docker foi feito em **Go**

---

## Arquitetura do docker

![Docker Architecture diagram](https://docs.docker.com/get-started/images/docker-architecture.webp)

A conexão entre **docker cliente** e **docker daemon** é feita por **API REST**, e o **daemon** pode se comunicar com outros **daemon**.

## Containers

**Containers** são ambientes isolados para rodar componentes de uma aplicação. Cada parte, como interface ou servidor, roda **separadamente**, garantindo que **diferenças de versão ou dependências não afetem o sistema principal nem outros projetos**.

Exemplo: um desenvolvedor usa `.NET 8` no trabalho e `.NET 9` no PC pessoal. Com containers, cada projeto pode usar sua versão sem interferência.

Se múltiplos containers rodarem em paralelo, eles **compartilham o mesmo kernel**, consumindo menos recursos que várias VMs.

Em nuvem, é comum usar **VMs para fornecer a infraestrutura** e **containers para executar os programas**, combinando isolamento e eficiência.

## Images

**Images** são os pacotes que criam os **containers**, contendo os arquivos, binários, bibliotecas e configurações.

> Na documentação **Images** são chamadas de pacotes padronizados

> Para uma images PostgreSQL, essa image irá empacotar os binários da base de dados, ficheiros de configuração e outras dependências. 
>
> Para uma aplicação web Python, irá incluir o `runtime` Python, o código e todas as suas dependências.

### Principios importantes das images:

1.  Images são imútaveis. Depois de criadas não podem ser modificadas, apenas adicionar por cima.

2.  Containers images são compostos de camadas. Cada camada representa um conjunto de mudanças no sistema que adiciona, remove ou modifica.

   > O 2 principio das imagens permite que o desenvolvedor possa fazer o seu ambiente por parte.
   >
   > Exemplo: primeiro configura a linguagem de programação e adiciona essa camada, então adiciona as depedencia e por fim adiciona o codigo.

**Docker Hub** é o marketplace de images oficial.

## Comandos Images

```cmd
docker search docker/welcome-to-docker   	       # Procurar image
docker pull docker/welcome-to-docker		  # Baixar image
docker image ls							# Listar images baixadas
docker image history docker/welcome-to-docker     # Ver camadas da image
```

> Adicionando `--no-trunc` os comando trazem mais informações, mas vem de forma desformatada dificultando a pesquisa.

## Comandos Containers

```cmd
docker run -d -p <porta> docker/welcome-to-docker         # Executa container
docker ps                                           				# Lista containers em execução
docker ps -a                                       				       # Lista todos os containers
docker stop <container-id>                          		       # Para container

```

> No comando de parar os container não é necessário escrever todo o ID, basta escrever o suficiente para ele ser único.

---

## Registry

**Registry** é uma centralização dos container images para compartilhamento, podendo ser publico ou privado. 

### Registry X Repository

**Registry** e **Repository** são conceito que andam junto, mas não são a mesma coisa, **repository** é uma coleção de container images  relacionados que ficam dentro de um **registry**.

Resumidamente:

- **Repository**: coleção de container imagens;
- **Registry**: coleção de **Repository**

## Comandos Registry

```cmd
docker build -t YOUR_DOCKER_USERNAME/docker-quickstart . #Constroi uma image

docker images #Lista a image mais nova

docker run -d -p 8080:8080 <YOUR_DOCKER_USERNAME>/docker-quickstart  # Executa o container para testa a image

docker tag YOUR_DOCKER_USERNAME/docker-quickstart YOUR_DOCKER_USERNAME/docker-quickstart:1.0 #Use o docker tag para adicionar uma tag a image

docker push YOUR_DOCKER_USERNAME/docker-quickstart:1.0 #Commita a image para o docker hub
```

> As tags nas images **permitem identificar e versionar** a image.

---

## Compose

Os containers devem seguir o princípio de **uma única responsabilidade**, como rodar apenas a interface ou apenas o servidor, mas não ambos no mesmo container.

**Docker Compose** permite iniciar **vários containers relacionados ao mesmo tempo**. Sem ele, o desenvolvedor teria que iniciar cada container manualmente com `docker run`.
 O arquivo de configuração é em **YAML (`.yml`)**.

> Geralmente o arquivo **compose** é chamado de `docker-compose.yml`

## Comandos Compose

```cmd
docker compose up #Inicia os containers

docker compose up -d --build # Sobe em background e reconstrói as images

docker compose down #Termina a aplicação

docker compose down --volumes #Termina e deleta os dados
```

---

## Volume

**Volumes** são usados para persistir dados de containers. Eles permitem que os dados sobrevivam à remoção de containers e podem ser compartilhados entre vários containers.

Por ser um assunto mais avançado, esta parte foi separada em outro [arquivo](Volumes.md).

---

# Links e referências

[Documentação oficial](https://docs.docker.com/get-started/get-docker/);

[Video do canal The Coding Sloth](https://www.youtube.com/watch?v=DQdB7wFEygo&t=563s);

---

# FIM

![MyRoomReveal](../../imagens/end/MessyRoom.jpeg)

> Imagem do jogo **NEET Girl Date Night**

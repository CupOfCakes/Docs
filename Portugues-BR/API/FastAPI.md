# FastAPI: Criação de API com python

---

## O que é FastAPI?

**FastAPI** é uma `biblioteca python`  que se propõe a ser a maneira mais rapida de criar uma **API web**, unindo ao python que tem uma das `sintaxes` mais fáceis/limpas gera uma modelo de API fácil e rapida de ser criado.

---

## Ferramentas complementares

- **Uvicorn**: é a biblioteca responsável por iniciar API;
- **Pydantic**: é a biblioteca que ajuda a fazer os `modelos de classes` para validação, tipagem e organização;

---

## Criando a API e as rotas

### Iniciando:

Para começar precisamos criar a variável que sera a nossa API e configuraremos.

```python
from fastapi import FastAPI

app = FastAPI()
```

Com isso nos instanciamos nossa aplicação, a variável `app` representa nossa API.

### Criando rotas

 Fazer as rotas é simples, tudo que precisa é definir o tipo de rota, o link e uma função.

#### Exemplo GET

```python
@app.get("/destaques")
def get_destaques():
    produtos = get.buscar_todos_produtos()
    destaques = get.buscar_por_ids(produtos, var.ids_destaque)
    return destaques
```



Nesse exemplo temos uma rota **GET** no link **/destaques** definido na primeira linha `@app.get("/destaques")`, e quando esse link é chamado a função `get_destaques`, no final é retornado ao cliente os destaques.

 #### Exemplo POST

```python
@app.post("/compra", status_code=201)
def post_compra(compra: Compra):
    post.salvar_compra(compra)
    return{"status":201, "mensagem": "compra registrada"}
```



Como agora estamos lidando com um **POST** precisamos receber uma variavel, geralmente toda conexão por API é feita por **JSON** por isso no exemplo tem a classe `Compra` com as classes podemos receber um objeto com atributos definidos previamente.

Sobre o **return**, não é obrigatorio fazer um return, você pode fazer caso queira mostrar os dados salvos para o usuario.

O `status_code` é um codigo enviado ao cliente para indicar o resultado da requisição como o infame erro **404**, no caso do exemplo temos o codigo **201** que significa "**CREATED**", como no exemplos estou criando uma compra e salvando os dados esse codigo se encaixa melhor, no cliente possivelmente sera utilizado `.ok` que basicamente um verificador do codigo enviado pela API por que todo codigo entre **200** e **299** significa que a requisição funcionou e o `.ok` vai retornar `True`, cada codigo tem sua peculiaridade, mas se esta nessa faixa significa que funcionou, mais na frente mostrarei outro uso desses codigos.

> ⚠️ IMPORTANTE:
> É possível passar dados requisições do tipo POST, porém isso compromete a segurança. 
>
> Dados na URL podem ficar expostos em históricos de navegador, logs de servidores, proxies, caches e ferramentas de monitoramento.
>
> Além disso, URLs podem ser reutilizadas ou reenviadas, o que pode causar a execução da requisição novamente, gerando efeitos indesejados, como o registro duplicado de uma compra.

#### Update e Delete

Os métodos de update e delete seguem a mesma estrutura básica do POST, porém cada método possui um propósito específico, abaixo os métodos.

- **PUT**: Atualização completa;
- **PATCH**: Atualização parcial;
- **DELETE**: deleção;

> ❗Em APIs REST, é comum identificar o recurso a ser atualizado ou removido passando o ID na URL em métodos de update e delete.  
> Isso faz parte das convenções REST, porém não é uma regra absoluta, e outras abordagens podem ser utilizadas conforme a necessidade do projeto.

---

## Tipos e HTTPException

> 💬 Comentário do autor:
> Durante o meu projeto eu fiz 4 rotas pq eu não sabia que podia usar **tipos** hahaha.

Os tipos permitem que o cliente envie dados para a API de forma validada, sendo muito comuns em requisições **GET** para filtros.

```python
from fastapi import HTTPException

@app.get("/produtos")
def get_produtos(tipo:str | None = None):
    produtos = get.buscar_todos_produtos()

    if tipo:
        if tipo not in produtos:
            raise HTTPException(
                status_code=404,
                detail=f"Categoria '{tipo}' não encontrada"
            )
        return produtos.get(tipo,[])
    return produtos
```

Nesse exemplo temos um função que retorna todos os produtos, mas na sua URL é possivel adicionar um **tipo** que como da pra ver no codigo é usado para pegar categoria dos produtos, se nenhum tipo for passado ele fica como `None(null)`, isso economiza de fazer uma rota pra cada categoria.

A parte com o **HTTPException** é usada pra retornar um erro, por que nesse caso aonde não temos a categoria informada o codigo não deu nenhum erro então ele retornaria **2xx** e nenhum dado, então pra identifica isso como um erro forçamos um return com codigo **404(Não encontrado)** e uma mensagem indicando que a categoria não foi encontrada.

---

## Configurações extras

### Pasta estática

Essa configuração serve para arquivos que precisam ser retornados pela API, como uma imagem, um pdf e etc.

Por exemplo imagina que tem uma pasta com imagens que serão usados por um site, então você precisaria criar uma rota serializar/desserializar e fazer todo o processo, configurando uma pasta pra ter arquivos estáticos todo esse processo é feito automaticamente.
```python
from fastapi.staticfiles import StaticFiles

app.mount("/static", StaticFiles(directory="static"), name="static")
```

Vamos explicar por partes essa linha:

- `app.mount()`: Indica pra API que essa configuração não é uma rota comum, são arquivos;
- `"/static"`: Nomeia a rota;
- `StaticFiles(directory="static")`: Indica a pasta no projeto que contem os arquivos;
- `name="static"`: Nomeia a rota internamente. 

Com essa configuração, é possível acessar arquivos diretamente pelo navegador, por exemplo:

`http://localhost:8000/static/FIM.png`

E será retornada uma imagem desde que o arquivo `FIM.png` exista na pasta `static` no projeto.

### CORS

> CORS significa "Cross-Origin Resource Sharing"

Resumidamente, CORS é um mecanismo de segurança do navegador que controla qual site pode acessar a API.

> 💬 Comentário do autor:
> Durante o meu projeto usei a IDE WebStorm para fazer a interface web e essa configuração foi importante para mim.

```python
from fastapi.middleware.cors import CORSMiddleware
 
app.add_middleware(
 	CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

Nesse exemplo dou permissão a qualquer cliente de acessar a API.

destrinchando essa configuração:

- `allow_origins=["*"]`: permite qualquer um acessar a API;
- `allow_credentials=True`: permite o envio de credenciais, como cookies e headers de autentificação;
- `allow_methods=["*"]`: permite chamar qualquer método;
- `allow_headers=["*"]`: permite qualquer header enviado pelo cliente.



> ⚠️ IMPORTANTE
> Este código remove todas as restrições de CORS e deve ser usado apenas em ambiente de desenvolvimento.
> Em produção, é recomendado limitar as origens permitidas.
>
> 
>
> 

---

## Pydantic 

Pydantic é uma biblioteca que muda a forma de criar `classes` e possui uma organização tipada que facilita as conversões

```python
from pydantic import BaseModel

class Item(BaseModel):
    id:int
    nome:str
    preco:float
    qtd:int

class Compra(BaseModel):
    data:str
    itens:list[Item]
    total:float
```

O BaseModel permite que os dados recebidos siga um modelo pré definido, isso evita erros e o uso de dados falsos ou desnecessários.

É uma boa pratica de programação usar classes para ter controle do modelo em que o **JSON**  é enviado e recebido.

###  Typing

É comum que junto do **Pydantic** tenha o **Typing**, ele permite que tenhamos dados opcionais na classe e ter um valor pré definido caso não venha

```python
from typing import Optional
from pydantic import BaseModel

class CompraUpdate(BaseModel):
    status: Optional[str] = None
    valor: Optional[float] = None
```

---

## Uvicorn

Uvicorn é o **servidor ASGI** responsável por executar a aplicação FastAPI.  

É ele quem inicia a API, fica escutando as requisições **HTTP** e mantém o servidor em execução.

``` bash
uvicorn main:app

uvicorn main:app --reload
```

> main é o nome do arquivo e app o nome da variável aonde a API foi instanciada

O primeiro comando inicia o servidor.
O segundo comando inicia o server em modo de desenvolvimento reiniciando o servidor automaticamente sempre que o código for alterado.

A iniciação é feita pelo terminal

Para encerrar o servidor, use `Ctrl + C` no mesmo terminal onde ele foi iniciado. 

Também é possível finalizar o processo manualmente usando comandos como `taskkill`, se necessário.

---

# FIM

acabou a explicação, agora temos a parte em q eu falo agua e faço comentarios pessoais.

não esperava q FastAPI me desse um doc tão longo, eu queria ate ter pulado o CORS de tão longo foi ficando, e ainda to pensando em fazer uma pra ASP.NET, ai vai 1 dia inteiro de digitação kkkkk.

sendo bem sincero, a FastAPI faz jus ao nome, é realmente "Fast" de fazer, mas não vejo sendo melhor doq o ASP.NET em projetos maiores, vc tem menos regras, o codigo é mais simples, mas não existe um jeito certo de fazer, me senti num sandbox.

Meu objetivo era estudar sobre API então fiz em FastAPI e em ASP.NET depois, e no segundo caso foi mais dificil, mas vejo sendo mais facil de escalar, por conta das regras eu sei cada caminho seguido e oq cada linha faz, no python eu puxo o json e ele vem da forma q esta la podendo ter oq eu quero ou não, as classes ajudam nisso, mas não são obrigatorias, ja no ASP são e isso facilita pq me obriga a sempre saber oq tem no json, se tiver um [] antes do json eu sei e tenho q fazer uma class pra indicar q ele existe, ate os nomes dos atributos eu tenho q saber, é muito mais dificil, mas pelo menos eu gosto mesmo reclamando muito.

![FIM.png](../../imagens/end/AkkoKagari-Python.jpg)
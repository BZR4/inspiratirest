# InspiraTiRestApiInstructions

# Criação de API REST

## Observação
Este projeto foi realizado em uma máquina virtual `Fedora` com 2GB de RAM, considerando que os computadores da Etec tem apenas 4GB, tive que mudar o uso do `Fedora` para o Windows para evitar lentidão no PC, então deixo à responsabilidade de vocês instalar o `Pip` (caso não esteja instalado) conforme este [link](https://python.org.br/instalacao-windows/) para podermos continuar. O Pip é o software que faz as instalações de dependências de software para o ambiente Python.

## Acriação de ambiente virtual de desenvolvimento
1. Crie um diretório pelo shell para iniciarmos a criação de nossa API REST, insira o comando abaixo:
```shell
mkdir REST_API
cd REST_API
```
2. Vamos criar um ambiente virtual de desenvolvimento embarcado para o Python, insira os comandos abaixo:
```shell
pip install virtualenv --user
virtualenv rest_api_vir --python=python3.7
```
3. Ativar o ambiente virtual:
```shell
source rest_api_vir/bin/activate
```
No comando acima, criamos um ambiente virtual e definimos uma versão padrão do Python para ser utilizada neste ambiente.
Note que o shel mudou e neste momento ele indica o ambiente virtual sendo a primeira informação do nosso shell.
```shell
(rest_api_vir) [aluno@unknown REST_API]$
```

## Instalação frameworks para desenvolvimento da API
Neste passo, vamos instalar o software necessário apenas no ambiente virtual para desenvolvermos nossa API.

1. Instale os Frameworks  Flask e Flask-Restful:
```shell
pip install Flask
pip install Flask-Restful
```
2. Verifique o software instalado no ambiente virtual:
```shell
pip freeze
```
O resultado em seu shell deve ser semelhante a este:
```shell
aniso8601==8.0.0
astroid==2.3.3
Click==7.0
Flask==1.1.1
Flask-RESTful==0.3.7
isort==4.3.21
itsdangerous==1.1.0
Jinja2==2.10.3
lazy-object-proxy==1.4.3
MarkupSafe==1.1.1
mccabe==0.6.1
pylint==2.4.4
pytz==2019.3
six==1.13.0
typed-ast==1.4.0
Werkzeug==0.16.0
wrapt==1.11.2
```
3. Tudo Ok? Confere! Agora vamos começar a programar!

## Criação de API
### Criação de arquivos do módulo principal
1. Inicie o `Visual Studio Code` e abra o diretório **REST_API**. Você neste ponto verá que dentro de nosso diretório existe apenas o sub-diretório `rest_api_vir`, nele tem tudo o precisamos para trabalhar até este ponto.
2. Crie o arquivo `app.py` na raíz do projeto, ou seja fora de `rest_api_vir`.
3. Instalação do `pylint`; ao criar o arquivo `app.py` o VSCode irá ofertar a instalação do pylint, aceite e instale; ela irá ajudar na criação do código verificando os comandos, identação dentre outras. Caso não receba a oferta digite `pip install -U pylint`.

### Configuração de Api para Hello World
1. Vamos configurar nossa API importando os frameworks e criando os objetos de inicialização, insira o código abaixo:
```python
from flask import Flask
from flask_restful import Resource, Api

app = Flask(__name__)
api = Api(app)

class Hello(Resource):

  def get(self):
    return {'hello':'Hello World! Esta é minha primeira API REST'}


api.add_resource(Hello, '/hello')

if __name__ == '__main__':
    app.run(debug=True)
```
### Testando a API

1. Vamos testar a API, insira os comandos abaixo, mas não esqueça de salvar o arquivo `app.py`.

Inicializar a API
```shell
pyhton app.py
```
Como retorno no shel, você verá o endereço do servidor onde a API está rodando `Running on http://127.0.0.1:5000/`.
Fazer uma requisição web via shell
```shell
curl http://127.0.0.1:5000/hello
```
No comando acima nós fizemos uma requisição ao servidor onde a API está rodando e nela, solicitamos um recurso denomidado `/hello`, como nossa API tem um recurso implementado na classe `Hello`, e esta possui um método responsável pelo **verbo** `GET`. Ao solicitar o recurso `/hello` a função criada `def get(self): return {'id_atrubuto':'conteudo'}` é executada e entrega ao usuário os dados solicitados via REST.
Você verá o resultado da requisição no shell.

#### Utilizando o Postman
Vamos realizar os testes de nossa API utilizando o software mais utilizado para trabalhar com REST, o `Postman`, instale o Postman(já estava instalado na VM).
Neste posso faremos juntos, para as devidas instruções do uso do Postman e criação das requisições com os demais verbos do REST.

### Retornando uma lista de elementos
Vamos alterar nosso código para retornar agora uma coleção de elementos mais bem elaborados em nossa API.
Agora iremos alterar a classe Hello para Piadas, esta classe deverá devolver uma lista de piadas nerds via REST.
1. Edite o arquivo `app.py` com o código abaixo:
```python
# -*- coding: utf-8 -*-
from flask import Flask
from flask_restful import Resource, Api

app = Flask(__name__)
api = Api(app)

piadas = [
        {
            'id':'1',
            'pergunta':'O que aconteceu com pintinho binário que não tinha 1?',
            'resposta': 'Foi compilar e explodiu!'
        },
        {
            'id':'2',
            'pergunta':'Eu gosto de iOS e Carlos Drummond?',
            'resposta': 'Carlos Drummond de Android'
        },
        {
            'id':'3',
            'pergunta':'O que o C++ disse para o C?',
            'resposta': 'Você não tem classe!'
        },
        {
            'id':'4',
            'pergunta':'Quanto programadores são necessários para trocar uma lâmpada?',
            'resposta': 'Nenhum, é problema de hardware.'
        },
        {
            'id':'5',
            'pergunta':'O que é um terapeuta?',
            'resposta': '1024 gigapeutas.'
        },
        {
            'id':'6',
            'pergunta':'Como o Nerd faz amigo oculto?',
            'resposta': '.amigo{visibility:hiden}'
        },
        {
            'id':'7',
            'pergunta':'Seja \"int x = 10; int y = 10; print(x + y);\". Qual o nome do filme?',
            'resposta': 'O código dá 20.'
        }
    ]


class Piadas(Resource):

    def get(self):
        return {'piadas':piadas}

api.add_resource(Piadas, '/piadasnerd')

if __name__ == '__main__':
    app.run(debug=True)

```
2. Vamos testar a alteração, se a API estiver em execução, assim quando a salvar o servidor será reiniciado então podemos fazer uma nova requisição:
```shell
curl http://localhost:5000/piadasnerd
```
Ou melhor ainda, criar a requisição no `Postman`.

### Implementando os VERBOS GET, POST, PUT e DELETE

Até o momento temos um recurso que nos retorna todas as piadas de Piadas(Resource). Vamos criar um novo recurso que manipule individualmente as piadas; para tal vamos criar uma classe para manipular nossos objetos d tipo Piada, esta classe será nosso `Model`.

1. Crie a classe `PiadaModel` que será responsável por manipular a criação de nossos objetos Piada e sua conversão para `JSON`; insira o trecho abaixo logo abaixo do final da classe `Piadas`:
```python
class PiadaModel:
    def __init__(self, id, pergunta, resposta):
        self.id = id
        self.pergunta = pergunta
        self.resposta = resposta

    def json(self):
        return {
            'id': self.id,
            'pergunta': self.pergunta,
            'resposta': self.resposta
        }
```
2. Adicione módulo reqparse para podermos utilizar a classe `RequestParser` para facilitar o preenchimento de nossos objetos e sua conversão para `JSON`; edite a importação de recursos no início do arquivo `app.py` inserindo o trecho abaixo:
```python
from flask_restful import Resource, Api, reqparse
```
3. Crie o **Resource** Piada. Esta classe será responsável por implementar as ações necessárias para gerenciar as solicitações para a API para com os demais verbos. Abaixo de `PiadaModel`, adicione o trecho abaixo:
```python
class Piada(Resource):
    atributos = reqparse.RequestParser()
    atributos.add_argument('pergunta')
    atributos.add_argument('resposta')
```
Neste trecho, criamos um objeto `atributos` que será responsável por preencher objetos de forma padronizada e automatizada por meio de `kwargs`.

4. Implementar o método auxiliar de busca em lista/dicionário. O método abaixo irá buscar um elemento em nossa lista e retorná-lo quando existir. Insira logo abaixo do passo anterior.
```python
def buscar_piada(id):
        for piada in piadas:
            if piada['id'] == id:
                return piada
        return False
```
5. Implementação o método `GET`. Neste método iremos utilizar o função do passo anterior para retornar um objeto individual de nossa lista.
```python
def get(self, id):
        piada = Piada.buscar_piada(id)
        if piada:
            return piada
        return {'mensagem': 'piada não encontrada.'}, 404
```
Utilizamos a função auxiliar e retornamos uma piada da lista; caso não encontrada uma mensagem padrão será retornada juntamente com o `HTML Status Code 404`.
6. Adicione um novo recurso à API. Insira o trecho abaixo para incluir um novo recurso, ou seja um novo `url` para fazer uma requisição pelo Postman e futuramente para qualquer sistema que faça requisições web.
```python
api.add_resource(Piada, '/piadasnerd/<string:id>')
```
7. Teste o novo recurso no `Postman`.
8. Implementando o método `POST`. Agora iremos realizar inserção de dados de uma nova piada ao nosso repertório.  para tal devemos enviar os dados que devam ser inseridos e juntamente com o novo **id**. Utilizaremos o recurso criado no passo acima. Os dados serão recebidos por uma requisição web, devemos capturá-los, transformá-los em Json e adicioná-lo à lista existente. Insira o trecho abaixo para realizar as ações.
```python
def post(self, id):
        dados = Piada.atributos.parse_args()
        objetoPiada = PiadaModel(id, **dados)
        piadaJson = objetoPiada.json()
        piadas.append(piadaJson)
        return piadaJson, 201
```
9. Testar no `Postman`. Este passo faremos juntos...
10. Implementando o método `PUT`. Agora iremos realizar uma atualização de dados de uma piada em específico em nossa lista; para tal devemos enviar os dados que devem ser atualizados e o **id** para localizar o objeto na API para atualizar seus dados. Utilizaremos o recurso criado no passo acima.
```python
def put(self, id):
        dados = Piada.atributos.parse_args() #1
        nova_piada_objeto =  PiadaModel(id, **dados) #2
        nova_piada = nova_piada_objeto.json() #3
        piada = Piada.buscar_piada(id) #4
        if piada:
            piada.update(nova_piada) #5
            return piada, 200 #6
        piadas.append(nova_piada) #7
        return nova_piada, 201 #8
```
A função `POST` assim como a anterior, **#1** encapsula a entrada de dados, **#2** obtém um objeto do tipo PiadaModel, **#3** converte o objeto preenchido em `JSON`, **#4** verifica se o id recebido existe na lista, se existir carrega para o objeto **piada**, **#5** se o objeto **piada** tiver conteúdo será atualizado com os dados do objeto recebido pelo recurso, o objeto nova_piada, **#6** retorna objeto como resposta à requisição; **#7** se não existir piada com o id recebido, a **nova_piada** será adicionada à lista; **#8** a piada inserida é retornada como resultado da requisição.
11. Implementando o método `DELETE`. Para remover um objeto da API vamos utilizar `Python List Comprehension`, insira o trecho abaixo:
```python
def delete(self, id):
        global piadas
        piadas = [piada for piada in piadas if piada['id'] != id]
        return {'mensagem': 'Piada apagada.'}
```
Nesta função informamos ao compilador que estamos utilizando a lista **piadas** como recurso global e realizamos um filtro na lista, durante a iteração estamos recriando a lista apenas com os elementos que possuírem ids diferentes ao id recebido na requisição.  
12. Você conseguiu! Criou uma REST API em memória! Divirta-se fazendo muitos testes, `GET`,`POST`,`PUT`, `DELETE`.

# Utilização de banco de dados na Api
Criar uma API em memória é legal, mas utilizar banco de dados é muito melhor! Vamos alterar nossa API para utilizar o banco de dados `SQLite3` para gerenciar os dados para persistência. Hands ON!
1. Com o ambiente virtual ativo, instale o SQLAlchemy(framework de gerenciamento de banco de dados multi-plataforma).
```python
pip install Flask-SQLAlchemy
```
2. Crie na raiz do projeto o arquivo `sql_alchemy.py` e insira o código abaixo.
```python
from flask_sqlalchemy import SQLAlchemy
database = SQLAlchemy()
```
3. Adicione a configuração do banco de dados na cabeçalho de `app.py`.
```python
app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///database.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
api = Api(app)
```
Neste treco configuramos o local e o nome do arquivo de banco de dados; o **SQLite** é um banco de dados em arquivo, ou seja não é necessário o uso de um servidor ou SGBD para utilizá-lo e inibimos os alertas de alteração do SQLAlchemy.
4. Atualize o módulo principal no final do arquivo `app.py`.
```python  
if __name__ == '__main__':
    from sql_alchemy import database
    database.init_app(app)
    app.run(debug=True)
```
Estamos importando o objeto `database` do arquivo `sql_alchemy.py` e inicializando-o a partir de `app.py`.
5. Criar o banco de dados antes da primeira requisição web. Vamos criar um método que crie o banco de dado automaticamente na API conforme nossas configurações prévias. Insira a função antes da criação dos recursos, após o método `DELETE`.
```python
@app.before_first_request
def cria_banco():
    banco.create_all()
```
6. Inicialize a API e faça uma requisição, valide se o banco de dados foi criado.
7. Vamos mapear os atributos da tabela a ser criada; atualize o construtor de `PiadaModel`:
```python
class PiadaModel(database.Model):
    __tablename__ = 'piadas'
    id = database.Column(database.String, primary_key=True)
    pergunta = database.Column(database.String())
    resposta = database.Column(database.String())
```
Neste trecho o estamos passando um objeto do `SQLAlchemy` como modelo e definindo a tabela a ser criada juntamente com seu atributos(colunas).
8. Altere os métodos do `PiadaModel` para utilizarem o acesso ao banco de dados.
```python
@classmethod
    def buscar_piada(cls, id):
        piada = cls.query.filter_by(id=id).first()
        if piada:
            return piada
        return None

    def save_piada(self):
        database.session.add(self)
        database.session.commit()

    def update_piada(self, pergunta, resposta):
        self.pergunta = pergunta
        self.resposta = resposta

    def delete_piada(self):
        database.session.delete(self)
        database.session.commit()
```

9. Inclua a importação do `SQLAlchemy` no cabeçalho.
```python
from sql_alchemy import database
```

10. Atualize o recurso `Piada` com a utilização de banco de dados `SQLite`.
```python
def buscar_piada(id):
        for piada in piadas:
            if piada['id'] == id:
                return piada
        return False

    def get(self, id):
        piada = PiadaModel.buscar_piada(id)
        if piada:
            return piada.json()
        return {'mensagem': 'piada nao encontrada.'}, 404

    def post(self, id):
        if PiadaModel.buscar_piada(id):
            return {
                "mensagem": "Piada id '{}' ja existe.".format(id)
            }, 400  # Bad Request

        dados = Piada.atributos.parse_args()
        piada = PiadaModel(id, **dados)

        try:
            piada.save_piada()
        except:
            return {"mensagem": "Ocorreu um erro ao salvar a piada."}, 500 #Internal Server Error
        return piada.json(), 201

    def put(self, id):
        dados = Piada.atributos.parse_args()
        piada = PiadaModel(id, **dados)

        piada_em_banco = PiadaModel.buscar_piada(id)

        if piada_em_banco:
            piada_em_banco.update_piada(**dados)
            piada_em_banco.save_piada()
            return piada_em_banco.json(), 200
        piada.save_piada()
        return piada, 201

    def delete(self, id):
        piada = PiadaModel.buscar_piada(id)
        if piada:
            piada.delete_piada()
            return {'mensagem': 'Piada apagada.'}
        return {'mensagem': 'Piada não encontrada.'}, 404
```

Note que o procediemnto é basicamente o mesmo, apenas trocamos as chamadas de uso em lista, por uso de objetos herdados de `SQLAlchemy`.

11. Remova ou comente a lista de piadas originais e teste sua API.
12. Teste sua API fazendo inserções, atualizações e remoções pelo `Postman`.
13. Instale o `DBeaver` (SGBD multiplataforma) e verifique as alterações no `SQLite`.

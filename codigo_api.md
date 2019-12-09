# Código da API

### app.py
```python
# -*- coding: utf-8 -*-
from flask import Flask
from flask_restful import Resource, Api, reqparse
from sql_alchemy import database
from flask_cors import CORS, cross_origin

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///database.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
api = Api(app)


piadas = [
    {
        'id': '1',
        'pergunta': 'O que aconteceu com pintinho binario que nao tinha 1?',
        'resposta': 'Foi compilar e explodiu!'
    },
    {
        'id': '2',
        'pergunta': 'Eu gosto de iOS e Carlos Drummond?',
        'resposta': 'Carlos Drummond de Android'
    },
    {
        'id': '3',
        'pergunta': 'O que o C++ disse para o C?',
        'resposta': 'Voce nao tem classe!'
    },
    {
        'id': '4',
        'pergunta': 'Quanto programadores são necessários para trocar uma lâmpada?',
        'resposta': 'Nenhum, é problema de hardware.'
    },
    {
        'id': '5',
        'pergunta': 'O que é um terapeuta?',
        'resposta': '1024 gigapeutas.'
    },
    {
        'id': '6',
        'pergunta': 'Como o Nerd faz amigo oculto?',
        'resposta': '.amigo{visibility:hiden}'
    },
    {
        'id': '7',
        'pergunta': 'Seja \"int x = 10; int y = 10; print(x + y);\". Qual o nome do filme?',
        'resposta': 'O código dá 20.'
    }
]

class Piadas(Resource):

    def get(self):
        return {'piadas': [piada.json() for piada in PiadaModel.query.all()]}


class PiadaModel(database.Model):
    __tablename__ = 'piadas'
    id = database.Column(database.String, primary_key=True)
    pergunta = database.Column(database.String())
    resposta = database.Column(database.String())

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


class Piada(Resource):
    atributos = reqparse.RequestParser()
    atributos.add_argument('pergunta')
    atributos.add_argument('resposta')

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


@app.before_first_request
def cria_database():
    database.create_all()


api.add_resource(Piadas, '/piadasnerd')
api.add_resource(Piada, '/piadasnerd/<string:id>')


cors = CORS(app, resources={r"/piadasnerd/*":{"origins":"*"}})

if __name__ == '__main__':
    from sql_alchemy import database
    database.init_app(app)
    app.run(debug=True)


```

### sql_alchemy.py
```python
from flask_sqlalchemy import SQLAlchemy
database = SQLAlchemy()
```

## index.html
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>API REST</title>
    <link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
    <link rel="stylesheet" href="http://fonts.googleapis.com/css?family=Roboto:300,400,500,700" type="text/css">
    <link rel="stylesheet" href="https://code.getmdl.io/1.3.0/material.indigo-pink.min.css">
    <script defer src="https://code.getmdl.io/1.3.0/material.min.js"></script>

</head>

<body>
    <div class="mdl-layout mdl-js-layout mdl-layout--fixed-header
            mdl-layout--fixed-tabs">
        <header class="mdl-layout__header">
            <div class="mdl-layout__header-row">
                <!-- Title -->
                <span class="mdl-layout-title">InspiraTI</span>
            </div>
            <!-- Tabs -->
            <div class="mdl-layout__tab-bar mdl-js-ripple-effect">
                <a href="#fixed-tab-1" class="mdl-layout__tab is-active">Cadastro</a>
                <a href="#fixed-tab-2" class="mdl-layout__tab" onclick="listarPiadas()">Listar</a>
            </div>
        </header>
        <div class="mdl-layout__drawer">
            <span class="mdl-layout-title">Title</span>
        </div>
        <main class="mdl-layout__content">
            <section class="mdl-layout__tab-panel is-active" id="fixed-tab-1">
                <div class="page-content">
                    <!-- Your content goes here -->
                    <form action="#">
                            <div class="mdl-cell mdl-cell--4-col mdl-cell--6-col-desktop mdl-cell--8-col-desktop">
                                <h3>Cadastrar Piada Nerd</h3>
                            </div>
                            <div class="mdl-grid">
                                <div class="mdl-cell mdl-cell--8-col mdl-cell--8-col-tablet mdl-cell--12-col-desktop">
                                    <div class="mdl-textfield mdl-js-textfield mdl-textfield--floating-label">
                                        <input class="mdl-textfield__input" type="text" id="id">
                                        <label class="mdl-textfield__label" for="id">ID</label>
                                    </div>
                                </div>
                              </div>
                              <div class="mdl-grid">
                                <div class="mdl-cell mdl-cell--8-col mdl-cell--8-col-tablet mdl-cell--12-col-desktop">
                                    <div class="mdl-textfield mdl-js-textfield mdl-textfield--floating-label">
                                        <input class="mdl-textfield__input" type="text" id="pergunta">
                                        <label class="mdl-textfield__label" for="pergunta">Pergunta</label>
                                    </div>
                                </div>
                              </div>
                              <div class="mdl-grid">
                                <div class="mdl-cell mdl-cell--8-col mdl-cell--8-col-tablet mdl-cell--12-col-desktop">
                                    <div class="mdl-textfield mdl-js-textfield mdl-textfield--floating-label">
                                        <input class="mdl-textfield__input" type="text" id="resposta">
                                        <label class="mdl-textfield__label" for="resposta">Resposta</label>
                                    </div>
                                </div>
                              </div>
                        <div class="mdl-grid">
                            <div class="mdl-cell mdl-cell--4-col">
                                <button
                                    class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect mdl-button--accent"
                                    onclick="save()">
                                    SALVAR
                                </button>
                            </div>
                        </div>
                    </form>


                </div>
            </section>
            <section class="mdl-layout__tab-panel" id="fixed-tab-2">
                <div class="page-content">
                    <!-- Your content goes here -->
                    <div id="root">
                        <ul class="demo-list-two mdl-list" id="lista">

                        </ul>
                    </div>

                </div>
            </section>
        </main>
    </div>
<script src="js/main.js"></script>
</body>

</html>

``` 

## js/main.js
```javascript
function listarPiadas() {
  document.getElementById('lista').innerHTML = '';
  fetch("http://localhost:5000/piadasnerd")
    .then(response => response.json())
    .then(json => {
      console.log(json)
      json.piadas.forEach(
        piada => {
          console.log(piada)

          var ul = document.getElementById("lista");
          var li = document.createElement('li');
          var span = document.createElement('span');
          span.classList.add('mdl-list__item-primary-content')
          var i = document.createElement('i');
          i.classList.add('material-icons')
          i.classList.add('mdl-list__item-avatar')
          i.textContent = 'insert_emoticon'

          let pergunta = document.createElement('span')
          pergunta.textContent = piada.pergunta
          let resposta = document.createElement('span')
          resposta.classList.add('mdl-list__item-text-body')
          resposta.textContent = piada.resposta


          span.appendChild(i)
          span.appendChild(pergunta)
          span.appendChild(resposta)

          li.classList.add("mdl-list__item");
          li.classList.add("mdl-list__item--three-line");
          li.appendChild(span);
          ul.appendChild(li);
        }
      )
    })
    .catch(erro => console.log(erro));
}

function save() {

  var data = {};
  data.id = document.getElementById('id').value;
  data.pergunta = document.getElementById('pergunta').value;
  data.resposta = document.getElementById('resposta').value;

  var url = `http://localhost:5000/piadasnerd/${data.id}`;

  console.log(`url: ${url}`)

  var json = JSON.stringify(data);
  var request = new XMLHttpRequest();
  request.open("POST", url, true);
  request.setRequestHeader('Content-type', 'application/json; charset=utf-8');
  request.onload = function () {
    var users = JSON.parse(request.responseText);
    if (request.readyState == 4 && request.status == "201") {
      console.table(users);
    } else {
      console.error(users);
    }
  }
  request.send(json);
}

```


Criação de uma API Rest com Python
===

você precisa instalar as libs do Flask que vamos utlizar. Nesse caso vamos utlizar: 
---

  * Flask
  * Flask-SQLAlchemy
  * Flask-Restful
  * Jsonify 

Para baixar essas libs basta executar no terminal o seguinte comando:

~~~
   $  pip install flask flask-jsonpify flask-sqlalchemy flask-restful
~~~

* Python

Agora vamos criar o nosso código em Python utilizando os seguintes trechos de código:

* Importação das libs  
~~~python
# imports das libs que iremos utlizar
from flask import Flask, request, jsonify
from flask_restful import Resource, Api
from sqlalchemy import create_engine
from json import dumps
~~~

* Recuperar o arquivo de banco e utilizar o flask

~~~python
db_connect = create_engine('sqlite:///exemplo.db')
app = Flask(__name__)
api = Api(app)
~~~

* Criar os endpoints de User, GET, POST E PUT

~~~python
class Users(Resource):
    def get(self):
        conn = db_connect.connect()
        query = conn.execute("select * from user")
        result = [dict(zip(tuple(query.keys()), i)) for i in query.cursor]
        return jsonify(result)

    def post(self):
        conn = db_connect.connect()
        name = request.json['name']
        email = request.json['email']

        conn.execute(
            "insert into user values(null, '{0}','{1}')".format(name, email))

        query = conn.execute('select * from user order by id desc limit 1')
        result = [dict(zip(tuple(query.keys()), i)) for i in query.cursor]
        return jsonify(result)

    def put(self):
        conn = db_connect.connect()
        id = request.json['id']
        name = request.json['name']
        email = request.json['email']

        conn.execute("update user set name ='" + str(name) +
                     "', email ='" + str(email) + "'  where id =%d " % int(id))

        query = conn.execute("select * from user where id=%d " % int(id))
        result = [dict(zip(tuple(query.keys()), i)) for i in query.cursor]
        return jsonify(result)
~~~

* Criar os endpoints de envio de ID, com os métodos GET e DELETE

~~~python
class UserById(Resource):
    def delete(self, id):
        conn = db_connect.connect()
        conn.execute("delete from user where id=%d " % int(id))
        return {"status": "success"}

    def get(self, id):
        conn = db_connect.connect()
        query = conn.execute("select * from user where id =%d " % int(id))
        result = [dict(zip(tuple(query.keys()), i)) for i in query.cursor]
        return jsonify(result)
~~~

* Mapear os endpoints criados e finalizar o arquivo

~~~python
api.add_resource(Users, '/users') 
api.add_resource(UserById, '/users/<id>') 

if __name__ == '__main__':
    app.run()
~~~

O arquivo completinho estará assim:

~~~python
from flask import Flask, request, jsonify
from flask_restful import Resource, Api
from sqlalchemy import create_engine
from json import dumps

db_connect = create_engine('sqlite:///exemplo.db')
app = Flask(__name__)
api = Api(app)


class Users(Resource):
    def get(self):
        conn = db_connect.connect()
        query = conn.execute("select * from user")
        result = [dict(zip(tuple(query.keys()), i)) for i in query.cursor]
        return jsonify(result)

    def post(self):
        conn = db_connect.connect()
        name = request.json['name']
        email = request.json['email']

        conn.execute(
            "insert into user values(null, '{0}','{1}')".format(name, email))

        query = conn.execute('select * from user order by id desc limit 1')
        result = [dict(zip(tuple(query.keys()), i)) for i in query.cursor]
        return jsonify(result)

    def put(self):
        conn = db_connect.connect()
        id = request.json['id']
        name = request.json['name']
        email = request.json['email']

        conn.execute("update user set name ='" + str(name) +
                     "', email ='" + str(email) + "'  where id =%d " % int(id))

        query = conn.execute("select * from user where id=%d " % int(id))
        result = [dict(zip(tuple(query.keys()), i)) for i in query.cursor]
        return jsonify(result)

class UserById(Resource):
    def delete(self, id):
        conn = db_connect.connect()
        conn.execute("delete from user where id=%d " % int(id))
        return {"status": "success"}

    def get(self, id):
        conn = db_connect.connect()
        query = conn.execute("select * from user where id =%d " % int(id))
        result = [dict(zip(tuple(query.keys()), i)) for i in query.cursor]
        return jsonify(result)

api.add_resource(Users, '/users') 
api.add_resource(UserById, '/users/<id>') 

if __name__ == '__main__':
    app.run()

~~~
Executar o projeto
---

Para executar o projeto é bem simples, basta utilizar o comando via terminal na raiz do seu projeto, lembrando que o exemplo.db também deve está na raiz do seu projeto:

~~~
$ python server.py run
~~~






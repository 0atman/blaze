#!blaze pex flask flask_restful --

# Imports
First the imports, this demo requires the `flask_restful` package.
Then we set up the Flask wsgi application object, `app` and the api wrapper, `api`.

```python
from flask import Flask
from flask_restful import Resource, Api

app = Flask(__name__)
api = Api(app)
```

# Flask Restful resources
We define a single `HelloWorld` resource, that responds with a simple json
object on a `GET` request.

```python
class HelloWorld(Resource):
    def get(self):
        return {'hello': 'world'}
```

# Routing
`api.add_resource()` wires the `Resource` class `HelloWorld` into the flask
router at `/`.

```
api.add_resource(HelloWorld, '/')
```

# Run Server
After we have created everything, we run the flask werkzeug server.

```
if __name__ == '__main__':
    app.run()
```

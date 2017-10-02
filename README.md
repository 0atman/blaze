
![blaze](https://user-images.githubusercontent.com/114097/31094015-8690b4c0-a7ab-11e7-9ee0-39dd1ec536d3.png)
> blaze-example.py.md

# Quickstart

```shell
sudo wget blaze.oat.sh/blaze -O /usr/bin/blaze && \
sudo chmod +x /usr/bin/blaze
```

# Motivation

I've been playing with many literate programming tools since this technique of document-first programming came into my life two years ago.

[Literate programming](https://en.wikipedia.org/wiki/Literate_programming) (LP), a concept that has been around since at least the 80s, is back in the spotlight since the [Eve](http://witheve.com/) language (released by the Eve team headed by Chris Granger of [Light Table](http://lighttable.com) fame) was released to the public in 2015.

While LP's original concept aimed to create a document-first system whereby the building blocks of the actual code could be read (by the human or compiler) in any order (as it is in Eve), the contemporary state of the art is focused on creating the document-first model with existing procedural languages.

There are two main kinds of tool in the modern LP category:

 1. Tools that write beautiful documentation based on comments in source code, such as [Docco](http://ashkenas.com/docco/) and (a personal favourite) [Marginalia](https://github.com/gdeer81/marginalia)
 2. Tools that allow you to execute code-fenced source code within some markup, such as [Literate](https://github.com/zyedidia/Literate), [litpro](https://github.com/jostylr/litpro), and tiny [lit](https://github.com/vijithassar/lit).

The first is an evolution of the documentation processors you will be familiar with, no real innovation has happened in this space since [Javadoc](https://en.wikipedia.org/wiki/Javadoc). (Much though I love [Sphynx](http://www.sphinx-doc.org/en/stable/index.html) et al)

The second category I think is worth exploring.

It is into this ecosystem I present [Blaze](https://gist.github.com/0atman/5ea526a3ae26409da50dd7697eb700e8).

## Blaze is Tiny:

```shell
#!/usr/bin/env sh
args=$1
script=$2

file_extension=$(echo $script |awk -F . '{if (NF>1) {print $NF}}')

if [ "$file_extension" = "md" ]
then
    cat $script | awk '{ if (/^```/) { i++; next } if ( i % 2 == 1) { print } }' > $script.out
    $args $script.out
    exit_code=$?
    rm $script.out
else
    $args $script
    exit_code=$?
fi
exit $exit_code
```

But what it gives you is the ability to execute your markdown files as though they were scripts: It is a drop-in replacement for `/usr/bin/env`:

```python
#!blaze python
print("hi")
```

It then allows as many paramaters to be passed to your interpreter as you like (unlike normal shebangs), which means you can use tools like [pex](https://github.com/pantsbuild/pex):

```python
#!blaze pex arrow --
import arrow
print("run", arrow.now().humanize())  # blaze only processes .md files, plain scripts can be run as-normal
```

> (Note that we are able to use pex's ephemeral venv trick to run python with any requirements pre-installed)

Blaze's REAL trick, is that if it is called with a `.md` file, it only executes code inside triple-backtick codefences, as in this all-encompasing example of a literate program with built-in requirements:

```python
#!blaze pex flask flask_restful --

# Imports
First the imports, this demo requires the `flask_restful` package.
Then we set up the Flask wsgi application object, `app` and the api wrapper, `api`.

``python
from flask import Flask
from flask_restful import Resource, Api

app = Flask(__name__)
api = Api(app)
``

# Flask Restful resources
We define a single `HelloWorld` resource, that responds with a simple json
object on a `GET` request.

``python
class HelloWorld(Resource):
    def get(self):
        return {'hello': 'world'}
``

# Routing
`api.add_resource()` wires the `Resource` class `HelloWorld` into the flask
router at `/`.

``
api.add_resource(HelloWorld, '/')
``

# Run Server
After we have created everything, we run the flask werkzeug server.

``
if __name__ == '__main__':
    app.run()
``
```

> (double backticks should be triple, but that messes with markdown highlighting - sorry!)

Magic, right?

## Overhead
Blaze introduces minimal startup overhead, somewhere between 5-20ms, an almost zero runtime overhead (`sh` is running, I suppose).

### Python Shebang
```shell
λ ./py-test.py
hi
./py-test.py  0.02s user 0.00s system 94% cpu 0.025 total
```

### Blaze Shebang
```shell
λ ./blaze-test.py
hi
./blaze-test.py  0.02s user 0.00s system 67% cpu 0.030 total
```

## Prior Art / Acknowledgements

Blaze is currently a hacked-together LP tool that is only suitable for one-off scripts. I borrowed the awk code-fence-stripping code from [@trauber](https://gist.github.com/trauber/4955706).


## And the name?
Blaze is lit, bro 😎

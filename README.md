# Quickstart

Download the script, put it somewhere on your `PATH`, and make it executable, eg:

```shell
sudo wget blaze.oat.sh/blaze -O /usr/bin/blaze && \
sudo chmod +x /usr/bin/blaze
```

> (future upgrades can be done in-place with `blaze --upgrade`)

Now go write your executable markdown with

`#!/usr/bin/blaze python (or ruby, node, whatever!)`

# "Literate Programming?"

Literate Programming (LP for short) flips code commenting on its head: In normal programming, we write comments inside code. In LP, you write executable code inside a human-readable document.

This documentation-first idea requires a mental shift: You are writing documentation that has occasional references to implamentation, not code that has a smattering of comments. This forces you to think of the audience as another programmer, not a machine. When you think about it, that's who the real audience has been the whole time.

Blaze allows you to write executable code inside Markdown documents.

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

## Usage
Fundamentally, Blaze is a drop-in replacement for `/usr/bin/env`. You stick it at the top of your script, and you can execute it. But Blaze's REAL trick, is that if called with an .md file, it executes code inside triple-backtick codefences: This gives you is the ability to execute your markdown files as though they were normal scripts (it runs python, ruby, nodejs, shell, and many more). Here's a ruby hello world example:

`myscript.rb.md`
````markdown
#!blaze python

# This file is just markdown

As is this text.
Whatever you put in markdown's code fences,
gets executed by blaze:

```ruby
print "hello world"
```
````

If you were to run this file, you would see this:

```shell
λ ./myscript.rb.md
hello world
```

Congratulations, you just executed a markdown file!

More examples in Ruby and Nodejs are in the [examples/](https://github.com/0atman/blaze/tree/master/examples) folder, but the principle is the same: Code inside backticks is executed.

# Advanced Usage

Blaze also allows as many paramaters to be passed to your interpreter as you like (unlike normal shebangs), which means you can use tools like python's [pex](https://github.com/pantsbuild/pex):

`myscript.py.md`
````markdown
#!blaze pex arrow --
import arrow

# `Arrow` humanized dates example

```python
print("run", arrow.now().humanize())
```
````
> (Note that we are able to use pex's ephemeral venv trick to run python with any requirements pre-installed)

Combine these techniques together, and you get an all-encompasing example of a standalone literate webserver with built-in requirements:

`myapi.py.md`
````markdown
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
````

Magic, right?

## Overhead
Blaze introduces minimal startup overhead, somewhere between 5-20ms, an almost zero runtime overhead (`sh` is running, I suppose).

# Mechanics

Here's the basics of Blaze, a small shell script:

```shell
#!/usr/bin/env sh
args=$1
script=$2

cat $script | awk '{ if (/^```/) { i++; next } if ( i % 2 == 1) { print } }' > $script.out
$args $script.out
rm $script.out
```

> (non-core code stripped from this example, for the real deal, check [the source](https://github.com/0atman/blaze/blob/master/blaze)

As you can see blaze runs your script through `awk` to strip all text outside triple-backtick code fences, then runs it with the interpreter of your choice. There's nothing to it really!

## Prior Art / Acknowledgements

Blaze is currently a hacked-together LP tool that is only suitable for one-off scripts. It would not be possible without [Rich Traube's](http://www.github.com/trauber) code-fence-stripping code [here](https://gist.github.com/trauber/4955706).

# Further Reading

> I started out with a document that was a simple bulleted list of the features I wanted it to have, took each feature one at a time and fleshed it out into a paragraph, and then picked out paragraphs to implement inline. A bit of rewriting, and when the document was done, the blog worked.

[-ashkenas.com/literate-coffeescript/](https://web.archive.org/web/20130531033055/http://ashkenas.com/literate-coffeescript/) (archived in 2013)



## And the name?
Blaze is lit, bro 😎

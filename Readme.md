Unofficial Heroku buildpack: Python + npm dependency resolution
========================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks) for Python apps, powered by [pip](http://www.pip-installer.org/).

[![Build Status](https://secure.travis-ci.org/heroku/heroku-buildpack-python.png?branch=master)](http://travis-ci.org/heroku/heroku-buildpack-python)

Differences
-----------

The main difference from the official Python buildpack is support for node dependencies via [npm](http://npmjs.org).

This is accomplished via an `npm_requirements.txt` which must be in the base directory (along with the `requirements.txt` file that is used by pip)

If an `npm_requirements.txt` file is present it works similar to the way you're used to with pip.

Each line of the file is appended to `npm install` so for example

npm_requirements.txt:

    coffee-script
    less@1.0.0
    
would result in the following commands being run to resolve the dependencies:

    $ npm install -g coffee-script
    $ npm install -g less@1.0.0
    
Why install Node along with Python?
-----------------------------------

Primarily because it's desirable to use technologies like [less-css](http://lesscss.org/) and [coffeescript](http://coffeescript.org/) on the server (with something like [django compressor](https://github.com/jezdez/django_compressor) for example)

Usage
-----

Example usage:

    $ ls
    Procfile  requirements.txt  web.py

    $ heroku create --stack cedar --buildpack git@github.com:jiaaro/heroku-buildpack-django.git

    $ git push heroku master
    ...
    -----> Fetching custom git buildpack... done
    -----> Python app detected
    -----> No runtime.txt provided; assuming python-2.7.3.
    -----> Preparing Python runtime (python-2.7.3)
    -----> Installing Distribute (0.6.34)
    -----> Installing Pip (1.2.1)
    -----> Installing dependencies using Pip (1.2.1)
           Downloading/unpacking Flask==0.7.2 (from -r requirements.txt (line 1))
           Downloading/unpacking Werkzeug>=0.6.1 (from Flask==0.7.2->-r requirements.txt (line 1))
           Downloading/unpacking Jinja2>=2.4 (from Flask==0.7.2->-r requirements.txt (line 1))
           Installing collected packages: Flask, Werkzeug, Jinja2
           Successfully installed Flask Werkzeug Jinja2
           Cleaning up...

You can also add it to upcoming builds of an existing application:

    $ heroku config:add BUILDPACK_URL=git://github.com/heroku/heroku-buildpack-python.git

The buildpack will detect your app as Python if it has the file `requirements.txt` in the root. 

It will use Pip to install your dependencies, vendoring a copy of the Python runtime into your slug. 

Specify a Runtime
-----------------

You can also provide arbitrary releases Python with a `runtime.txt` file.

    $ cat runtime.txt
    python-3.3.0
    
Runtime options include:

- python-2.7.3
- python-3.3.0
- pypy-1.9 (experimental)

To change the vendored virtualenv, unpack the desired version to the `src/` folder, and update the virtualenv() function in `bin/compile` to prepend the virtualenv module directory to the path. The virtualenv release vendors its own versions of pip and setuptools.

Changing Buildpacks
-------------------

If you've already deployed an app to heroku and you'd like to switch to this buildpack from the standard python buildpack, just run the following command with the heroku command line app:

    heroku config:add BUILDPACK_URL=git://github.com/jiaaro/heroku-buildpack-django.git
    

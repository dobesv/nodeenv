Node.js virtual environment
===========================

``nodeenv`` (node.js virtual environment) is a tool to create 
isolated node.js environments.

It creates an environment that has its own installation directories, 
that doesn't share libraries with other node.js virtual environments.

Also the new environment can be integrated with the environment which was built
by virtualenv_ (python).

If you use nodeenv feel free to add your project on wiki: `Who-Uses-Nodeenv`_.

.. _Who-Uses-Nodeenv: https://github.com/ekalinin/nodeenv/wiki/Who-Uses-Nodeenv

.. image:: https://travis-ci.org/ekalinin/nodeenv.svg?branch=master
    :target: https://travis-ci.org/ekalinin/nodeenv

.. contents:: :local:


Install
-------

Global installation
^^^^^^^^^^^^^^^^^^^

You can install nodeenv globally with `easy_install`_::

    $ sudo easy_install nodeenv

or with `pip`_::

    $ sudo pip install nodeenv

or on Debian using `dpkg`_::

    $ ln -s debian-upstream debian
    $ dpkg-buildpackage -uc -us -b
    $ sudo dpkg -i $(ls -1rt ../nodeenv_*.deb | tail -n1)

.. _dpkg: https://www.debian.org/doc/manuals/debian-faq/ch-pkgtools.en.html

Local installation
^^^^^^^^^^^^^^^^^^

If you're using virtualenv_ then you can install nodeenv via
pip_/easy_install_ inside any virtual environment built with virtualenv::

    $ virtualenv env
    $ . env/bin/activate
    (env) $ pip install nodeenv
    (env) $ nodeenv --version
    0.6.5

If you want to work with the latest version of the nodeenv you can 
install it from the github `repository`_::

    $ git clone https://github.com/ekalinin/nodeenv.git
    $ ./nodeenv/nodeenv.py --help

or with `pip`_::

    $ pip install -e git+https://github.com/ekalinin/nodeenv.git#egg=nodeenv

.. _repository: https://github.com/ekalinin/nodeenv
.. _pip: http://pypi.python.org/pypi/pip
.. _easy_install: http://pypi.python.org/pypi/setuptools


Dependency
----------

For nodeenv
^^^^^^^^^^^

* python (2.6+, 3.3+, or pypy)
* make
* tail

For node.js
^^^^^^^^^^^

* libssl-dev

Usage
-----

Basic
^^^^^

Create new environment::

    $ nodeenv env

Activate new environment::

    $ . env/bin/activate

Check versions of main packages::

    (env) $ node -v
    v0.10.26

    (env) $ npm -v
    1.4.3

Deactivate environment::

    (env) $ deactivate_node

Advanced
^^^^^^^^

Get available node.js versions::


    $ nodeenv --list
    7.10.0  7.10.1  8.0.0   8.1.0   8.1.1   8.1.2   8.1.3   8.1.4
    8.2.0   8.2.1   8.3.0   8.4.0   8.5.0   8.6.0   8.7.0   8.8.0
    8.8.1   8.9.0   8.9.1   8.9.2   8.9.3   8.9.4   8.10.0  8.11.0
    8.11.1  9.0.0   9.1.0   9.2.0   9.2.1   9.3.0   9.4.0   9.5.0
    9.6.0   9.6.1   9.7.0   9.7.1   9.8.0   9.9.0   9.10.0  9.10.1
    9.11.0  9.11.1

Install node.js "0.4.3" without ssl support with 4 parallel commands 
for compilation and npm.js "0.3.17"::

    $ nodeenv --without-ssl --node=0.4.3 --npm=0.3.17 --jobs=4 env-4.3

Install node.js from the source::

    $ nodeenv --node=0.10.25 --source env-0.10.25-prebuilt

It's much faster to install from the prebuilt package than Install & compile
node.js from source::

    $ time nodeenv --node=0.10.25 --prebuilt env-0.10.25-prebuilt
     + Install node.js (0.10.25) ... done.

    real    0m6.928s
    user    0m0.408s
    sys     0m1.144s

    $ time nodeenv --node=0.10.25 --source env-0.10.25-src
     + Install node.js (0.10.25) ... done.

    real    4m12.602s
    user    6m34.112s
    sys     0m30.524s

Create a new environment with the system-wide node.js::

    $ nodeenv --node=system

Saving the versions of all installed packages to a file::

    $ . env-4.3/bin/activate
    (env-4.3)$ npm install -g express
    (env-4.3)$ npm install -g jade
    (env-4.3)$ freeze ../prod-requirements.txt

If you want to list locally installed packages use ``-l`` option::

    (env-4.3)$ freeze -l ../prod-requirements.txt

Create an environment from a requirements file::

    $ nodeenv --requirements=../prod-requirements.txt --jobs=4 env-copy

Requirements files are plain text files that contain a list of packages 
to be installed. These text files allow you to create repeatable installations.
Requirements file example::

    $ cat ../prod-requirements.txt
    connect@1.3.0
    express@2.2.2
    jade@0.10.4
    mime@1.2.1
    npm@0.3.17
    qs@0.0.7

If you already have the python virtualenv tool, and want to use nodeenv and
virtualenv in conjunction, then you should create (or activate) the python
virtual environment::

    # in case of using virtualenv_wrapper
    $ mkvirtualenv my_env

    # in case of using virtualenv
    $ . my_env/bin/activate

and add a node virtual environment to this existing new_venv::

    $ nodeenv -p
    
If you need to set the path to make used to build node::

	$ nodeenv -m /usr/local/bin/gmake ENV

That's all. Now, all your node.js modules will be installed into your virtual
environment::

    $ workon my_env
    $ npm install -g coffee-script
    $ which coffee
    /home/monty/virtualenvs/my_env/bin/coffee

If environment's directory already exists then you can use ``--force`` option::

    $ nodeenv --requirements=requirements.txt --jobs=4 --force env

If you already have an environment and want to update packages from requirements
file you can use ``--update`` option::

    $ . env-4.3/bin/activate
    (env-4.3)$ nodeenv --requirements=requirements.txt --update env-4.3

If you want to call node from environment without activation then you should
use `shim` script::

    $ ./env-4.3/bin/shim --version
    v0.4.3


If you want to install iojs instead of nodejs then use ``--iojs``::

    $ virtualenv env
    $ . env/bin/activate
    (env) $ nodeenv --iojs --list
    1.0.0   1.0.1
    (env) $ nodeenv --iojs -p --prebuilt
     * Install iojs (1.0.1) ... done.
     * Appending data to ~/tmp/env/bin/activate


Configuration
-------------
You can use the INI-style file ``~/.nodeenvrc`` to set default values for many options,
the keys in that file are the long command-line option names.

These are the available options and their defaults::

    [nodeenv]
    debug = False
    jobs = 2
    make = make
    node = latest
    npm = latest
    prebuilt = False
    profile = False
    with_npm = False
    without_ssl = False

Alternatives
------------

There are several alternatives that create isolated environments:

* `nave <https://github.com/isaacs/nave>`_ - Virtual Environments for Node.
  Nave stores all environments in one directory ``~/.nave``. Can create
  per node version environments using `nave use envname versionname`.
  Can not pass additional arguments into configure (for example --without-ssl)
  Can't run on windows because it relies on bash.

* `nvm <https://github.com/creationix/nvm/blob/master/nvm.sh>`_ - Node Version
  Manager. It is necessarily to do `nvm sync` for caching available node.js
  version.
  Can not pass additional arguments into configure (for example --without-ssl)

* virtualenv_ - Virtual Python Environment builder. For python only.

.. _`virtualenv`: https://github.com/pypa/virtualenv

LICENSE
=======

BSD / `LICENSE <https://github.com/ekalinin/nodeenv/blob/master/LICENSE>`_

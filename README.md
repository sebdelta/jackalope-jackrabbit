# Jackalope [![Build Status](https://secure.travis-ci.org/jackalope/jackalope-jackrabbit.png?branch=master)](http://travis-ci.org/jackalope/jackalope-jackrabbit)

A powerful implementation of the [PHPCR API](http://phpcr.github.com).

Jackalope binding for the jackrabbit backend server.

Discuss on jackalope-dev@googlegroups.com
or visit #jackalope on irc.freenode.net

License: This code is licenced under the apache license.
Please see the file LICENSE in this folder.


# Preconditions

* php >= 5.3
* libxml version >= 2.7.0 (due to a bug in libxml [http://bugs.php.net/bug.php?id=36501](http://bugs.php.net/bug.php?id=36501))
* phpunit >= 3.6 (if you want to run the tests)
* [composer](http://getcomposer.org/)

# Installation

If you do not yet have composer, install it like this

    curl -s http://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin

To install jackalope itselves, run the following in the parent directory of where you want jackalope

    git clone git://github.com/jackalope/jackalope-jackrabbit.git
    cd jackalope-jackrabbit
    php /usr/local/bin/composer.phar install --install-suggests

Note that the --install-suggests parameter is only needed if you want to be
able to run the test suite. If you already installed jackalope without the test
suite, you need to remove composer.lock before running composer again with the
--install-suggests parameter.


## Jackrabbit storage server

Besides the Jackalope repository, you need the Jackrabbit server component. For instructions, see [Jackalope Wiki](https://github.com/jackalope/jackalope/wiki/Running-a-jackrabbit-server)
Make sure you have at least the version specified in [the VERSION constant of the protocol implementation](https://github.com/jackalope/jackalope-jackrabbit/blob/master/src/Jackalope/Transport/Jackrabbit/Client.php)


## phpunit tests

If you want to run the tests, please see the
[README file in the tests folder](https://github.com/jackalope/jackalope-jackrabbit/blob/master/tests/README.md)
and check if you told composer to install the suggested dependencies (see Installation)


## Enable the commands

There are a couple of useful commands to interact with the repository.

To use the console, copy cli-config.php.dist to cli-config.php and adjust it
to use jackrabbit or doctrine dbal and configure the connection parameters.
Then you can run the commands from the jackalope directory with ``./bin/jackalope``

NOTE: If you are using PHPCR inside of **Symfony**, the DoctrinePHPCRBundle
provides the commands inside the normal Symfony console and you don't need to
prepare anything special.

Jackalope specific commands:

* ``jackalope:run:jackrabbit [--jackrabbit_jar[="..."]] [start|stop|status]``:
    Start and stop the Jackrabbit server

Commands available from the phpcr-utils:

* ``phpcr:workspace:create <name>``: Create a workspace *name* in the repository
* ``phpcr:register-node-types --allow-update [cnd-file]``: Register namespaces
    and node types from a "Compact Node Type Definition" .cnd file
* ``phpcr:dump [--sys_nodes[="..."]] [--props[="..."]] [path]``: Show the node
    names under the specified path. If you set sys_nodes=yes you will also see
    system nodes. If you set props=yes you will additionally see all properties
    of the dumped nodes.
* ``phpcr:purge``: Remove all content from the configured repository in the
     configured workspace
* ``phpcr:sql2``: Run a query in the JCR SQL2 language against the repository
    and dump the resulting rows to the console.



# Bootstrapping

Jackalope relies on autoloading and namespaces and folders are compliant with
PSR-0. You should use the autoload file generated by composer:
``vendor/.composer/autoload.php``

If you want to integrate jackalope into other PSR-0 compliant code and use your
own classloader, find the mapping in ``vendor/.composer/autoload_namespaces.php``


## Bootstrapping Jackrabbit

Minimalist sample code to get a PHPCR session with the jackrabbit backend.

    $jackrabbit_url = 'http://127.0.0.1:8080/server/';
    $user           = 'admin';
    $pass           = 'admin';
    $workspace      = 'default';

    $repository = \Jackalope\RepositoryFactoryJackrabbit::getRepository(
        array('jackalope.jackrabbit_uri' => $jackrabbit_url)
    );
    $credentials = new \PHPCR\SimpleCredentials($user, $pass);
    $session = $repository->login($credentials, $workspace);

To use a workspace different than ``default`` you need to create it first. The
easiest is to run the command ``bin/jackalope phpcr:workspace:create <myworkspace>``
but you can of course also use the PHPCR API to create workspaces from your code.


# Usage

The entry point is to create the repository factory. The factory specifies the
storage backend as well. From this point on, there are no differences in the
usage (except for supported features, that is).

    // see Bootstrapping for how to get the session.

    $rootNode = $session->getNode("/");
    $whitewashing = $rootNode->addNode("www-whitewashing-de");
    $session->save();

    $posts = $whitewashing->addNode("posts");
    $session->save();

    $post = $posts->addNode("welcome-to-blog");
    $post->addMixin("mix:title");
    $post->setProperty("jcr:title", "Welcome to my Blog!");
    $post->setProperty("jcr:description", "This is the first post on my blog! Do you like it?");

    $session->save();


See [PHPCR Tutorial](https://github.com/phpcr/phpcr/blob/master/doc/Tutorial.md)
for a more detailed tutorial on how to use the PHPCR API.


# Implementation notes

See [doc/architecture.md](https://github.com/jackalope/jackalope/blob/master/doc/architecture.md)
for an introduction how Jackalope is built. Have a look at the source files and
generate the phpdoc.


# TODO

The best overview of what needs to be done are the functional test failures and
skipped tests. Have a look at [JackrabbitImplementationLoader](https://github.com/jackalope/jackalope-jackrabbit/blob/master/tests/inc/JackrabbitImplementationLoader.php) to
see what is currently not working and start hacking :-)


# Contributors

* Christian Stocker <chregu@liip.ch>
* David Buchmann <david@liip.ch>
* Tobias Ebnöther <ebi@liip.ch>
* Roland Schilter <roland.schilter@liip.ch>
* Uwe Jäger <uwej711@googlemail.com>
* Lukas Kahwe Smith <smith@pooteeweet.org>
* Daniel Barsotti <daniel.barsotti@liip.ch>
* [and many others](https://github.com/jackalope/jackalope-jackrabbit/contributors)

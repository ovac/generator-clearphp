# generator-cleanphp

[![NPM version][npm-image]][npm-url] [![NPM downloads][npm-downloads]][npm-url] [![Dependency Status][depstat-image]][depstat-url] [![Development Dependency Status][devdepstat-image]][devdepstat-url]

is a Yeoman generator for scaffolding PHP projects following a custom implementation of clean architecture principles. During scaffolding, you can select from a variety of 3rd party tools and service intergrations.

## Usage

To **install the PHP project generator** and its dependencies, please run:

```
npm install -g yo generator-cleanphp
```

To **scaffold a new PHP project**, create a new directory, `cd` into it and run the generator:
  
```
mkdir vendorname-project
cd vendorname-project
yo cleanphp
```

## Generators

The generator consists of several subgenerators:
 
* [cleanphp](#app) (aka [cleanphp:app](#app))
* [cleanphp:main](#main)
* [cleanphp:git](#git)
* [cleanphp:codeclimate](#codeclimate)
* [cleanphp:coverage](#coverage)
* [cleanphp:scrutinizer](#scrutinizer)
* [cleanphp:docs](#docs)
* [cleanphp:apidocs](#apidocs)

The subgenerators partly depend on each other:

![Subgenerator dependencies](https://rawgit.com/jkphl/generator-cleanphp/master/doc/generator-dependencies.svg)

Each subgenerator will only run once and automatically pull in the other generators it depends on. You can run them individually at any time, using them as supplementary add-ons to your project. 

### App

The `cleanphp:app` subgenerator is basically a meta generator calling all of the other subgenerators.

```
yo cleanphp
```

### Main

The `cleanphp:main` generator creates the base project structure and setup. During installation, you will be asked a couple of questions like the vendor and project name, the minimum PHP version and some information about the project author. Running

```
yo cleanphp:main
```

will scaffold these files and directories for you:

```bash
|-- .editorconfig
|-- .yo-rc.json
|-- CHANGELOG.md
|-- CONDUCT.md
|-- LICENSE
|-- README.md
|-- composer.json
|-- composer.lock
|-- doc
|-- phpunit.php
|-- phpunit.xml.dist
`-- src
    `-- <Project name> 
        |-- Application
        |-- Domain
        |-- Infrastructure
        |-- Ports
        `-- Tests

```

#### Composer dependencies

* [phpunit/phpunit](https://github.com/sebastianbergmann/phpunit): Unit testing framework
* [clue/graph-composer](https://github.com/jkphl/graph-composer): Library for creating dependency graphs of your project

#### Files & directories

| File               | Description                                                                                                                                                                                                                                   |
|:-------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `.editorconfig`    | [Editorconfig](http://editorconfig.org) definitions file                                                                                                                                                                                      |
| `.yo-rc.json`      | Yeoman's configuration file where your answers are stored between generator runs. Don't edit this file by hand. However, if you're sure you'll never run any of the generators again, you can safely delete this file.                        |
| `CHANGELOG.md`     | Changelog of your project. Try to stick to [Keep a CHANGELOG](http://keepachangelog.com/) principles when adding entries.                                                                                                                     |
| `CONDUCT.md`       | Contributor Code of Conduct, adapted from the [Contributor covenant](http://contributor-covenant.org/version/1/4/) version 1.4.                                                                                                               |
| `LICENCE`          | The license you selected during installation.                                                                                                                                                                                                 |
| `README.md`        | The main README file of your project. Be aware that it might be amended and overwritten when you run additional subgenerators at a later time. However, you'll always have the chance to review the differences (if any) and skip the update. |
| `composer.json`    | The [Composer](https://getcomposer.org/) configuration of your project.                                                                                                                                                                       |
| `doc`              | This is the directory where your project documentation should reside. You can add some basic files with the [cleanphp:docs](#docs) generator.                                                                                                 |
| `phpunit.php`      | Bootstrap file for [PHPUnit](https://github.com/sebastianbergmann/phpunit) including the Composer autoloader.                                                                                                                                 |
| `phpunit.xml.dist` | [PHPUnit](https://github.com/sebastianbergmann/phpunit) configuration                                                                                                                                                                         |
| `src`              | Base directory for your PHP project files. [See below](#clean-architecture) for some words on the clean architecture principles proposed by the generator.                                                                                    |

#### Scripts

The initial setup will configure a Composer script you can use for creating a SVG dependency graph of your project.
  
```
composer run depgraph
```

This will create a graph like this:

![PHP project dependency graph (example)](https://rawgit.com/jkphl/generator-cleanphp/master/doc/dependency-graph.svg)

stored here:

```bash
|-- doc
|   `-- dependencies.svg
```

By default, the dependency graph is embedded into the `README.md`. However, unless you run the [cleanphp:git](#git) subgenerator you'll have to create and update the graph manually each time you change the Composer dependencies of your project. 

### Composer setup

The default composer dependencies are:

* `php`

The default composer development dependencies are:

* `clue/graph-composer`

### Unit tests

If you enable Unit tests, the following resources are added:

```
|-- phpunit.php
|-- phpunit.xml.dist
`-- src
  `-- <Project name>
      `-- Tests
```

The following composer development dependencies are added:

* `phpunit/phpunit`

## Subgenerators

### Code Climate

If you enable [code climate](https://codeclimate.com) support (account needed), the following resources are added:

```
|-- .codeclimate.yml
`-- phpmd.xml
```

The following composer development dependencies are added:

* `codeclimate/php-test-reporter`

The following lines are added to the Travis CI configuration (`.travis.yml`):

```
after_script:
  - bash -c 'if [ "$TRAVIS_PHP_VERSION" != "hhvm" ]; then vendor/bin/test-reporter; fi;'
addons:
    code_climate:
        repo_token: <CODE-CLIMATE-REPO-TOKEN-HERE>
 ```
 
### Scrutinizer

If you enable [scrutinizer](https://scrutinizer-ci.com/) support (account needed), the following resources are added:

```
`-- .scrutinizer.yml
```

Code coverage data submission to Scrutinizer is added to the Travis CI configuration (`.travis.yml`):

```
after_script:
  - bash -c 'if [ "$TRAVIS_PHP_VERSION" != "hhvm" ]; then wget https://scrutinizer-ci.com/ocular.phar; fi;'
  - bash -c 'if [ "$TRAVIS_PHP_VERSION" != "hhvm" ]; then php ocular.phar code-coverage:upload --format=php-clover build/logs/clover.xml; fi;'
```

 
### Code coverage

If you enable code coverage support ([Coveralls](https://coveralls.io/) account needed), the following composer development dependencies are added:

* `satooshi/php-coveralls`

Code coverage data submission to Coveralls is added to the Travis CI configuration (`.travis.yml`):

```
after_script:
  - bash -c 'if [ "$TRAVIS_PHP_VERSION" != "hhvm" ]; then php vendor/bin/coveralls -v; fi;'
```

### Documentation

If you enable documentation support ([Read the Docs](https://readthedocs.org/) account needed), the following resources are added:

```
|-- mkdocs.yml
`-- doc
    |-- index.md
    `-- todo.md
```

### API documentation

If you enable API documentation support (via phpDox), the following resources are added:

```
`-- phpdox.xml.dist
```

The following composer development dependencies are added:

* `theseer/phpdox`
* `phploc/phploc`
* `phpmd/phpmd`

```
vendor/bin/phploc.bat --count-tests --progress --log-xml build/phploc.xml src
vendor/bin/phpmd.bat src xml phpmd.xml --reportfile build/pmd.xml
vendor/bin/phpunit.bat
vendor/bin/phpdox.bat 
```

Clean Architecture
------------------

Known problems / To-do
----------------------

Currently there are no known problems.


Changelog
---------

Please refer to the [changelog](CHANGELOG.md) for a complete release history.


Legal
-----
Copyright © 2017 Joschi Kuphal <joschi@kuphal.net> / [@jkphl](https://twitter.com/jkphl). *generator-cleanphp* is licensed under the terms of the [MIT license](LICENSE.txt).


[npm-url]: https://npmjs.org/package/generator-cleanphp
[npm-image]: https://badge.fury.io/js/generator-cleanphp.svg
[npm-downloads]: https://img.shields.io/npm/dm/generator-cleanphp.svg
[depstat-url]: https://david-dm.org/jkphl/generator-cleanphp#info=dependencies
[depstat-image]: https://david-dm.org/jkphl/generator-cleanphp.svg
[devdepstat-url]: https://david-dm.org/jkphl/generator-cleanphp#info=devDependencies
[devdepstat-image]: https://david-dm.org/jkphl/generator-cleanphp/dev-status.svg


[homepage]: http://contributor-covenant.org

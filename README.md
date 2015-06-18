# Asparagus

[![Build Status](https://secure.travis-ci.org/Benestar/asparagus.png?branch=master)](http://travis-ci.org/Benestar/asparagus)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/Benestar/asparagus/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/Benestar/asparagus/?branch=master)
[![Code Coverage](https://scrutinizer-ci.com/g/Benestar/asparagus/badges/coverage.png?b=master)](https://scrutinizer-ci.com/g/Benestar/asparagus/?branch=master)
[![Download count](https://poser.pugx.org/benestar/asparagus/d/total.png)](https://packagist.org/packages/benestar/asparagus)
[![License](https://poser.pugx.org/benestar/asparagus/license.svg)](https://packagist.org/packages/benestar/asparagus)

[![Latest Stable Version](https://poser.pugx.org/benestar/asparagus/version.png)](https://packagist.org/packages/benestar/asparagus)
[![Latest Unstable Version](https://poser.pugx.org/benestar/asparagus/v/unstable.svg)](//packagist.org/packages/benestar/asparagus)

**Asparagus** is a SPARQL abstraction layer for PHP. It's design is inspired
by the DBAL query builder.

## Installation

You can use [Composer](http://getcomposer.org/) to download and install
this package as well as its dependencies. Alternatively you can simply clone
the git repository and take care of loading yourself.

### Composer

To add this package as a local, per-project dependency to your project, simply add a
dependency on `benestar/asparagus` to your project's `composer.json` file.
Here is a minimal example of a `composer.json` file that just defines a dependency on
Asparagus 0.1:

```js
{
    "require": {
        "benestar/asparagus": "~0.1"
    }
}
```

### Manual

Get the Asparagus code, either via git, or some other means. Also get all dependencies.
You can find a list of the dependencies in the "require" section of the composer.json file.
The "autoload" section of this file specifies how to load the resources provide by this library.

## Usage

Most of the methods in `QueryBuilder` return the builder instance so you can build a query
by calling the methods one by one. Currently, the `QueryBuilder` supports to manage prefixes,
select variables, add basic triple conditions and group them by subject and predicate, and
full support for all query modifiers SPARQL provides.

The `QueryBuilder` instance can be passed to a `QueryExecuter` or the SPARQL can be obtained
as is using `getSPARQL` or formatted using `format`.

In the following example, a simple SPARQL query is generated asking for all persons which
have a name and an email address stored in the database.

```php
use Asparagus\QueryBuilder;

$prefixes = array(
	'test' => 'http://www.example.com/test#'
);

$queryBuilder = new QueryBuilder( $prefixes );
$queryBuilder->select( 'name', 'email' )
	->where( '?person', 'test:name', '?name' )
	->also( 'test:email', '?email' )
	->limit( 10 );

echo $queryBuilder->format();
```

The generated query looks like:

```sparql
PREFIX test: <http://www.example.com/test#>

SELECT ?name ?email WHERE {
	?person test:name ?name ;
		test:email ?email .
}
LIMIT 10
```

## Tests

This library comes with a set up PHPUnit tests that cover all non-trivial code. You can run these
tests using the PHPUnit configuration file found in the root directory. The tests can also be run
via TravisCI, as a TravisCI configuration file is also provided in the root directory.

## Release notes

### 0.2 (dev)

* Renamed `QueryBuilder::plus` to `QueryBuilder::also`
* `QueryBuilder::select`, `QueryBuilder::groupBy` and `QueryBuilder::orderBy` now expect prefixed
  variables instead of just the variable name
* Removed `QueryBuilder::prefix` as prefixes should be defined in the constructor
* Added more validation for variables and prefixes. `QueryBuilder::getSPARQL` will throw a
  `RangeException` if the validation fails.
** Selected variables that don't occur in the conditions are detected
** Prefixes which haven't been declared are detected
** Variable names and IRIs now have to mach the correct format

### 0.1 (2015-06-17)

Initial release with these features:

* A `QueryBuilder` with basic functionality to generate SPARQL queries
* A `QueryFormatter` to make SPARQL queries human-readable
* A `QueryExecuter` which sends queries to a SPARQL endpoint and parses the result

## License

Asparagus is licensed under the GNU General Public License Version 2. A copy of the license can be
found in the [LICENSE file](LICENSE).

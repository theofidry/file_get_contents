humbug_get_contents
===================

[![Build Status](https://travis-ci.org/humbug/file_get_contents.svg)](https://travis-ci.org/humbug/file_get_contents)

Defines a `humbug_get_contents()` function that will transparently call `file_get_contents()`,
except for HTTPS URIs where it will inject a context configured to enable secure
SSL/TLS requests on all versions of PHP less than 5.6.

All versions of PHP below 5.6 not only disable SSL/TLS protections by default, but
have most other default options set insecurely. This has led to
the spread of insecure uses of `file_get_contents()` to retrieve HTTPS resources. For example,
PHAR files or API requests. Without SSL/TLS protections, all such requests are vulnerable
to Man-In-The-Middle attacks where a hacker can inject a fake response, e.g. a tailored php
file or json response.

Installation
============

```sh
composer require padraic/humbug_get_contents
```

Usage
=====

```php
$content = humbug_get_contents('https://www.howsmyssl.com/a/check');
```

You can use this function as an immediate alternative to file_get_contents() in any code
location where HTTP requests are probable.

This solution was originally implemented within the Composer Installer, so this is a
straightforward extraction of that code into a standalone package with just the one function.
It borrows functions from both Composer and Sslurp.

In rare cases, this function will complain when attempting to retrieve HTTPS URIs. This is
actually the point ;). An error should have two causes:

* A valid cafile could not be located, i.e. your server is misconfigured or missing a package
* The URI requested could not be verified, i.e. in a browser this would be a red page warning.

Neither is, in any way, a justification for disabling SSL/TLS and leaving end users vulnerable
to getting hacked. Resolve such errors; don't ignore or workaround them.

###Headers

You can set request headers, and get response headers, using the following functions.
This support is based around stream contexts, but is offered in some limited form
here as a convenience. If your needs are going to extend this, you should use a
more complete solution and double check that it fully enables and supports TLS.

```php
/**
 * Don't end headers with \r\n when setting via array
 */
humbug_set_headers(array(
    'Accept-Language: da',
    'User-Agent: Humbug'
));
$response = humbug_get_contents('http://www.example.com');
```

Request headers are emptied when used, so you would need to reset on each
`humbug_get_contents()` call.

To retrieve an array of the last response headers:

```php
$response = humbug_get_contents('http://www.example.com');
$headers = humbug_get_headers();
```

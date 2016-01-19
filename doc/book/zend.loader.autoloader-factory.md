# The AutoloaderFactory

## Overview

Starting with version 2.0, Zend Framework now offers multiple autoloader strategies. Often, it will
be useful to employ multiple autoloading strategies; as an example, you may have a class map for
your most used classes, but want to use a PSR-0 style autoloader for 3rd party libraries.

While you could potentially manually configure these, it may be more useful to define the autoloader
configuration somewhere and cache it. For these cases, the `AutoloaderFactory` will be useful.

## Quick Start

Configuration may be stored as a PHP array, or in some form of configuration file. As an example,
consider the following PHP array:

```php
$config = array(
    'Zend\Loader\ClassMapAutoloader' => array(
        'application' => APPLICATION_PATH . '/.classmap.php',
        'zf'          => APPLICATION_PATH . '/../library/Zend/.classmap.php',
    ),
    'Zend\Loader\StandardAutoloader' => array(
        'namespaces' => array(
            'Phly\Mustache' => APPLICATION_PATH . '/../library/Phly/Mustache',
            'Doctrine'      => APPLICATION_PATH . '/../library/Doctrine',
        ),
    ),
);
```

An equivalent INI-style configuration might look like the following:

```php
Zend\Loader\ClassMapAutoloader.application = APPLICATION_PATH "/.classmap.php"
Zend\Loader\ClassMapAutoloader.zf          = APPLICATION_PATH "/../library/Zend/.classmap.php"
Zend\Loader\StandardAutoloader.namespaces.Phly\Mustache = APPLICATION_PATH
"/../library/Phly/Mustache"
Zend\Loader\StandardAutoloader.namespaces.Doctrine       = APPLICATION_PATH "/../library/Doctrine"
```

Once you have your configuration in a PHP array, you simply pass it to the `AutoloaderFactory`.

```php
// This example assumes ZF is on your include_path.
// You could also load the factory class from a path relative to the
// current script, or via an absolute path.
require_once 'Zend/Loader/AutoloaderFactory.php';
Zend\Loader\AutoloaderFactory::factory($config);
```

The `AutoloaderFactory` will instantiate each autoloader with the given options, and also call its
`register()` method to register it with the SPL autoloader.

## Configuration Options

**$options**  
The `AutoloaderFactory` expects an associative array or `Traversable` object. Keys should be valid
autoloader class names, and the values should be the options that should be passed to the class
constructor.

Internally, the `AutoloaderFactory` checks to see if the autoloader class referenced exists. If not,
it will use the \[StandardAutoloader\](zend.loader.standard-autoloader) to attempt to load the class
via the `include_path` (or, in the case of "Zend"-namespaced classes, using the Zend Framework
library path). If the class is not found, or does not implement the
\[SplAutoloader\](zend.loader.spl-autoloader) interface, an exception will be raised.

## Available Methods

factory  
Instantiate and register autoloaders `factory($options)`

**factory()** This method is **static**, and is used to instantiate autoloaders and register them
with the SPL autoloader. It expects either an array or `Traversable` object as denoted in the
Options section
&lt;zend.loader.autoloader-factory.options&gt;.

<!-- -->

getRegisteredAutoloaders  
Retrieve a list of all autoloaders registered using the factory `getRegisteredAutoloaders()`

**getRegisteredAutoloaders()** This method is **static**, and may be used to retrieve a list of all
autoloaders registered via the `factory()` method. It returns simply an array of autoloader
instances.

<!-- -->

getRegisteredAutoloader  
Retrieve an autoloader by class name `getRegisteredAutoloader($class)`

**getRegisteredAutoloader()** This method is **static**, and is used to retrieve a specific
autoloader. It expects a string with the autoloader class name. If the autoloader is not registered,
an exception will be thrown.

<!-- -->

unregisterAutoloaders  
Unregister all autoloaders registered via the factory. `unregisterAutoloaders()`

**unregisterAutoloaders()** This method is **static**, and can be used to unregister all autoloaders
that were registered via the factory. Note that this will **not** unregister autoloaders that were
registered outside of the factory.

<!-- -->

unregisterAutoloader  
Unregister an autoloader registered via the factory. `unregisterAutoloader($class)`

**unregisterAutoloader()** This method is **static**, and can be used to unregister an autoloader
that was registered via the factory. Note that this will **not** unregister autoloaders that were
registered outside of the factory. If the autoloader is registered via the factory, after
unregistering it will return `TRUE`, otherwise `FALSE`.

## Examples

Please see the \[Quick Start\](zend.loader.autoloader-factory.quick-start) for a detailed example.

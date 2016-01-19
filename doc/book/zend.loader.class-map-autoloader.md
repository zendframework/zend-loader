# The ClassMapAutoloader

## Overview

The `ClassMapAutoloader` is designed with performance in mind. The idea behind it is simple: when
asked to load a class, see if it's in the map, and, if so, load the file associated with the class
in the map. This avoids unnecessary filesystem operations, and can also ensure the autoloader "plays
nice" with opcode caches and PHP's realpath cache.

Zend Framework provides a tool for generating these class maps; you can find it in
`bin/classmap_generator.php` of the distribution. Full documentation of this is provided in the
Class Map
generator &lt;zend.loader.classmap-generator&gt; section.

## Quick Start

The first step is to generate a class map file. You may run this over any directory containing
source code anywhere underneath it.

```php
php classmap_generator.php Some/Directory/
```

This will create a file named `Some/Directory/autoload_classmap.php`, which is a PHP file returning
an associative array that represents the class map.

Within your code, you will now instantiate the `ClassMapAutoloader`, and provide it the location of
the map.

```php
// This example assumes ZF is on your include_path.
// You could also load the autoloader class from a path relative to the
// current script, or via an absolute path.
require_once 'Zend/Loader/ClassMapAutoloader.php';
$loader = new Zend\Loader\ClassMapAutoloader();

// Register the class map:
$loader->registerAutoloadMap('Some/Directory/autoload_classmap.php');

// Register with spl_autoload:
$loader->register();
```

At this point, you may now use any classes referenced in your class map.

## Configuration Options

The `ClassMapAutoloader` defines the following options.

**$options**  
The `ClassMapAutoloader` expects an array of options, where each option is either a filename
referencing a class map, or an associative array of class name/filename pairs.

As an example:

```php
// Configuration defining both a file-based class map, and an array map
$config = array(
    __DIR__ . '/library/autoloader_classmap.php', // file-based class map
    array(                              // array class map
        'Application\Bootstrap' => __DIR__ . '/application/Bootstrap.php',
        'Test\Bootstrap'        => __DIR__ . '/tests/Bootstrap.php',
    ),
);
```

## Available Methods

\_\_construct  
Initialize and configure the object `__construct($options = null)`

**Constructor** Used during instantiation of the object. Optionally, pass options, which may be
either an array or `Traversable` object; this argument will be passed to setOptions()
&lt;zend.loader.class-map-autoloader.methods.set-options&gt;.

<!-- -->

setOptions  
Configure the autoloader `setOptions($options)`

**setOptions()** Configures the state of the autoloader, including registering class maps. Expects
an array or `Traversable` object; the argument will be passed to registerAutoloadMaps()
&lt;zend.loader.class-map-autoloader.methods.register-autoload-maps&gt;.

<!-- -->

registerAutoloadMap  
Register a class map `registerAutoloadMap($map)`

**registerAutoloadMap()** Registers a class map with the autoloader. `$map` may be either a string
referencing a PHP script that returns a class map, or an array defining a class map.

More than one class map may be registered; each will be merged with the previous, meaning it's
possible for a later class map to overwrite entries from a previously registered map.

<!-- -->

registerAutoloadMaps  
Register multiple class maps at once `registerAutoloadMaps($maps)`

**registerAutoloadMaps()** Register multiple class maps with the autoloader. Expects either an array
or `Traversable` object; it then iterates over the argument and passes each value to
registerAutoloadMap()
&lt;zend.loader.class-map-autoloader.methods.register-autoload-map&gt;.

<!-- -->

getAutoloadMap  
Retrieve the current class map `getAutoloadMap()`

**getAutoloadMap()** Retrieves the state of the current class map; the return value is simply an
array.

<!-- -->

autoload  
Attempt to load a class. `autoload($class)`

**autoload()** Attempts to load the class specified. Returns a boolean `false` on failure, or a
string indicating the class loaded on success.

<!-- -->

register  
Register with spl\_autoload. `register()`

**register()** Registers the `autoload()` method of the current instance with
`spl_autoload_register()`.

## Examples

### Using configuration to seed ClassMapAutoloader

Often, you will want to configure your `ClassMapAutoloader`. These values may come from a
configuration file, a cache (such as ShMem or memcached), or a simple PHP array. The following is an
example of a PHP array that could be used to configure the autoloader:

```php
// Configuration defining both a file-based class map, and an array map
$config = array(
    APPLICATION_PATH . '/../library/autoloader_classmap.php', // file-based class map
    array(                              // array class map
        'Application\Bootstrap' => APPLICATION_PATH . '/Bootstrap.php',
        'Test\Bootstrap'        => APPLICATION_PATH . '/../tests/Bootstrap.php',
    ),
);
```

An equivalent INI style configuration might look like this:

```php
classmap.library = APPLICATION_PATH "/../library/autoloader_classmap.php"
classmap.resources.Application\Bootstrap = APPLICATION_PATH "/Bootstrap.php"
classmap.resources.Test\Bootstrap = APPLICATION_PATH "/../tests/Bootstrap.php"
```

Once you have your configuration, you can pass it either to the constructor of the
`ClassMapAutoloader`, to its `setOptions()` method, or to `registerAutoloadMaps()`.

```php
/* The following are all equivalent */

// To the constructor:
$loader = new Zend\Loader\ClassMapAutoloader($config);

// To setOptions():
$loader = new Zend\Loader\ClassMapAutoloader();
$loader->setOptions($config);

// To registerAutoloadMaps():
$loader = new Zend\Loader\ClassMapAutoloader();
$loader->registerAutoloadMaps($config);
```

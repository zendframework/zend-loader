# The StandardAutoloader

## Overview

`Zend\Loader\StandardAutoloader` is designed as a
[PSR-0](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md)-compliant
autoloader. It assumes a 1:1 mapping of the namespace+classname to the filesystem, wherein namespace
separators and underscores are translated to directory separators. A simple statement that
illustrates how resolution works is as follows:

```php
$filename = str_replace(array('_', '\\'), DIRECTORY_SEPARATOR, $classname)
          . '.php';
```

Previous incarnations of PSR-0-compliant autoloaders in Zend Framework have relied upon the
`include_path` for file lookups. This has led to a number of issues:

- Due to the use of `include`, if the file is not found, a warning is raised -- even if another
autoloader is capable of resolving the class later.
- Documenting how to setup the `include_path` has proven to be a difficult concept to convey.
- If multiple Zend Framework installations exist on the `include_path`, the first one on the path
wins -- even if that was not the one the developer intended.

To solve these problems, the `StandardAutoloader` by default requires that you explicitly register
namespace/path pairs (or vendor prefix/path pairs), and will only load a file if it exists within
the given path. Multiple pairs may be provided.

As a measure of last resort, you may also use the `StandardAutoloader` as a "fallback" autoloader --
one that will look for classes of any namespace or vendor prefix on the `include_path`. This
practice is not recommended, however, due to performance implications.

Finally, as with all autoloaders in Zend Framework, the `StandardAutoloader` is capable of
registering itself with PHP's SPL autoloader registry.

> ## Note
#### Vocabulary: Namespaces vs. Vendor Prefixes
In terms of autoloading, a "namespace" corresponds to PHP's own definition of namespaces in PHP
versions 5.3 and above.
A "vendor prefix" refers to the practice, popularized in PHP versions prior to 5.3, of providing a
pseudo-namespace in the form of underscore-separated words in class names. As an example, the class
`Phly_Couch_Document` uses a vendor prefix of "Phly", and a component prefix of "Phly\_Couch" -- but
it is a class sitting in the global namespace within PHP 5.3.
The `StandardAutoloader` is capable of loading either namespaced or vendor prefixed class names, but
treats them separately when attempting to match them to an appropriate path.

## Quick Start

Basic use of the `StandardAutoloader` requires simply registering namespace/path pairs. This can
either be done at instantiation, or via explicit method calls after the object has been initialized.
Calling `register()` will register the autoloader with the SPL autoloader registry.

If the option key 'autoregister\_zf' is set to true then the class will register the "Zend"
namespace to the directory above where its own classfile is located on the filesystem.

### Manual Configuration

```php
// This example assumes ZF is on your include_path.
// You could also load the autoloader class from a path relative to the
// current script, or via an absolute path.
require_once 'Zend/Loader/StandardAutoloader.php';
$loader = new Zend\Loader\StandardAutoloader(array('autoregister_zf' => true));

// Register the "Phly" namespace:
$loader->registerNamespace('Phly', APPLICATION_PATH . '/../library/Phly');

// Register the "Scapi" vendor prefix:
$loader->registerPrefix('Scapi', APPLICATION_PATH . '/../library/Scapi');

// Optionally, specify the autoloader as a "fallback" autoloader;
// this is not recommended.
$loader->setFallbackAutoloader(true);

// Register with spl_autoload:
$loader->register();
```

### Configuration at Instantiation

The `StandardAutoloader` may also be configured at instantiation. Please note:

- The argument passed may be either an array or a `Traversable` object.
- The argument passed is also a valid argument for passing to the `setOptions()` method.

The following is equivalent to the previous example.

```php
require_once 'Zend/Loader/StandardAutoloader.php';
$loader = new Zend\Loader\StandardAutoloader(array(
    'autoregister_zf' => true,
    'namespaces' => array(
        'Phly' => APPLICATION_PATH . '/../library/Phly',
    ),
    'prefixes' => array(
        'Scapi' => APPLICATION_PATH . '/../library/Scapi',
    ),
    'fallback_autoloader' => true,
));

// Register with spl_autoload:
$loader->register();
```

## Configuration Options

The `StandardAutoloader` defines the following options.

**namespaces**  
An associative array of namespace/path pairs. The path should be an absolute path or path relative
to the calling script, and contain only classes that live in that namespace (or its subnamespaces).
By default, the "Zend" namespace is registered, pointing to the parent directory of the file
defining the `StandardAutoloader`.

**prefixes**  
An associative array of vendor prefix/path pairs. The path should be an absolute path or path
relative to the calling script, and contain only classes that begin with the provided vendor prefix.

**fallback\_autoloader**  
A boolean value indicating whether or not this instance should act as a "fallback" autoloader (i.e.,
look for classes of any namespace or vendor prefix on the `include_path`). By default, `false`.

**autoregister\_zf**  
An boolean value indicating that the class should register the "Zend" namespace to the directory
above where its own classfile is located on the filesystem.

## Available Methods

\_\_construct  
Initialize a new instance of the object `__construct($options = null)`

**Constructor** Takes an optional `$options` argument. This argument may be an associative array or
`Traversable` object. If not null, the argument is passed to
\[setOptions()\](zend.loader.standard-autoloader.methods.set-options).

<!-- -->

setOptions  
Set object state based on provided options. `setOptions($options)`

**setOptions()** Takes an argument of either an associative array or `Traversable` object.
Recognized keys are detailed under \[Configuration
options\](zend.loader.standard-autoloader.options), with the following behaviors:

- The `namespaces` value will be passed to registerNamespaces()
    &lt;zend.loader.standard-autoloader.methods.register-namespaces&gt;.
- The `prefixes` value will be passed to registerPrefixes()
    &lt;zend.loader.standard-autoloader.methods.register-prefixes&gt;.
- The `fallback_autoloader` value will be passed to setFallbackAutoloader()
    &lt;zend.loader.standard-autoloader.methods.set-fallback-autoloader&gt;.

<!-- -->

setFallbackAutoloader  
Enable/disable fallback autoloader status `setFallbackAutoloader($flag)`

**setFallbackAutoloader()** Takes a boolean flag indicating whether or not to act as a fallback
autoloader when registered with the SPL autoloader.

<!-- -->

isFallbackAutoloader  
Query fallback autoloader status `isFallbackAutoloader()`

**isFallbackAutoloader()** Indicates whether or not this instance is flagged as a fallback
autoloader.

<!-- -->

registerNamespace  
Register a namespace with the autoloader `registerNamespace($namespace, $directory)`

**registerNamespace()** Register a namespace with the autoloader, pointing it to a specific
directory on the filesystem for class resolution. For classes matching that initial namespace, the
autoloader will then perform lookups within that directory.

<!-- -->

registerNamespaces  
Register multiple namespaces with the autoloader `registerNamespaces($namespaces)`

**registerNamespaces()** Accepts either an array or `Traversable` object. It will then iterate
through the argument, and pass each item to
\[registerNamespace()\](zend.loader.standard-autoloader.methods.register-namespace).

<!-- -->

registerPrefix  
Register a vendor prefix with the autoloader. `registerPrefix($prefix, $directory)`

**registerPrefix()** Register a vendor prefix with the autoloader, pointing it to a specific
directory on the filesystem for class resolution. For classes matching that initial vendor prefix,
the autoloader will then perform lookups within that directory.

<!-- -->

registerPrefixes  
Register many vendor prefixes with the autoloader `registerPrefixes($prefixes)`

**registerPrefixes()** Accepts either an array or `Traversable` object. It will then iterate through
the argument, and pass each item to
\[registerPrefix()\](zend.loader.standard-autoloader.methods.register-prefix).

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

Please review the \[examples in the quick start\](zend.loader.standard-autoloader.quick-start) for
usage.

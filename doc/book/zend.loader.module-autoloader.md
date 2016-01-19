# The ModuleAutoloader

## Overview

`Zend\Loader\ModuleAutoloader` is a special implementation of the Zend\\\\Loader\\\\SplAutoloader 
&lt;zend.loader.spl-autoloader&gt; interface, used by
\[Zend\\ModuleManager\](zend.module-manager.intro) to autoload `Module` classes from different
sources.

Apart from being able to autoload modules from directories, the `ModuleAutoloader` can also autoload
modules packaged as [Phar archives](http://php.net/phar), which allows for packaging your modules in
a single file for easier distribution. Supported archive formats are: `.phar`, `.phar.gz`,
`.phar.bz2`, `.phar.tar`, `.phar.tar.gz`, `.phar.tar.bz2`, `.phar.zip`, `.tar`, `tar.gz`, `.tar.bz2`
and `.zip`. It is, however, recommended to avoid compressing your packages (be it either gz, bz2 or
zip compression), as it introduces additional CPU overhead to every request.

## Quickstart

As the `ModuleAutoloader` is meant to be used with the `ModuleManager`, for examples of it's usage
and how to configure it, please see the Module Autoloader Usage
&lt;zend.module-manager.module-autoloader.usage&gt; section of the `ModuleManager` documentation.

## Configuration Options

The `ModuleAutoloader` defines the following options.

**$options**  
The `ModuleAutoloader` expects an array of options, where each option is either a path to scan for
modules, or a key/value pair of explicit module paths. In the case of explicit module paths, the key
is the module's name, and the value is the path to that module.

```php
$options = array(
    '/path/to/modules',
    '/path/to/other/modules',
    'MyModule' => '/explicit/path/mymodule-v1.2'
);
```

## Available Methods

\_\_construct  
Initialize and configure the object `__construct($options = null)`

**Constructor** Used during instantiation of the object. Optionally, pass options, which may be
either an array or `Traversable` object; this argument will be passed to setOptions()
&lt;zend.loader.module-autoloader.methods.set-options&gt;.

<!-- -->

setOptions  
Configure the module autoloader `setOptions($options)`

**setOptions()** Configures the state of the autoloader, registering paths to modules. Expects an
array or `Traversable` object; the argument will be passed to registerPaths()
&lt;zend.loader.module-autoloader.methods.register-paths&gt;.

<!-- -->

autoload  
Attempt to load a `Module` class. `autoload($class)`

**autoload()** Attempts to load the specified `Module` class. Returns a boolean `false` on failure,
or a string indicating the class loaded on success.

<!-- -->

register  
Register with spl\_autoload. `register()`

**register()** Registers the `autoload()` method of the current instance with
`spl_autoload_register()`.

<!-- -->

unregister  
Unregister with spl\_autoload. `unregister()`

**unregister()** Unregisters the `autoload()` method of the current instance with
`spl_autoload_unregister()`.

<!-- -->

registerPaths  
Register multiple paths with the autoloader. `registerPaths($paths)`

**registerPaths()** Register a paths to modules. Expects an array or `Traversable` object. For an
example array, please see the \[Configuration options\](zend.loader.module-autoloader.options)
section.

<!-- -->

registerPath  
Register a single path with the autoloader. `registerPath($path, $moduleName=false)`

**registerPath()** Register a single path with the autoloader. The first parameter, `$path`, is
expected to be a string. The second parameter, `$moduleName`, is expected to be a module name, which
allows for registering an explicit path to that module.

<!-- -->

getPaths  
Get all paths registered with the autoloader. `getPaths()`

**getPaths()** Returns an array of all the paths registered with the current instance of the
autoloader.

## Examples

Please review the \[examples in the quick start\](zend.loader.module-autoloader.quickstart) for
usage.

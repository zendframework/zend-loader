# The PluginClassLocator interface

## Overview

The `PluginClassLocator` interface describes a component capable of maintaining an internal map of
plugin names to actual class names. Classes implementing this interface can register and unregister
plugin/class associations, and return the entire map.

## Quick Start

Classes implementing the `PluginClassLocator` need to implement only three methods, as illustrated
below.

```php
namespace Zend\Loader;

interface PluginClassLocator
{
    public function registerPlugin($shortName, $className);
    public function unregisterPlugin($shortName);
    public function getRegisteredPlugins();
}
```

## Configuration Options

This component defines no configuration options, as it is an interface.

## Available Methods

registerPlugin  
Register a mapping of plugin name to class name `registerPlugin($shortName, $className)`

**registerPlugin()** Implement this method to add or overwrite plugin name/class name associations
in the internal plugin map. `$shortName` will be aliased to `$className`.

<!-- -->

unregisterPlugin  
Remove a plugin/class name association `unregisterPlugin($shortName)`

**unregisterPlugin()** Implement this to allow removing an existing plugin mapping corresponding to
`$shortName`.

<!-- -->

getRegisteredPlugins  
Retrieve the map of plugin name/class name associations `getRegisteredPlugins()`

**getRegisteredPlugins()** Implement this to allow returning the plugin name/class name map.

## Examples

Please see the \[Quick Start\](zend.loader.plugin-class-locator.quick-start) for the interface
specification.

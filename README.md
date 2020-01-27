# Guidelines

The KrystalCode Drupal 8 Standards is a set of rules that builds on top of the [Drupal 8 coding standards](https://www.drupal.org/docs/develop/standards). The main goal is to improve readability.

## Classes, properties and methods

### Extends and implements

Similarly to [PSR-2](https://www.php-fig.org/psr/psr-2/#41-extends-and-implements), implements MUST be split across multiple lines IF the line length exceeds 80 characters.

```
<?php

namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class ClassName extends ParentClass implements
    \ArrayAccess,
    \Countable,
    \Serializable {

    // constants, properties, methods

}
```

## Code structure

### Module files

Breakdown your file into sections:

* Hooks
* Callbacks
* Public API - functions that constitute a Public API and may be used by other modules; they should remain unchanged for the same major version.
* Functions for internal use - functions meant to be used only within the scope of the module and its submodules; they are not guaranteed to remain unchanged.

#### Example

```
<?php

/**
 * Hooks and functionality for the My Module module.
 */

/**
 * Hooks.
 */

// Hooks here.

/**
 * Callbacks.
 */

// Callbacks here.

/**
 * Public API.
 */

// Public API functions here.

/**
 * Functions for internal use.
 */

// Functions for internal use here.
```

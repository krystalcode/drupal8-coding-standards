# Guidelines

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
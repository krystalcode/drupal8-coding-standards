# Guidelines

The KrystalCode Drupal 8 Standards is a set of rules that builds on top of the [Drupal 8 coding standards](https://www.drupal.org/docs/develop/standards). The main goal is to improve readability.

## Declare statements, namespace, and import statements

### Import statements

Similarly to [PSR-12](https://www.php-fig.org/psr/psr-12/#3-declare-statements-namespace-and-import-statements), `use` import statements MUST be grouped in blocks that are ordered in the following sequence:
* Class-based `use` import statements.
* Function-based `use` import statements.
* Constant-based `use` import statements.

All statements within each group SHOULD be listed in alphabetical order.

```
use Vendor\Package\AnotherNamespace\ClassE as E;
use Vendor\Package\{ClassA as A, ClassB, ClassC as C};
use Vendor\Package\SomeNamespace\ClassD as D;

use function Another\Vendor\functionD;
use function Vendor\Package\{functionA, functionB, functionC};

use const Another\Vendor\CONSTANT_D;
use const Vendor\Package\{CONSTANT_A, CONSTANT_B, CONSTANT_C};
```

If the number of lines in each block exceeds 5, `use` import statements MUST be further grouped in sub-blocks based on the provider of the imported item. The first breakdown SHOULD be as follows:
* Drupal modules
* Drupal core
* External libraries

```
use Drupal\my_module\Entity\MyEntity;
use Drupal\commerce_product\Entity\ProductInterface;
use Drupal\user\UserInterface;

use Drupal\Core\Entity\EntityTypeManagerInterface;
use Drupal\Core\Form\FormInterface;

use Symfony\Component\DependencyInjection\ContainerInterface;
```


If the Drupal modules block grows large it SHOULD be further broken down as follows:
* Custom modules
* Contrib modules
* Core modules

```
use Drupal\my_module\Entity\MyEntity;
use Drupal\my_module\Entity\Storage\MyEntityStorageInterface;

use Drupal\commerce_product\Entity\ProductInterface;
use Drupal\commerce_product\Entity\ProductVariationInterface;
use Drupal\commerce_order\Entity\OrderInterface;

use Drupal\taxonomy\TermInterface;
use Drupal\user\UserInterface;
```

## Classes, properties and methods

### Extends and implements

Similarly to [PSR-2](https://www.php-fig.org/psr/psr-2/#41-extends-and-implements), implements MUST be split across multiple lines IF the line length exceeds 80 characters.

```
// commerce_sheets/src/Plugin/QueueWorker/Import.php

class Import extends QueueWorkerBase implements
  ContainerFactoryPluginInterface {
}
```

### Methods

Methods should be ordered based on their visibility i.e. public methods first, protected methods then and private methods at the end.

```
class MyClass {

  public function myPublicFunction {

  }

  protected function myProtectedFunction {

  }

  private function myPrivateFunction {

  }
}
```

### Method arguments

Similarly to [PSR-2](https://www.php-fig.org/psr/psr-2/#44-method-arguments), the argument list MUST be split across multiple lines IF the line length exceeds 80 characters.

```
// commerce_sheets/src/Plugin/QueueWorker/Import.php

/**
 * Constructs a new Import object.
 *
 * @param array $configuration
 *   A configuration array containing information about the plugin instance.
 * @param string $plugin_id
 *   The plugin_id for the plugin instance.
 * @param array $plugin_definition
 *   The plugin implementation definition.
 * @param \Drupal\Core\Entity\EntityTypeManagerInterface $entity_type_manager
 *   The entity type manager.
 * @param \Psr\Log\LoggerInterface $logger
 *   The Commerce Sheets logger.
 * @param \Drupal\commerce_sheets\Sheet\ReaderInterface $reader
 *   The Commerce Sheets reader service.
 */                                                                   
public function __construct(
  array $configuration,
  $plugin_id,
  array $plugin_definition,
  EntityTypeManagerInterface $entity_type_manager,
  LoggerInterface $logger,
  ReaderInterface $reader
) {
  parent::__construct($configuration, $plugin_id, $plugin_definition);

  $this->importStorage = $entity_type_manager
    ->getStorage('commerce_sheets_import');

  $this->logger = $logger;
  $this->reader = $reader;
}
```

## Code structure

### Module files

If the module file grows large, break its content into sections:

* Hooks
* Callbacks
* Public API - functions that constitute a Public API and may be used by other modules; they should remain unchanged for the same major version.
* Functions for internal use - functions meant to be used only within the scope of the module and its submodules; they are not guaranteed to remain unchanged.

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

# Hook guidelines

## Objectives

### Dependency injection & OOP

In the spirit of following object-oriented programming, that the rest of Drupal is built based on, service classes should be defined for hooks. Dependency injection principles can then be implemented as well for improved testability.

### Readability

`.module` files tend to become quite lengthy as several hook implementations are added. Logically grouping them into service classes improves the structure and the readability of the code.

## Naming conventions

### Hook classes

Hooks should be grouped together in a way that makes logical sense for the module e.g. per hook or per hook type. For example, a class `EntitySave` can hold implementations of `hook_entity_insert`, `hook_node_update` and `hook_commerce_order_presave`.

Hook classes should not be too lengthy; in the above example, if there are too many hook implementations related to entity save operations they can be further split in `EntityInsert`, `EntityUpdate` and `EntityPreSave` classes, or per entity type e.g. `EntitySave`, `NodeSave` and `CommerceOrderSave`.

All hook classes should be placed under the `src/Hook` folder.

### Hook methods

The hook methods should be named in a way that makes sense in the context of the hook class. In the `EntitySave` service class example, the methods could be:

* `entityInsert()`
* `nodeUpdate()`
* `commerceOrderPreSave()`

In a `CommerceOrderSave` service class, the last method could be simply `preSave()`.

## Example

Here is an example from the Group Commerce Context module.

```
// gcommerce_context.module
/**
 * Implements hook_ENTITY_TYPE_insert().
 *
 * @see \Drupal\gcommerce_context\Hook\EntitySave::commerceOrderInsert()
 */
function gcommerce_commerce_order_insert(EntityInterface $entity) {
  \Drupal::service('gcommerce_context.hook.entity_save')
    ->commerceOrderInsert($entity);
}
```

```
// gcommerce_context.services.yml
# Hooks.
# Implemented as services for OOP.
gcommerce_context.hook.entity_save:
  class: 'Drupal\gcommerce_context\Hook\EntitySave'
  arguments:
    - '@gcommerce_context.manager'
    - '@current_user'
    - '@entity_type.manager'
```

```
// src/Hook/EntitySave.php
<?php

namespace Drupal\gcommerce_context\Hook;

use Drupal\commerce_order\Entity\OrderInterface;
use Drupal\gcommerce_context\Context\ManagerInterface;

use Drupal\Core\Entity\EntityTypeManagerInterface;
use Drupal\Core\Session\AccountProxyInterface;

/**
 * Holds methods implementing hooks related to entity saving.
 */
class EntitySave {

  /**
   * The shopping context manager.
   *
   * @var \Drupal\gcommerce_context\Context\ManagerInterface
   */
  protected $contextManager;

  /**
   * The current user account.
   *
   * @var \Drupal\Core\Session\AccountInterface
   */
  protected $currentUser;

  /**
   * The entity type manager.
   *
   * @var \Drupal\Core\Entity\EntityTypeManagerInterface
   */
  protected $entityTypeManager;

  /**
   * Constructs a new EntitySave object.
   *
   * @param \Drupal\gcommerce_context\Context\ManagerInterface $context_manager
   *   The shopping context manager.
   * @param \Drupal\Core\Session\AccountProxyInterface $current_user
   *   The current user account proxy.
   * @param \Drupal\Core\Entity\EntityTypeManagerInterface $entity_type_manager
   *   The entity type manager.
   */
  public function __construct(
    ManagerInterface $context_manager,
    AccountProxyInterface $current_user,
    EntityTypeManagerInterface $entity_type_manager
  ) {
    $this->contextManager = $context_manager;
    $this->currentUser = $current_user->getAccount();
    $this->entityTypeManager = $entity_type_manager;
  }

  /**
   * Implements hook_ENTITY_TYPE_insert().
   *
   * We add new cart orders to the user's current context, if any.
   *
   * Orders can be created in different circumstances outside of the process of
   * adding products to the cart. For example, a store manager can create an
   * order on behalf of another user. We therefore take the following
   * precautions.
   * - We do nothing if the order is not a cart.
   * - We do nothing if the current user is not the order's customer.
   *
   * @param \Drupal\commerce_order\Entity\OrderInterface $order
   *   The order that was created.
   */
  public function commerceOrderInsert(OrderInterface $order) {
    if ($this->currentUser->id() != $order->getCustomerId()) {
      return;
    }

    if (!$order->get('cart')) {
      return;
    }

    $context = $this->contextManager->get();
    if (!$context) {
      return;
    }

    $content_storage = $this->entityTypeManager->getStorage('group_content');
    $content = $content_storage->createForEntityInGroup(
      $order,
      $context,
      'group_commerce_order:' . $order->bundle()
    );
    $content_storage->save($content);
  }

}

```

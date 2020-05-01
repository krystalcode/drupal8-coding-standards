# Field guidelines

## Objectives

### Performance

### Reduce  number of fields/database tables

### Length

## Naming conventions

### General

#### Main field name part

Fields should be named after their type and be descriptive of their storage settings. For example:
* Unformatted strings (`string` type) with the default storage settings should be named `${PREFIX}string`.
* Formatted strings (`text` type) with the default storage settings should be named `${PREFIX}text`.
* Unformatted long strings (`string_long`) with the default storage settings should be named `${PREFIX}string_long`.
* Formatted long strings (`text_long`) with the default storage settings should be named `${PREFIX}text_long`.
* Formatted long strings with summary (`text_with_summary`) with the default storage settings should be named `${PREFIX}text_with_summary`.
* Integers (`integer` type) with the default storage settings should be named `${PREFIX}integer`.
* Timestamps (`timestamp` type) with the default storage settings should be named `${PREFIX}timestamp`.

`${PREFIX}` is a prefix, `field_` by default in Drupal e.g. `field_string`, `field_text` etc. The choice of the prefix to use is discussed later in this document.

The reason for naming fields after their type (when the storage settings are left to their defaults) is that it allows for maximum reusability of fields. Say we have a `Blog` content type with a short description field and an `Page` content type with a subtitle field. If we were to name the short description field `${PREFIX}short_description` we couldn't really reuse it for the `Page` content type; we would have to create another field called `${PREFIX}subtitle` ending up with 2 fields where we really need only 1. Naming the field `${PREFIX}string` allows us to only create 1 field and use it for multiple content types.

It is important to note that this works well when the storage settings are left to the field type's default. A commentary on customized storage settings is found later in this document.

#### Numbering suffix

When there are multiple fields of the same type on the same entity type and bundle, we add a numbering suffix e.g. `${PRPEFIX}string_2`, `${PREFIX}string_3` etc. The first occurence of the type should be left without a suffix i.e. `${PREFIX}string` and NOT `${PREFIX}string_1`.

#### Cardinality

Field cardinality (allowed number of values) is configured in the field storage settings; it therefore needs to be reflected in the field's machine name. It is recommended that fields meant to hold multiple values have their cardinality always set to unlimited. Field storage settings are more difficult to change once data have been generated for the field; it's therefore best to use always unlimited cardinality and impose any limits in the code e.g. in form or entity validation.

Following this convention we do not need a suffix identifying the cardinality; we just need to convey whether the field is meant to hold one or multiple values. We use the singular and plural version of the field type's machine name to convey that. For example:
* Single value string fields should be named `${PREFIX}string`.
* Multiple value string fields should be named `${PREFIX}strings`.
* Single value integer fields should be named `${PREFIX}integer`.
* Multiple value integer fields should be named `${PREFIX}integers`.

### Special cases

#### Entity reference fields

Entity reference fields is a spaces case; we want to identify the not only the field type but also the type of the entity(ies) being referenced by the field because that is set in the field's storage settings. In the vast majority of cases entity type machine names are unique and they don't overlap with field type machine names making it highly unlikely to cause a confusion. We therefore name entity reference fields after the entity type. For example:
* Fields referencing nodes (content items) should be named `${PREFIX}node(s)`.
* Fields referencing users should be named `${PREFIX}user(s)`.
* Fields referencing files should be named `${PREFIX}file(s)`.
* Fields referencing images should be named `${PREFIX}image(s)`.
* Fields referencing images should be named `${PREFIX}profile(s)`.

When the entity type machine name is long and there is no chance of ambiguity throughout the system, it can be shorten in a way that it makes sense for the case. For example:
* Fields referencing taxonomy terms should be named `${PREFIX}term(s)` and NOT `${PREFIX}taxonomy_term(s)`.
* Fields referencing products on applications using the Drupal Commerce ecosystem should be named `${PREFIX}product(s)` and NOT `${PREFIX}commerce_product(s)`.
* Fields referencing product attributes on applications using the Drupal Commerce ecosystem should be named `${PREFIX}product_attribute(s)` and NOT `${PREFIX}commerce_product_attribute(s)`.

The idea here is to have names as descriptive as necessary and as short as possible. In the above cases it is unambiguous that when using the name `term` or `product` we are referring to the `taxonomy_term` and `commerce_product` entity types respectively.

Note: It is common practice to add a `_ref` or `_reference` suffix in entity reference fields e.g. `${PREFIX}node_reference`. We do not adopt this approach in this guideline because it is redundant and makes field machine names unnecessarily long. It is extremely rare to have an entity type machine name being the same with a field type machine name which means that by seeing a field like `${PREFIX}node` it is unambiguous that it is a reference to a node entity. Adding the extra suffix makes the field machine names longer and that together with the fact that entity type machine names can be long some times risk exceeding the maximum number of characters that a machine name can have e.g. `${PREFIX}product_attribute_reference`. That can be made worse if we also have other suffixes such as multiple-value fields with a numbering suffix e.g. `${PREFIX}product_attributes_2_reference`. Using `${PREFIX}product_attributes_2` is shorter and unambiguous.

#### Custom field storage settings

As mentioned, the field's machine name should be descriptive of the field type and the field's storage settings. We use as a convention the field type as the main field name part when the storage settings are left to the defaults. If the storage settings are customized the field's machine name needs to reflect the customizations.

For example, if we create a `string` field that is meant to hold a code with maximum 3 digits we may want to optimize storage by setting the field length to 3. The field could be named `${PREFIX}string_3char` and it can then be reused to store a currency code in one bundle and a country code in another.

Another common use case is enumeration fields e.g. the `list_integer` field type. The possible values for the field are stored in the storage settings making them more difficult to reuse. For example, a field enumerating industries would set the possible values (e.g. Agriculture, Entertainment, Manufacturing etc.) in its storage settings and it can only be reused on other bundles for the same purpose. The field machine name should therefore be specific e.g. `${PREFIX}industry_type(s)`. 

### Prefix

Drupal allows adding a prefix to field machine names. The purpose of the following naming conventions are:
* Keep the field machine names short.
* Make it clear who is the provider of the field.
* Prevent conflicts between modules providing fields that have the same name.

#### Custom modules

Fields provided by custom modules should use the `app_` prefix e.g. `app_string(s)` or `app_integer(s)`. It is short, it makes it clear that it is an application-specific field, and it prevents conflicts with fields provided by core or contrib modules. 

#### Contrib modules

Contrib modules can use a custom short and descriptive prefix for fields they provide depending on the context. For example:
* The Entity Sync module expects a `sync_remote_id` field for holding the ID of the remote entity that we are syncing with. It is short, it makes it clear that it is an Entity Sync field and it is improbable to conflict with a field provided by another module.
* The Commerce Product module provides the `variations` field on its product entity and the Commerce Order module provides the `order_items` field on its order entity. Being the providers of all these entities and having a clear convention of what the use of these fields are, it removes the possibility of conflict with other fields referencing the same entity types. For example, if another custom or contrib module would need to add a field referencing variations on the product entity, it would be called `app_variations`, `field_variations`, or in general `${PREFIX}variations` whatever the chosen prefix might be.

## Field name constants

The most important reasons that developers tend to name fields based on their use instead of their field types (as proposed in this guideline) is to make it easier to remember a field machine name when they write code and to recognise a field machine name when they read code. I can immediately understand which field is `${PREFIX}subtitle` but I may not remember which one is `${PREFIX}string`. In the latter case, I would have to go to the Drupal UI or look at the configuration files to find which field are we referring to.

However, using field machine names based on their use does not cover all usability issues for developers. When writing code, I may not remember if the field is called `${PREFIX}subtitle` or `${PREFIX}sub_title`, especially if somebody else added the field or if long time has passed by since I added the field. There is also the chance of making a typo and risking regressions.

To solve these usability issues this guideline introduces the use of class constants for managing field machine names. It provides the following benefits:
* Text editors commonly recognize constants and provide features such as autocomplete, thus making it unnecessary to remember field machine name and eliminating the possibility of making typos.
* It allows for better documentation about the purpose or usage of the fields by adding comments in the class constants.
* It provides a central place for defining field machine names; if, for whatever reason, the machine name of a field needs to be changed we only need to change the value of the class constant (and any YAML configuration files where we cannot use constants) and the changes will apply to the whole codebase.

Fields should be grouped in blocks ordered as follows:
* Base fields.
* Shared bundle fields.
* Bundle-specific fields.

Fields within a block should be listed in alphabetical order based on field machine names.

In many cases, the same field may be reused for the same purpose across multiple bundles. For example, all product entity bundles commonly have a `variations` field that in all cases holds the product variations. These are a 'shared bundle fields' and we can use one constant only for all of them called `VARIATIONS`.

In other cases, however, we may reuse a field across bundles but for different purpose. For example, a `default` product entity bundle may have a taxonomy term reference field called `app_term` referencing the product category while another `branded` bundle may use the same field to reference the product brand. In that case we can define two bundle-specific constants: `DEFAULT__CATEGORY` and `BRANDED__BRAND`. The pattern here is to use the bundle name followed by the field name and separated by two underscores. We use two underscores instead of one so that the separation is clear in case the bundle or field name parts contain underscores as well e.g. `PARTNER_PRODUCT__REMOTE_ID`.

Here is an example class defining the fields for a product entity.

```
// app_product/src/MachineName/Field/Product.php
<?php

namespace Drupal\app_product\MachineName\Field;

/**
 * Class holding machine names for `commerce_product` entity fields.
 */
class Product {
  
  /**
   * Base fields.
   */

  /**
   * Holds the primary ID for the product. 
   */
  const ID = 'product_id';

  /**
   * Holds the product variations.
   */
  const VARIATIONS = 'variations';

  /**
   * Bundle fields reused across bundles for the same purpose.
   */

  /**
   * Holds the product images.
   */
  const IMAGES = 'app_images';

  /**
   * Bundle fields: `photography`.
   */

  /**
   * Holds additional images for photography products.
   *
   * Photography products can have extra images that are displayed in a special
   * carousel on the product page. 
   */
  const PHOTOGRAPHY__EXTRA_IMAGES = 'app_images_2';
}
```

In the above example, when we need to use the fields in the code we can conveniently use the constants without having to remember the field machine names as our text editor will provide autocomplete suggestions for us. If the field name changes for whatever reason, we do not have to make changes in the code; we just need to update the constant. For example, if we want to get the images for the product:

```
use Drupal\app_product\MachineName\Field\Product as ProductField;

$product->get(ProductField::IMAGES);
$product->get(ProductField::PHOTOGRAPHY__EXTRA_IMAGES);
```

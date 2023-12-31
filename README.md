<h1 align="center">parse-server-metafields</h2>
<h4 align="center">
  <img src="https://img.shields.io/npm/v/parse-server-metafields/latest.svg" />
</h4>

A utility to decorate Parse-Server object classes, enabling getters, setters, and integration with components like DataGrids by providing field metadata.

## Installation

Using npm:

```bash
npm install parse-server-metafields
```

Using yarn:

```bash
yarn add parse-server-metafields
```

## Peer Dependencies

Ensure that you have `parse` and `@goplan-finance/utils` installed in your project, as they are peer dependencies of this package.

## Usage

### Decorators

#### `@ParseModel`

This decorator registers a ParseModel with optional configurations. It's used to annotate classes that represent Parse objects.

Example:

```typescript
import {BaseObject} from "@goplan-finance/utils";

@ParseModel({identifier: 'test', label: 'Test Label', i18n: 'test_i18n'})
class Test extends BaseObject {
  constructor() {
    super(Test.className);
  }
}
Test.register(); // Registers the class with Parse
```

#### `@ParseField`

This decorator registers a field with optional configurations. It can be used to annotate properties within a `ParseModelClass`.

**Automatic Getters and Setters**: One of the powerful features of the `@ParseField` decorator is its ability to automatically generate getters and setters for the annotated properties. This allows for seamless access and modification of data within a Parse Object. When you annotate a property with `@ParseField`, the decorator will create getter and setter methods that internally use Parse's `get` and `set` methods. This provides a more intuitive way to interact with the properties of a Parse Object.

Example:

```typescript
import {BaseObject} from "@goplan-finance/utils";

class UserProfile extends BaseObject {
  static className = 'UserProfile';  
  constructor() {
    super(UserProfile.className);
  }
}

class TestModel extends BaseObject {
  static className = 'TestModel';
  constructor() {
    super(TestModel.className);
  }

  @ParseField({
      type: 'pointer',
      link: UserProfile,
      label: 'User Profile',
      i18n: 'user_profile',
      addGetterSetter: true, // by default, this is true, create getter and setter when the property doesn't have.
  })
  profile: UserProfile;
}
```

In the above example, `TestModel` has a pointer to `UserProfile`. The `@ParseField` decorator provides metadata about the `profile` field, indicating that it's a pointer to another `ParseModelClass`,
and also sets up the getter and setter for it.
- the key of the field is `profile` so the getter key will also be the same.

### Functions

#### `resolveChainedFields`

This function provides a comprehensive view of the fields in a given `ParseModelClass` and its linked entities. It's particularly useful when working with Parse's Pointer type, which allows one `ParseModelClass` to reference another.

When you have a model that contains pointers to other models, it's often necessary to understand not just the fields of the primary model but also the fields of the models it points to. This can be especially important when building UI components like DataGrids, where you might want to display data from both the primary model and its linked entities.

Example:

```typescript
const fields = resolveChainedFields(TestModel);
```

In this example, calling `resolveChainedFields` on `TestModel` will return fields from both `TestModel` and `UserProfile`.

### Metadata Added to the class

- **ParseModel**:
  ```typescript
        classConstructor: ParseModelClass;
        identifier: string;
        label: string;
        i18n: string;
  ```
- **ParseField**: Array of fields metas
  ```typescript
    type: string;
    label: string;
    i18n: string;
    gridConfig: Partial<GridOptions>;
    addGetterSetter?: boolean;
    link?: ParseModelClass;
  ```

## Contributing

For bugs and feature requests, please create an issue on the [GitHub repository](https://github.com/newera-systems/parsesuite-metafields/issues).

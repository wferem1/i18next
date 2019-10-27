# i18next

This is an adaptation of i18next standard in dart. This package is still a work in progress.
Mind that this is still a pre-1.0.0 so breaking changes may occur frequently.

- [x] Support for variables
- [x] Support for namespaces
- [x] Support for context
- [x] Support for simple plural forms (one or plural)
- [ ] Support for multiple plural forms (one, few, many, plural, ...)
- [x] Plural and context fallbacks
- [ ] Locale and namespace fallbacks 
- [x] Get string or object tree
- [x] Support for nesting
- [ ] Sprintf support
- [ ] Retrieve resource files from server
- [ ] Resource caching
- [ ] Custom post processing

## Usage

Simply declare the package in your `pubspec.yaml`

```yaml
dependencies:
  i18next: ^0.0.1
```

Then create your instance of `I18Next` class providing a `locale`, `data source``, and `interpolation options`.

```dart
I18Next(
  locale,
  (namespace, locale) => /* return your namespace file here */,
    // this can be omitted by the default options
  interpolation: InterpolationOptions(formatter: customFormatter),
);
```

## Syntax

For the simple and straightforward usages:

```json
{
  "key": "Hello World!",
  "nested": {
    "key": "My nested key"
  }
}
```

```dart
i18next.t('key'); // 'Hello World!'
i18next.t('nested.key'); // 'My nested key'

// unmapped keys usually return themselves (when graceful fallback fails)
i18next.t('unspecifiedKey'); // 'unspecifiedKey'
```

- Basic [Interpolation](https://www.i18next.com/translation-function/interpolation):

```json
{
  "myKey": "Hello {{name}}!"
}
```

```dart
i18next.t('key', arguments: {'name': 'World'}); // 'Hello World!'
```

- [Nesting](https://www.i18next.com/translation-function/nesting):

```json
{
  "nesting1": "1 $t(nesting2)",
  "nesting2": "2 $t(nesting3)",
  "nesting3": "3"
}
```

```dart
i18next.t('nesting1'); // "1 2 3"
```

- [Plurals](https://www.i18next.com/translation-function/plurals)

```json
{
  "key": "item",
  "key_plural": "items",
  "keyWithCount": "{{count}} item",
  "keyWithCount_plural": "{{count}} items"
}
```

```dart
i18next.t('key', count: 0); // 'items'
i18next.t('key', count: 1); // 'item'
i18next.t('key', count: 5); // 'items'
i18next.t('keyWithCount', count: 0); // '0 items'
i18next.t('keyWithCount', count: 1); // '1 item'
i18next.t('keyWithCount', count: 5); // '5 items'
```

There are also ways of dealing with locales with multiple plural: `zero, one, few, many, others` ([key identifier](https://jsfiddle.net/sm9wgLze)) (**Unsupported**)

- Contexts like gender, are marked via underscores

```json
{
    "genderMessage": "They", 
    "genderMessage_male": "Him",
    "genderMessage_female": "Her"
}
```

```dart
i18next.t('genderMessage'); // 'They'
i18next.t('genderMessage', context: 'male'); // 'Him'
i18next.t('genderMessage', context: 'female'); // 'Her'
```

And can be used with plurals

```json
{
  "friend": "A friend",
  "friend_plural": "{{count}} friends",
  "friend_male": "A boyfriend",
  "friend_female": "A girlfriend",
  "friend_male_plural": "{{count}} boyfriends",
  "friend_female_plural": "{{count}} girlfriends"
}
```

```dart
i18next.t('friend'); // 'A friend'
i18next.t('friend', count: 1); // 'A friend'
i18next.t('friend', count: 100); // '100 friends'

i18next.t('friend', context: 'male', count: 1); // 'A boyfriend'
i18next.t('friend', context: 'female', count: 1); // 'A girlfriend'
i18next.t('friend', context: 'male', count: 100); // '100 boyfriends'
i18next.t('friend', context: 'female', count: 100); // '100 girlfriends'
```

- [Formatting](https://www.i18next.com/translation-function/formatting)

```json
{
  "key1": "The current date is {{now, MM/DD/YYYY}}",
  "key2": "{{text, uppercase}} just uppercased"
}
```

```dart
i18next.t('key1', arguments: { 'now': DateTime.now() }); // 'The current date is 01/01/2020'
i18next.t('key2', arguments: { 'text': 'my text' }); // 'MY TEXT just uppercased'
```

There are other usages and possibilities as well, this is just an example of what is defined by this format.

- [Namespaces](https://www.i18next.com/principles/namespaces): A namespace can be thought of as logical groupings of different sets of translations. In a given namespace you could have a set of languages, each with their own set of keys. They can also be understood as separate files.
For example:

    - **common.json:** Things that are reused everywhere, eg. Button labels 'save', 'cancel'
    - **validation.json:** All validation texts
    - **glossary.json:** Words we want to be reused consistently inside texts

```json
// common.json
{
  "myKey": "This key is in common"
}

// feature.json
{
  "myKey": "This key is in my feature"
}
```

```dart
i18next.t('common:myKey'); // 'This key is in common'
i18next.t('feature:myKey'); // 'This key is in my feature'
```

- Context/plural fallback mechanism:

```json
{
  "friend": "A friend",
  "friend_female": "A girlfriend" 
}
```

```dart
i18next.t('friend'); // 'A friend'

i18next.t('friend', count: 1); // 'A friend'
// It fallbacks to `friend` since `friend_plural` is not present
i18next.t('friend', count: 2); // 'A friend' 

i18next.t('friend', context: 'female'); // 'A girlfriend'
// It fallbacks to `friend` since `friend_male` is not present
i18next.t('friend', context: 'male'); // 'A friend' 
```

There is a way to also set the default namespace or a order of namespaces so a key knows where to start looking for the translation.
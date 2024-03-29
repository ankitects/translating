# For Developers

When adding user-visible strings to Anki's codebase, extra work is required
to make the strings translatable.

Anki's codebase has recently migrated away from the old gettext system,
and now only uses Fluent's `col.tr` and `aqt.utils.tr`.

Please start by taking a look at the [core documentation](./core.md) to see
how strings are presented to translators. Note how translators can not see the
areas of the code where a string is used, so comments are often required to help
the translators understand what they are translating.

## Adding New Strings

As an example, imagine we want to add the string "You have x add-ons".

To add a new translatable string to the codebase, we first need to identify
whether it belongs in the `core` module (text likely to be used by all Anki clients),
or whether it is specific to the computer version (the `desktop` module).
Add-ons are only supported by the computer version, so we'll want to use
the `desktop` module in this example.

- The English `core` files are stored in `ftl/core`
- The English `desktop` files are stored in `ftl/qt`

We'll look for a file like `ftl/qt/addons.ftl`, and add one if no appropriate
one exists. Then we need to add the string to the file.

Each string needs a key that uniquely identifies it. It should start with
the same text as the filename, and then contain at least a few words separated
by hyphens. For example, we might add the following to the file:

```properties
addons-you-have-count = You have { $count } add-ons.
```

If `count` is 5, then the string will be "You have 5 add-ons.". This means
it will look strange if `count` is 1 - we'd see "You have 1 add-ons." instead
of "You have 1 add-on.". We'll need to add another string to cover the singular
case:

```properties
addons-you-have-count = You have { $count ->
    [one] 1 add-on
   *[other] {$count} add-ons
   }.
```

The text above tells Fluent to use the first string in the singular case,
and use the second string in all other cases.

While an improvement, this can make it a bit hard for translators when the
"you have" part needs to change depending on the number. So while more verbose,
it is better to list out the alternatives in full where possible:

```properties
addons-you-have-count = { $count ->
    [one] You have { $count } add-on.
   *[other] You have { $count } add-ons.
   }
```

This leaves the most control in the hands of the translators to be able
to translate the sentence with a natural structure.

Note how the variable was used in the singular case as well. While in English
$count will only be 1 in the singular case, in other languages, the
singular case can be used for other numbers, such as 21. While translators
could replace the `1` with `{ $count }` in their translation, they sometimes
do not realise they need to, so using the variable makes mistakes less likely.

Please note that the bulk of the strings in the .ftl files were imported
from the older gettext system, so many of them may not demonstrate best
practice. The older system also encouraged constructs like:

```python
msg = "%s %d %s" % (_("Studied"), card_count, _("today"))
```

Please avoid constructing strings in Python like this, as it makes it hard
for translators to translate, and not all languages use English spaces.
The above example would be better made into a single translatable string
that takes a count argument.

Finally, we should also add one or more comments for translators:

```properties
### Lines starting with three hashes are shown in the translation system
### for every string in the file.
### You can use it for a general summary of what a file is dealing with. Not
### all files will need this.

## Lines starting with two hashes are shown for every following string, until
## another set of two hashes is encountered, or the file ends.

# This is a comment that will only apply to the following string. Eg:
# Shown in the add-on management screen (Tools>Add-ons), in the title bar.
addons-you-have-count = { $count ->
    [one] You have { $count } add-on.
   *[other] You have { $count } add-ons.
   }
```

## Accessing the New String

Once you've added one or more strings to the .ftl files, run Anki in the source
tree as usual, which will compile the new strings, and make them accessible in
Python/Typescript/Rust.

### Python

To resolve a string, you can use the following code:

```python
from aqt.utils import tr

msg = tr.addons_you_have_count(count=3)
```

- Note how a snake_case() function has been automatically defined as part of
  the build process, with the correct arguments. These will be checked by
  the tests, to ensure you don't accidentally use a missing string, or
  omit an argument/pass the wrong one.
- The generated function has a docstring based on the original text in the ftl
  file, so if you're using an editor like PyCharm, you can hover the mouse over
  a function to see the text it will resolve to.
- Code in qt/ can use the `aqt.utils.tr()` function, but code in pylib should
  use `col.tr()` instead.

If you'd like to test out the strings in the Python repl, make sure to
call set_lang() first.

```python
>>> from anki.lang import set_lang
>>> from aqt.utils import tr
>>> set_lang("en", "")
>>> tr.addons_you_have_count(count=3)
'You have \u20683\u2069 add-ons.'
```

Note the unicode characters that were inserted. These ensure that when left-to-right
and right-to-left languages are mixed, the text flows correctly. For debugging,
you can strip the characters off:

```python
>>> from anki.lang import without_unicode_isolation as nosep
>>> nosep(addons_you_have_count(count=3))
'You have 3 add-ons.'
>>> nosep(addons_you_have_count(count=1))
'You have 1 add-on.'
```

### TypeScript

A global is available with automatically generated functions in camelCaps. Eg:

```typescript
import * as tr from "../lib/ftl";

console.log(tr.statisticsCardsPerDay({ count: 5 }));
```

The functions have docstrings that have the original text, so you can hover over
them to see what the text says.

To make use of the translations, they first need to be fetched from the backend.
You'll need to specify the modules you want included, eg:

```typescript
import { setupI18n, ModuleName } from "../lib/i18n";

await setupI18n({ modules: [ModuleName.STATISTICS] });
```

### Rust

The backend and collection structures have an I18n object in .tr, which can be
called to get a translation, eg, in a Collection method:

```rust
println!("{}", self.tr.database_check_missing_templates(5))
```

The docstring contains the original translation, and your editor can
show you the names of each argument.

The return value is a Cow - if you need a String, you can call `.to_string()` or
`.into()` on it.

### Legacy style

Anki versions before 2.1.44 used a single function and a separate constant instead.
Eg, instead of

```python
from aqt.utils import tr

msg = tr.addons_you_have_count(count=3)
```

they instead used:

```python
from aqt.utils import tr, TR

msg = tr(TR.ADDONS_YOU_HAVE_COUNT, count=3)
```

This old style will still work for now, but will be removed in the future.

## Repeated Content

Sometimes you will need multiple translations that have some text shared between
them. For example, the search code has a list of separate error message, such as:

```properties
search-invalid-added = Invalid search: 'added:' must be followed by a positive number of days.
search-invalid-edited = Invalid search: 'edited:' must be followed by a positive number of days.
```

Since there are a lot of separate messages with the same prefix, we can split it off
into a separate string:

```properties
search-invalid-search = Invalid search: { $reason }
search-invalid-added = 'added:' must be followed by a positive number reason.
search-invalid-edited = 'edited:' must be followed by a positive number of days.
```

The code can then compose the message from two separate entries. Note that we're
using a Fluent variable in the outer string, as some languages may want to place
the reason first, not include a space after the colon, and so on.

## Avoid HTML where possible

Translators may not have any development experience, and HTML can be difficult to read
and translate correctly. Prefer plain text where possible, and when HTML is required
or would make things much clearer, consider using markdown instead (see the search
translations for an example of how this is done).

## Avoid Strings That Will Change

Avoid doing things like listing out a series of options in a string, if there's any
chance that list will change in the future. When the string later gets updated
(and assuming people don't forget to update it), it will need to be given a new ID
so that translators become aware of it, and doing so will mean all the existing
translations get invalidated until a translator has a chance to update them, which
may take months and sometimes even years.

## Avoid Excess Strings

Please try to be conservative with the number of new strings you add, as translator time
is precious, and the more strings included in Anki, the more overwhelming it can be
for translators. If you need to display an error message for some obscure error that most
people will never hit, it probably doesn't need a translation.

## Add-ons

Add-ons can make use of existing strings in Anki, but if you wish to add new
strings, you'll need to add them to your add-on rather than Anki. How you approach
translations in your add-on(s) is up to you:

- If you don't care about translations, the simplest solution is to use plain strings.
- The next-easiest option is to place all strings in a dict or python module,
  and accept pull requests from users that add a new language. You can then
  select the appropriate dict by looking at what anki.lang.currentLang is set
  to.
- If you want proper plural support, you'll want to consider using gettext,
  though it is considerably more involved than the above solution. It requires
  separate build steps to extract strings from your source and build compiled
  versions of the strings. You can have translators email you a completed .po
  file, or send you a PR. There are online translation services like Launchpad
  and Crowdin that support gettext, but you would need to either manually upload
  and download files, or spend some time setting up scripts to do so.

Fluent is not currently recommended for add-ons. There is limited tooling
support for it, and you would need to bundle the Fluent Python libraries with
your add-on.

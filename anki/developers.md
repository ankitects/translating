# For Developers

When adding user-visible strings to Anki's codebase, extra work is required
to make the strings translatable.

Anki's codebase currently uses a mix of gettext's `_())` and `ngettext()`
functions, and Fluent's `col.backend.translate()` and `tr()`. We will be
gradually migrating away from gettext, so please use Fluent for any new
strings you add.

Please start by taking a look at the [core documentation](/anki/core.md) to see
how strings are presented to translators. Note how the strings do not
appear in the context of code, so comments are often required to help the
translators understand what they are translating.

## Adding New Strings

As an example, imagine we want to add the string "You have x add-ons".

To add a new translatable string to the codebase, we first need to identify
whether it belongs in the `core` module (text likely to be used by all Anki clients),
or whether it is specific to the computer version (the `desktop-ftl` module).
Add-ons are only supported by the computer version, so we'll want to use
the `desktop-ftl` module in this example.

- The `core` files are stored in `rslib/ftl/*.ftl`
- The `desktop-ftl` files are stored in `qt/ftl/*.ftl`

We'll look for a file like `qt/ftl/addons.ftl`, and add one if no appropriate
one exists. Then we need to add the string to the file.

Each string needs a key that uniquely identifies it. It should start with
the same text as the filename, and then contain at least a few words separated
by hyphens. For example, we might add the following to the file:

```
addons-you-have-count = You have {$count} add-ons.
```

The problem with this is that it will look strange when count is 1. We had
better use a different string in the single add-on case:

```
addons-you-have-count = You have { $count ->
    [one] 1 add-on
   *[other] {$count} add-ons
   }.
```

While an improvement, this can make it a bit hard for translators when the
"you have" part needs to change depending on the number. So while more verbose,
it is better to list out the alternatives in full where possible.

```
addons-you-have-count = { $count ->
    [one] You have 1 add-on.
   *[other] You have {$count} add-ons.
   }
```

This leaves the most control in the hands of the translators to be able
to translate the sentence with a natural structure.

You may find older translations in Anki's codebase that do something
like:

```
msg = "%s %d %s" % (_("Studied"), card_count, _("today"))
```

Please avoid constructing strings in Python like this, as it makes it hard
for translators to translate, and not all languages use English spaces.
The above example would be better made into a single translatable string
that takes a count argument.

Finally, we should also add one or more comments for translators:

```
### Lines starting with three hashes are shown in the translation system
### for every string in the file.
### You can use it for a general summary of what a file is dealing with. Not
### all files will need this.

## Lines starting with two hashes are shown for every following string, until
## another set of two hashes is encountered, or the file ends.

# This is a comment that will only apply to the following string. Eg:
# Shown in the add-on management screen (Tools>Add-ons), in the title bar.
addons-you-have-count = { $count ->
    [one] You have 1 add-on.
   *[other] You have {$count} add-ons.
   }
```

## Accessing the new String

Once you've added one or more strings to the .ftl files, run `make develop` in
the top level of the Anki repo, which will compile the new strings and make
them accessible in Python. To resolve a string, you can use the following
code:

```
from aqt.utils import tr
from anki.rsbackend import FString

msg = tr(FString.ADDONS_YOU_HAVE_COUNT, count=3)
```

- Note how a SCREAMING_CASE constant has been automatically defined as part of
the build process, allowing for code completion.
- You can provide as many arguments as you need, as either strings,
integers, or floats.
- Code in qt can use the global `tr()` function, but code in pylib should
use `col.backend.translate()` instead.

If you'd like to test out the strings in the Python repl, make sure to
call set_lang() first.

```
>>> from anki.lang import set_lang
>>> from anki.rsbackend import FString
>>> from aqt.utils import tr
>>> set_lang("en", "")
>>> tr(FString.ADDONS_YOU_HAVE_COUNT, count=3)
'You have \u20683\u2069 add-ons.'
```

Note the unicode characters that were inserted. These ensure that when left-to-right
and right-to-left languages are mixed, the text flows correctly. For debugging,
you can strip the characters off:

```
>>> from anki.lang import without_unicode_isolation as nosep
>>> nosep(tr(FString.ADDONS_YOU_HAVE_COUNT, count=3))
'You have 3 add-ons.'
>>> nosep(tr(FString.ADDONS_YOU_HAVE_COUNT, count=1))
'You have 1 add-on.'
```

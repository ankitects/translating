# Desktop Interface

The following text refers to the `desktop` module, which uses the legacy
_gettext_ system. The `desktop-ftl` module uses the new Fluent translation
system like the `core` module. For more information on working with
Fluent, please see the [core](/anki/core.md) section.

## Context

The `desktop` module is built with an older translation system called
gettext. While a few strings have had comments added to make things
clearer, unfortunately most of the text is not documented at the moment.

For each piece of text, the location it occurs in the source code will be
shown. For example, for the phrase "A big thanks to everyone who has provided
translations, bug reports, and suggestions", the context might appear as:

    #: ../dtop/aqt/about.py:146

The context lists which file(s) the text appears in, and the line
numbers in the file. In this case, we can guess that this file is
related to the About screen.

If you strip off the first "../dtop" section, you're left with
something like "aqt/about.py:146". You can then visit
<https://github.com/ankitects/anki/>, locate the same filename, and click on
it. The file will be displayed with line numbers on the left, and by
matching up the line numbers (they may differ by a few lines sometimes),
you may be able to get a better understanding of what the string refers
to.

## Formatting

When translating, some phrases contain special markers in the text,
which indicate extra text will be put in the marker's place. For
example, a string like `Cards: %d` or `Error: %%` means that the %d/%s
part will be replaced with some other value, such as `Cards: 10`, or
`Error: file not found`. When writing your translation, please leave the
special markers as they are, such as `カード: %d`.

Sometimes the text will contain named replacement markers, so that the
text can be reordered. For example, `%(a)d of %(b)d` - it would be
translated like `%(a)d von %(b)d`. If you need to reverse A and B in
your language (eg `%(b)dの%(a)d`), that's fine as long as the names
remain the same.

In some languages, words change depending on the associated number, such
as "1 dog" vs "2 dogs". You may be prompted to enter different words
for "one" and "many" for some phrases, depending on the plural forms
your language uses.

When translating numbered/plural items, please translate all cases, or
none. If you partially translate a numbered item, it will cause
problems.

Menu items have an & to indicate which character is the shortcut key,
such as `&File`. In languages that use roman text, you can place the &
over a different character such as `&Datei`; in other languages there
may be a different convention. Japanese for example includes the roman
character afterwards instead, like `ファイル (&F)`

## Other Languages

If you're not sure how something should be translated, you can click on the
Locales tab on the right. That will reveal how the text has been translated in
other languages, and may make it clearer what has to be changed vs what needs
to stay the same.

## Testing

Each time a new Anki beta is released, it will include any translation
changes that were made since the last time. Please give the betas a try,
so you can see how your translations appear in the app. There's a beta
testing section on the support site that you can follow for beta
updates.

When translating, please try to keep the translations about the same
length as the original English text. If it is not possible, and you find
that text is not appearing properly in a beta (such as two labels
overlapping), please report the issue on the support site.

When testing the betas, if you notice that some text is not
translatable, please report this on the support site so it can be fixed.

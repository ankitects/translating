# Mobile Interface

## Context

For each phrase that requires translation, you can see some context to
help understand where the phrase will be used. For example, for the
phrase "A big thanks to everyone who has provided translations, bug
reports, and suggestions", you can see the line:

    COMMENT: About Screen

Some parts that may need more explanation:

**Actions, Actions Manager**

These are the various functions you can access from the tools
screen, or by assigning them to a tap, swipe or the top bar. Actions
have a normal description, and a short description that is used when
they are placed in the top bar - for example, `Show Answer` becomes
`S.Ans` when shown in its short form. When translating, please try
to use a translation that is about the same length.

**Edit Screen**

The screen shown when using the Add and Edit actions.

**Standard Models**

Note types like "Basic (and reversed card)"

**Syncer**

Part of the sync feature.

**Utils**

Utilities used by various parts of AnkiMobile's code.

## Formatting

When translating, some phrases contain special markers in the text,
which indicate extra text will be put in the marker's place. For
example, a string like `Cards: %d` or `Error: %@` means that the %d/%@
part will be replaced with some other value, such as `Cards: 10`, or
`Error: file not found`. When writing your translation, please leave the
special markers as they are, such as `カード: %d`.

Sometimes the text will contain more than one replacement marker, like
`%1$d of %2$d` to represent something like `2 of 5`. You can reorder the
markers if it makes more sense in your language, eg `%2$dの%1$d` to
represent something like `5の2`.

In some languages, words change depending on the associated number, such
as "1 dog" vs "2 dogs". You may be prompted to enter different words
for "one" and "many" for some phrases.

When translating numbered/plural items, please translate all cases, or
none. If you partially translate a numbered item, it will cause
problems.

The ampersand symbol (`&`) needs to be written as `&nbsp;`.

## Other Languages

If you're not sure how something should be translated, you can click on the
Locales tab on the right. That will reveal how the text has been translated in
other languages, and may make it clearer what has to be changed vs what needs
to stay the same.

## Testing

Once a language has reached about 50% translation, it will be included in beta
builds. Please give the betas a try, so you can see how your translations
appear in the app. If you're not already a beta tester, please request an
invite on the support site.

When translating, please try to keep the translations about the same
length as the original English text. If it is not possible, and you find
that text is not appearing properly in a beta (such as two labels
overlapping), please report the issue on the support site.

When testing the betas, if you notice that some text is not
translatable, please report this on the support site so it can be fixed.

Thank you again for your help!

# glossarify-md

![Tests](https://github.com/about-code/glossarify-md/workflows/Tests/badge.svg)

*glossarify-md* is a command line tool to help Markdown writers with

- **Cross-Linking** (prime use case): autolink terms to some definition in a glossary
- **Indexes**: generate indexes from glossary terms and navigate to where they were mentioned
- **Lists**: generate arbitrary lists such as *List of Tables*, *List of Figures*, *List of Listings*, *List of Definitions*, *List of Formulas*, and so forth...

[vuepress](https://vuepress.vuejs.org) users might be interested in learning [how to use the tool with vuepress](https://github.com/about-code/glossarify-md/blob/master/doc/vuepress.md).


## Table of Contents

## Install

#### Option 1: Run in any directory:
```
npm i -g glossarify-md
```

```
glossarify-md --config ./glossarify-md.conf.json
```

#### Option 2: Install locally to your project:

```
npm i glossarify-md
```

```
npx glossarify-md --config ./glossarify-md.conf.json
```

or add as an npm-script to your `package.json`:

*package.json*
```json
scripts: {
  "glossarify": "glossarify-md --config ./glossarify-md.conf.json"
}
```

```
npm run glossarify
```


## Sample

We assume a sample project with the following structure:

```
${root}
   +- src/
   |    +- pages/
   |    |    |- page1.md
   |    |    `- page2.md
   |    |
   |    |- README.md
   |    |- requirements.md
   |    `- glossary.md
   |
   +- target/  (Generated output directory)
   `- glossarify-md.conf.json
```

Your original glossary is a file

*src/glossary.md*

```md
# Glossary

## Term

A glossary term has a short description. The full description contains both sentences.
```

Your original files may just use the term *Term* anywhere in text:

*src/pages/page1.md*

```md
# Demo

This is a text which uses a glossary Term to describe something.
```

Then run *glossarify-md* with a [glossarify-md.conf.json](#configuration).


## Results

Augmented versions of the source files have been written to the output directory. Headings in glossary files have been made linkable...

*./target/glossary.md*:

```md
# [Glossary](#glossary)

## [Term](#term)

A glossary term has a short description. The full description contains both sentences.
```

> ## [Glossary](#glossary)
>
> ### [Term](#term)
>
> A glossary term has a short description. The full description contains both sentences.

... and occurrences of the term in markdown files have been linked to the term definition in the glossary:

*./target/pages/page1.md*

```md
# [Demo](#demo)

This is a text which uses a glossary [Term ↴][1] to describe something.

[1]: ../glossary.md#term "A glossary term has a short description."
```

> ## [Demo](#demo)
>
> This is a text which uses a glossary [Term ↴][1] to describe something.
>
> [1]: #term "A glossary term has a short description."

Some syntactic positions of a term are **excluded** from being linked to the glossary. These are

- Headlines
- Blockquotes
- Preformatted blocks
- (Markdown) links

> **Important:** As of now, we can't prevent text between the opening and closing angle brackets of an HTML link to be linkified. In a markdown syntax tree something like `<a href="">term</a>` is represented as distinct HTML nodes with arbitrary complex markdown nodes in between. Therefore a term found between those HTML tags currently results in
>
> ```
> <a href="">[term][1]</a>
>
> [1] ./glossary.md#term
> ```

> **Note:** Terms found in blockquotes are not automatically linked to a glossary definition since a quoted source entity may not share the same definition of a term as the entity who quotes it. It may use a term in a completely different semantic context.

## Configuration

### Via File

*glossarify-md.conf.json* (minimal):

```json
{
  "$schema": "./node_modules/glossarify-md/conf.schema.json",
  "baseDir": "./src",
  "outDir": "../target",
  "outDirDropOld": true,
  "glossaries": [
     { "file": "./glossary.md" },
  ],
  "includeFiles": ["."],
  "excludeFiles": ["node_modules"],
  "linking": "relative"
}
```
**Note:** All paths (except of `$schema`) must be relative to `baseDir`. `baseDir` itself is relative to the location of the config file.


More options see [Additional Features](#additional-features) or [Options](#options) below.

### Via Command Line

Use `--shallow` or `--deep`

1. to provide a configuration solely via command line
2. to merge a configuration via command line with
   - the implicit default configuration in `./node_modules/glossarify-md/conf.json.schema`
   - a configuration file (to modify it for a particular execution)

*Example: Shallow-Merge with implicit default configuration*
~~~
glossarify-md
  --shallow "{ 'baseDir':'./src', 'outDir':'../target' }"
~~~

*Example: Override glossaries array in a config file via CLI*
~~~
glossarify-md
  --config ./glossarify-md.conf.json
  --shallow "{ 'glossaries': [{'file':'./replace.md'}] }"
~~~

*Example: Extend glossaries array in a config file via CLI*
~~~
glossarify-md
  --config ./glossarify-md.conf.json
  --deep "{'glossaries': [{'file':'./extend.md'}] }"
~~~


## Additional Features

### Aliases and Synonyms

Aliases can be defined in an HTML comment with the keyword `Aliases:` followed by a comma-separated list of alternative terms.

*glossary.md*

```md
# Glossary

## Cat
<!-- Aliases: Cats, Wildcat, House Cat -->
Cats are cute, ...dogs are loyal.
```

In the output files aliases will be linked to their related term:

*./target/pages/page2.md*

```md
# About Cats

[Cats](./glossary.md#cat) and kitten almost hidden spotting mouses in their houses. [The Author]
```

### Term Hints

*glossarify-md.conf.json*
```json
"glossaries": [
    { "file": "./glossary.md", "termHint": "↴"},
]
```

Glossaries can be associated with *term hints*. Term hints may be used to indicate that a link refers to a glossary term and in case of [multiple glossaries](#multiple-glossaries) to which one.

> **Since v2.0.0**:
Use `"${term}"` to control placement of a `termHint`. For example, `"☛ ${term}"` puts the symbol `☛` in front of the link.

### Multiple Glossaries
Sometimes you might whish to have multiple glossaries. For example as a Requirements Engineer you may not just have a glossary of business terms but also a requirements catalogue:

*glossarify-md.conf.json*

```json
"glossaries": [
    { "file": "./glossary.md",  "termHint": "↴" },
    { "file": "./requirements.md", "termHint": "★" }
]
```

*requirements.md*

```md
## REQ-1: The system MUST provide service X
<!-- Aliases: REQ-1 -->
...
## REQ-2: The system MAY provide service Y
<!-- Aliases: REQ-2 -->
...
```

By adding *requirements.md* to the list of glossaries every use of *REQ-1* or *REQ-2* gets linked to the requirements catalogue. Read on to find out how to generate an index in order to answer the question in which particular sections those requirements got mentioned.

### Index of terms and where they have been used

> **Since v3.0.0**

*glossarify-md.conf.json*

```json
"generateFiles": {
    "indexFile": {
       "file": "./book-index.md",
       "title": "Book Index"
    }
}
```

This option will generate a file `./book-index.md` with a list of glossary terms and links to book sections in which they have been mentioned. The `title` argument is optional. If missing the value given in the example will be the default.

> **Note**: If you plan on translating markdown to HTML, e.g. with [vuepress](https://vuepress.vuejs.org), be aware that a file `index.md` will translate to `index.html` which is typically reserved for the default HTML file served under a domain. You may want to choose another name.

### List of Figures

> **Since v3.3.0**
>
> - Alternatively read [Arbitrary Lists with Anchors](#arbitrary-lists-with-anchors) (since 3.5.0)

*glossarify-md.conf.json*
```json
"generateFiles": {
    "listOfFigures": { "file": "./figures.md", "title": "Figures" }
}
```

This option will generate an index file `./figures.md` with a list of figures grouped by sections of occurrence. You can control heading depth for grouping and e.g. generate a flat list without any grouping using

*glossarify-md.conf.json*
```json
"indexing": {
    "groupByHeadingDepth": 0
}
```

### List of Tables

> **Since v3.4.0**
>
> - Alternatively read [Arbitrary Lists with Anchors](#arbitrary-lists-with-anchors) (since 3.5.0)

Generate a file `./tables.md` with a list of tables grouped by sections of occurrence. See [`groupByHeadingDepth`](#list-of-figures) to find out how to control grouping.

*glossarify-md.conf.json*
```json
"generateFiles": {
    "listOfTables": { "file": "./tables.md", "title": "Tables" }
}
```

Markdown tables have no inherent notion of a table label. *glossarify-md* scans for two patterns of user-defined table labels and attempts to infer a table label otherwise.

#### Invisible table label

```md
<!-- table: Average Prices by Article Category -->
| Category | Description | Price Avg. |
| -------- | ----------- | ---------- |
| 1        | Video Game  | $35.66     |
| 2        | Film        | $10.13     |
| 3        | Book        | $23.45     |
```

#### Visible table label

A visible table label will be inferred from an italic phrase terminated by a colon two lines prior to the table. The phrase can be a distinct paragraph...

```md
[...] which we can see from the average price by article category.

*Average prices by article category:*

| Category | Description | Price Avg. |
| -------- | ----------- | ---------- |
| 1        | Video Game  | $35.66     |
| 2        | Film        | $10.13     |
| 3        | Book        | $23.45     |
```

... or a phrase inlined into text:

```md
[...] which we can see from the *Average prices by article category:*

| Category | Description | Price Avg. |
| -------- | ----------- | ---------- |
| 1        | Video Game  | $35.66     |
| 2        | Film        | $10.13     |
| 3        | Book        | $23.45     |
```

#### Labels for generated list items will be inferred in this order (first-match):

1. **HTML comment** in the line above the table
1. **emphasized text** at the end of the preceding paragraph
1. **column headers** separated by comma, e.g. *Category, Description, Price Avg.*
1. **preceding section heading** (multiple tables without column headers in the same section may be labeled ambiguously)
1. **filename** in which the table has been found.

### Arbitrary Lists with Anchors

> **Since v3.5.0**

You can generate arbitrary *List of ...* lists by using HTML anchors (`<a>`) and anchor *classes*.

> Anchors can be directly navigated to while [`listOfTables`](#list-of-tables) or [`listOfFigures`](#list-of-figures) only generate links to headings preceding a table or figure.

For example, to generate a *List of Tables* add

*glossarify-md.conf.json*
```json
"generateFiles": {
    "listOf": [
       { "class": "table", "file": "./tables.md", "title": "List of Tables" }
    ]
}
```

and mark the position of a table with an HTML anchor:

```md
<a id="avg" class="table">Average prices by article category:</a>

| Category | Description | Price Avg. |
| -------- | ----------- | ---------- |
| 1        | Video Game  | $35.66     |
| 2        | Film        | $10.13     |
| 3        | Book        | $23.45     |
```

Type less by passing the anchor `class` as an `id`-prefix:

```md
<a id="table-avg">Average prices by article category</a>
```

Hide anchors with a `title` attribute:

```md
<a id="table-avg" title="Average prices by article category"></a>
```

#### Labels for generated list items will be inferred in this order (first-match):

1. `title` attribute value (`<a id="..." "title"="label"></a>`)
1. Inner text of anchor tag (`<a id="...">label</a>`)
1. `id` attribute value, yet without list prefix (`<a id="prefix-label"></a>`)
1. Preceding section heading if `id` is just the list prefix (`<a id="prefix"></a>`)
1. Filename if `id` is just the list prefix and there is no preceding section heading, either.


### Sorting your glossaries

> **Since v3.6.0**

Add `sort` direction `"asc"` or `"desc"` to glossaries for which you want *glossarify-md* to sort them for you:

*glossarify-md.conf.json*
```json
"glossaries": [
    { "file":"./glossary.md", "termHint": "", "sort": "asc" }
]
```

Internally sorting uses `Intl.Collator` and falls back to `String.localeCompare` if the `Intl` API is missing. You can tweak collation by adding `i18n` options:

*glossarify-md.conf.json*
```json
"i18n": {
   "locale": "de",
   "ignorePunctuation": true
},
"glossaries": [
    ...
]
```

The i18n-object is passed *as is* to the collator function. Thus you can use additional options documented on [Mozilla Developer Portal](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Collator):

### Node Support Matrix

> **Since v3.0.0**

The term *support* refers to *runs on the given platform*. Compatibility is maintained with *best effort* and under the terms and conditions of [LICENSE](#license).

| Node-Version |                                                                    compatibility & support status                                                                     |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Current      | Tested. Should Node introduce breaking changes which affect *glossarify-md*, then we may choose to step back from supporting *Current* until it becomes the next LTS. |
| 12.x LTS     | Tested + Supported                                                                                                                                                    |
| 10.x LTS     | Tested + Supported                                                                                                                                                    |

## Options

#### `help`

Show all options and default values.

#### `baseUrl`

- **Range:** `string`

URL to prepend to links. Only effective with `linking: "absolute"`.
In most situations, e.g. when hosting markdown files in a repository or
processing markdown files with an MD to HTML converter omitting a pre-defined
`baseUrl` and using `linking: "relative"` is likely to work better.

#### `baseDir``

- **Range:** `string`

Path to directory where to search for the glossary and markdown files. All paths in a config file except for `$schema` will be relative to *baseDir*. *baseDir* itself and `$schema` are relative to the location of the config file.

#### `excludeFiles

- **Range:** `string[]`

Paths or Glob-Patterns of files to exclude. Use `keepRawFiles` if you just
want to ignore certain markdown files from being modified.

#### `experimentalFootnotes`

- **Range:** `boolean`

Enable support for markdown footnote syntax as defined at <https://pandoc.org/MANUAL.html#footnotes>. Footnotes will be considered an *experimental* feature until they become official part of the CommonMark Specification at <https://spec.commonmark.org>.

#### `generateFiles.indexFile`

- **Range:** `{file: string, [title: string]}`
- **Since:** v3.0.0

If available, generates an index of glossary terms with links to files in which they have been mentioned. See section [Additional Features](https://github.com/about-code/glossarify-md#index-of-terms-and-where-they-have-been-used) for a configuration example.

> **Important:** The `string` value range is *deprecated*. It will be kept throughout all versions of v3.x but is eventually going to be removed. Please use the object value range as shown in the example.

#### `generateFiles.listOfFigures`

- **Range:** `{file: string, [title: string]}`
- **Since:** v3.3.0

If available, generates a list of figures with links to sections where the figures have been mentioned. See section [Additional Features](https://github.com/about-code/glossarify-md#list-of-figures) for a configuration example.

#### `generateFiles.listOfTables`

- **Range:** `{file: string, [title: string]}`
- **Since:** v3.4.0

If available, generates a list of tables. See section [Additional Features](https://github.com/about-code/glossarify-md#list-of-tables) for an example.

#### `generateFiles.listOf`

- **Range:** `Array<{class: string, file: string, [title: string]}>`
- **Since:** v3.5.0

If available, generates a list from HTML anchors exposing the configured `class` attribute. See section [Additional Features](https://github.com/about-code/glossarify-md#arbitrary-lists-with-anchors) for an example.

#### `glossaries`

- **Range:** `Array<{file: string, [termHint: string], [sort: string]}>`

A list of glossary configuations, each with a path to the glossary file. Every
glossary may have an optional *termHint*. A *termHint* is a symbol character
being appended to term occurrences in order to indicate which glossary or
category a term belongs to. A term hint may be any UTF-8 character or character
sequence. If you would like to have the glossary sorted provide a *sort* direction
`"asc"` or `"desc"`.

#### `ignoreCase`

- **Range:** `boolean`

When true any occurrence of a term will be linked no matter how it was spelled.

#### `includeFiles`

- **Range:** `string[]`

Paths or Glob-Patterns for files to include.

#### `indexing.groupByHeadingDepth`

- **Range:** `number` in [1-6]
- **Since:** v3.4.0

This option affects outputs generated with `generateFiles`. By default when
indexing terms and markdown elements they are being grouped by the heading of
the section they've been found in. In larger books with a lot of sections and
subsections this can lead to *Index* files or *Tables of X* to be generated with
lots of low-level sections and much detail. Yet sometimes it may be preferable
to only list the book chapter or high-level sections which some element has been
found in. This option allows to set the depth by which indexed elements shall be
grouped where `1` refers to chapters (`#` headings). Note that grouping by
high-level sections doesn't mean that only links to the high-level sections are
generated. Where it makes sense links to low-level sections of occurrence are
just being shortened.

#### `i18n`

- **Range**:` { locale: string, [localeMatcher: string],
    [caseFirst: string], [ignorePunctuation: boolean],
    [numeric: boolean], [sensitivity: string], [usage: string] }`

Locale options to control [sorting](#sorting-your-glossaries). See [`Intl.Collator`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Collator/Collator).

#### `keepRawFiles`

- **Range:** `string[]`

Paths or Glob-Patterns for (markdown) files to copy to `outDir` but ignore in
glossarification and linking. Non-markdown files will always be kept as is so no
need to add those.

#### `linking`

- **Range:** `"relative" | "absolute"`

Whether to create absolute or relative link-urls to the glossary.
The use of `"absolute"` may require a `baseUrl`.

> **Important:** Using `"absolute"` without a `"baseUrl"` will produce an absolute file system path which you might not want to publish.

#### `outDir`

- **Range:** `string`

The directory where to write output files to.

> **Important:** using `.` or `./` is going to overwrite your input files. Only do this on a copy of your input
> files or if you are able to roll back any changes or if you know the outcome satisfies your needs.

The recommendation is to write outputs to a separate directory such as `../out` or `../glossarified`. or `../target`.

#### `outDirDropOld`

- **Range:** `boolean`

If `true` remove old `outDir` before writing a new one, otherwise overwrite files. Drops orphan files that have intentionally been removed from `baseDir`.

#### `reportNotMentioned`

- **Range:** `boolean`

Report on terms which exist in a glossary but have neither been mentioned directly nor with any of its aliases.

#### Special Thanks go to

- [John Gruber](https://daringfireball.net/projects/markdown/), author of the Markdown syntax
- [Titus Wormer](https://github.com/wooorm), author of [unifiedjs](https://unifiedjs.com/), [remarkjs](https://github.com/remarkjs) and many more
- and all the other great people publishing modules of value to the tool - directly or transitively.

## License

GIVEN command line

-   `--config "./glossarify-md.conf.json"`
-   `--shallow "{ 'baseUrl':'cli://localhost', 'linking':'absolute', 'glossaries':[{'file': './glossary2.md'}] }"`

AND a config file:

_./glossarify-md.conf.json_

```json
{
    "$schema": "../../../../conf.schema.json",
    "baseUrl": "file://localhost",
    "baseDir": ".",
    "outDir": "../../../output-actual/config-cli/arg-shallow",
    "includeFiles": ["."],
    "glossaries": [
        { "file": "./glossary.md"}
    ],
    "linking": "relative"
}
```

WITH

-   options `baseDir` and `outDir`  not present on the command-line

AND

-   options `baseUrl` and `linking` and `glossaries` present on the command-line _AND_ in the config-file

THEN

-   options `baseDir` and `outDir` MUST be read **from the config file**

AND

-   options `baseUrl` and `linking` and `glossaries` MUST be read **from the command-line**

AND

-   as a result the term '[Term][1]' MUST be linked absolutely to `cli://localhost/glossary2.md#term`.

[1]: cli://localhost/glossary2.md#term

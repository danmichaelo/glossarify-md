# Using *glossarify-md* with [vuepress](https://vuepress.vuejs.org)

Below we assume a *sample* project structure like this:

```
${root}
   +- docs/
   |   +- .vuepress/
   |   |   |- public/
   |   |   '- config.js
   |   |
   |   +- images/
   |   |   '...
   |   +- section-1/
   |   |   |- page-1.md
   |   |   |- page-2.md
   |   |   '- page-3.md
   |   |
   |   '- glossary.md
   |
   +- glossarified/                  (Generated)
   +- node_modules/
   |- glossarify-md.conf.json
   |- package.json
   '- .gitignore
```

## Install into your project

```
npm i --save glossarify-md
```

## Configure *glossarify-md*

*glossarify-md.conf.json*
```json
{
    "$schema": "./node_modules/glossarify-md/conf.schema.json",
    "baseDir": "./docs",
    "outDir": "../glossarified",
    "outDirDropOld": true,
    "includeFiles": ["."],
    "excludeFiles": ["**/*.exclude.md"],
    "keepRawFiles": ["**/*.raw.md"],
    "glossaries": [
        { "file": "./glossary.md", "termHint": "↴"},
    ],
    "linking": "relative",
    "ignoreCase": false
}
```

> **☛ Note:** All relative paths inside the config file are being interpreted
> relativ to `baseDir`.

> **☛ Note:** Consider adding the target of `outDir` to *.gitignore*.

> **☛ Tip:** You are free to choose a different structure, e.g. with `.vuepress/` or `images/` being siblings *next to* `baseDir` (docs) rather than being children of it. This reduces the number of files being copied from `baseDir` to `outDir` (glossarified) and could improve build times if there are many static assets. Relative paths may just become a bit longer.

> **☛ Since v2.1.0** you can enable `experimentalFootnotes` if you use *vuepress* with [markdown-it-footnote](https://www.npmjs.com/package/markdown-it-footnote) plug-in.

## Configure *vuepress*

*vuepress* and *glossarify-md* use "slug" algorithms to create friendly URL fragments (#...) for section links. When *vuepress* translates glossarified markdown to HTML it slugifies anchor names a second time by its own algorithm. Unfortunately it does so inconsistently for links with unicode characters. E.g it translates the heading anchor `#äquator` for a glossary term *Äquator* into an HTML anchor `#aquator` with a different first letter ([vuejs/vuepress#1815](https://github.com/vuejs/vuepress/issues/1815)). Though, any links to it still point to `#äquator`. As a consequence there'll be term links navigating to the glossary, though, not to the anchor position of the term's definition ([about-code/glossarify-md#27](https://github.com/about-code/glossarify-md/issues/27)).

Fortunately *vuepress* allows for replacing its default slug algorithm, so we can fix this. We can configure it to use the same slugger used by *glossarify-md* which doesn't modify unicode characters and may prevent other issues arising from using different algorithms:

*.vuepress/config.js*
```js
const glossarify = require("glossarify-md");
module.exports = {
    /* ... */
    markdown: {
      slugify: glossarify.getSlugger()
    }
};
```
> **⚠ Important:** Changing the slug algorithm should be considered a BREAKING CHANGE to existing, published docs. URLs or URL fragments potentially change. Bookmarks of your readers may no longer work as expected. If this is important to you or your readers verify the outcome carefully before you publish your changes.

> **⚠ Important:**  If you have previously adapted to the peculiarities of *vuepress* and referred to auto-generated link headings via refs like `[Äquator](#aquator)` - so translated to ASCII by yourself - then those links *may* require an update afterwards.

> **☛ Note:** If you decide to drop *glossarify-md* later you might not want to have slugs change again. *glossarify-md* uses [github-slugger](https://npmjs.com/package/github-slugger) internally. You can use it directly like so:
>
>  *.vuepress/config.js*
>  ```js
>   const GitHubSlugger = require("github-slugger");
>   module.exports = {
>       /* ... */
>       markdown: {
>         slugify: (value) => {
>           const slugifier = new GitHubSlugger();
>           return slugifier.slug(value);
>         }
>       }
>   };
>  ```


## Configure Build Scripts

*package.json*
```json

"scripts": {
  "glossarify": "glossarify-md --config ./glossarify-md.conf.json",
  "start": "vuepress dev docs",
  "glossarified": "npm run glossarify && vuepress dev glossarified",
  "build": "npm run glossarify && vuepress build glossarified",
}
```
- `npm start` builds and serves files from `docs/` with *live-reload*. This is
what you probably want while writing. Since glossarified sources are written to
a separate `glossarified/` directory you won't see glossary terms linked in this build mode.

- `npm run glossarified` builds and serves the glossarified version from `glossarified/` output directory. No live-reload if `docs/` changes.

- `npm run build` just builds the glossarified version.

More information see [README.md](../README.md).

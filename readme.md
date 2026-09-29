# @stackline/rehype-retext

> rehype plugin to transform to retext.

[![npm version](https://img.shields.io/npm/v/@stackline/rehype-retext.svg?style=flat-square)](https://www.npmjs.com/package/@stackline/rehype-retext)
[![license](https://img.shields.io/npm/l/@stackline/rehype-retext.svg?style=flat-square)](https://github.com/alexandroit/stackline-rehype-retext)
[![GitHub repository](https://img.shields.io/badge/GitHub-repository-181717?style=flat-square&logo=github)](https://github.com/alexandroit/stackline-rehype-retext)
[![Docs](https://img.shields.io/badge/docs-alexandro.net-0f766e?style=flat-square)](https://alexandro.net/docs/vanilla/rehype-retext/)
[![Reddit community](https://img.shields.io/badge/community-r%2FStackline-ff4500?style=flat-square&logo=reddit&logoColor=white)](https://www.reddit.com/r/Stackline/)

**[Documentation](https://alexandro.net/docs/vanilla/rehype-retext/)** | **[npm](https://www.npmjs.com/package/@stackline/rehype-retext)** | **[Issues](https://github.com/alexandroit/stackline-rehype-retext/issues)** | **[Repository](https://github.com/alexandroit/stackline-rehype-retext)**

**Current package version:** `1.0.2`

---

## Why this package?

`@stackline/rehype-retext` is the Stackline-maintained distribution of `rehype-retext@3.0.2`. It is an independent continuation of [rehype-retext](https://github.com/rehypejs/rehype-retext); original authors and licenses remain credited below.

## Compatibility

| Item | Value |
| :--- | :--- |
| Package | `@stackline/rehype-retext@1.0.2` |
| API target | `rehype-retext@3.0.2` |
| Supported Node.js | `See supported framework requirements` |
| License | `MIT` |
| Module type | `module` |
| Main entry | `index.js` |
| Types | `index.d.ts` |
| Runtime dependencies | `unified, @types/hast, @types/unist, hast-util-to-nlcst` |

## Installation

```bash
npm install @stackline/rehype-retext
```

Preserve existing imports and plugin resolution with an npm alias:

```bash
npm install rehype-retext@npm:@stackline/rehype-retext
```

## Usage and API reference

### rehype-retext


**[rehype][]** plugin to support **[retext][]**.

## Contents

*   [What is this?](#what-is-this)
*   [When should I use this?](#when-should-i-use-this)
*   [Install](#install)
*   [Use](#use)
*   [API](#api)
    *   [`unified().use(rehypeRetext, destination)`](#unifieduserehyperetext-destination)
*   [Types](#types)
*   [Compatibility](#compatibility)
*   [Security](#security)
*   [Related](#related)
*   [Contribute](#contribute)
*   [License](#license)

## What is this?

This package is a [unified][] ([rehype][]) plugin to support [retext][].

**unified** is a project that transforms content with abstract syntax trees
(ASTs).
**rehype** adds support for HTML to unified.
**retext** adds support for natural language to unified.
**hast** is the HTML AST that rehype uses.
**nlcst** is the natural language AST that retext uses.
This is a rehype plugin that transforms hast into nlcst to support retext.

## When should I use this?

This project is useful if you want to check natural language in HTML.
The retext ecosystem has many useful plugins to check prose, such as
[`retext-indefinite-article`][retext-indefinite-article] which checks that `a`
and `an` are used correctly, or [`retext-readability`][retext-readability] which
checks that sentences are not too complex.
This plugins lets you use them on HTML documents.

This plugin is not able to apply changes by retext plugins (such
as done by `retext-smartypants`) to the HTML content.

This plugin is built on [`hast-util-to-nlcst`][hast-util-to-nlcst], which does
the work on syntax trees.
rehype focusses on making it easier to transform content by abstracting such
internals away.

## Install

This package is [ESM only](https://gist.github.com/sindresorhus/a39789f98801d908bbc7ff3ecc99d99c).
In Node.js (version 12.20+, 14.14+, or 16.0+), install with [npm][]:

```sh
npm install @stackline/rehype-retext
```

In Deno with [Skypack][]:

```js
import rehypeRetext from 'https://cdn.skypack.dev/rehype-retext@3?dts'
```

In browsers with [Skypack][]:

```html
<script type="module">
  import rehypeRetext from 'https://cdn.skypack.dev/rehype-retext@3?min'
</script>
```

## Use

Say we have the following file `example.html`:

```html
<!doctype html>
<meta charset=utf8>
<title>Hello!</title>
<article>
  A implicit sentence.
  <h1>This and and that.</h1>
</article>
```

And our module `example.js` looks as follows:

```js
import {read} from 'to-vfile'
import {reporter} from 'vfile-reporter'
import {unified} from 'unified'
import rehypeParse from 'rehype-parse'
import rehypePresetMinify from 'rehype-preset-minify'
import rehypeStringify from 'rehype-stringify'
import rehypeRetext from '@stackline/rehype-retext'
import retextEnglish from 'retext-english'
import retextIndefiniteArticle from 'retext-indefinite-article'
import retextRepeatedWords from 'retext-repeated-words'

main()

async function main() {
  const file = await unified()
    .use(rehypeParse)
    .use(
      rehypeRetext,
      unified()
        .use(retextEnglish)
        .use(retextIndefiniteArticle)
        .use(retextRepeatedWords)
    )
    .use(rehypePresetMinify)
    .use(rehypeStringify)
    .process(await read('example.html'))

  console.error(reporter(file))
  console.log(String(file))
}
```

Now running `node example.js` yields:

```html
example.html
    5:3-5:4  warning  Use `An` before `implicit`, not `A`  retext-indefinite-article  retext-indefinite-article
  6:12-6:19  warning  Expected `and` once, not twice       and                        retext-repeated-words

⚠ 2 warnings
```

```html
<!doctypehtml><meta charset=utf8><title>Hello!</title><article>A implicit sentence.<h1>This and and that.</h1></article>
```

## API

This package exports no identifiers.
The default export is `rehypeRetext`.

### `unified().use(rehypeRetext, destination)`

**[rehype][]** plugin to support **[retext][]**.
There are no options but `destination` is required.

###### `destination`

`destination` is either a parser or a processor.

*   If a destination [processor][] is given, runs the plugins attached to it
    with the new nlcst tree ([*bridge mode*][bridge]).
    This given processor must have a parser attached (this can be done by using
    the plugin `retext-english` or similar) and should use other retext plugins
*   If a parser is given, runs further plugins attached to the same processor
    with the new tree (*mutate mode*).
    Such parsers are exported by packages like `retext-english` as `Parser`.
    You should use other retext plugins after `rehype-retext`.

As HTML defines paragraphs, that definition is used for
[**Paragraph**][paragraph]s: `<p>` and `<h1-6>` are explicitly
supported, and implicit paragraphs in flow content are also supported.

## Types

This package is fully typed with [TypeScript][].
It exports the `Parser` and `Processor` types, which specify the interfaces of
the accepted destination.

## Compatibility

Projects maintained by the unified collective are compatible with all maintained
versions of Node.js.
As of now, that is Node.js 12.20+, 14.14+, and 16.0+.
Our projects sometimes work with older versions, but this is not guaranteed.

This plugin works with `unified` version 6+, `rehype` version 4+, and `retext`
version 7+.

## Security

`rehype-retext` does not change the syntax tree so there are no openings for
[cross-site scripting (XSS)][xss] attacks.

## Related

*   [`rehype-remark`](https://github.com/rehypejs/rehype-remark)
    — rehype plugin to turn HTML into markdown
*   [`remark-retext`](https://github.com/remarkjs/remark-retext)
    — remark plugin to support retext
*   [`remark-rehype`](https://github.com/remarkjs/remark-rehype)
    — remark plugin to turn markdown into HTML

## Contribute

See [`contributing.md`][contributing] in [`rehypejs/.github`][health] for ways
to get started.
See [`support.md`][support] for ways to get help.

This project has a [code of conduct][coc].
By interacting with this repository, organization, or community you agree to
abide by its terms.

## License

[MIT][license] © [Titus Wormer][author]



[build-badge]: https://github.com/rehypejs/rehype-retext/workflows/main/badge.svg

[build]: https://github.com/rehypejs/rehype-retext/actions

[coverage-badge]: https://img.shields.io/codecov/c/github/rehypejs/rehype-retext.svg

[coverage]: https://codecov.io/github/rehypejs/rehype-retext

[downloads-badge]: https://img.shields.io/npm/dm/rehype-retext.svg

[downloads]: https://www.npmjs.com/package/rehype-retext

[size-badge]: https://img.shields.io/bundlephobia/minzip/rehype-retext.svg

[size]: https://bundlephobia.com/result?p=rehype-retext

[sponsors-badge]: https://opencollective.com/unified/sponsors/badge.svg

[backers-badge]: https://opencollective.com/unified/backers/badge.svg

[collective]: https://opencollective.com/unified

[chat-badge]: https://img.shields.io/badge/chat-discussions-success.svg

[chat]: https://github.com/rehypejs/rehype/discussions

[npm]: https://docs.npmjs.com/cli/install

[skypack]: https://www.skypack.dev

[health]: https://github.com/rehypejs/.github

[contributing]: https://github.com/rehypejs/.github/blob/HEAD/contributing.md

[support]: https://github.com/rehypejs/.github/blob/HEAD/support.md

[coc]: https://github.com/rehypejs/.github/blob/HEAD/code-of-conduct.md

[license]: license

[author]: https://wooorm.com

[typescript]: https://www.typescriptlang.org

[unified]: https://github.com/unifiedjs/unified

[rehype]: https://github.com/rehypejs/rehype

[retext]: https://github.com/retextjs/retext

[processor]: https://github.com/unifiedjs/unified#processor

[bridge]: https://github.com/unifiedjs/unified#processing-between-syntaxes

[paragraph]: https://github.com/syntax-tree/nlcst#paragraph

[xss]: https://en.wikipedia.org/wiki/Cross-site_scripting

[retext-indefinite-article]: https://github.com/retextjs/retext-indefinite-article

[retext-readability]: https://github.com/retextjs/retext-readability

[hast-util-to-nlcst]: https://github.com/syntax-tree/hast-util-to-nlcst

## Credits and original authors

- Original project: [rehype-retext](https://github.com/rehypejs/rehype-retext).
- Titus Wormer.
- Copyright (c) 2016 Titus Wormer <tituswormer@gmail.com>.
- Stackline maintenance: [Alexandro Paixao Marques](https://www.linkedin.com/in/aleinfo/) and [Stackline contributors](https://github.com/alexandroit).

Original copyright, license notices and contributor acknowledgements remain part of this distribution. Stackline maintenance does not replace authorship of the original work.

## Community and Links

- [Stackline website](https://alexandro.net/)
- [GitHub projects](https://github.com/alexandroit)
- [npm packages](https://www.npmjs.com/~alex360qc)
- [Reddit community — r/Stackline](https://www.reddit.com/r/Stackline/)
- [Maintainer LinkedIn](https://www.linkedin.com/in/aleinfo/)

Use this repository's issue tracker for reproducible bugs and feature requests. Join r/Stackline for examples, usage questions and release discussions.

# glsl-parser

[
![npm version](https://badge.fury.io/js/glsl-parser.svg)
](https://badge.fury.io/js/glsl-parser)
[
![license](https://img.shields.io/npm/l/glsl-parser.svg)
](https://github.com/stackgl/glsl-parser/blob/master/LICENSE)

> 日本語のREADMEはこちらです: [README.ja.md](README.ja.md)

A GLSL parser that converts a stream of tokens from [glsl-tokenizer](https://github.com/stackgl/glsl-tokenizer) into an Abstract Syntax Tree (AST).

It can be used as a stream for parsing GLSL source on the fly, or synchronously for parsing a complete list of tokens.

## Install

```sh
npm install glsl-parser
```

## Usage

### Streaming API

The streaming API is ideal for parsing large files or for use in toolchains with other streaming modules.

**`const ParseStream = require('glsl-parser/stream')`**

Creates a GLSL parser stream. It's a transform stream that consumes tokens and emits AST nodes as they are fully parsed.

```javascript
const fs = require('fs')
const TokenStream = require('glsl-tokenizer/stream')
const ParseStream = require('glsl-parser/stream')

const parser = ParseStream()

fs.createReadStream('test.glsl')
  .pipe(TokenStream())
  .pipe(parser)
  .on('data', function(node) {
    console.log('Parsed node of type:', node.type)
  })
  .on('end', function() {
    // The full AST is available on the stream's `program` property
    console.log(JSON.stringify(parser.program, null, 2))
  })
```

**`parser.program`**

While the stream is running, you can access the `program` property on the parser instance to get the full AST of the code parsed so far.

### Synchronous API

The synchronous API is simpler if you already have the full GLSL source in memory.

**`const ParseTokens = require('glsl-parser/direct')`**

A function that synchronously parses an array of tokens from `glsl-tokenizer` and returns the complete AST.

```javascript
const fs = require('fs')
const TokenString = require('glsl-tokenizer/string')
const ParseTokens = require('glsl-parser/direct')

const src = fs.readFileSync('test.glsl', 'utf8')
const tokens = TokenString(src)
const ast = ParseTokens(tokens)

console.log(JSON.stringify(ast, null, 2))
```

## AST Nodes

The parser produces a tree of nodes. The `type` property of each node can be one of the following:

*   `stmtlist`
*   `stmt`
*   `struct`
*   `function`
*   `functionargs`
*   `decl`
*   `decllist`
*   `for
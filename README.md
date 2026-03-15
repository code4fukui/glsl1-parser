# glsl-parser

> 日本語のREADMEはこちらです: [README.ja.md](README.ja.md)

A GLSL parser that takes tokens from [glsl-tokenizer](http://github.com/stackgl/glsl-tokenizer) and turns them into an AST.

May either be used synchronously or as a stream.

## API

### `stream = require('glsl-parser/stream')`

Creates a GLSL parser stream, which emits nodes as they're parsed.

```javascript
var TokenStream = require('glsl-tokenizer/stream')
var ParseStream = require('glsl-parser/stream')
var fs = require('fs')

fs.createReadStream('test.glsl')
  .pipe(TokenStream())
  .pipe(ParseStream())
  .on('data', function(x) {
    console.log('ast of', x.type)
  })
```

### `ast = stream.program`

The full program's AST, which will be updated with each incoming token.

### `ast = require('glsl-parser/direct')(tokens)`

Synchronously parses an array of tokens from `glsl-tokenizer`.

```javascript
var TokenString = require('glsl-tokenizer/string')
var ParseTokens = require('glsl-parser/direct')
var fs = require('fs')

var src = fs.readFileSync('test.glsl', 'utf8')
var tokens = TokenString(src)
var ast = ParseTokens(tokens)

console.log(ast)
```

## Nodes

The parser supports the following node types:

* `stmtlist`
* `stmt`
* `struct`
* `function`
* `functionargs`
* `decl`
* `decllist`
* `forloop`
* `whileloop`
* `if`
* `expr`
* `precision`
* `comment`
* `preprocessor`
* `keyword`
* `ident`
* `return`
* `continue`
* `break`
* `discard`
* `do-while`
* `binary`
* `ternary`
* `unary`

## Known Issues

* The parser has some limitations in handling "unhygienic" `#if` / `#endif` macros. These should be avoided, or the macros should be placed at the statement level to surround parseable code.
* The parser may face some challenges in distinguishing between expressions and declarations when the statement starts with a previously declared `struct`.

## License

MIT, see [LICENSE.md](LICENSE.md) for more details.
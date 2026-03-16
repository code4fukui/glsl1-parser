# glsl1-parser

GLSL言語のトークンを抽象構文木(AST)に変換するパーサーです。ストリーミングまたは同期的に解析が可能です。

## 機能
- GLSL言語のトークンを抽象構文木(AST)に変換
- ストリーミングおよび同期的な解析に対応

## 必要環境
- Node.js

## 使い方
### ストリーミング
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

### 同期的
```javascript
var TokenString = require('glsl-tokenizer/string')
var ParseTokens = require('glsl-parser/direct')
var fs = require('fs')

var src = fs.readFileSync('test.glsl', 'utf8')
var tokens = TokenString(src)
var ast = ParseTokens(tokens)

console.log(ast)
```

## ライセンス
MIT
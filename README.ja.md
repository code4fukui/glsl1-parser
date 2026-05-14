# glsl-parser

[
![npm version](https://badge.fury.io/js/glsl-parser.svg)
](https://badge.fury.io/js/glsl-parser)
[
![license](https://img.shields.io/npm/l/glsl-parser.svg)
](https://github.com/stackgl/glsl-parser/blob/master/LICENSE)

[glsl-tokenizer](https://github.com/stackgl/glsl-tokenizer) からのトークンストリームを抽象構文木 (AST) に変換するGLSLパーサーです。

GLSLソースをオンザフライでパースするためのストリームとして、またはトークンの完全なリストを同期的にパースするために使用できます。

## インストール

```sh
npm install glsl-parser
```

## 使い方

### ストリーミングAPI

ストリーミングAPIは、大きなファイルのパースや、他のストリーミングモジュールと組み合わせたツールチェーンでの使用に最適です。

**`const ParseStream = require('glsl-parser/stream')`**

GLSLパーサーストリームを作成します。これはトークンを消費し、完全にパースされたASTノードを出力する変換 (transform) ストリームです。

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
    // 完全なASTはストリームの `program` プロパティから取得できます
    console.log(JSON.stringify(parser.program, null, 2))
  })
```

**`parser.program`**

ストリームの実行中にパーサーインスタンスの `program` プロパティにアクセスすると、それまでにパースされたコードの完全なASTを取得できます。

### 同期API

すでにメモリ上に完全なGLSLソースがある場合は、同期APIの方がシンプルです。

**`const ParseTokens = require('glsl-parser/direct')`**

`glsl-tokenizer` からのトークン配列を同期的にパースし、完全なASTを返す関数です。

```javascript
const fs = require('fs')
const TokenString = require('glsl-tokenizer/string')
const ParseTokens = require('glsl-parser/direct')

const src = fs.readFileSync('test.glsl', 'utf8')
const tokens = TokenString(src)
const ast = ParseTokens(tokens)

console.log(JSON.stringify(ast, null, 2))
```

## ASTノード

パーサーはノードのツリーを生成します。各ノードの `type` プロパティは以下のいずれかになります:

*   `stmtlist`
*   `stmt`
*   `struct`
*   `function`
*   `functionargs`
*   `decl`
*   `decllist`
*   `for

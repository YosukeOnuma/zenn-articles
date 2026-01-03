---
title: "Gemini CLIがgitignore中のファイルを読み込めない時は"
emoji: "🙈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Gemini","CLI","AI"]
published: true
---

# 状況
Gemini CLIでファイルを読み込んで欲しい時，**そのファイルが`.gitignore`対象の場合，読み込んでくれないことがある．**

例：（gemini -pはプロンプト実行コマンド）

```bash
gemini -p "/path/to/file.mp3 を読み込んで"
```
こんなエラーが出る
```
Error executing tool read_file: File path '/path/to/file.mp3' is ignored by configured ignore patterns.
```

# 原因
Gemini CLIの内部ツールの一部は，デフォルトで`.gitignore`対象のファイルを無視する．詳しくは[公式ドキュメント](https://geminicli.com/docs/tools/file-system/)を参照．

# 対処法
方針としては，
1. `.gitignore`されているファイルの読み込みを許可
2. （optional）本当にgeminiに読んで欲しくないファイルは`.geminiignore`で指定

### 1. gitignoreを無視する
使うのは以下の設定ファイル．なければ作成する．
- グローバル設定: `~/.gemini/settings.json`
- プロジェクト設定: `(project path)/.gemini/settings.json`

後者が優先される．
そこに以下を追加する．

```json
{
  "context": {
    "fileFiltering": {
      "respectGitIgnore": false
    }
  }
}
```

### 2.（optional）読んで欲しくないものは.geminiignoreで除外する
使うのは以下の設定ファイル．なければ作成する．
- グローバル設定: `~/.gemini/.geminiignore`
- プロジェクト設定: `(project path)/.gemini/.geminiignore`

後者が優先される．
そこに，geminiに読んで欲しくないファイル・ディレクトリのパスを，`gitignore`と同じ書式で記述する．
例：
```
# mp3ファイルを除外
*.mp3
```

`.geminiignore`の詳細は[公式ドキュメント](https://geminicli.com/docs/cli/gemini-ignore/)を参照．


## 他の方法もあるかも
上記以外にも，例えば`gemini`コマンドにオプションをつける方法もあるかもしれませんが，見つけられませんでした．ご存知でしたらぜひコメント等で教えてください

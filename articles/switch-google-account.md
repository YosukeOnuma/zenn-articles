---
title: "RaycastでGoogleサービスを特定アカウントで開く設定"
emoji: "🚀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Raycast", "Google", "効率化", "macOS"]
published: true # false: 下書き / true: 公開
---

## 解決したいこと

Gmail、Gemini、Google Driveなどを開く際、サービスごとにメインアカウントが異なると、切り替えが面倒です。そこで、macOS用ランチャーツール「Raycast」を使い、Googleサービスを特定のアカウントで一発で開く方法を紹介します。

## 解決策：URLでのユーザー指定とRaycast Quicklink

Googleサービスは、URLに `/u/[数字]/` を含めることで、ログイン中のアカウントを指定できる場合があります。このURLとRaycastのQuicklink機能を組み合わせます。

### 手順1：ユーザー番号での挙動確認

まず、使用したいアカウントが何番目かを確認し、URLで指定できるか試します。

1. ブラウザで、使用したいすべてのアカウントにログイン
2. 対象サービスのURL末尾に `/u/[数字]/` を付けてアクセスします
  例：Geminiを2番目(番号は`1`)のアカウントで開きたい場合
    `https://gemini.google.com/u/1/`
3. 意図したアカウントでサービスが開くことを確認します

### 手順2：Raycastのインストール

まだRaycastを導入していない場合は、Raycast公式サイトまたは`brew install --cask raycast`でインストール。
https://www.raycast.com/
Raycastはこれ以外にも様々なショートカットを登録できるランチャーアプリです。オススメ👍

### 手順3：Raycast Quicklinkの登録

Raycastに、手順1で確認したURLをQuicklinkとして登録します。

1.  **Raycastを起動**します（デフォルトは `⌥ Space`）
2.  設定画面→`Extensions`→＋マークから`Create Quicklink`を選択
3.  **Name:** 分かりやすい名前を入力します（例：`Open Gemini (仕事用)`）
4.  **Link:** 手順1で確認したURLを入力します（例：`https://gemini.google.com/u/1/`）
5.  **Application:** 開きたいブラウザ（Safari, Chromeなど）を選択します
6.  `Create Quicklink` を実行して保存します

↓設定例
![](/images/switch-google-account/raycast_settings.png)


これで、Raycastを起動して設定したName（例：`Gemini (仕事用)`）を入力・実行すれば、指定したアカウントでGeminiが一発で開くようになります。
さらに効率化したい場合は、Raycastの設定画面から、作成したQuicklinkにホットキーを割り当てることも可能です。

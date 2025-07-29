---
title: "Googleサービスを特定アカウントで一発で開く【Raycast】"
emoji: "🚀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Raycast", "Google", "効率化", "macOS"]
published: true # false: 下書き / true: 公開
---

## 解決したいこと

Gmail、Gemini、Google Driveなどを開く際、サービスごとにメインアカウントが異なると、切り替えが面倒です。そこで、macOS用ランチャーツール「Raycast」を使い、Googleサービスを特定のアカウントで一発で開く方法を紹介します。

## 解決策：URLでのユーザー指定とRaycast Quicklink

Googleサービスは、URL末尾に `/u/[メールアドレス]/` を含めることで、ログイン中のアカウントを指定できます^[ログアウト中のアカウントではうまくいかないと思います]。このURLとRaycastのQuicklink機能を組み合わせます。

### 手順1：Raycastのインストール

まだRaycastを導入していない場合は、Raycast公式サイト、または`brew install --cask raycast`でインストール。
https://www.raycast.com/
Raycastはこれ以外にも様々なショートカットを登録できるランチャーアプリです。オススメ👍

### 手順2：Raycast Quicklinkの登録

RaycastのQuicklink機能にURLを登録します。

1.  **Raycastを起動**します（デフォルトは `⌥ Space`）
2.  設定画面→`Extensions`→＋マークから`Create Quicklink`を選択
3.  **Name:** 呼び出し用の名前を入力します（例：`Open Gemini (仕事用)`）
4.  **Link:** メールアドレスを含めたURLを入力します（例：`https://gemini.google.com/u/xxxx@gmail.com/`）
5.  **Application:** 開きたいブラウザ（Safari, Chromeなど）を選択します
6.  `Create Quicklink` を実行して保存します

↓設定例
![](/images/switch-google-account/raycast_settings_ver2.png =500x)


これで、Raycastを起動して設定したName（例：`Open Gemini (仕事用)`）を入力・実行すれば、指定したアカウントでGeminiが一発で開くようになります。
さらに効率化したい場合は、Raycastの設定画面から、作成したQuicklinkにホットキーを割り当てることも可能です。

## 更新履歴
(2025/7/29) koniさんのご指摘により、登録するURL部分を改善しました。

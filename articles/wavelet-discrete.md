---
title: "離散ウェーブレット変換をわかりやすく"
emoji: "🌊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["wavelet","信号処理","math"]
published: true
---

## 解説事項
- 離散ウェーブレット変換（多重解像度解析に同じ[^1]）
- （余談）ウェーブレットパケット分解

[^1]: 本来離散WTは「離散化したウェーブレット変換」だが，巷ではその特別な場合である多重解像度解析を指すことが多い

そもそものウェーブレット変換や連続ver.の解説は[前回](https://zenn.dev/numa03/articles/wavelet-abstract)参照

## 離散ウェーブレット変換を一言で
- 信号を周波数帯域ごとの成分に段階的に分割していく
- 分割は冗長性がなく，逆変換が可能である


## 離散ウェーブレット変換（DWT）
DWTは，連続WTを**冗長性なく**離散化したものと捉えるとよく，以下のフィルタリングと等価になる．DWTでは**信号にフィルタをかけて高周波成分（detail coefficient）と低周波成分（approximation coefficient）の２つの時系列を得る．さらに低周波成分を２分し，これを続けていく**．周波数領域で言えば左下図（[出典](https://youtu.be/fxfS0vSAsTA?si=W2CqC20c88sayV7H)）のように，２分割を繰り返すことに相当する．時系列データのフローとしては右下図がわかりやすいかもしれない．分割の深さをレベルと呼び，例えばレベル3なら4つの時系列が出力される．なおこのフィルタはマザーウェーブレットから算出される．以上の操作は「多重解像度解析」とも呼ばれる．

![](/images/wavelet-discrete/dwt.png)

## DWTの式

フィルタリングによる高周波成分と低周波成分への分解は，ウェーブレット関数 $\psi$成分とそれに直交するスケーリング関数 $\phi$成分を得ることに相当する．１段階目のフィルタリングは，次のように表せる．ただし$x(t)$は元信号で，$k$は時刻ずらしのステップである．

$$
x(t) = \sum_k d_k\psi_{1,k}(t) + \sum_k c_k\phi_{1,k}(t)
$$

この$c_k$列がdetail coefficient, $d_k$列がapproximation coefficientである．そしてスケーリング関数$\phi$側を再び分解していき，レベル$L$まで行えば，

$$
x(t) = \sum_{l=1}^L \sum_k d_{l,k}\psi_{l,k}(t) + \sum_k c_{L,k}\phi_{L,k}(t) \tag{1}
$$

となる．スケーリング関数は常に一項存在し，ウェーブレット成分でない「残り」と捉えても良い．ウェーブレット関数$\psi$はレベル $l$ごとに異なり，

$$
\psi_{l,k}(t) = \sqrt{2^{J-l}}\psi(2^{J-l}t-k),\quad \phi_{l,k}(t) = \sqrt{2^{J-l}}\phi(2^{J-l}t-k) \tag{2}
$$

である．$J$は正整数である．

では，この式の意味を理解しよう．スケール$a$，シフト$b$のウェーブレットは，

$$
\psi_{a,b}(t) = \frac{1}{\sqrt{a}} \psi\bigg(\frac{t-b}{a}\bigg)
$$

と表せることを考えると（[前回](https://zenn.dev/numa03/articles/wavelet-abstract)参照），式(2)と照らし合わせて

$$
a=\frac{1}{2^{J-l}},\quad b=\frac{k}{2^{J-l}}
$$

という関係がわかる．**つまり，分解のレベル $l$が+1されると，ウェーブレット$\psi$のスケール$a$が２倍になり，シフト$b$もステップ$k$ごとの大きさが２倍になる**．DTWは時間・周波数の領域全体を，下図のように高周波側から段階的に分割しているのだ．

![](/images/wavelet-discrete/window_level.png)

レベル１では，図中で黄色の高周波なウェーブレット関数の成分を求め，残り下半分がスケーリング関数の成分に対応する．レベル２ではスケールが倍，すなわち周波数が半分のウェーブレットの成分（図中で緑）を求めるが，シフトが倍になることで時間解像度が半分になっている．このような分割を**２進分割**と呼ぶ．**分割が深まるごとに低周波側の周波数解像度を上げながら，時間解像度が下がる様がわかるだろう．**

## Pythonでの実装と確認
DWTをPythonで実装する．さらに，得られた帯域ごとの成分列の長さを確認する

```py
import numpy as np
import pywt

# 1次元のデータを作成（長さ256）
data = np.random.randn(256)

# 離散ウェーブレット変換
wavelet = 'db1'     # マザーウェーブレットを指定（ここはなんでも良い）
coeffs = pywt.wavedec(data, wavelet, level=3)

# 各成分の長さをprint
print(f"level 3 approx. coef. : len = {len(coeffs[0])}")
print(f"level 3 detail coef. : len = {len(coeffs[1])}")
print(f"level 2 detail coef. : len = {len(coeffs[2])}")
print(f"level 1 detail coef. : len = {len(coeffs[3])}")
```
出力は以下となった
```
level 3 approx. coef. : len = 32
level 3 detail coef. : len = 32
level 2 detail coef. : len = 64
level 1 detail coef. : len = 128
```

レベルが深いほど係数が短いのは，時間解像度が低くなっていることを意味する．さらには，32+32+64+128=256（元の信号長）であることから，離散ウェーブレット変換が冗長性なく信号を分解していることが窺える．このことを特にHaarウェーブレットで説明している[こちらの記事](https://qiita.com/kaityo256/items/70dc20658ef98d229de9)も参照されたい．



## （余談）ウェーブレットパケット分解（WPD）

離散WTは低周波成分を段階的に分解していった一方で，WPDは高周波数成分も同様に分解していく．すなわち周波数方向の分割は２進分割ではなく等間隔になり，周波数領域全体を等しく分解できる．処理の流れは下図（[出典](https://youtu.be/fxfS0vSAsTA?si=73Fh_yG-5Z167lha)）の通り，ただしLPFはローパスフィルタ，HPFはハイパスフィルタ．

![](/images/wavelet-discrete/wpd.png)

ただし時間解像度は一律になるため，ウェーブレット変換の旨みである可変解像度はなくなってしまう．


## 参考
- [ウェーブレット変換の基礎と応用事例](https://www.slideshare.net/slideshow/ss-42388444/42388444#12)
- [ウェーブレット変換の総まとめ](https://shop.cqpub.co.jp/hanbai/books/30/30961/30961_9syo.pdf)
- [Wavelets-based Feature Extraction](https://youtu.be/fxfS0vSAsTA?si=73Fh_yG-5Z167lha)
- [多重解像度解析（Wiki）](https://ja.wikipedia.org/wiki/多重解像度解析)
- 数理科学　ウェーブレットビギナーズガイド（東京電機大学出版局）


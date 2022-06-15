# STA-Calc2022

- 2T9 35 多田健人

## 起動方法

- アプリケーションのルートディレクトリでnpm run dev

## 特徴

- Vue3で作成

- evalで計算を実現
    - 正規表現で変な値（悪意のあるコード）が来ないか制御
    - evalで計算しているので、小数点でもカッコでも（一応）計算できる

- 見やすいUI
    - Vue3のUIフレームワーク、element-plusを使用

- Log機能
    - localStorage仕様
    - Log削除も
    - Logから値も戻せる

## その他

- 指数表記まで行くとバグります


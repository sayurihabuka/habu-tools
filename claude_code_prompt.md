# Claude Code への引き継ぎプロンプト

以下をそのままClaude Codeのチャット欄に貼り付けてください。

---

## プロジェクト概要

小学3年生（青ちゃん）向けの週間学習チェックリストアプリを作っています。
HTMLファイル1枚で動くシンプルなWebアプリです。

---

## ファイル情報

- **ローカルファイル**: `/Volumes/Extreme SSD/案件フォルダ/羽深家のツール作成/青用/曜日別学習リスト/index.html`
- **GitHub リポジトリ**: `https://github.com/sayurihabuka/hahuka-tools`
- **公開URL**: `https://sayurihabuka.github.io/hahuka-tools/`

---

## Firebase 設定情報

```javascript
const firebaseConfig = {
  apiKey: "AIzaSyADUL3Zu7ZkguKuz7LUSarfAj7JFkteTMw",
  authDomain: "hahuka-tools.firebaseapp.com",
  databaseURL: "https://hahuka-tools-default-rtdb.firebaseio.com",
  projectId: "hahuka-tools",
  storageBucket: "hahuka-tools.firebasestorage.app",
  messagingSenderId: "1097903942252",
  appId: "1:1097903942252:web:f5713546094f3b4f55c649"
};
```

---

## 現在の状況と問題

### やりたいこと
チェックの状態を **Firebase Realtime Database** に保存して、複数デバイス（スマホ・PC）でリアルタイム共有できるようにしたい。

### 現在の問題
`index.html` の `<script>` タグを `<script type="module">` に書き換えて Firebase SDK を追加しようとしているが、うまく動いていない。

ブラウザのコンソールに `Uncaught SyntaxError: Unexpected token '}'` というエラーが出ている（898行目付近）。

### 原因の可能性
- `updateOverall()` 関数の定義が壊れている（`function updateOverall() {` の行がない状態になっている可能性）
- `type="module"` のscriptタグ内では `onclick="関数名()"` がグローバルから呼べないため、`window.関数名 = 関数名` の追記が必要

---

## お願いしたい作業

1. `index.html` を開いて構文エラーを修正してください
2. Firebase Realtime Database 対応に書き換えてください
   - `<script type="module">` で Firebase SDK をインポート
   - `localStorage` の読み書きを Firebase の `get` / `set` に置き換え
   - `onValue` でリアルタイム同期
   - `window.toggleWeekItem` と `window.toggleDay` をグローバルに公開
3. 動作確認後、git commit & push してください

---

## 現在のlocalStorage構造（Firebase移行前）

```
study_v2_kokugo  → { checked: [bool×8], lastResetTs: number }
study_v2_sansu   → { checked: [bool×5], lastResetTs: number }
study_v2_saile   → { checked: [bool×4], lastResetTs: number }
study_v2_eigo    → { checked: [bool×3], lastResetTs: number }
daily_<dayId>_keisan/goi/piano/kumon → { checked: [bool], lastResetTs: number }
```

Firebase では `checklist/<key>` というパスに同じ構造で保存します。

---

## カテゴリリセット設定

- kokugo / sansu: 水曜18:00
- saile: 土曜18:00
- eigo: 木曜18:00
- 毎日系（piano/keisan/goi/kumon）: 月曜00:00

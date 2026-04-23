# 羽深家 週間学習チェックリスト

## プロジェクト概要

羽深家の子供たち向けの週間学習チェックリストアプリ。
HTMLファイル1枚で動くWebアプリ。Firebase Realtime Database でチェック状態を複数デバイスにリアルタイム同期。

---

## アプリ一覧

| 対象 | 説明 | ローカル | 公開URL |
|------|------|----------|---------|
| 青（小3女の子） | 小学3年生向け | `ao/index.html` | https://sayurihabuka.github.io/habu-tools/ao/ |
| 空（中1女の子） | 中学1年生向け | `sora/index.html` | https://sayurihabuka.github.io/habu-tools/sora/ |

- **ローカルルート**: `/Volumes/Extreme SSD/案件フォルダ/羽深家のツール作成/曜日別学習リスト/`
- **GitHub**: `https://github.com/sayurihabuka/habu-tools`

---

## Firebase 設定

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

- SDK: Firebase 10.12.2（ESモジュール形式）
- APIキー制限: `sayurihabuka.github.io/*` のみ許可済み
- Security Rules: `.read: true, .write: true`

---

## 青（ao）のデータ構造

Firebaseキープレフィックス: `checklist/study_v2_*`

| キー | items数 | リセット |
|------|---------|---------|
| study_v2_kokugo | 8 | 水曜18:00 |
| study_v2_sansu | 6 | 水曜18:00 |
| study_v2_saile | 5 | 土曜18:00 |
| study_v2_eigo | 4 | 木曜18:00 |
| study_v2_daily_\<day\>_keisan/goi/piano/kumon | 1 | 月曜00:00 |

### 青 itemIdx 割り当て

**kokugo（8項目）**
- 0=大問1・2(木), 1=文法語句(木), 2=漢字(木), 3=書き写し(金)
- 4=復習テスト(土), 5=文法解き直し(土), 6=漢字解き直し(土), 7=漢字確認(月)

**sansu（6項目）**
- 0=基礎A(水), 1=応用B(水), 2=復習テスト(水)
- 3=解き直し(土), 4=確認(火), 5=チャレンジ(金)

**saile（5項目）**
- 0=基礎A(土), 1=応用B(土), 2=応用C(日), 3=解き直し(月), 4=確認(土)

**eigo（4項目）**
- 0=レッスン宿題(月), 1=書き取り宿題(月), 2=レッスン予習(火), 3=予習(水)

---

## 空（sora）のデータ構造

Firebaseキープレフィックス: `checklist/sora_*`

各曜日のチェックが独立するよう、英語・数学は曜日ごとに別キーを使用。

| キー | items数 | リセット |
|------|---------|---------|
| sora_eigo_\<day\> | 10 | 火曜20:00 |
| sora_suugaku_\<day\> | 4 | 水曜20:00 |
| sora_daily_\<day\>_keisan/douji/piano | 1 | 月曜00:00 |

`<day>` = mon / tue / wed / thu / fri / sat / sun

### 空 英語（ES150）10項目
- 0=Words to Remember1, 1=Words to Remember2, 2=Sentences to Remember
- 3=Reading Audio, 4=Reading Video, 5=English Skills
- 6=Speaking Practice, 7=Reading Along, 8=Grammar Review, 9=復習テストの解き直し

### 空 数学（MS180）4項目
- 0=例題, 1=練習問題, 2=Homework, 3=Homework 解き直し

### 空の曜日別表示
| 曜日 | 英語項目 | 数学項目 | 備考 |
|------|---------|---------|------|
| 日 | Before(0,1,2) + After(6,7,8) | - | |
| 月 | Before(0,1,2) + After(8) | 解き直し(3) | |
| 火 | Before(0,1,2) + After(9,8) | - | J PREP(英語) 17:30〜20:30 |
| 水 | Before(0,1,2) + After(8) | 例題(0),練習(1),HW(2) | J PREP(数学) 17:30〜20:30 |
| 木 | Before(0,1,2,3,4,5) + After(6,7,8) | HW(2) | |
| 金 | Before(0,1,2) + After(6,7,8) | HW(2) | |
| 土 | Before(0,1,2) + After(6,7,8) | - | ピアノ 17:30〜18:00 |

---

## 共通実装ポイント

- `<script type="module">` → `window.toggleWeekItem` / `window.toggleDay` をグローバル公開
- `localChangeTimes`（5秒グレース）で先祖返り（state reverting）防止
- リセット書き込みを `update()` でバッチ化（onValue連鎖を抑制）
- `setInterval(checkResets, 60000)` で毎分リセット確認

---

## リポジトリ構成

```
habu-tools/
├── ao/
│   ├── index.html
│   ├── favicon.png
│   └── favicon.ico
├── sora/
│   ├── index.html
│   ├── favicon.png
│   └── favicon.ico
├── old/
│   └── study-checklist-weekly5.html  ← 青の元デザイン参考
├── CLAUDE.md
├── claude_code_prompt.md
└── README.md
```

---

## デザイン仕様

曜日カラー（青・空共通）: 月=ピンク、火=オレンジ、水=ブルー、木=グリーン、金=黄、土=紫、日=赤

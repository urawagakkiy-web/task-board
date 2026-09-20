# CLAUDE.md

このファイルは、Claude Code (claude.ai/code) が **開発1号** を扱うときのガイダンスです。

## プロジェクト概要

**開発1号 / task-board** — React で作るタスクボード。

- やることを入力して追加、チェックボックスで完了・未完了を切り替え、不要なタスクは削除
- 完了済みのタスクはグレー＋取り消し線で表示する
- タスクは `localStorage` に保存し、リロードしても消えない
- `index.html` をダブルクリックしてブラウザで開ければ動く。**React の読み込みにネット接続が必要**

## デプロイ先

https://urawagakkiy-web.github.io/task-board/

`main` ブランチにプッシュすると自動で反映される。詳しくは「公開（GitHub Pages）」を参照。

## 技術スタック

| 種類 | 使うもの | 読み込み方 |
|---|---|---|
| UIライブラリ | React 18.3.1（UMD） | cdnjs から `<script>` |
| DOM描画 | ReactDOM 18.3.1（UMD） | cdnjs から `<script>` |
| JSX変換 | Babel Standalone 7.26.4 | cdnjs から `<script>`、ブラウザ上で変換 |
| スタイル | 素のCSS（`style.css`） | `<link rel="stylesheet">` |
| 保存 | Web Storage API（`localStorage`） | `store` ラッパー経由 |
| ホスティング | GitHub Pages（`main` 直下を配信） | プッシュで自動反映 |

- **ビルドツール・パッケージマネージャ・トランスパイル済みバンドルは使わない。** `npm install` も `package.json` も無い
- **バージョンは URL に固定して書く**（`react/18.3.1/...`）。`latest` や範囲指定は使わない。壊れたときに原因が追えなくなるため
- React 19 には UMD ビルドが無い。**18系から上げない**（上げるならバンドラ導入が必要になり、この構成では扱えない）
- TypeScript・状態管理ライブラリ・UIフレームワーク・CSSフレームワークは入れない
- Babel は `data-presets="react"` のみ。JSX変換だけに使う
- テストフレームワークは無い。確認は「動作確認」の節のとおりブラウザで行う

## 言語

**ユーザーへの返答・UI・解説文・コメント・README・コミットメッセージは、すべて日本語で書く。**

## Git運用ルール（重要）

**コードを変更するたびに、コミットして GitHub にプッシュする。**
「あとでまとめて」はやらない。1つの変更が動く状態になったら、その場で下記を実行する。

```bash
git add -A
git commit -m "変更内容を日本語で1行"
git push
```

### 守ること

- **動作確認 → コミット → プッシュ**の順。ブラウザで開いて動かないものはコミットしない
- 1コミット = 1つのまとまった変更。無関係な変更を混ぜない
- 作業ブランチは `main`。研修用の単独作業なので、直接 `main` にコミットしてよい
- **プッシュまで終えて初めて「完了」**。ローカルのコミットで止めない
- 秘密情報（APIキー等）はコミットしない。`.env` は必ず `.gitignore` に入れる

### コミットメッセージの書式

1行目に何をしたかを日本語で簡潔に。必要なら空行のあと箇条書きで詳細を添える。

```
スタート画面のリード文とテーマ色を調整

- :root のカラートークンを2色に整理
- スマホ幅で見出しが折り返す問題を修正

Co-Authored-By: Claude Opus 5 (1M context) <noreply@anthropic.com>
```

### GitHubリポジトリ

https://github.com/urawagakkiy-web/task-board

```bash
git remote -v   # origin git@github.com:urawagakkiy-web/task-board.git
```

認証は SSH 鍵（`git@github.com:`）を使う。`gh` コマンドは未導入なので、
GitHub 側の操作（リポジトリ作成・PR など）はブラウザで行う（使うなら `brew install gh`）。

### 除外設定（`.gitignore`）

```
# macOS
.DS_Store

# エディタ
.vscode/
.idea/

# Office の一時ロックファイル
~$*

# 依存パッケージ
node_modules/

# 環境変数（APIキーなどの秘密情報）
.env
.env.local
.env.*.local
```

## ファイル構成

```
開発1号/
├── index.html   # 画面の骨組み ＋ React本体（JSX）
├── style.css    # 見た目。テーマ色は :root のCSS変数に集約
├── README.md    # リポジトリの説明（公開URL入り）
├── .nojekyll    # GitHub Pages で Jekyll の処理を無効化（消さない）
├── .gitignore
└── CLAUDE.md
```

CSS は必ず `style.css` に置き、`index.html` に `<style>` を書き足さない。

**JSX は `index.html` 内の `<script type="text/babel">` に直接書く。**
外部ファイル（`app.jsx` など）に切り出すと、Babel Standalone がそれを fetch する際に
`file://` では CORS で読み込めず、ダブルクリック起動が壊れるため。

CDN は cdnjs から React / ReactDOM / Babel Standalone の3本のみ。他のライブラリは足さない。
読み込みに失敗したときは `#root` にその旨を表示するフォールバックを入れてある。

## 公開（GitHub Pages）

公開URL は「デプロイ先」のとおり。

`main` ブランチの**リポジトリ直下**をそのまま配信している（Settings → Pages → Deploy from a branch → `main` / `/ (root)`）。
ビルドは無く、**`main` にプッシュすれば1分ほどで反映される**。デプロイ用のワークフローやコマンドは不要。

公開を壊さないための決まり:

- `index.html` はリポジトリ直下に置く。サブフォルダへ移動しない
- ファイルの参照は必ず**相対パス**（`./style.css`）。`/style.css` のような絶対パスはサブパス配信で404になる
- `.nojekyll` を消さない（Jekyll に余計な処理をさせないため）
- 外部ライブラリは **https** の CDN から読む。http だと混在コンテンツでブロックされる
- 公開先は誰でも見られる。**秘密情報をコードに書かない**（タスクの中身は各自のブラウザの localStorage に入るだけで、サーバーには送られない）

反映されないときは、リポジトリの Actions タブで `pages build and deployment` の成否を見る。

## コマンド

```bash
# アプリを開く
open 開発1号/index.html
```

ビルド・インストール・テスト実行のコマンドは存在しない。

## 実装の方針

### 命名規約

**コンポーネント**

- ファイルは分けず、`index.html` の JSX ブロック内に**上から「小さい部品 → それを使う親」の順**で定義する
- 名前は **PascalCase**。`TaskItem` のように**単数形の名詞**にする（`TaskItems` のような複数形は使わない）
- 状態を持つのは親の `TaskBoard` だけ。子（`TaskItem`）は props を受け取って描画するだけにする
- 1コンポーネント = 1つの関数。`function TaskItem({ task, onToggle, onDelete }) { ... }` のように**props は引数で分割代入**する

**props**

- 値を渡す props は**中身を表す名詞**（`task`）。`data` や `item` のような曖昧な名前にしない
- イベントを渡す props は **`on` + 動詞**（`onToggle` / `onDelete`）。渡す側の関数名は**動詞 + 名詞**（`toggleTask` / `deleteTask`）にして、呼び名と実体を区別する

**変数・関数**

| 対象 | 規約 | 例 |
|---|---|---|
| 状態 | `[名詞, set名詞]` | `[tasks, setTasks]` |
| 真偽値の状態 | 状態を表す形容詞・過去分詞 | `saveFailed`（`isXxx` は使わない） |
| イベントハンドラ | 動詞 + 名詞 | `addTask` / `toggleTask` / `deleteTask` |
| コンポーネント外の関数 | camelCase の動詞始まり | `loadTasks` |
| 定数 | UPPER_SNAKE_CASE | `STORAGE_KEY` |
| 単一のユーティリティ | camelCase の名詞 | `store` |
| イベント引数 | `event`（`e` と略さない） | `(event) => setText(event.target.value)` |
| 配列のコールバック引数 | 要素の単数形 | `tasks.map((task) => ...)` |

**CSSクラス**

- **kebab-case の英語**で、見た目ではなく**役割**を表す名前にする（`.add-form` / `.task-list` / `.count`）。`.gray-text` のような見た目由来の名前は使わない
- 状態は**元のクラスに足す形**で表す（`className={task.done ? "task done" : "task"}`）。状態専用のクラスを別に作らない
- CSS変数も**役割で命名**する（`--ink` / `--danger` / `--done-ink`）。`--red` のような色名にしない

### コンポーネントと状態

- 関数コンポーネント＋フック（`useState` / `useRef`）だけで書く。クラスコンポーネントは使わない
- 状態は `TaskBoard` に集約し、子（`TaskItem`）へは props で渡す。状態管理ライブラリは入れない
- 状態の更新は必ず**新しい配列・オブジェクトを作る**（`map` / `filter` / スプレッド）。
  `tasks[i].done = true` のような破壊的変更はしない（再描画されない）
- タスクの `id` は `useRef` のカウンタで採番する。配列の index を `key` に使わない（削除でずれるため）

### データ

タスクは `{ id, text, done }` の配列。
**件数をどこにもハードコードしない。** 「未完了 ◯ 件」などの表示は `tasks` から算出する。

### 保存まわり（重要）

タスクは `localStorage` に保存し、リロードしても残るようにしている。

**`localStorage` を直接呼ばない。必ず `store` ラッパー経由で読み書きする。**
`file://` や一部ブラウザでは `localStorage` へのアクセスが `SecurityError` を投げ、直接呼ぶと初期描画ごと落ちる。
`store` は失敗時にメモリへ退避して `store.ok` を `false` にし、画面上部に
「このブラウザでは保存できません」の警告（`.warn`）を出す。

- キー: `STORAGE_KEY = "task-board-tasks"`
- 形式: `[{ id, text, done }]`
- 読み込みは `loadTasks()` が形を検証してから使う。壊れた JSON や不正な要素が入っていても落ちず、捨てて続行する
- `id` は読み込み時に 1 から振り直す。保存された `id` は信用しない
- 保存は `useEffect(..., [tasks])` で、tasks が変わるたびに書き出す

### 配色

`:root` の CSS 変数だけでテーマが決まるようにする。個々のセレクタに色を直書きしない。

### アクセシビリティ・表示

- スマホ幅（375px）で横スクロールが出ないこと
- ボタン・選択肢はタップしやすい大きさにする
- 状態は色だけでなく記号（○×など）でも示す

## 動作確認

テストはないので、**ブラウザで実際に開いて通しで操作する**ことで確認する。
ブラウザペインから JS を実行して一通りの流れを再現するのが早い。
**「ファイルを書いた」で終わらせない。**確認できたらコミットしてプッシュする。

`file://` で開くとブラウザによっては `localStorage` が使えず保存を確認できない。
保存まわりを確認するときは簡易サーバー経由で開く。

```bash
python3 -m http.server 8000   # http://localhost:8000/
```

公開後は https://urawagakkiy-web.github.io/task-board/ でも同じ動作になるか確認する。

# CLAUDE.md

このファイルは、Claude Code (claude.ai/code) が **開発1号** を扱うときのガイダンスです。

## プロジェクト概要

**開発1号 / task-board** — React で作るタスクボード。

- やることを入力して追加、チェックボックスで完了・未完了を切り替え、不要なタスクは削除
- 完了済みのタスクはグレー＋取り消し線で表示する
- タスクは `localStorage` に保存し、リロードしても消えない
- 技術スタック: **React 18（CDN の UMD ビルド）+ Babel Standalone**
- ビルドツール・パッケージマネージャ・テストフレームワークは使わない（npm install も不要）
- `index.html` をダブルクリックしてブラウザで開ければ動く。**React の読み込みにネット接続が必要**

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
├── .gitignore
└── CLAUDE.md
```

CSS は必ず `style.css` に置き、`index.html` に `<style>` を書き足さない。

**JSX は `index.html` 内の `<script type="text/babel">` に直接書く。**
外部ファイル（`app.jsx` など）に切り出すと、Babel Standalone がそれを fetch する際に
`file://` では CORS で読み込めず、ダブルクリック起動が壊れるため。

CDN は cdnjs から React / ReactDOM / Babel Standalone の3本のみ。他のライブラリは足さない。
読み込みに失敗したときは `#root` にその旨を表示するフォールバックを入れてある。

## コマンド

```bash
# アプリを開く
open 開発1号/index.html
```

ビルド・インストール・テスト実行のコマンドは存在しない。

## 実装の方針

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

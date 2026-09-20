# タスクボード（task-board）

React で作ったシンプルなタスク管理アプリ。

**公開URL: https://urawagakkiy-web.github.io/task-board/**

## できること

- テキストを入力してタスクを追加（Enter キーでも追加できる）
- チェックボックスで完了・未完了を切り替え
- 不要なタスクを削除
- 完了済みのタスクはグレー＋取り消し線で表示
- タスクは `localStorage` に保存され、リロードしても消えない

## 技術構成

- React 18（CDN の UMD ビルド）+ Babel Standalone
- ビルドツール・パッケージマネージャは不要。`npm install` もいらない
- 静的ファイルだけで動くので、GitHub Pages にそのまま置ける

```
task-board/
├── index.html   # 画面の骨組み ＋ React本体（JSX）
├── style.css    # 見た目
├── .nojekyll    # GitHub Pages で Jekyll の処理を無効化
└── README.md
```

## ローカルで動かす

`index.html` をダブルクリックすればブラウザで開く（React の読み込みにネット接続が必要）。

ただし `file://` で開くと、ブラウザによっては `localStorage` が使えず保存されない。
保存も含めて確認したい場合は簡易サーバー経由で開く。

```bash
python3 -m http.server 8000
# http://localhost:8000/ をブラウザで開く
```

## 公開について

`main` ブランチにプッシュすると GitHub Pages に自動で反映される（反映まで1分ほど）。

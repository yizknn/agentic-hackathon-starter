# 🍽️ Shuffle Lunch App

社内メンバーを入力すると、3〜4人のランチグループを自動生成し、各グループにランチの行き先とトークテーマを提案するWebアプリケーションです。

![Shuffle Lunch App](https://img.shields.io/badge/Vue.js-3.0-4FC08D?style=flat-square&logo=vue.js) ![CSS](https://img.shields.io/badge/CSS-Modules-1572B6?style=flat-square&logo=css3) ![TypeScript](https://img.shields.io/badge/TypeScript-Supported-3178C6?style=flat-square&logo=typescript)

## ✨ 主な機能

- 📝 **参加者入力**: 改行・カンマ区切りで参加者を入力
- 🎲 **自動グループ分け**: 3〜4人のグループに最適分割
- 🍽️ **ランチ先提案**: 15種類のジャンルからランダム選択
- 💬 **トークテーマ**: 会話のきっかけとなるテーマを提案
- 📱 **レスポンシブ**: モバイル・タブレット・デスクトップ対応
- 📋 **結果コピー**: ワンクリックでSlack等に共有可能

## 🚀 クイックスタート

### フロントエンド（Vue.js）の起動

```bash
cd frontend
npm install
npm run dev
```

http://localhost:5173 でアプリが起動します。

### 使い方

1. **参加者入力**: テキストエリアに参加者の名前を入力
   ```
   田中太郎
   佐藤花子
   山田次郎
   鈴木一郎
   高橋美咲
   ```

2. **シャッフル実行**: 「🎲 シャッフル実行」ボタンをクリック

3. **結果確認**: 生成されたグループとランチ先・トークテーマを確認

4. **結果共有**: 「📋 結果をコピー」でSlack等に共有

## 📱 アプリの使用例

### 入力例
```
田中太郎
佐藤花子 
山田次郎
鈴木一郎
高橋美咲
渡辺健太
中村さくら
```

### 出力例
```
**Team A**
メンバー:
  • 田中太郎
  • 佐藤花子
  • 山田次郎
ランチ先: イタリアン
トークテーマ: 最近見たおすすめ映画・ドラマ

**Team B**
メンバー:
  • 鈴木一郎
  • 高橋美咲
  • 渡辺健太
  • 中村さくら
ランチ先: 中華料理
トークテーマ: 好きな旅行先・行ってみたい場所
```

## 🛠️ 技術スタック

- **Frontend**: Vue.js 3 (Composition API)
- **Build Tool**: Vite
- **Styling**: CSS Modules
- **Language**: JavaScript/TypeScript対応
- **Deploy**: 静的ホスティング対応（Vercel, Netlify等）

## 🎯 グループ分けアルゴリズム

- **制約**: 1グループ3〜4人
- **特例**: 5人の場合は5人1組を許可
- **最適化**: 4人グループを優先して効率的に分割
- **ランダム**: Fisher-Yatesシャッフルによる完全ランダム分割

### 分割例
| 人数 | グループ構成 |
|------|-------------|
| 7人  | 4人 + 3人 |
| 10人 | 4人 + 3人 + 3人 |
| 12人 | 4人 × 3グループ |

## 📁 プロジェクト構成

```
.
├── frontend/          # Vue.js アプリケーション
│   ├── src/
│   │   ├── App.vue   # メインコンポーネント
│   │   └── main.js   # エントリーポイント
│   ├── index.html    # HTMLテンプレート
│   ├── package.json  # 依存関係
│   └── vite.config.js # Vite設定
├── spec.md           # 機能仕様書
└── README.md         # このファイル
```

## 🔧 開発環境セットアップ

### 必要なツール
- Node.js 16.0.0 以上
- npm または yarn

### セットアップ手順
1. リポジトリをクローン
```bash
git clone https://github.com/yizknn/agentic-hackathon-starter.git
cd agentic-hackathon-starter
```

2. フロントエンドの依存関係をインストール
```bash
cd frontend
npm install
```

3. 開発サーバー起動
```bash
npm run dev
```

### その他のコマンド
```bash
# 本番ビルド
npm run build

# プレビュー
npm run preview

# リント
npm run lint
```

## 🚀 デプロイ

### Vercel
```bash
npm run build
# distフォルダをVercelにデプロイ
```

### Netlify
```bash
npm run build
# distフォルダをNetlifyにデプロイ
```

## 🤝 コントリビューション

1. このリポジトリをフォーク
2. フィーチャーブランチを作成 (`git checkout -b feature/amazing-feature`)
3. 変更をコミット (`git commit -m 'Add some amazing feature'`)
4. ブランチにプッシュ (`git push origin feature/amazing-feature`)
5. プルリクエストを作成

## 📄 ライセンス

MIT License - 詳細は [LICENSE](LICENSE) ファイルを参照してください。

## 📞 サポート

- Issue: [GitHub Issues](https://github.com/yizknn/agentic-hackathon-starter/issues)
- 仕様書: [spec.md](spec.md)

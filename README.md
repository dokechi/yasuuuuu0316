# sale-site-rebuild

`https://gekiyasu.blog/feed` を母艦にした、GitHub Pages + n8n の組み直し版です。

## 方向性

- GitHub側は **表示だけ**
- n8n側は **RSS取得 → 点数化 → Discord通知 → sales.json更新**
- 最初のRSSは **安いぞ！激安特価ニュース速報**
- 後から RSS を増やすときは「同じ役割」ではなく「別の役割」を足す

## ファイル

- `index.html`
- `sales.json`
- `n8n_gekiyasu_to_github_discord_v2.json`
- `.nojekyll`

## GitHub Pages の設定

1. GitHub で public リポジトリを作る
2. このフォルダの中身をアップロード
3. **Settings → Pages**
4. **Deploy from a branch**
5. Branch は `main`
6. Folder は `/ (root)` か `/docs`
7. 数分待ってURLを確認

GitHub Pages は branch / root か `/docs` を公開元にできます。

## n8n でやること

1. `n8n_gekiyasu_to_github_discord_v2.json` をインポート
2. `Config` ノードの以下を自分の値に変更
   - `githubOwner`
   - `githubRepo`
   - `githubBranch`
   - `githubPath`
   - `githubToken`
   - `discordWebhookUrl`
3. `Manual Trigger` で1回実行
4. Discord に候補通知が来るか確認
5. GitHub の `sales.json` が更新されるか確認
6. 問題なければ `Schedule Trigger` を有効化

## 今回のスコアの考え方

- %OFF
- 円OFF
- ポイント
- クーポン
- 定期便
- Amazon系
- 食品・日用品
- 単価表現
- 「本日まで」「数量限定」などの急ぎ要素
- 「送料別」「納期注意」「微妙」などの減点要素

### ざっくり閾値

- `siteMinScore`: サイトに残す最低点
- `discordMinScore`: Discordに通知する最低点
- `score >= 13`: 本命
- `食品・日用品 かつ score >= 9`: 生活防衛
- それ以外で `siteMinScore` 以上: レーダー

## 後でRSSを増やすとき

いきなり同じ特価ブログを何本も入れない方が安全です。  
最初は `gekiyasu.blog/feed` だけで回して、偏りが見えてから足してください。

- 商品単位の特価 → 今回の母艦
- 店単位のアウトレットURL監視 → 別トラック
- セールイベント → 別トラック

n8n の RSS Read ノードは最初の入力アイテムだけを処理するので、複数フィードを1本のワークフローで読む場合は Loop Over Items を使う構成にするのが安全です。

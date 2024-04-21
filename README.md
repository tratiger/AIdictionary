# AIdictionary
Notionにわからない単語を書いておくだけで自動で意味と例文と語源をNotionに保存する（一時間おきに自動更新）

ChatGPTのAPIとNotionAPIを取得して貼り付けるだけで動く 　わからない人は詳細を見て

実行環境はGAS

Google App Script GitHub アシスタントでGASをgithubで管理できる

## データベース(notion)の設定
以下のようにしてください

![スクリーンショット 2024-04-21 123121](https://github.com/tratiger/AIdictionary/assets/143635372/d4f342ae-5bf5-43ea-9378-9fb4b6942508)

## 詳細手順
*New integrationの作成

*上の画像と同じデータベースを作成

*ShareからAdd people, emails, groups, or integrationsをクリックして、先程作成したインテグレーションに共有

*ページのURLにDatabase IDが含まれるのでメモ

https://www.notion.so/myworkspace/*a8aec43384f447ed84390e8e42c2e089*?v=...

                                  　　|----------- Database ID -----------|

*chatgptのapiキーを取得

*スプレッドシートとかでGASひらく

*コードこぴってapi,id入れて実行

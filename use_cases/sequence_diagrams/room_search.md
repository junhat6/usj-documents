# ルーム検索シーケンス図

## 概要
このドキュメントでは、USJマッチングアプリにおけるルーム検索機能のシーケンスについて説明します。

## アクター
- 参加者／募集者（User）
- フロントエンド（UI）
- バックエンド（API）
- データベース（DB）

## シーケンスの流れ
1. ユーザーがルーム検索画面を表示
2. フロントエンドがバックエンドに検索リクエストを送信
   - エンドポイント: GET /rooms
   - パラメータ: tags, min, max, keyword
3. バックエンドがデータベースに検索クエリを実行
4. データベースが検索結果を返却
5. バックエンドがフロントエンドに検索結果を返却
   - ステータス: 200 OK
   - レスポンス: ルーム情報のリスト
6. フロントエンドがユーザーにルーム一覧を表示

## シーケンス図
```plantuml
@startuml
actor "参加者／募集者" as User
participant "フロントエンド\n(UI)" as Frontend
participant "バックエンド\n(API)"   as Backend
database "データベース"            as DB

User -> Frontend : ルーム検索画面を表示
Frontend -> Backend : GET /rooms?tags=&min=&max=&keyword=
Backend -> DB : SELECT * FROM rooms WHERE tags,人数,キーワード
DB --> Backend : 検索結果リスト
Backend -> Frontend : 200 OK\n[{roomId, tags, members, …}, …]
Frontend -> User : ルーム一覧を表示／フィルタ更新

@enduml
``` 
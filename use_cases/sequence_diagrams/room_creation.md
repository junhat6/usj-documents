# ルーム作成シーケンス図

## 概要
このドキュメントでは、USJマッチングアプリにおけるルーム作成機能のシーケンスについて説明します。

## アクター
- 募集者（Recruiter）
- フロントエンド（UI）
- バックエンド（API）
- データベース（DB）

## シーケンスの流れ
1. 募集者がルーム作成画面を表示
2. フロントエンドが募集者に入力フォームを提示
3. 募集者がルーム情報を入力
   - 入力項目: 人数, タグ, アトラクション
4. フロントエンドがバックエンドにルーム作成リクエストを送信
   - エンドポイント: POST /rooms
   - リクエストボディ: min, max, tags, attraction
5. バックエンドがデータベースにルーム情報を登録
   - クエリ: INSERT INTO rooms (…)
6. データベースが成功レスポンスを返却
7. バックエンドがフロントエンドにルーム作成完了を通知
   - ステータス: 201 Created
   - レスポンス: roomId など
8. フロントエンドが募集者にルームIDを表示し、共有用QRコードを生成

## シーケンス図
```plantuml
@startuml
actor "募集者" as Recruiter
participant "フロントエンド\n(UI)" as Frontend
participant "バックエンド\n(API)"   as Backend
database "データベース"            as DB

Recruiter -> Frontend : ルーム作成画面を表示
Frontend -> Recruiter : 入力フォーム提示

Recruiter -> Frontend : ルーム情報入力（人数, タグ, アトラクション）
Frontend -> Backend : POST /rooms\n{min, max, tags, attraction}
Backend -> DB : INSERT INTO rooms (…)
DB --> Backend : 成功レスポンス
Backend -> Frontend : 201 Created\n{roomId, …}
Frontend -> Recruiter : ルームID表示／共有用QR生成

@enduml
``` 
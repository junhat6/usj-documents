# プロフィール編集シーケンス図

## 概要
このドキュメントでは、USJマッチングアプリにおけるプロフィール編集機能のシーケンスについて説明します。

## アクター
- 参加者／募集者（User）
- フロントエンド（UI）
- バックエンド（API）
- データベース（DB）
- 通知サービス（Push Gateway）

## シーケンスの流れ
1. ユーザーがプロフィール編集画面を開く
2. フロントエンドがバックエンドにプロフィール情報をリクエスト
   - エンドポイント: GET /users/{userId}/profile
3. バックエンドがデータベースからユーザー情報を取得
   - クエリ: SELECT * FROM users WHERE userId=…
4. データベースがユーザー情報を返却
5. バックエンドがフロントエンドにプロフィール情報を返却
   - ステータス: 200 OK
   - レスポンス: プロフィールデータ
6. フロントエンドがユーザーに編集フォームを表示
7. ユーザーがプロフィール情報を変更（name, age, tagsなど）
8. フロントエンドがバックエンドに更新リクエストを送信
   - エンドポイント: PUT /users/{userId}/profile
   - リクエストボディ: updatedProfile
9. バックエンドがデータベースのユーザー情報を更新
   - クエリ: UPDATE users SET … WHERE userId=…
10. データベースが更新結果を返却
11. バックエンドがフロントエンドに更新完了を通知
    - ステータス: 200 OK
    - レスポンス: profileData
12. フロントエンドがユーザーに更新完了メッセージと新しい情報を表示

## シーケンス図
```plantuml
@startuml
actor "参加者／募集者" as User
participant "フロントエンド\n(UI)"       as Frontend
participant "バックエンド\n(API)"         as Backend
database "データベース"                   as DB
participant "通知サービス\n(Push Gateway)" as PushService

User -> Frontend : プロフィール編集画面を開く
Frontend -> Backend : GET /users/{userId}/profile
Backend -> DB : SELECT * FROM users WHERE userId=…
DB --> Backend : {userId, name, age, tags, language, …}
Backend -> Frontend : 200 OK\n{profileData}
Frontend -> User : 編集フォーム表示

User -> Frontend : プロフィール情報変更（name, age, tags…）
Frontend -> Backend : PUT /users/{userId}/profile\n{updatedProfile}
Backend -> DB : UPDATE users SET … WHERE userId=…
DB --> Backend : 更新結果
Backend -> Frontend : 200 OK\n{profileData}
Frontend -> User : 更新完了メッセージ／新情報表示

@enduml
``` 
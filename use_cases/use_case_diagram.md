# 3. ユースケース図（Use Case Diagram）

## 概要
このドキュメントでは、USJマッチングアプリのユースケース図について説明します。

## アクター
- 募集者（Recruiter）
- 参加者（Participant）
- 運営管理者（Admin）

## ユースケース一覧

### 募集者（Recruiter）のユースケース
1. ルーム作成
2. チャット/通知
3. プロフィール閲覧
4. プロフィール編集

### 参加者（Participant）のユースケース
1. チャット/通知
2. フレンド登録
3. プロフィール閲覧
4. ルーム参加
5. プロフィール編集
6. 募集ルーム検索

### 運営管理者（Admin）のユースケース
1. ログ監視

## ユースケース図
```plantuml
@startuml
left to right direction
skinparam packageStyle rectangle

actor "募集者" as Recruiter
actor "参加者" as Participant 
actor "運営管理者" as Admin

rectangle "USJマッチングアプリ" {
    (ルーム作成) as UC1
    (チャット/通知) as UC2
    (フレンド登録) as UC3
    (プロフィール閲覧) as UC4
    (ルーム参加) as UC5
    (プロフィール編集) as UC6
    (募集ルーム検索) as UC7
    (ログ監視) as UC8
}

Recruiter --> UC1
Recruiter --> UC2
Recruiter --> UC4
Recruiter --> UC6

UC2 <-- Participant
UC3 <-- Participant
UC4 <-- Participant
UC5 <-- Participant
UC6 <-- Participant
UC7 <-- Participant

Admin --> UC8

@enduml
``` 
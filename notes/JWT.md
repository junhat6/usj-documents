




この構成で、**「トークンさえあればどのリクエストでもユーザー認証される」**状態になります。


jwt(json web token)の基本構造
```
<ヘッダー>.<ペイロード>.<署名>
```
#### ヘッダー
どんな署名アルゴリズムを使っているかを表す部分
```
{
  "alg": "HS256",  // HMAC + SHA-256
  "typ": "JWT"
}
```
これをbase64エンコード→`eyJhbGciOiJIUzI1NiJ9`
base64とはバイナリデータや日本語などをURLでも安全に扱える文字列に変換する方法

#### ペイロード
実際に「誰なのか」「いつ作られたか」「いつ切れるか」などの情報
```
{
  "sub": "junichi",     // 認証対象（subject）
  "iat": 1749883758,     // 発行時刻（issued at）
  "exp": 1749887358      // 有効期限（expiry）
}
```
これもbase64URLエンコードされます
ほかにもロールや状態情報なども保持できる
```
{
  "role": "admin",              // ロール（管理者 or 一般ユーザー）
  "email_verified": true        // 状態情報（メール認証済み など）
}
```
なぜ必要なのか？
サーバーはトークンからユーザー情報を取り出す。このときにDBにアクセスはしない。
つまりDBアクセス不要で高速＆ステートレスに認証できる。
ロールもtoken情報だけでアクセス制御できるのも魅力DBにいちいちアクセスしなくていい
ユーザー状態も同様。
ただし、ペイロードの情報はbase64エンコードしただけなので、エンコードの反対のでコードをすれば内容が確認できる。
なので、パスワード、クレカ情報などの木陸奥情報はおペイロードには入れない。

|項目|内容|
|---|---|
|目的|ユーザー情報や権限をトークン内に持たせるため|
|メリット|サーバーが毎回DBを見ずにユーザーを認識できる|
|含める情報|`sub`, `iat`, `exp`, `role`, `email_verified`など|
|注意点|暗号化されていないので機密情報はNG|
#### 署名
base64URLエンコードされたheader,payloadの文字列とサーバー側に保存されている秘密鍵から署名を作る。
```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  秘密鍵
)
```
この署名のおかげでトークンが解散されていないか確認可能。
例えばpayloadで権限を一般ー＞管理者に変更したbase64をつけてログインしても署名と一致しないから改ざんされていることがわかる。




## 🧱 まずは前提を決めよう

json

コピーする編集する

`// ヘッダー（Header） {   "alg": "HS256",   "typ": "JWT" }  // ペイロード（Payload） {   "sub": "junichi" }  // 秘密鍵（Secret Key） secret = "my-secret-key"`

---

## ① ヘッダーとペイロードをBase64URLエンコード

|種類|JSON文字列|Base64URL|
|---|---|---|
|ヘッダー|`{"alg":"HS256","typ":"JWT"}`|`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9`|
|ペイロード|`{"sub":"junichi"}`|`eyJzdWIiOiJqdW5pY2hpIn0`|

💡 Base64URLとは：

- Base64と似てるけど `+` → `-`, `/` → `_`, `=` を取り除く形式
    

---

## ② 署名用文字列を作る

makefile

コピーする編集する

`dataToSign = header + "." + payload            = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJqdW5pY2hpIn0"`

---

## ③ 署名（Signature）をHMAC-SHA256で作る

plaintext

コピーする編集する

`signature = HMACSHA256(   "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJqdW5pY2hpIn0",   "my-secret-key" )`

### 🧪 実際の結果：

（オンライン HMAC 生成ツールやKotlinで確認すると）

plaintext

コピーする編集する

`16進（Hex） →   c6 0a 99 6b ... 8b 85 7a b0  Base64URLエンコード → xgqZa6RjzhAd0TxmJpvyZDuLAtMZggU4Z3Nu5Phw8kM`

---

## ④ 完成したJWTトークン

コピーする編集する

`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJqdW5pY2hpIn0.xgqZa6RjzhAd0TxmJpvyZDuLAtMZggU4Z3Nu5Phw8kM`

---

## ✅ サーバーはどう検証する？

1. トークンを3つに分割（ヘッダー・ペイロード・署名）
    
2. ヘッダーとペイロードを `Base64URL.decode()` して確認
    
3. 自分の秘密鍵 `"my-secret-key"` で `HMACSHA256` を再計算
    
4. 計算した署名と受け取った署名が一致するかチェック
    

➡️ 一致すれば「改ざんされていない」  
➡️ 一致しなければ「誰かが中身をいじった！」と判定されます

## 🎯 まとめ：手動でJWTを作る流れ

|ステップ|内容|
|---|---|
|①|JSONヘッダー・ペイロードをBase64URLエンコード|
|②|`header.payload` を連結|
|③|`HMACSHA256(..., secretKey)` で署名作成|
|④|`header.payload.signature` でJWT完成|
|⑤|検証時は秘密鍵で再計算して照合する|

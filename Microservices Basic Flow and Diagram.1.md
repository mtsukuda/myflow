
# Microservices Architecture
Microservices の基本構成図と基本フローを記述します。

_<font size="-1">このページは [StackEditor](https://stackedit.io/) で作成・編集されています。</font>_

## 基本構成図
Microservices の基本構成図。User は API Gateway を通じてアクセス、明示的なログイン（ログアウト）リクエスト以外は各サービスが一旦リクエストを受け取り、内部的に gRPC で Login Service に問い合わせ、ログイン状態かどうかを確認した上で、各サービスを継続するかどうかを決定する。
```mermaid
%% Microservices の基本構成図
graph TD
  USER[User]
    USER-->GATEWAY[API Gateway]
    GATEWAY-->LOGIN[Login Service]
    GATEWAY-->ACCOUNT(Account Service)
    GATEWAY-->WALLET(Wallet Service)
    GATEWAY-->OTHER1(Other Service 1)
    GATEWAY-->OTHER2(Other Service 2)
    ACCOUNT---LOGIN
    WALLET---LOGIN
    OTHER1---LOGIN
    OTHER2---LOGIN
```

## サービスシーケンス
このセクションでは各サービスシーケンスを記述します。

### ログイン（Login）サービス
```mermaid
%% Login Service シーケンス
  sequenceDiagram
    User->>API Gateway: Login Request
    API Gateway->>Login Service: Login Request
      alt is exist & correct
        Login Service-->>User: login succeed :D
      else is unexsit
        Login Service--xUser: login failer
      end
```

### アカウント（Account）サービス
```mermaid
%% Account Service シーケンス
  sequenceDiagram
    User->>API Gateway: Account Data Request
    API Gateway->>Account Service: Account Data Request
    Account Service->>Login Service: Login Check (gRPC)
      alt is NOT Logged in
        Login Service-->>User: Redirect Login
      else is Unexsit User
        Login Service--xUser: Kick it out!
      end
      Login Service-->>Account Service: Login Check OK
      Account Service-->>User: Account Data
```

### ウォレット（Wallet）サービス
```mermaid
%% Account Service シーケンス
  sequenceDiagram
    User->>API Gateway: Wallet Data Request
    API Gateway->>Wallet Service: Wallet Data Request
    Wallet Service->>Login Service: Login Check (gRPC)
      alt is NOT Logged in
        Login Service-->>User: Redirect Login
      else is Unexsit User
        Login Service--xUser: Kick it out!
      end
      Login Service-->>Wallet Service: Login Check OK
      Wallet Service-->>User: Wallet Data
```

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTY3MDM2NTkzMF19
-->
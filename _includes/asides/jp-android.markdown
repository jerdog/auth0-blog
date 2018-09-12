## 補足：Android アプリを Auth0 でセキュアにする

アプリケーションを Auth0 でセキュアにすることは非常に簡単で、たくさんの素晴らしい機能を提供します。Auth0 を使うと、数行のコード行を書くだけで、強固な[ID 管理ソリューション](https://auth0.com/user-management)、[シングル サインオン](https://auth0.com/docs/sso/single-sign-on)、[ソーシャル ID プロバイダー（Facebook、GitHub、Twitter など）](https://auth0.com/docs/identityproviders)のサポート、および[エンタープライズ ID プロバイダー（Active Directory、LDAP、SAML、カスタムなど）](https://auth0.com/enterprise)のサポートを得ることができます。

以下のセクションでは、Android アプリをセキュアにする Auth0 を使用する方法を学んでいきます。ご覧のように、そのプロセスはシンプルで素早くできます。

### 依存関係

Android アプリを Auth0 でセキュアにするには、[Auth0.Android](https://github.com/auth0/Auth0.Android) ライブラリをインポートするだけです。このライブラリはツールキットで、基本的な多数の Auth0 API 機能と素晴らしい方法で通信することができます。

このライブラリをインポートするには、build.gradle ファイルに次の依存関係を含みます。

```groovy
dependencies {
    compile 'com.auth0.android:auth0:1.12.0'
}
```

その後、アプリの AndroidManifest.xml ファイルを開き、次のアクセス許可を追加します。

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

### クライアントを作成する

ライブラリをインポートしてアクセス許可を追加した後、新しいクライアントアプリケーションを Auth0 ダッシュボードに作成します。ところで、Auth0 アカウントをお持ちでない方は、[**無料**](https://auth0.com/signup)[**アカウントを作成する**](https://auth0.com/signup) でアカウントを作成されることをお勧めします。

Auth0 ダッシュボードで、_クライアント_ に移動し、_クライアントを作成する_ ボタンをクリックします。表示のフォームで、クライアントの名前を定義し、その _ネイティブ_ タイプを選択します。その後、_作成_ ボタンを押します。これで、次のようなスクリーンが表示されます。

![Android application on Auth0's dashboard](https://cdn2.auth0.com/docs/media/articles/angularjs/app_dashboard.png)

このスクリーンで、コールバック URL を構成します。これは、Auth0 がユーザーを認証した後にリダイレクトするアプリケーションの URL です。

クライアント _設定_ ページの _許可されたコールバック URL_ フィールドにあるアプリのコールバック URL をホワイトリストします。コールバック URL を設定しなければ、ユーザーがログインしたときに不一致のエラーが表示されます。

```bash
demo://bkrebs.auth0.com/android/OUR_APP_PACKAGE_NAME/callback
```

OUR\_APP\_PACKAGE\_NAME とアプリケーションのパッケージ名を置換することを忘れないようにしましょう。この名前は app/build.gradle ファイルの applicationId 属性にあります。

### 資格情報を設定する

アプリケーションは Auth0 と通信するために、クライアントの詳細情報が必要です。この詳細情報は [Auth0 ダッシュボード](https://manage.auth0.com/) にあるクライアントの _設定_ セクションにあります。

次の情報が必要です。

- クライアント ID
- ドメイン

これらの値は将来、変更する必要があるかもしれないので、ハードコードしないことをお勧めします。代わりに、値を定義するときは @string/com\_auth0\_domain のような文字列リソースを使いましょう。

res/values/strings.xml ファイルを次のように編集しましょう。

```xml
<resources>
    <string name="com_auth0_client_id">2qu4Cxt4h2x9In7Cj0s7Zg5FxhKpjooK</string>
    <string name="com_auth0_domain">bkrebs.auth0.com</string>
</resources>
```

これらの値はクライアントの _設定_ セクションにある値と置換します。

### Android ログイン

ログイン機能をアプリに実装するには、SDK が必要とするマニフェスト プレースホルダーを追加します。これらプレースホルダーは前に構成された認証コールバック URL をキャプチャする intent-filter を定義するために内部で使用します。

マニフェスト プレースホルダーを追加するには、次の行を追加しましょう。

```java
private void login() {
    Auth0 auth0 = new Auth0(this);
    auth0.setOIDCConformant(true);
    WebAuthProvider.init(auth0)
                  .withScheme("demo")
                  .withAudience(String.format("https://%s/userinfo", getString(R.string.com_auth0_domain)))
                  .start(MainActivity.this, new AuthCallback() {
                      @Override
                      public void onFailure(@NonNull Dialog dialog) {
                        // Show error Dialog to user
                      }

                      @Override
                      public void onFailure(AuthenticationException exception) {
                        // Show error to user
                      }

                      @Override
                      public void onSuccess(@NonNull Credentials credentials) {
                          // Store credentials
                          // Navigate to your main activity
                      }
                });
}
```

その後、Android Studio 内で **Gradle ファイルでプロジェクトを同期する** を実行するか、コマンドラインから ./gradlew clean assembleDebug を実行します。
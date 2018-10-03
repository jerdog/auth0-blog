---
layout: post
title: "Vue.js で最新のアプリケーションを構築する"
metatitle: "Vue.js で最新のアプリケーションを構築する"
description: "本書では、Django と Vue.js でフルスタック アプリケーションを着実に作成しましょう"
metadescription: "本書では、Django と Vue.js でフルスタック アプリケーションを着実に作成しましょう"
date: 2018-05-22 17:28
category: Technical Guide, Stack, Fullstack
author:
  name: "Ahmed Bouchefra"
  url: "https://www.techiediaries.com"
  mail: "techiediaries9@gmail.com"
  avatar: "https://cdn.auth0.com/blog/guest-author/ahmed-bouchefra.jpg"
design:
  bg_color: "#092e20"
  image: https://cdn.auth0.com/blog/building-modern-applications-with-django-and-vuejs/logo.png
tags:
- django
- vue.js
- python
- javascript
- rest
- restful
- api
- auth0
- identity
related:
- 2016-09-29-jp-angular-2-authentication
- 2017-04-18-jp-vuejs2-authentication-tutorial
- 2017-02-21-jp-reactjs-authentication-tutorial
lang: ja
alternate_locale_en: building-modern-applications-with-django-and-vuejs
---

**TL;DR：** 本書では、Django、Django REST フレームワーク、Vue.js を使用して、REST API バックエンドと Vue.js フロントエンドでアプリケーションを開発します。API は Axios（HTTP クライアントライブラリ）の助けで Vue.js フロントエンドで消費され、JWT 認証は Auth0 で処理されます。

プロジェクトの要件すべてをインストールしてから、Django と Vue.js をサブプロジェクトとしてブートストラップします。

これから作成するデモプロジェクトの最終ソースコードをこの [GitHub レポジトリ](https://github.com/auth0-blog/django-vue.js)で見つけることができます。

---

## 要約

本書は次のセクションに分かれています。

- Python および Django 入門
- js および Vue.js 機能の入門
- バックエンド プロジェクトをブートストラップする
- フロントエンド プロジェクトをブートストラップする
- JSON Web トークン入門
- Django を Auth0 と統合する
- Auth0 を Vue.js と統合する
- 結論および次のステップ

## Python および Django 入門

[Python](https://www.python.org/) は汎用プログラミング言語で、現在最も人気の高いプログラミング言語です。読み取り可能で効率的、[簡単に学べる](http://lifehacker.com/five-best-programming-languages-for-first-time-learners-1494256243/1497409477)言語です。Python は Linux、Windows、Mac などの主要オペレーティングシステムで利用可能な高移植性言語でもあります。

Python には、Web 開発者の生産性を高め、プロトタイプを瞬時に構築できる開発者にとって素晴らしい多数のツールやフレームワークがあります。Web 開発者にとって最も人気の高い Python ランドスケープは [Django](https://www.djangoproject.com/) です。Django は開発者が瞬時にプロトコルを構築できるため、_厳しい締切がある完璧主義者のフレームワーク_ として告知されています。

Django は Python のパワーを使って SQL の１行.を書かなくてもデータベース要件のモデルを作ることができるクラスベースのビュー、強力な ORM のような素晴らしい機能を開発者に提供します。Django ORM はデータベースで作業するときのあらゆる複雑さを取り除き、最も重要なことはクライアントが使用するデータベースシステムを決定していないときでも対応できることです。SQLite データベース（特別なインストールは必要ありません）を使って開発を始めることができ、クライアントが後で、使用する適切な RDBMS（リレーショナル データベース管理システム）を決定したときに適切なデータベースシステムに変えることができます。

Django も、後でデータベース構造を変更するときにデータを失わずにデータベースを安全に移行できる強力な移行システムが同梱されています。

Django を使用するもうひとつの強みは大きな進化するコミュニティがあることです。このコミュニティは Web 開発者たちのよくある問題を対象にしたオープンソースパッケージを作成したので、プロジェクトを構築するときに車輪を再発明する必要がありません。

Django には、コミュニティが作成したチュートリアルのセットで補足されたフレームワークのすべての機能を説明した優れた総括的なドキュメンテーションがあります。

Django は初心者にも適しており、Web アプリケーションを構築するのにフレームワークの各機能に熟練する必要はありません。

{% include jp-tweet_quote.html quote_text="Django は初心者にも適しており、Web アプリケーションを構築するのにフレームワークの各機能に熟練する必要はありません。" %}

## Vue.js および Vue.js 機能の入門

[Vue.js は JavaScript でユーザーインタフェイスを構築するためのプログレッシブなフレームワークです](https://vuejs.org/)。アプリケーションのビューレイヤーで Vue.js を使用したり、その他のフロントエンド [ツール](https://vuejs.org/v2/guide/single-file-components.html) とを合わせてシングルページ アプリケーション(SPA)を構築するために使用したりできます。

Vue.js は Angular.js と React の両方の最良のものをひとつのライブラリにしました。例えば、丁度 React のように Vue.js は[仮想 DOM](https://vuejsdevelopers.com/2017/02/21/vue-js-virtual-dom/) とコンポーネント ベースのアプローチを使用します。また、同様の構文を Angular.js ディレクティブ（例 `v-if` や `v-for`）にも使用します。

Vue.js には次のような多数の機能があります。

- 仮想 DOM
- 再有効化およびコンポーザブルなコンポーネントの表示
- 優れた性能
- [Weex](https://weex.incubator.apache.org/)と [NativeScript](https://github.com/rigor789/nativescript-vue)のおかげの、iOS と Android 向けネイティブレンダリング

## バックエンド プロジェクトをブートストラップする

バックエンドプロジェクトを作成する前に、開発マシンに要件をインストールする必要があります。Django の場合、[Python 3](https://www.python.org/download/releases/3.0/)、[PIP](https://pypi.python.org/pypi/pip)、[`venv`](https://docs.python.org/3/library/venv.html) をインストールする必要があります。

Django で開発する際には完全なデータベースの管理システムをインストールする必要はありません。特別なインストールを必要としないファイルベースのデータベースを使用できる SQLite データベースを使用できます。

### 要件をインストールする

Python 3 から始めましょう。Python 3 がすでにマシンにインストールされているかもしれません。インストールされていなくても、そのプロセスは簡単です。[公式ダウンロードページ](https://www.python.org/downloads/)に移動し、オペレーティングシステムのインストーラを選びます。

端末またはコマンド プロンプトから次のコマンドを実行させて、Python 3 がインストールされているかを確認します。

```bash
python3 --version
```

`venv` モジュール（Python 3 標準ライブラリの一部）はプロジェクトに軽量仮想環境を作ることができます。これは Python プロジェクトごとに独立した環境を持つことができます（例えば、依存関係はプロジェクトごとにシステム全体のパッケージから独立しています）。

独立した環境を設定することでインストールした Python パッケージをよりコントロールできます。競合を心配することなく同じパッケージの別のバージョンを持つことができるので、別のパッケージバージョンの別の Python プロジェクトで作業することが可能になります。また、各環境はそれ独自の Python バイナリを持つことができるので、別の Python バージョンの各種環境を作ることができます。

仮想環境を作成したら、`venv` が最新の Python バイナリをインストールする処理します。

次に、既定で Python Package Index （[PyPI](https://pypi.python.org/pypi)）からパッケージをインストールする Python のパッケージ マネージャー、[`pip`](https://packaging.python.org/key_projects/#pip) をインストールする必要があります。

次のコマンドを実行して開発マシンに `pip` がインストールされているか確認できます。

```bash
# 環境によって、pip または pip3
pip --version
```

公式 [python.org](https://www.python.org/downloads) インストーラを使って（または MAC の Homebrew を通して） Python がインストールされていれば、`pip` がインストールされているはずです。また、`venv` 環境の内側であれば、pip はすでにインストールされています。Linux の場合は、別にインストールする必要があるかもしれません。それが環境であれば、この[ガイド](https://packaging.python.org/guides/installing-using-linux-tools/)と[このページ](https://pip.pypa.io/en/stable/installing/)をご覧ください。

### 仮想環境を作る

すべてのバックエンド要件をインストールしたら、プロジェクトの依存関係をインストールする仮想環境を作っていきます。端末に移動して次のコマンドを実行します。

```bash
# プロジェクトのディレクトリを作成する
mkdir django-vue

# カーソルをそれに移動する
cd django-vue

# 仮想環境をそれで初期化する
python3 -m venv ./
```

次に、以下の `source` を使って仮想環境をアクティブにする必要があります。

```bash
source ./bin/activate
```

### Django をブートストラップする

ここで、`pip` を使って次のように Django をインストールします。

```bash
pip install django
```

次のステップはこのコマンドを使って Django プロジェクトを作成します。

```bash
django-admin startproject djangovue .
```

では、次のように [`catalog`](https://github.com/redsolution/django-catalog) で Django プロジェクトを整理する次のコマンドを実行します。

```bash
python manage.py startapp catalog
```

また、このアプリをインストール済みアプリのリストに追加する必要もあります。`settings.py` ファイルを開き、アプリを次のように追加します。

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'catalog'
]
```

それからデータベースを移行し、次のように開発サーバーを開始します。

```bash
python manage.py migrate
python manage.py runserver
```

Web ブラウザーの [http://127.0.0.1:8000](http://127.0.0.1:8000/) に移動したら、次のホームページが表示されます。

![Django localhost starter page](https://cdn.auth0.com/blog/django-vuejs/django-basic-app.png)

## フロントエンド プロジェクトをブートストラップする

Vue.js には、開発者がすばやく SPA を生成できる [CLI ユーティリティ](https://github.com/vuejs/vue-cli)があります。この CLI は最新フロントエンド ワークフロー向けの事前に構成済みのビルドをセットアップし、基本的なプロジェクトの定型をスキャフォールディングするのにわずか数分しかかかりません。この定型はホットリローディング、lint-on-save、生産準備完了ビルドの機能で出荷されます。

Vue.js CLI をインストールする前に、Node.js 6.x 以降、または NPM バージョン 3 以降で開発環境にする必要があります。この二つは[公式 Node.js Web サイト](https://nodejs.org/en/download/)に移動し、オペレーティングシステムに適切なインストーラをダウンロードしてインストールできます。開発マシンには、[NVM](https://github.com/creationix/nvm) の使用をお勧めします。

次は、端末またはコマンド プロンプトに移動し、次のコマンドを実行します。

```bash
npm install -g vue-cli
```

`sudo` を NPM コマンドに追加し、[NPM 構成](https://docs.npmjs.com/getting-started/fixing-npm-permissions)によってはコマンドをグローバルにインストールする必要があるかもしれません。

Vue.js CLI をインストールした後、それを使って [Webpack テンプレート](https://github.com/vuejs-templates/webpack)をベースに新しい Vue.js アプリケーションを生成します。Django プロジェクトのルート内で、次のコマンドを実行します。

```bash
# Webpack テンプレートで Vue.js を初期化する
vue init webpack frontend
```

Vue.js はプロジェクトを作成している間に、たくさんの質問をします。次のように回答します。

* *Project name:* フロントエンド
* *Project description:* _js プロジェクト
* *Author:* _名前 [name@somewhere.com](mailto:your.name@somewhere.com)
* *Vue build:* Runtime + Compiler: recommended for most users
* *Install vue-router?* Yes
* *Use ESLint to lint your code?* Yes
* *Pick an ESLint preset:* Standard
* *Set up unit tests:* No
* *Setup e2e tests with Nightwatch?* No
* *Should we run `npm install` for you after the project has been created? (recommended)* Yes, use NPM

それから、初期 Vue.js プロジェクトを実行するには、次のコードを実行します。

```bash
# 作業ディレクトリをフロントエンドに変える
cd frontend

# NPM dev スクリプトを実行する
npm run dev
```

これらを実行したら、[http://127.0.0.1:8080](http://127.0.0.1:8080/) に移動してブラウザーの Vue.js アプリケーションにアクセスできます。

![Vue.js localhost basic application](https://cdn.auth0.com/blog/django-vuejs/vuejs-basic-app.png)

## JWT 入門

[JWT](https://jwt.io/) は JSON Web トークンを表し、要求を含む JSON (JavaScript Object Notation) オブジェクトです。[RFC 7519](https://tools.ietf.org/html/rfc7519) の正式な定義は次のとおりです。

JSON Web トークン (JWT) は２つのパーティ間で転送されるクレームを表すための、コンパクトで URL セーフな手段です。JWT のクレームは JSON Web 署名 (JWS) 構造のペイロードとして、または JSON Web 暗号化 (JWE) 構造のプレーンテキストとして使用される JSON オブジェクトとしてエンコードされ、そのクレームはメッセージ認証コード (MAC) や暗号化されてデジタル署名されたり整合性が保護されるようになります。

JWT はヘッダー、ペイロード、署名の３つの部分からなります。ヘッダーはペイロードやヘッダー（例 `RS256`）を署名している間に使用されるアルゴリズムなどの情報を含み、ペイロードはそのクレームを保留します。JWT についての詳細は、[このレファレンスをご覧ください](https://auth0.com/docs/jwt)。

## Auth0 API を作成する

アプリケーションで Auth0 認証を使用する前に、Auth0 アカウントを作る必要があります。まだアカウントをお持ちでない方は[こちらから無料アカウントを登録してください](https://auth0.com/signup)。

登録プロセスの一環として、 **Auth0 ドメイン** を作成するように依頼されます。それは `django-vue-js.auth0.com` などです。この詳細を学びたい方は[**基本的なドキュメンテーションをご覧ください**](https://auth0.com/docs/getting-started/the-basics)。Auth0 ドメインについてやその使い方について説明しています。

その後、Auth0 ダッシュボードの [API セクション](https://manage.auth0.com/#/apis) に移動し、_API の作成_ ボタンをクリックします。

フォームが表示されますので、名前、識別子、署名アルゴリズムなど API の詳細を入力してください。

![Auth0 Create API Form](https://cdn.auth0.com/blog/django-vuejs/auth0-api-form.png)

このフォームを入力するとき、次のプロパティを使用できます。

- _名前：_Django Vue.js API
- _識別子：_ http://djangovuejs.digituz.com.br
- _署名アルゴリズム：_RS256

その後、_作成_ ボタンをクリックします。すると、API 設定をカスタマイズできるページが表示されます。

これだけです！これで、Django アプリケーションと Auth0 を統合する準備ができました。

## Django を Auth0 と統合する

このセクションでは、Auth0 で Django REST API をセキュアにする方法を学んでいきます。

次は、API を構築しますが、その前に Auth0 を使って JWT 認証をバックエンドに追加します。

このために、[Django REST フレームワーク](http://www.django-rest-framework.org/)をインストールする必要があります。また、JWT 認証を処理するために `djangorestframework-jwt` パッケージもインストールする必要があります。JWT トークンを署名するために Auth0 を使って `djangorestframework-jwt` をセットアップします。

それをするには、端末に戻り、以前作成した仮想環境がアクティブ化されていることを確認し、それからプロジェクトルートで次のコマンドを実行して `pip` を使ってすべてのパッケージをインストールします。

```bash
pip install djangorestframework
pip install djangorestframework-jwt
pip install cryptography
pip install python-jose
```

> 暗号パッケージをインストールするときに問題が発生したら、`python3-dev` をインストールする必要があるかもしれません。

次に、`settings.py` のインストール済みアプリのリストに３つのパッケージを追加します。

```python
INSTALLED_APPS = [
    #...
    'rest_framework',
    'rest_framework_jwt'
]
```

また、`REST_FRAMEWORK` アプリケーションの定義を次のように `settings.py` に追加する必要もあります。

```python
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': (
        'rest_framework.permissions.IsAuthenticated',
    ),
    'DEFAULT_AUTHENTICATION_CLASSES': (
       'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
    ),
}
```

次のステップは、次の依存関係を `settings.py`ファイルにインポートします。

```python
import json
from six.moves.urllib import request
from cryptography.x509 import load_pem_x509_certificate
from cryptography.hazmat.backends import default_backend
```

それから、次のコードをこのファイルの終わりに追加します。

```python
AUTH0_DOMAIN = '<YOUR_AUTH0_DOMAIN>'
API_IDENTIFIER = '<YOUR_API_IDENTIFIER>'
PUBLIC_KEY = None
JWT_ISSUER = None

if AUTH0_DOMAIN:
    jsonurl = request.urlopen('https://' + AUTH0_DOMAIN + '/.well-known/jwks.json')
    jwks = json.loads(jsonurl.read().decode('utf-8'))
    cert = '-----BEGIN CERTIFICATE-----\n' + jwks['keys'][0]['x5c'][0] + '\n-----END CERTIFICATE-----'
    certificate = load_pem_x509_certificate(cert.encode('utf-8'), default_backend())
    PUBLIC_KEY = certificate.public_key()
    JWT_ISSUER = 'https://' + AUTH0_DOMAIN + '/'

def jwt_get_username_from_payload_handler(payload):
    return 'auth0user'

JWT_AUTH = {
    'JWT_PAYLOAD_GET_USERNAME_HANDLER': jwt_get_username_from_payload_handler,
    'JWT_PUBLIC_KEY': PUBLIC_KEY,
    'JWT_ALGORITHM': 'RS256',
    'JWT_AUDIENCE': API_IDENTIFIER,
    'JWT_ISSUER': JWT_ISSUER,
    'JWT_AUTH_HEADER_PREFIX': 'Bearer',
}
```

`<YOUR_AUTH0_DOMAIN>` を自分の Auth0 ドメインに置き換え、`<YOUR_API_IDENTIFIER>` を Auth0 API を作成しているときに使用した識別子と置き換えます。

公開キーは `request.urlopen()` メソッドを使って、`https://YOUR_AUTH0_DOMAIN/.well-known/jwks.json` から取得します。このキーは `JWT_AUTH` （`djangorestframework-jwt` の設定オブジェクト）の `PUBLIC_KEY` に署名されます。

`JWT_ISSUER` は https でプレフィックスされた Auth0 ドメイン名です。

`JWT_ALGORITHM` は  **RS256**  に設定される必要があります。つまり、Auth0 API も必ず  **RS256** を使用する必要があります。

`JWT_AUTH_HEADER_PREFIX` は  **ベアラー**  に設定されます。これはほとんどのフレームワークが使用する既定の接頭語です（`djangorestframework-jwt` は既定で  **JWT**  接頭語を使用します）。

また、`JWT_PAYLOAD_GET_USERNAME_HANDLER` をカスタムメソッドに設定しました。これは **access\_token**  ペイロードから Django ユーザーにユーザ名をマップするために、`djangorestframework-jwt` にカスタムメソッドを使用するように示します。

ほとんどの場合、Auth0 はプロファイルなど高度な機能を含むすべてをハンドルするので、ユーザーをデータベースに格納する必要があります。作成する一般的な（フェイクでも良い）ユーザーが存在してそれをすべての Auth0 ユーザーにマップするかをチェックするカスタム関数を使用できます。

それをするには、アプリケーションに移動し、_auth0user_ などのように有効ユーザー名でユーザーを作ります。Django 管理インターフェイスを介して実行するか、次のコードを追加して移行で実行します。

```bash
# 空のデータ移行を作るc
python manage.py makemigrations --empty catalog
```

これは `catalog/migrations/0001_initial.py` ファイルを生成します。このファイルでは、コンテンツを次のコードに置き換えることができます。

```python
from django.db import migrations
from django.conf import settings

def create_data(apps, schema_editor):
    User = apps.get_model(settings.AUTH_USER_MODEL)
    user = User(pk=1, username="auth0user", is_active=True , email="admin@techiediaries.com")
    user.save()

class Migration(migrations.Migration):
    dependencies = [
    ]
    operations = [
        migrations.RunPython(create_data),
    ]
```

> 移行についての詳細は、[公式ドキュメント](https://docs.djangoproject.com/en/2.0/topics/migrations/#data-migrations)をご覧ください。

次は、移行を実行して次のように初期データを作ります。

```bash
python manage.py migrate
```

これは JWT ペイロードを _auth0user_ ユーザーでマップするカスタムメソッドです。

```python
def jwt_get_username_from_payload_handler(payload):
    return 'auth0user'
```

既存ユーザーのユーザー名は `djangorestframework-jwt` にそのユーザーを正常に認証させるという唯一の目的以外に使用しないので、それをデータベースに返すことができることに注意してください。

### Django ビューを追加する

Auth0 を通して JWT 認証が機能するようにするには、２つのビュー関数を `catalog/views.py` に追加します。

```python
from rest_framework.decorators import api_view
from django.http import HttpResponse


def public(request):
    return HttpResponse("You don't need to be authenticated to see this")


@api_view(['GET'])
def private(request):
    return HttpResponse("You should not see this message if not authenticated!")
```

また、対応する URL を `urls.py` に作成するようにします。このファイルのコンテンツ全体を次と置き換えます。

```python
from django.conf.urls import url
from catalog import views


urlpatterns = [
    url(r'^api/public/', views.public),
    url(r'^api/private/', views.private)
]
```

Django プロジェクトを今、返せば (`python manage.py runserver`)、[http://127.0.0.1:8000/api/public/](http://127.0.0.1:8000/api/public/) URL のみへのアクセスになります。他のもの (`/api/private/`) にはアクセスされるアクセス トークンが必要です。

### Django で CORS を有効にする

Django エンドポイントを使用する Vue.js アプリケーションを作るとき、Django プロジェクトで CORS を有効にする必要があります。そのためには、次のように[`django-cors-headers`](https://github.com/ottoyiu/django-cors-headers)[ ユーティリティをインストール](https://github.com/ottoyiu/django-cors-headers)します。

```bash
pip install django-cors-headers
```

その後、このユーティリティを次のように `settings.py` ファイルのインストール済みアプリに追加します。

```python
INSTALLED_APPS = [
    # ...
    'corsheaders',
]
```

それから、`CorsMiddleware` を次のように同じファイルに追加します。

```python
MIDDLEWARE = [  # Or MIDDLEWARE_CLASSES on Django < 1.10
    # ...
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
]
```

最後にすることは、Django アプリケーションで承認される元を構成することです。

```python
CORS_ORIGIN_WHITELIST = (
    'localhost:8080',
)
```

この構成を最後のアイテムとして `settings.py` ファイルに追加します。

## Auth0 を Vue.js フロントエンドと統合する

このセクションでは、どのようにして Auth0 認証をフロントエンド Vue.js アプリケーションに追加するかを見ていきます。また、Auth0 から取得して、アクセストークンを使用するボタンも追加します。これで、ユーザーは `/api/private/` エンドポイントからメッセージをフェッチできます。

### Auth0 クライアントを作成する

Vue.js フロントエンドアプリケーションを Auth0 で表すには、Auth0 クライアントを作る必要があります。そのためには、[Auth0 ダッシュボード](https://manage.auth0.com/)に移動し、_新規クライアント_ ボタンをクリックします。

クライアントの名前とクライアントのタイプを入力できるページが表示されます。アプリケーションの名前には _Django Vue.js アプリ_ を入力し、_シングルページ Web アプリケーション_ タイプを選択します。

![Creating an Auth0 Client](https://cdn.auth0.com/blog/django-vuejs/auth0-client.png)

このフォームを入力したら、_作成_ ボタンをクリックします。すると、_設定_ タブがあるページにリダイレクトされます。

### コールバック URL を構成する

新規 Auth0 クライアントの _許可されたコールバック URL_ フィールドでアプリ (`http://localhost:8080/`) のコールバック URL をホワイトリストする必要があります。これは _設定_ タブで実行します。このタブで、上述のフィールドを見つけ、`http://localhost:8080/` をそれに追加します。それから _保存_ ボタンをクリックします。

### 認証サービスを作る

では、Vue.js アプリケーションで Auth0 を統合ていきましょう。そのためには、[`auth0.js`](https://github.com/auth0/auth0.js) と `eventemitter3` ライブラリを次のようにインストールする必要があります。

```bash
# ディレクトリをフロントエンドに変更する
cd frontend

# 依存関係をインストールする
npm install --save auth0-js
npm install --save eventemitter3
```

Axios をインストールして AJAX 要求を Django バックエンドに送ります。

```bash
npm install --save axios
```

これら３つの依存関係をインストールしたら、`./frontend/src/auth/` の下に新規ファイルを作ります。このファイルを `AuthService.js` と呼び、次のコードを追加します。

```js
import auth0 from 'auth0-js'
import EventEmitter from 'eventemitter3'
import router from './../router'

export default class AuthService {
  authenticated = this.isAuthenticated();
  authNotifier = new EventEmitter();

  constructor () {
    this.login = this.login.bind(this)
    this.setSession = this.setSession.bind(this)
    this.logout = this.logout.bind(this)
    this.isAuthenticated = this.isAuthenticated.bind(this)
    this.handleAuthentication = this.handleAuthentication.bind(this)
  }

 // auth0.WebAuth のインスタンスをAPI および Client
 // 資格情報で作る
  auth0 = new auth0.WebAuth({
    domain: '<YOUR_AUTH0_DOMAIN>',
    clientID: '<YOUR_CLIENT_ID>',
    redirectUri: '<YOUR_CALLBACK_URL>',
    audience: '<YOUR_AUDIENCE>',
    responseType: 'token id_token',
    scope: 'openid profile'
  });

 // このメソッドは Auth0 ログインページが
 // トリガーされる authorize() メソッドを呼び出す
  login () {
    this.auth0.authorize()
  }

 // このメソッドはコールバック URL から認証情報を得るために
 // Auth0 の parseHash() メソッドを呼び出す
  handleAuthentication () {
    this.auth0.parseHash((err, authResult) => {
      if (authResult && authResult.accessToken && authResult.idToken) {
        this.setSession(authResult)
      } else if (err) {
        console.log(err)
        alert(`Error: ${err.error}. Check the console for further details.`)
      }
      router.replace('/')
    })
  }

  // ユーザーの access\_token、id\_token、および access\_token が
  // ローカル ストレージで期限切れになる時間を格納する
  setSession (authResult) {
    // アクセス トークンが時間切れになる時間を設定し
    let expiresAt = JSON.stringify(
      authResult.expiresIn * 1000 + new Date().getTime()
    )
    localStorage.setItem('access_token', authResult.accessToken)
    localStorage.setItem('id_token', authResult.idToken)
    localStorage.setItem('expires_at', expiresAt)
    this.authNotifier.emit('authChange', {authenticated: true})
  }

  // そのアクセスと ID トークンをローカル ストレージから削除し、
  // authChange イベントを発行し
  logout () {
    localStorage.removeItem('access_token')
    localStorage.removeItem('id_token')
    localStorage.removeItem('expires_at')
    this.authNotifier.emit('authChange', false)
    // ホームルートに移動する
    router.replace('/')
  }

  // そのユーザーが認証されているか確認する
  isAuthenticated () {
    // 現在の時間がアクセストークンの
    // 有効期限を過ぎているかを確認する
    let expiresAt = JSON.parse(localStorage.getItem('expires_at'))
    return new Date().getTime() < expiresAt
  }

  // アクセストークンを得る静的メソッド
  static getAuthToken () {
    return localStorage.getItem('access_token')
  }

  // ユーザー プロファイルを得るメソッド
  getUserProfile (cb) {
    const accessToken = localStorage.getItem('access_token')
    if (accessToken) return this.auth0.client.userInfo(accessToken, cb)
    else return null
  }
}
```

`<YOUR_AUTH0_DOMAIN>`、`<YOUR_CLIENT_ID>`、`<YOUR_CALLBACK_URL>`、および `<YOUR_AUDIENCE>` をクライアントと API 設定からの値と置き換えます。対象ユーザー プロパティは Auth0 API の識別子を参照します（手順に従うと `http://djangovuejs.digituz.com.br` になります）。

### ホームページを作成する

これから、Vue.js ホームページをリファクタ―してユーザーが Auth0 を介して認証し、公共メッセージやプライベートメッセージをフェッチできるようにします。そのためには、`./frontend/src/App.vue` を開き、`<template>` コードを次に置き換えます。

{% highlight html %}
{% raw %}
<template>
  <div>
    <button
      class="btn btn-primary btn-margin"
      v-if="!authenticated"
      @click="login()">
      Log In
    </button>

    <button
      class="btn btn-primary btn-margin"
      v-if="authenticated"
      @click="privateMessage()">
      Call Private
    </button>

    <button
      class="btn btn-primary btn-margin"
      v-if="authenticated"
      @click="logout()">
      Log Out
    </button>
    {{message}}
    <br>
  </div>
</template>
{% endraw %}
{% endhighlight %}

### メソッドを作成する

`./frontend/src/App.vue` で、`<script>` タグを次に置き換えます。

{% highlight html %}
{% raw %}
<script>
import AuthService from './auth/AuthService'
import axios from 'axios'

const API_URL = 'http://localhost:8000'
</script>
{% endraw %}
{% endhighlight %}

これは `./auth/AuthService` と `axios` ライブラリから `AuthService` をインポートします。その後、バックエンド サーバーの URL を保留する _API_URL_ 定数を宣言します。

`API_URL` 定数の後、`AuthService` インスタンスを作ります。

```js
const auth = new AuthService()
```

それから auth 定義の下で、アプリケーション コンポーネントを次のように定義していきます。

```js
export default {
  name: 'app',
  data () {
    this.handleAuthentication()
    this.authenticated = false

    auth.authNotifier.on('authChange', authState => {
      this.authenticated = authState.authenticated
    })

    return {
      authenticated: false,
      message: ''
    }
  },
}
```

このコードでは、認証状態を変更するときに `AuthService` によって作成された `authChange` イベントをリッスンします。それからその結果を `authenticated` 変数に割り当てます。また、`message` 変数を空に設定します。この変数は保護されたエンドポイントからの応答を保留します。

最後にすることは、`app` コンポーネントの `methods` プロパティを次のように定義することです。

```js
export default {
  // name: 'app',
  // data () { ... },
  methods: {
    // このメソッドは AuthService login() メソッドを呼び出す
    login () {
      auth.login()
    },
    handleAuthentication () {
      auth.handleAuthentication()
    },
    logout () {
      auth.logout()
    },
    privateMessage () {
      const url = `${API_URL}/api/private/`
      return axios.get(url, {headers: {Authorization: `Bearer ${AuthService.getAuthToken()}`}}).then((response) => {
        console.log(response.data)
        this.message = response.data || ''
      })
    }
  }
}
```

`login()`、`handleAuthentication()`、および `logout()` メソッドは AuthService で対応するメソッドのラッパーです。

`private()` メソッドでは、`axios.get()` メソッドを使って GET 要求を `http://localhost:8000/api/private/` に送ります。このエンドポイントは Auth0 で保護されているので、`Authorization` ヘッダーも追加します。このアクセストークンは `AuthService.getAuthToken()` メソッドを使ってローカル ストレージから取得されました。

## Django、Vue.js、および Auth0 統合をテストする

ここで、すべてが適切に構成されたので、Django、Vue.js、およびAuth0 の統合をテストします。そのためには、プロジェクトのルートディレクトリに戻り、次のコマンドを実行します。

```bash
# 背景で Django を実行する
python manage.py runserver &

# フロントエンド ディレクトリに移動する
cd frontend

# 背景で Vue.js を実行する &
npm run dev &
```

バックエンドとフロントエンドの両方のプロジェクトを実行したら、Vue.js ホームページ ([http://localhost:8080/](http://localhost:8080/)) に移動してテストします。

ホームページには、_ログイン_ ボタンが表示されます。そのボタンをクリックすると、Auth0 ログインページにリダイレクトされます。

![Auth0 login page](https://cdn.auth0.com/blog/django-vuejs/auth0-authentication.png)

認証が終わったら、Vue.js アプリケーションにリダイレクトされ、_プライベートの呼び出し_ と _ログアウト_ ボタンが表示されます。_プライベートの呼び出し_ ボタンをクリックすると、GET 要求がアクセストークンと共に Django フレームワークに発行され、セキュリティメッセージをフェッチします。

{% include jp-tweet_quote.html quote_text="Django と Vue.js を統合するのがどんなに簡単かを学びました。" %}

## 結論および次のステップ

本書では、Django バックエンドプロジェクトと Vue.js フロントエンドアプリケーションの両方をブートストラップしました。JWT 認証を Auth0 を使ってバックエンドにも追加しました。Django REST フレームワークを使ってどのようにして REST API を作り、それを Axios を使って Vue.js フロントエンドから消費する方法について学びたい方は、コメント欄にメッセージをご記入ください。メッセージをお寄せいただくことによって、私たちがこのトピックの人気を知ることができます。

お楽しみください！

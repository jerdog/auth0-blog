---
layout: post
title: "Android 開発：使用すべき 15 のライブラリ"
metatitle: "Android 開発：使用すべき 15 のライブラリ"
description: "Android アプリケーションの作成に役立つ素晴らしいライブラリを見てみましょう。"
metadescription: "Android アプリケーションの作成に役立つ素晴らしいライブラリを見てみましょう。"
date: 2018-09-12 16:30
category: Technical Guide, Deep Dive, Android
post_length: 2
auth0_aside: true
author:
  name: "Idorenyin Obong"
  url: "https://twitter.com/kingidee/"
  mail: "idee4ril@gmail.com"
  avatar: "https://cdn.auth0.com/blog/guest-author/idorenyin-obong.jpg"
design:
  bg_color: "#2C4721"
  image: "https://cdn.auth0.com/blog/android-libraries/logo.png"
tags:
- android
- development
- libraries
- tips
related:
- jp-angular-2-authentication
- jp-reactjs-authentication
---

**TL;DR：** 本書では、日々の Android 開発に役立つ 15 のライブラリを簡単に見ていきます。これらを使って、より良いパフォーマンスとより良いユーザー エクスペリエンスを提供するアプリケーションを作成することができます。

{% include jp-tweet_quote.html quote_text="大人気の Android ライブラリはより良いパフォーマンスとユーザーエクスペリエンスを提供するアプリケーションの作成に役立ちます。" %}

## 大人気の Android ライブラリ

ライブラリはプラットフォームやスタックに関連付けられたソフトウェア開発に大変革をもたらします。ライブラリを使って、他の開発者の作業を利用して、より重要度の低い定型句コードでより早く、より効果的にアクション/機能を実行します。本書では、Android 開発におけるさまざまなカテゴリやそれらに使用される共通ライブラリについて学んでいきます。

## Android ライブラリ — 画像読み込み

画像読み込みライブラリは複数の画像を同時に読み込んで大量のメモリが消費されないようにするときにとても便利です。画像は Android 開発で起きるメモリ不足エラーの最大の源です。よって、これらライブラリはシームレスなユーザーエクスペリエンスを提供するためにメモリ使用量を最小限にして、画像を読み込んだりキャッシュしたりする面倒を減らします。おなじみの 2 つの画像読み込みライブラリ、Glide と Picasso を見てみましょう。

### Glide

[Glide](https://github.com/bumptech/glide) はスムーズなスクロールにフォーカスした画像読み込みライブラリです。Glide は、ストレージ オーバーヘッドとデコード時間を最小限にするために自動式スマート ダウンサンプリングとキャッシュを画像読み込みを適用して、できるだけ速くかつスムーズに行います。また、バイト配列のようなリソースを再利用し、必要なときにアプリケーションリソースを自動的にリリースします。Glide の最新バージョンは書き込みのときに SDK の API レベル 14 (Android 4.0)以上、SDK の API 26 (Android 8.0)以降のコンパイルが必要です。

#### GLIDE を使用する

まず、プロジェクト `build.gradle` ファイルに Maven および Google レポジトリを次のように追加します。

```groovy
repositories {
  mavenCentral()
  google()
}
```

それから、アプリモジュール `build.gradle` ファイルにライブラリ依存関係を追加して、それを同期して次のようにライブラリを使用可能にします。

```groovy
implementation 'com.github.bumptech.glide:glide:4.4.0'
annotationProcessor 'com.github.bumptech.glide:compiler:4.4.0'
```

すると、次のように 1 行のコードでリモート URL から画像をロードできます。

```java
GlideApp
  .with(this)
  .load("https://res.cloudinary.com/demo/video/upload/dog.png")
  .into(imageView);
```

`with` メソッドは `Context`、 `Activity`、`Fragment` または `View` オブジェクトを取ることができます。 `load` メソッドはリモート URL またはドローアブルファイル（例 `R.drawable.image`）を取ります。引数として `into`メソッドにパスされた `imageView` インスタンスはタイプ [ImageView](https://developer.android.com/reference/android/widget/ImageView.html) であるべきです。

`gradle` バージョンが 3.0 以下の場合は、依存関係を追加するために `implementation` の代わりに `compile` キーワードを使用するように **注意してください** 。

### Picasso

[Picasso](http://square.github.io/picasso/) はもうひとつの素晴らしい Android 用画像ライブラリです。オープンソースの世界に強く依存し、貢献する会社の [Square](http://square.github.io/) が作成・管理しており、画像読み込み・処理を提供しています。Picasso を使用すると、画像を外部の場所から表示するプロセスが簡素化されます。Picasso は複雑な画像変換、ディスクへの自動キャッシュ、`ImageView` リサイクル、アダプタのダウンロード取り消しをサポートします。

このライブラリはプロセスの全ステップを処理します。HTTP 要求を処理することから始まり、画像のキャッシュも処理します。丁度 Glide と同様です。

#### PICASSO を使用する

まず、Picasso 依存関係を次のようにアプリモジュール `build.gradle` ファイルに追加します。

```groovy
implementation 'com.squareup.picasso:picasso:2.5.2'
```

その後、`gradle` ファイルを同期し、次のように 1 行のコードで画像リソースをロードします。

```java
Picasso
  .with(this)
  .load("https://res.cloudinary.com/demo/video/upload/dog.png")
  .into(imageView);
```

ご覧のように、Picasso が提供した API は Glide が提供したものと非常によく似ています。

長い間、どっちのライブラリのパフォーマンスが良いかということについて議論され、論争されてきました。以下の表には両者の長所と短所が記載されています。

![Android Image Libraries Comparison](https://cdn.auth0.com/blog/android-libraries/glide-picasso-comparison.png)    

[_出典元：_](https://medium.com/@multidots/glide-vs-picasso-930eed42b81d)[_Glide vs Picasso_](https://medium.com/@multidots/glide-vs-picasso-930eed42b81d)

## Android ライブラリ — ビデオ

ビデオの表示は開発の間に難しい作業が課せられます。処理するプロセスと詳細があまりにも多すぎるからです。この種では、利用可能なオプションがいくつかあります。しかし、最も普及していて強力なものは `ExoPlayer` で、この章ではこれについてフォーカスしていきます。

### ExoPlayer

[ExoPlayer](https://github.com/google/ExoPlayer) は Google が開発した Android Media Player ライブラリです。オーディオとビデオを再生するときに（ローカルとインターネット上の両方で）Android の MediaPlayer API に代替オプションが与えられ、他の利点もあります。`ExoPlayer` は Android の MediaPlayer API が現在サポートしていない [DASH や SmoothStreaming 適応型再生](https://google.github.io/ExoPlayer/guide.html) のような機能を提供します。`ExoPlayer` の最大の利点のひとつはカスタマイズが簡単なことです。

#### EXOPLAYER を使用する

まず最初に、JCenter および Google レポジトリを次のようにプロジェクト `build.gradle` 構成ファイルに追加します。

```groovy
repositories {
    jcenter()
    google()
}
```

次に、Gradle コンパイル依存関係を次のように同じファイルに追加します。

```groovy
implementation 'com.google.android.exoplayer:exoplayer:2.6.0'
```

それから、レイアウトリソースファイルに `SimpleExoPlayerView` コンポーネントを次のように追加します。

```xml
<com.google.android.exoplayer2.ui.SimpleExoPlayerView
   android:id="@+id/simple_exoplayer_view"
   android:layout_width="match_parent"
   android:layout_height="wrap_content"/>
```

その後、対応するアクティビティ クラスで、次のように `ExoPlayer` のクラスのインスタンスを作成します。

```java
SimpleExoPlayerView simpleExoPlayerView;
SimpleExoPlayer player;
```

それから、次のようにアクティビティの onCreate メソッドで `simpleExoPlayerView` を初期化します。

```java
simpleExoPlayerView = findViewById(R.id.simple_exoplayer_view);
```

そして `onStart`  メソッドで、 次のように `setupPlayer`  メソッドを呼び出します。

```java
@Override
protected void onStart() {
    super.onStart();
    setupPlayer();
}
```

次のように `setupPlayer`  メソッドも呼び出します。

```java
void setupPlayer(){
    BandwidthMeter bandwidthMeter = new DefaultBandwidthMeter();
    TrackSelection.Factory videoTrackSelectionFactory =
            new AdaptiveTrackSelection.Factory(bandwidthMeter);
    TrackSelector trackSelector =
            new DefaultTrackSelector(videoTrackSelectionFactory);

    //initialize the player with default configurations
    player = ExoPlayerFactory.newSimpleInstance(this, trackSelector);

    //Assign simpleExoPlayerView
    simpleExoPlayerView.setPlayer(player);

    // Produces DataSource instances through which media data is loaded.
    DataSource.Factory dataSourceFactory =
            new DefaultDataSourceFactory(this, Util.getUserAgent(this, "CloudinaryExoplayer"));

    // Produces Extractor instances for parsing the media data.
    ExtractorsFactory extractorsFactory = new DefaultExtractorsFactory();

    // This is the MediaSource representing the media to be played.
    MediaSource videoSource = new ExtractorMediaSource(videoUri,
            dataSourceFactory, extractorsFactory, null, null);

    // Prepare the player with the source.
    player.prepare(videoSource);
}
```

ここで、既定の構成で `player` を初期化し、それを `SimpleExoPlayerView` インスタンスに割り当てます。`videoUri` は `Uri` のタイプです。格納された全てのファイルには `Uri` があり、そのファイルを一意のアドレスにします。ビデオをリモート URL から表示するのであれば、次のように解析する必要があります。

```java
Uri videoUri = Uri.parse("any_remote_url");
```

これが `ExoPlayer` の基本的な実装になります。Google では [このライブラリで始める方法についての素晴らしいチュートリアル](https://codelabs.developers.google.com/codelabs/exoplayer-intro) を提供しています。

## Android ライブラリ — ネットワーク

最近のほとんど全てのモバイルアプリは、機能を実行するために何らかのネットワークコミュニケーションを必要とします。これまで、ネットワーク要求をしたければ、`Async` タスククラスを実行して [`HttpsUrlConnection`](https://developer.android.com/reference/javax/net/ssl/HttpsURLConnection.html) を使ってデータをフェッチする必要がありました。しかし、これは、特に大量のデータを返す API を使うときは、あまり有効ではありません。

幸運なことに、スレッドやデバイスのリソースを適切に管理しながら、このプロセスを最適化する素晴らしいネットワーク ライブラリがあります。代替オプションの中で、Retrofit と Volley が際立ちます。この 2 つでは Retrofit の方が人気が高いので、これを見ていきましょう。

### Retrofit

[Retrofit](http://square.github.io/retrofit/) は Square（Picasso をサポートする同じ会社）が開発・管理する Android / Java 用の [タイプセーフ](https://en.wikipedia.org/wiki/Type_safety) HTTP クライアントです。Retrofit は Android 開発では最もよく使われているネットワークライブラリです。Retrofit は注釈だけで要求本文の追加、エンドポイントの操作、ヘッダーの操作、クエリ パラメータの追加、要求メソッドの選択が簡単にできます。Retrofit はコンバータを使って、POJO の解析を処理します。

#### RETROFIT を使用する

Retrofit を使用するには、まず、次のように依存関係をアプリ build.gradleファイルに追加します。

```groovy
implementation 'com.squareup.retrofit2:retrofit:2.3.0'
```

また、使用する予定のコンバーターの依存関係も挿入します。コンバーターは応答本文に対する Java オブジェクトのマッピングを処理します。コンバーターについての詳細は [こちら](https://github.com/square/retrofit/wiki/Converters) をご覧ください。

```groovy
implementation 'com.squareup.retrofit2:converter-gson:2.3.0'
implementation 'com.squareup.retrofit2:converter-scalars:2.3.0'
```

上記の最初のコンバーター(`converter-gson`)はその名前のとおり、JSON フォーマットから/までマップします。ふたつめは `String` のようにプリミティブ データ型で処理したいときに使用します。ご希望のコンバーターをインポートした後、次のようにアクセスするエンドポイントを構成するインターフェイスを作成します。

```java
public interface ApiService {
    @GET("/data")
    Call<ResponseClass> fetchData(@Body JsonObject jsonObject);
}
```

そのスニペットから、要求に対して `JsonObject` `@Body`を必要とするエンドポイント `/data` があります。また、要求の予想応答本文にマップされる `ResponseClass` もあります。ただし、クラスは簡潔にするため、ここでは省略されます。POJO に対して JSON オブジェクトをマップするには、[JsonSchema2Pojo](https://auth0.com/blog/android-development-15-libraries-you-should-be-using/jsonschema2pojo.org) ライブラリを使用します。

利用可能なエンドポイントを定義した後、次のように Retrofit クライアントを作成します。

```java
public class RetrofitClient {    
    static ApiService getService(){
        OkHttpClient.Builder httpClient = new OkHttpClient.Builder();
        Retrofit.Builder builder = new Retrofit.Builder()
                .baseUrl("http://127.0.0.1:5000/")
                .addConverterFactory(GsonConverterFactory.create());

        Retrofit retrofit = builder
                .client(httpClient.build())
                .build();

        return retrofit.create(ApiService.class);
    }
}
```

Retrofit オブジェクトを構築するとき、必要な数のコンバーターを追加できます。これによってデータを解析するオプションが広がります。その後、次のように呼び出しでネットワーク要求をします。

```java
RetrofitClient.getService().fetchData(jsonObject).enqueue(new Callback<ResponseClass>() {
    @Override
    public void onResponse(Call<ResponseClass> call, Response<ResponseClass> response) {

    }

    @Override
    public void onFailure(Call<ResponseClass> call, Throwable t) {

    }
});
```

`jsonObject` が要求パラメータを含む場合です。この要求は上記のコードスニペットで定義されたように `http://127.0.0.1:5000/data` エンドポイントに対して行われます。この最後のスニペットでご覧のように、Retrofit もコールバック メソッドを提供して要求の状況を提供します。

## Android ライブラリ — 依存関係挿入

[依存関係挿入](https://en.wikipedia.org/wiki/Dependency_injection) はオブジェクトがその依存関係を構成する必要がない場合の概念です。代わりに、依存関係は別のオブジェクトによってパスされます。この原理はクラスを実装から切り離すのに役立ちます。コードがゆったりと結合され、管理やテストが簡単になるので、エンジニアプラクティスの良い、注目に値するソフトウェアです。依存関係挿入のライブラリはたくさんありますが、Dagger2 が主力のようです。

### Dagger2

[Dagger2](https://github.com/google/dagger) は Java および Android 用の完全静的、コンパイル時間 [依存関係挿入](http://en.wikipedia.org/wiki/Dependency_injection) のフレームワークです。これは [Square](http://square.github.io/) が作成した以前の [バージョン（Dagger1）](https://github.com/square/dagger) のアップグレードで、現在は Google が管理しています。最新の Dagger バージョンには Android 用の Android 固有ヘルパーが含まれています。具体的には、新しいコードジェネレータを使用する自動生成のサブコンポーネントが含まれています。Dagger2 は非常に深く、適切に理解するには簡単な使用例以上のものが必要とされますが、とにかく見てみましょう。

#### DAGGER2 を使用する

いつものように、次のようにアプリモジュール `build.gradle` ファイルに依存関係を追加します。

```groovy
implementation 'com.google.dagger:dagger:2.14.1'
annotationProcessor 'com.google.dagger:dagger-compiler:2.14.1'
// これを追加したので Android サポートライブラリを使用できます
implementation 'com.google.dagger:dagger-android-support:2.14.1'
annotationProcessor 'com.google.dagger:dagger-android-processor:2.14.1'
```

その後、アクティビティ ビルダー モジュールクラスを作成して Dagger が依存関係を必要とするそのアクティビティのサブコンポーネントを作成できるようにします。

```java
@Module
public abstract class ActivityBindingModule {
    @ContributesAndroidInjector()
    abstract MainActivity mainActivity();
}  
```

任意ですが、アクティビティへの特定の依存関係でモジュールクラスを作成できます。それから、例えば次のように、そのモジュールを `@ContributesAndroidInjector` 注釈のコンストラクターに追加します。

```java
@ContributesAndroidInjector(modules = {MainActivityModule.class} )
abstract MainActivity mainActivity();
```

また、1 つのアクティビティクラス以上に使用する依存関係を提供するためにモジュールクラスをもうひとつ作成できます。

`Module` クラスを `@Module`  で注釈して、オブジェクトを提供する担当にします。オブジェクトは提供されているものと同じ戻り値の型があるメソッド（通常 `@Provides`  または `@Binds`  の注釈付き）を作成して提供されます。以下のサンプルのモジュールクラスはアプリに `String` を提供します。

```java
  @Module
  public abstract class AppModule {
      @Provides
      static String providesString(){
          return "I love Auth0";
      }
  }
```

`@Module`  を配置したら、抽象クラスまたはインターフェイスを作成します。この場合 `AppComponent`  と名付けます。`AppComponent`  には `@Component` の注釈付けをします。注釈はサポートライブラリからの `AndroidSupportInjection` クラスを含む、上記で作成したモジュールクラスに入れます。Dagger はクラスを生成してから、このインターフェイスを実装します。このクラスはパスしたモジュールから挿入されたインスタンスを提供します。この `Component` インターフェイスは次のように表示されます。

```java
@Singleton
@Component(modules = {AndroidSupportInjectionModule.class, AppModule.class, ActivityBindingModule.class})
public interface AppComponent extends AndroidInjector<AppController> {
    @Override
    void inject(App instance);

    @Component.Builder
    interface Builder {
        @BindsInstance
        Builder application(Application application);
        AppComponent build();
    }
}
```

`AppController` はアプリのアプリケーションクラスで、オブジェクトはアプリのライフサイクルで初期化されます。`AppController` は次のように表示されます。

```java
public class App extends Application implements HasActivityInjector {
      @Inject
      DispatchingAndroidInjector<Activity> activityDispatchingAndroidInjector;
      @Override
      public void onCreate() {
        super.onCreate();
        DaggerAppComponent.builder().application(this)
            .build().inject(this);
      }
      @Override
      public DispatchingAndroidInjector<Activity> activityInjector() {
        return activityDispatchingAndroidInjector;
      }
}
```

`DispatchingAndroidInjector<Activity>` のインスタンスを作成し、実装されたメソッドに返します。これは、アクティビティにメンバー挿入するためです。それから `Component` を構築し、`Application` クラスにそれを挿入します。

最後に、依存関係を使用するつもりのアクティビティで、`HasSupportFragmentInjector` を実装し、次のように `@Inject` 注釈を使って依存関係にアクセスします。

```java
public class MainActivity extends AppCompatActivity implements HasSupportFragmentInjector {

    @Inject
    DispatchingAndroidInjector<Fragment> fragmentDispatchingAndroidInjector;

    @Inject
    String string;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        AndroidInjection.inject(this);
        super.onCreate(savedInstanceState);
        Log.d("TAG",string);
    }

    @Override
    public AndroidInjector<Fragment> supportFragmentInjector() {
        return fragmentDispatchingAndroidInjector;
    }
}
```

`App` クラスで同じようなことをしましたが、唯一の違いはここでは `HasSupportFragmentInjector` を実装しているので `DispatchingAndroidInjector<T>` には `Fragment` があります。この論理は、`Application` には `Activities` が含まれ、これらは `Fragments` を囲むということです。この後、`AndroidInjection.inject(this)` を `onCreate` メソッドに呼び出します。

この簡単な例を使ってこのアプリを実行している方には、`I love Auth0` がログに書き出されます。依存関係をクラスで初期化する代わりに、挿入できました。これが Dagger2 が提供する機能の一部です。詳細については、次の 2 つの記事を参照してください。

- [Dagger 2 使用の新 Android インジェクタ  —  パート 1](https://medium.com/@iammert/new-android-injector-with-dagger-2-part-1-8baa60152abe)
- [Android 用 Dagger 2.11](https://medium.com/@idee4ril/dagger-2-11-with-android-a606c132826c)

## Android ライブラリ — ビューバインディング

ビューバインディング ライブラリはビューを変数に割り当て、アクティビティクラスのそれらにアクセスする場合、定型句コードを減らす必要があるときに出現します。このエリアのライブラリは限られています。基本的に、重要な点はButterKnife と Android データ バインディングの 2 つです。

### Butterknife

[ButterKnife](http://jakewharton.github.io/butterknife/) は [Jake Wharton](https://twitter.com/JakeWharton) が開発したビューバインディング ライブラリです。Butterknife は `ids` をビューに割り当てるときに役立つライブラリで、過剰 `findViewById` を避けます。ドキュメントの「シャープさが無限に少ないだけで Butterknife は Dagger のよう」によると、ビューバインディングは依存関係挿入の形態として見ることができる、ということです。ButterKnife では、注釈は代わりに定型句コードを生成するために使用されます。

### Butterknife を使用する

Butterknife を使用するには、依存関係をアプリモジュール `build.gradle` ファイルに次のように追加する必要があります。

```groovy
implementation 'com.jakewharton:butterknife:8.8.1'
annotationProcessor 'com.jakewharton:butterknife-compiler:8.8.1'
```

それから、アクティビティで、`@BindView` 注釈を使って次のように `id` をそのビューに割り当てます。

```java
class MainActivity extends AppCompatActivity {
    @BindView(R.id.firstname) EditText firstName;

    @Override public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main_activity);
        ButterKnife.bind(this);
        // TODO ...
    }
}
```

上記のコード スニペットでは、`bind` メソッドを使って `onCreate` メソッドの ButterKnife を初期化したことに注意してください。代わりに、`ButterKnife` を `Fragment` に使用したければ、次のように onCreateView メソッドでそれを初期化します。

```java
View view = inflater.inflate(R.layout.sample_fragment, container, false);
ButterKnife.bind(this,view);
```

ButterKnife を使って `OnClickListeners` の作成を避けます。例えば、`@OnClick` 注釈とビューを一緒に使って、次のようにクリックリスナーをビューに追加します。

```java
@OnClick(R.id.button)
void buttonClicked() {
  // TODO ...
}
```

ButterKnife についてのドキュメントは [こちらから](http://jakewharton.github.io/butterknife/) アクセスしてそのライブラリの機能について学ぶことができます。

### Android データバインディング ライブラリ

[Android データバインディング ライブラリ](https://developer.android.com/topic/libraries/data-binding/index.html) は Android Support Library に内蔵されています。Android Studio のバージョン `1.3` 以降が必要です。ButterKnife とは異なりこのライブラリは注釈を使用しません。

### データバインディング ライブラリを使用する

アプリモジュール `build.gradle` ファイルで次のようにデータバインディングと同期化が可能になります。

```java
android {
    ....
    dataBinding {
        enabled = true
    }
}
```

その後、レイアウトファイルのルートタグを次のように `layout` に設定します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <android.support.constraint.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <TextView
            android:id="@+id/textview"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />

    </android.support.constraint.ConstraintLayout>

</layout>
```

それから、アクティビティ バインディング クラスがレイアウトの名前`activity.main.xml` を基に生成されます(`ActivityMainBinding`)。このクラスのインスタンスを使ってビューにアクセスします。他のユーティリティを処理するために生成された別のクラス `DataBindingUtil` もあります。

それから、アクティビティクラスでは次のようになります。

```java
public class MainActivity extends AppCompatActivity {

    ActivityMainBinding binding;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        binding.textview.setText("Hello world!");
    }
}
```

Android データバインディング ライブラリはその他たくさんの機能と共に、`onClick` リスナーの置換を提供します。これら機能については公式 [ドキュメント](https://developer.android.com/topic/libraries/data-binding/index.html) をご覧ください。

ButterKnife と Android Databinding Library の 2 つのライブラリはビューにアクセスするために書くコードの量を大きく減らします。しかし、Android DataBinding ライブラリは ButterKnife と比較したとき、より簡単な設定、少ないコードで結果達成、より多くの機能などを提供するため、勝ち目があるようです。

## Android ライブラリ — リアクティブプログラミング

リアクティブプログラミングは、データがコンポーネント（ソース）から別のコンポーネント（サブスクライバー）に生成されるパラダイムです。これによって非同期のタスクを効果的に処理できます。よって、リアクティブプログラミング ライブラリはデータをソースからサブスクライバーにパスするのに役立つライブラリです。Android 開発者にとって最も人気の高い代替オプションは RxJava2 と RxAndroid です。よって、これらを見ていきましょう。

### RxJava2 &amp; RxAndroid

[RxJava](https://github.com/ReactiveX/RxJava) はリアクティブプログラミング を実装させるライブラリですから、リアクティブアプリケーションを作ります。[RxJava2](https://github.com/ReactiveX/RxJava/wiki/What%27s-different-in-2.0) は RxJava の旧バージョンのアップデートです。RxJava2 には Observables、Observers、Schedulers があります。

`Observables` はデータソースで、`Observer`、`Single`、`Flowable`、`Maybe`、`Completable` のさまざまなタイプがあります。これら各タイプには固有の使用ケースがあり、その詳細については [こちら](https://mindorks.com/course/demo/learn-rxjava/chapter/id/2) をご覧ください。

特に `Flowable` には [バックプレッシャ](https://github.com/ReactiveX/RxJava/wiki/Backpressure) サポートが付いています。バックプレッシャは `Observer` が `Observable` にシグナルを送るとき、後者が値を早く発行し過ぎることです。`Observers` はデータレシーバー（またはコンシューマ）で、`Schedulers` はスレッドの管理に役立ちます。

一方、[RxAndroid](https://github.com/ReactiveX/RxAndroid) は RxJava2 の拡張子です。メインスレッドでスケジュールする `Scheduler` のプロビジョニングまたは指定された `Looper` のような Android プラットフォームに特有の機能を提供します。

#### RXANDROID を使用する

RxAndroid を使用するには、次のようにアプリモジューラ `build.gradle` ファイルに依存関係を追加する必要があります。

```groovy
compile 'io.reactivex.rxjava2:rxandroid:2.0.1'
// RxAndroid リリースはとてもまれなので、バグ修正と最新機能には 
// RxJava の最新バージョンに明示的に依存することを推奨します。
compile 'io.reactivex.rxjava2:rxjava:2.1.7'
```

それから、`CompositeDisposable` のインスタンスをクラスに作成します。`CompositeDisposable` は次のように複数の破棄可能を保留できるコンテナーです。

```java
disposables.add(
  Observable.just("Hello world!")
  // バックグラウンドスレッドで実行します
  .subscribeOn(Schedulers.io())
  // メインスレッドで通知を受けます
  .observeOn(AndroidSchedulers.mainThread())
  .subscribeWith(new DisposableObserver<String>() {
      @Override
      public void onComplete() {

      }

      @Override
      public void onError(Throwable e) {

      }

      @Override
      public void onNext(String value) {

      }
  })
);
```

上記のコードスニペットでは、`disposables` 変数に追加したものは 1 つの文字列を発行するデータソース、プロセスが行われるスレッド、オブザーバーが結果の通知を受けるスレッド、サブスクライバー/オブザーバーで成ります。`DisposableObserver<String>` はオブザーバーで、実装する 3 つのメソッドを提供します。ひとつめはデータが発行されたときに呼び出される `onNext`、ふたつめはエラーが発生したことを示す onError、みっつめは `Observable` がデータ発行を終了し、これ以上 `onNext` を呼び出さないことを示す `onCompleted` です。

それから、アクティビティがバックグラウンドにあるときに、次のようにリソースを消去してメモリリークを避けます。

```java
    @Override
    protected void onPause() {
        super.onPause();
        disposables.clear();
    }
```

基本的ですが、興味深い Mindorks によるチュートリアルがありますので、RxJava2 についての詳細は [こちら](https://mindorks.com/course/learn-rxjava) をご覧ください。

## Android ライブラリ — テストする

ソフトウェアのテストは単に、バグを見つける目的で行われます。テストは長い間にわたって進化し、ソフトウェア開発プロセスの最終段階のひとつをとっても、はるかに超えるものです。実際、テストは最初に書き込みが行われる場合はコーディング段階の初期で採用され、それからソフトウェアの論理が実装されてテストが予期することのみをパスします。これは通常、[_テスト駆動開発_](https://en.wikipedia.org/wiki/Test-driven_development) として参照されます。アプリケーションのテストを書き込むことはバグを素早く見つけたりアプリケーションの強化に役立つので良いプラクティスです。さまざまな長所のテストライブラリがたくさんあり、Android 開発で使用できます。そのうちの 4 つ、JUnit、Mockito、Robolectric、Espresso を見てみましょう。

### JUnit

[JUnit](http://junit.org/) は単体テストに使用するフレームワークです。単体テストはテストのタイプで、ソースコードの個々の単体がテストされます。このフレームワークには `assert` メソッドのセットが含まれ、実際の結果に対して期待される結果をチェックします。JUnit は注釈を多用します。その例を少し挙げると、`@Test`（テストメソッドを識別する）、 `@Before`（テストが呼び出される前に呼び出されるべきメソッドを宣言する）、`@After`（テストの後に呼び出されるべきメソッドを宣言する）があります。

#### JUNIT を使用する

まず、次のようにアプリモジュール `build.gradle` ファイルに依存関係を追加します。

```groovy
testImplementation 'junit:junit:4.12'
```

それから、サンプルのテストクラスは次のように表示されます。

```java
public class ExampleUnitTest {
    @Test
    public void additionIsCorrect() throws Exception {
        assertEquals(4, 2 + 2);
    }
}
```

ここでは、2 と 2 を足すと 4 になることを確認します。JUnit テストは通常、JVM で実行し、デバイスやエミュレーターを必要としないので、 早くできます。JUnit テストの詳細については [こちら](http://www.vogella.com/tutorials/JUnit/article.html) をご覧ください。

### Mockito

ほとんどの場合、テストを書き込むクラスは他のクラスに依存します。この目的だけのためにこれらクラスを構成するのは荒ただしい作業になります。このような場合に Mockito を使用します。これはモッキングフレームワークで、モック（フェイク）オブジェクトを作成・構成するのに役立ちます。通常、JUnit と一緒に使用します。

### Mockito を使用する

まず、JCenter レポジトリ、`jcenter()` がプロジェクト `build.gradle` ファイルにあるかを確認します。次に、その依存関係をアプリモジュール `build.gradle` ファイルに追加して次のように同期します。

```groovy
testCompile 'junit:junit:4.12'
// 単体テストの ために Mockito を使用したい ならば 必須です
testCompile 'org.mockito:mockito-core:2.13.0'
// Android テストの ために Mockito を使用したい ならば 必須です
androidTestCompile 'org.mockito:mockito-android:2.13.0'
```

それから、次のように Mockito を使用します。

```java
@Test
public void mockitoTest throws Exception {
    List mockedList = mock(List.class);

    //using mock object
    mockedList.add("one");
    mockedList.clear();

    //verification
    verify(mockedList).add("one");
    verify(mockedList).clear();
}
```

ここで、リストをモックし、それにデータを追加し、それを消去します。それから、これらアクションが実施されたことを検証します。Mockito を使ったテストについての詳細は、公式 [ドキュメント](http://static.javadoc.io/org.mockito/mockito-core/2.13.0/org/mockito/Mockito.html#0.1) をご覧ください。

### Robolectric

[Robolectric](http://robolectric.org/) はもうひとつの単体テストライブラリです。JUnit との違いは、この Robolectric は Android 開発者に役立つように作られたことです。Robolectric はビューのインフレ、リソース読み込みなどその他多数を処理します。これにより、テストは Espresso などでデバイスを起動せずに、Android フレームワーク依存関係がある実際のデバイスでできるほとんどのことができます。ある意味、Robolectric はテストのために Android SDK をシュミレートします。Robolectric では、Mockito のような追加のモッキングフレームワークが必要ありません。

#### ROBOLECTRIC を使用する

Robolectric を使用するには、次のようにアプリ `build.gradle` ファイルに依存関係を追加します。

```groovy
testCompile "org.robolectric:robolectric:3.6.1"
```

それから、次のようにサンプルのテストクラスを作成します。

```java
@RunWith(RobolectricTestRunner.class)
@Config(constants = BuildConfig.class)
public class MyActivityTest {

  @Test
  public void checkActivityNotNull() throws Exception {
    Activity activity = Robolectric.setupActivity(MyActivity.class);
    assertNotNull( activity );
  }
}
```

これは非常に基本的なテストで、アクティビティをセットアップし、Null でないことをチェックします。Robolectric を使ったテストについての詳細は [こちら](http://www.vogella.com/tutorials/Robolectric/article.html) からご覧ください。

### Espresso

[Espresso](https://developer.android.com/training/testing/espresso/index.html) はテストフレームワークで、[Android Testing Support Library](https://developer.android.com/tools/testing-support-library/index.html) の一部です。このテストフレームワークで、Android アプリ用のユーザーインターフェイス テストを作成できます。つまり、Espresso で `TextView` のテキストが別のテキストに一致するかをチェックできるテストを書くことができます。Espresso テストは実際のデバイスとエミュレーターの両方で実行でき、まるで実際のユーザーがアプリを使っているかのように動作します。

#### ESPRESSO を使用する

まず、次のようにアプリモジュール `build.gradle` ファイルにこれら依存関係を追加します。

```groovy
androidTestCompile 'com.android.support.test.espresso:espresso-core:3.0.1'
androidTestCompile 'com.android.support.test:runner:1.0.1'
```

それから、同じ `gradle` ファイルに、インストルメンテーションランナーを設定します。その後に、次のような Gradle ファイルの同期化を忘れないようにしましょう。

```groovy
defaultConfig {
    applicationId "com.my.awesome.app"
    minSdkVersion 15
    targetSdkVersion 26
    versionCode 1
    versionName "1.0"

    testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
}
```

それから、次のようなテストファイルを（例： `EspressoSampleTest`）を作成できます。

```java
@RunWith(AndroidJUnit4.class)
public class EspressoSampleTest {
    @Rule
    public ActivityTestRule<MainActivity> mActivityRule =
            new ActivityTestRule(MainActivity.class);

    @Test
    public void isHelloWorldDisplayed() {
        onView(withText("Hello world!"))
          .check(matches(isDisplayed()));
    }
}
```

このテストクラスは `MainActivity` が開いたときに「世界の皆さん、こんにちは」のメッセージが表示されるかを確認します。

さまざまなテストライブラリの調査から、JUnit はその他のライブラリと共に作動するので、何とも競合しないと推測されます。Espresso はユーザーインターフェイス テストに良いプラットフォームを提供します。Mockito と Robolectric の両方には同じような機能がありますが、Robolectric は少ないコードでテストを達成します。Robolectric はビューをテストするなど、多くの機能も提供します。Robolectric には Google エンジニアのサポートがあるという有利な点もあります。

## Android ライブラリ — データベースヘルパー

オフラインデータの永続性はユーザーエクスペリエンスを高めるのに非常に重要です。通常、アプリを次回起動するのに必要な重要情報を保存するアプリケーション、またはインターネット接続が使えないときにデータを使えるようにするアプリケーションが必要です。データを保存することはキーと値を組み合わせるよりももっと複雑ですから、このタスクを Android に簡単に保存するために多数のライブラリが作成されました。本章では、Room のような素晴らしい永続化ライブラリを見て行きます。

### Room

[Room](https://developer.android.com/topic/libraries/architecture/room.html) は永続化ライブラリで、Android [Architecture Components](https://developer.android.com/topic/libraries/architecture/index.html) の一部です。Room は最小の定型句コードに一貫するローカルデータを提供します。SQLite 上に抽象化レイヤーを提供するので、アプリのデータベースと取り組むのが簡単になります。このライブラリにはコンパイル時間で SQL クエリの検証、メインスレッド上のデータベースクエリの拒否（データベースの初期化中に明示しているときは除く）、実装ベストプラクティスの提示などたくさんの長所があります。

Room はデータベース、DAO (Data Access Object)、エンティティの 3 つのメインコンポーネントでできています。それぞれはライブラリを機能させるために相関性があります。`Entity` クラスはデータベーステーブルを表し、`@Entity` の注釈が必要です。クラスの変数はテーブルにある列を表します。DAO はデータベースにアクセスするために使用するメソッドを含むインターフェイスです。Room はインターフェイスを使用し、次のようにユーザーのための実装クラスを生成します。

基本的な DAO 操作に固有の注釈には@Insert、`@Update`、`@Delete`、`@Query` の 4 つがあります。それから、データベースクラスがあります。これは `@Database` の注釈が付いた抽象クラスで、`RoomDatabase` を拡張します。このクラスは使用するエンティティと DAO のリストを定義します。

#### ROOM を使用する

Room を使用するには、まず Google Maven レポジトリをプロジェクト `build.gradle` ファイルに追加します。前回の Android プロジェクトは次で事前に構成されています。

```groovy
allprojects {
    repositories {
        // ... other repositories
        google()
    }
}
```

それから、Room 依存関係を次のようにアプリモジュール `build.gradle` ファイルに追加します。

```groovy
implementation "android.arch.persistence.room:runtime:1.0.0"
annotationProcessor "android.arch.persistence.room:compiler:1.0.0"
```

次に、`Entity` クラスを作成します。いつものように、かなりシンプルなものを使います。そして、`id` と `name` の 2 つの列があります。`id` 列は主キーで、追加した全ての行が固有になるように自動生成されます。

```java
@Entity(tableName = Person.TABLE_NAME)
public class Person {
    public static final String TABLE_NAME = "person";
    String name;
    @PrimaryKey(autoGenerate = true)
    public int id;
}
```

テーブルはずっと小文字で名付けることがベストプラクティスです。Java クラスに名付けるときとは同じ慣習ではないので、テーブルにはカスタム名を使用できます。通常、Room はテーブル名としてクラス名を使用します。その後、DAO を作成します。DAO はデータベース自体にインターフェイスを提供し、データのクエリと保存を処理します。次に、次のようにデータベース操作に使用するメソッドを含む DAO を作成します。

```java
@Dao
public interface PersonDao {
    // Adds a person to the database
    @Insert
    void insert(Person person);

    // Removes a person from the database
    @Delete
    void delete(Person person);

    // Gets all people in the database
    @Query("SELECT * FROM "+Person.TABLE_NAME)
    List<Person> getAllPeople();
}
```

インターフェイスはさまざまなメソッドに配置された注釈で一目瞭然です。その後、データベースクラスを作成します。これは、次のようにデータベースを管理し、インスタンスを DAO に提供することが担当の抽象クラスです。

```java
@Database(entities = {Person.class}, version = 1)
public abstract class AppDatabase extends RoomDatabase {
    public abstract PersonDao getPersonDao();
}
```

これらクラスを配置したら、データベースのインスタンスを取得し、クエリを作り始めます。アプリのライフサイクルでデータベースのインスタンスを 1 つだけ作ることが推奨されています。これを次のように `Application` クラスでできます。

```java
public class App extends Application {
    private static AppDatabase appDatabase;

    @Override
    public void onCreate() {
        super.onCreate();

        // initialize the db once during the app lifecycle
        appDatabase =  Room.databaseBuilder(
            getApplicationContext(),
            AppDatabase.class,
            "person.db"
        ).build();
    }

    public static AppDatabase provideDb(){
        return appDatabase;
    }
}
```

それから、データベースインスタンスにアクセスして、データをデータベースに挿入します。これは次のように DAO クラスの `insert` メソッドを呼び出して実行します。

```java
Executors.newSingleThreadExecutor().execute(new Runnable() {
    @Override
    public void run() {
        // insert into the database
        Person person = new Person();
        person.name= "Idorenyin Obong"
        App.provideDb().getPersonDao().insert(person);
    }
});
```

ここで、`Person` のインスタンスを作成し、データベースに挿入しました。前述したように、`id` は自動的に作成されるので、名前だけを割り当てました。バックグラウンドでクエリを実行する新しいスレッドを作成したことにもご注意ください。データベースでその他の演算操作を実施する上記のパターンを同様に採用します。

Google は、ユーザーがこのライブラリで始めることができるように [ナイスチュートリアル](https://codelabs.developers.google.com/codelabs/android-persistence/#0) を提供しています。

## Android ライブラリ - カスタムフォント

ほとんど全ての Android 開発者はアプリの外観に一生懸命です。ときには、アプリが全デバイスに同じ感覚を与えることができるように、固有のフォントを選ぶために一層の努力を要することがあります。このような状況で、アプリの全てのテキストにカスタムフォントを使うことができるライブラリがあります。

### Calligraphy

[Calligraphy](https://github.com/chrisjenx/Calligraphy) は最も人気が高いカスタムフォント ライブラリのひとつで、使い方はとても簡単です。このライブラリで、アプリケーション全体で同じフォントを使ったり個々のテキストにフォントを定めたり簡単にできます。

### Calligraphy を使用する

いつものように、次のようにアプリモジュール `build.gradle` ファイルに依存関係を追加し、それを同期化します。

```groovy
implementation 'uk.co.chrisjenx:calligraphy:2.3.0'
```

それから、`assets` フォルダを作成し、そこにカスタムフォントを挿入します。Android Studio のプロジェクトディレクトリにあるアプリケーションのルート フォルダを右クリックし、[New]、[Folder] を選択し、それから [Assets Folder] を選択します。これで、アセットフォルダが生成されます。その後、そのライブラリを初期化し、次のように `Application` クラスに既定のフォントを設定します。

```java
public class App extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        CalligraphyConfig.initDefault(new CalligraphyConfig.Builder()
                .setDefaultFontPath("red-velvet.ttf")
                .setFontAttrId(R.attr.fontPath)
                .build()
        );
    }
}
```

最後に、次のように各アクティビティの `attachBaseContext` メソッドを上書きします。

```java
@Override
protected void attachBaseContext(Context newBase) {
    super.attachBaseContext(CalligraphyContextWrapper.wrap(newBase));
}
```

これで完成です！これで、Red Velvet フォントがこのアプリの既定のフォントになりました。次のように単一のテキストに特定のフォントを適用することもできます。

```xml
<TextView
    android:text="@string/hello_world"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    fontPath="fonts/red-velvet.ttf"/>
```

### カスタムフォントとサポートライブラリ

Android Support Library のお陰で、バージョン `26`以上からアプリの依存関係を増やさずにカスタムフォントを使用できます。これはどっちみち `build.gradle` ファイルで `appcompat` 依存関係を見つけるのでこのようになります。これは新しい Android プロジェクトを作成するときに自動的に追加されます。

```groovy
implementation 'com.android.support:appcompat-v7:26.1.0'
```

#### SUPPORT LIBRARY でカスタムフォントを使用する

まず、`fonts` リソースフォルダを作成します。これは `res` フォルダを右クリックしてから、[New → Android リソース] ディレクトリを選択します。その後、リソースタイプとしてのフォントを選択し、[OK] を選択します。それから `font` リソースディレクトリ（例 `redvelvet`）に希望するフォントファイルを追加します。

カスタムフォントを次のように直接 XML レイアウトに適用します。

```xml
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:fontFamily="@font/redvelvet"/>
```

さらに、新しいフォントファミリを作成できます。フォントファミリはスタイルや太さの詳細を合わせたフォントファイルのセットです。そのフォントソースフォルダを右クリックし、[New] をクリックし、それから [New Font Resource File] をクリックして名前を挿入し、[OK] を選択します。サンプルのフォントファミリは次のようなものがあります。

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:app="http://schemas.android.com/apk/res-auto">
    <font
        app:font="@font/redvelvet"
        app:fontStyle="normal"
        app:fontWeight="400" />
</font-family>
```

フォントを `XML` レイアウトに適用すると、使用されたテキストスタイルを基にシステムが正しいフォントをピックします。フォントを `XML` レイアウトに適用することはさておき、それらをプログラムや複数のフォームで適用することも可能ですので、公式 [ドキュメント](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) でご覧ください。Support Library でカスタムフォントを使用することについての素晴らしいリソースは [こちら](https://segunfamisa.com/posts/custom-fonts-with-android-support-library) をご覧ください。

## ジョブのスケジューリング

往々にして Android アプリケーションはユーザーのインタラクションから操作する必要があることがあります。このために、アプリのパフォーマンスとデバイスを全般に最適化するためにタスクを非同期、かつインテリジェントに処理する必要があります。これで、バックグラウンドで処理するタスクが均等に呼び出されます。

Android にはバックグラウンドタスクをスケジューリングする独自の API、[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) があり、API 21 (Android 5.0) 以降をサポートするときのみ使用できるという短所があります。

このカテゴリの 2 つの共通オプションは [Evernote](https://evernote.com/) による [Android ジョブライブラリ](https://github.com/evernote/android-job) と Firebase による [Firebase Jobdispatcher](https://github.com/firebase/firebase-jobdispatcher-android) があります。

Firebase Jobdispatcher には Google Play 開発者サービスが必要というもうひとつの代償がありますが、ずっと前の API 9 (Android 2.3) まで互換性があります。Android ジョブは Android の JobScheduler と Firebase Jobdispatcher の取り組みを結合して安定したジョブスケジューリングライブラリを提供します。広く普及されているのも不思議ではありません。

### Android ジョブ

[Android ジョブ](https://github.com/evernote/android-job) はバックグラウンドでジョブを処理するために使用する Android ライブラリです。Android のバージョンによって `JobScheduler`、`GcmNetworkManager`、または `larmManager` のどれかが使用されます。これが、このライブラリが人々の心を引き付ける理由です。ひとつのコードベース内で別の API を使って、使用するスケジューリング API を知るために API バージョンを確認する代わりに、Android ジョブはコードサイズの他にストレスも減らして実行してくれます。このライブラリは API 14 (Android 4.0) 以降が必要です。

### Android ジョブを使用する

Android ジョブを使用するには、次のようにアプリモジュール `build.gradle` ファイルに依存関係を追加し、それを同期化します。

```groovy
compile 'com.evernote:android-job:1.2.1'
```

それから、次のように `Application` クラスの `JobManager` を初期化します。

```java
public class App extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        JobManager.create(this).addJobCreator(new SampleJobCreator());
    }
}
```

この `Application` クラスはアプリケーションサイクルでオブジェクトを一度だけ初期化するために使用されます。`SampleJobCreator` はジョブの一意タグを基にした `Job` のインスタンスを返すクラスです。`SampleJobCreator` は次のように表示されます。

```java
public class SampleJobCreator implements JobCreator {
    @Override
    @Nullable
    public Job create(@NonNull String tag) {
        switch (tag) {
            case FirstJob.TAG:
                return new FirstJob();
            default:
                return null;
        }
    }
}
```

最後に、`FirstJob` と名付けられた `Job` クラスは次のように表示されます。

```java
public class FirstJob extends Job {
    public static final String TAG = "first_job_tag";
    @Override
    @NonNull
    protected Result onRunJob(@NonNull Params params) {
        // run your job here
        return Result.SUCCESS;
    }

    public static void scheduleJob(long timeJobShouldStart) {
        new JobRequest.Builder(ReviewStayJob.TAG)
                .setExact(timeJobShouldStart)
                .build()
                .schedule();
    }
}
```

上記のコードスニペットでは、`onRunJob` メソッドで何をするかを `Job` に伝えました。それから、`FirstJob.scheduleJob(timeinMills)`（`timeInMillis` がデータ型 `long` の場合）を呼び出してジョブをスケジュールします。[GitHub repo](https://github.com/evernote/android-job) はデバイスが充電しているときにジョブを実行するように伝えるなど、もっと複雑なスケジューリングのオプションを提供します。

{% include jp-tweet_quote.html quote_text="Android ライブラリに関する素晴らしい記事を読みました。" %}

{% include asides/jp-android.markdown %}

## まとめ

Android を開発しながら、ライブラリが、さまざまな機能を実行するために書かれた定型句コードの量をどのようにして大きく減らすかを学んできました。また、これらライブラリをほとんどのケースで使用する方法についても学びました。利用可能なライブラリの中で最高のライブラリについて知ることは、アプリの品質改善に役立ち、少ない時間で多くを作成できます。ですから、これらライブラリについて知り、使用することは有利な状況につながることは間違いありません。

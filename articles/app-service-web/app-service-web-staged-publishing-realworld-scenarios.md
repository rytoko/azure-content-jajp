<properties
   pageTitle="Web アプリに対して DevOps 環境を効果的に使用する"
   description="デプロイメント スロットを使用してアプリケーションの複数の開発環境をセットアップおよび管理する方法を説明する"
   services="app-service\web"
   documentationCenter=""
   authors="sunbuild"
   manager="yochayk"
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="web"
   ms.date="05/31/2016"
   ms.author="sumuth"/>

# Web アプリに対して DevOps 環境を効果的に使用する

この記事では、開発、ステージング、品質保証、運用など、複数のバージョンのアプリケーションに合わせて Web アプリケーションのデプロイメントをセットアップおよび管理する方法について説明します。アプリケーションの各バージョンは、デプロイメント プロセスでの特定のニーズに対応する開発環境と考えることができます。たとえば、開発チームは品質保証環境を使用することで、変更を運用環境にプッシュする前にアプリケーションの品質をテストすることができます。複数の開発環境のセットアップは、困難な作業になる可能性があります。これらの環境全体にわたってリソース (コンピューティング、Web アプリ、データベース、キャッシュなど) を追跡および管理し、環境間でコンテンツをデプロイする必要があるからです。

## 非運用環境 (ステージング、開発、品質保証) のセットアップ
運用 Web アプリがアップされ実行されたら、次のステップとして非運用環境を作成します。デプロイメント スロットを使用するには、**Standard** または **Premium** の App Service プラン モードで実行していることを確認します。デプロイメント スロットは、実際には固有のホスト名を持つライブ Web アプリです。Web アプリのコンテンツと構成の各要素は、(運用スロットを含む) 2 つのデプロイ スロットの間でスワップすることができます。デプロイ スロットにアプリケーションをデプロイすることには、次のメリットがあります。

1. ステージング デプロイ スロットで Web アプリの変更を検証した後に、運用スロットにスワップできます。
2. スロットに Web アプリをデプロイした後に運用サイトにスワップすると、運用サイトへのスワップ前にスロットのすべてのインスタンスが準備されます。これにより、Web アプリをデプロイする際のダウンタイムがなくなります。トラフィックのリダイレクトはシームレスであるため、スワップ操作によって破棄される要求はありません。このワークフロー全体は、スワップ前の検証が必要ない場合、[自動スワップ](web-sites-staged-publishing.md#configure-auto-swap-for-your-web-app)を構成することで自動化できます。
3. スワップ後も、以前のステージング Web アプリ スロットに元の運用 Web アプリが残っているため、運用スロットにスワップした変更が想定どおりでない場合は、適切な動作が確認されている元の Web アプリにすぐに戻すことができます。

ステージング デプロイメント スロットをセットアップするには、「[Azure App Service で Web アプリ用にステージング環境をセットアップする](web-sites-staged-publishing.md)」を参照してください。どの環境も独自のリソース セットを備える必要があります。たとえば、Web アプリでデータベースを使用する場合は、運用 Web アプリとステージング Web アプリの両方がそれぞれ別々のデータベースを使用する必要があります。ステージング開発環境をセットアップする場合は、データベース、ストレージ、キャッシュなどのステージング開発環境リソースを追加します。

## 複数の開発環境を使用する例

プロジェクトは少なくとも 2 つの環境 (開発環境および運用環境) でソース コード管理に従う必要があります。ただし、コンテンツ管理システムやアプリケーション フレームワークなどを使用する場合には、アプリケーションがそのままでは、このシナリオをサポートしないといった問題に直面する可能性があります。このことは、以下に説明するいくつかの一般的なフレームワークの場合に当てはまります。CMS/フレームワークを使用する場合は、以下に示すような多くの質問が頭に浮かびます。

1. それをさまざまな環境に分割するにはどうすればよいか
2. 変更できるファイルはどれか、フレームワークのバージョン更新プログラムに影響を与えないファイルはどれか
3. 環境ごとに構成を管理するにはどうすればよいか
4. モジュール/プラグインのバージョン更新プログラム、コア フレームワークのバージョン更新プログラムを管理するにはどうすればよいか

プロジェクトのために複数の環境をセットアップする方法は多数あります。以下の例では、それぞれのアプリケーションについて該当する方法を 1 つだけ紹介します。

### WordPress
このセクションでは、WordPress のスロットを使用してデプロイメント ワークフローをセットアップする方法を説明します。ほとんどの CMS ソリューションの場合と同様に WordPress でも、そのまま、複数の開発環境を使用することはできません。App Service Web Apps には、コードの外部に構成設定を容易に格納できる機能がいくつかあります。

ステージング スロットを作成する前に、複数の環境をサポートするようにアプリケーション コードをセットアップします。WordPress で複数の環境をサポートするには、ローカル開発 Web アプリの `wp-config.php` を編集し、ファイルの先頭に次のコードを追加する必要があります。これにより、アプリケーションは選択された環境に基づく適切な構成を利用できるようになります。


	// Support multiple environments
	// set the config file based on current environment
	/**/
	if (strpos(filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING),'localhost') !== false) {
	    // local development
	    $config_file = 'config/wp-config.local.php';
	}
	elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
	      //single file for all azure development environments
	      $config_file = 'config/wp-config.azure.php';
	}
	$path = dirname(__FILE__) . '/';
	if (file_exists($path . $config_file)) {
	    // include the config file if it exists, otherwise WP is going to fail
	    require_once $path . $config_file;
	}



Web アプリのルートの下に `config` というフォルダーを作成し、`wp-config.azure.php` (Azure 環境) と `wp-config.local.php` (ローカル環境) の 2 つのファイルを追加します。

`wp-config.local.php` に次のコードをコピーします。

```
	
	<?php
	
	// MySQL settings
	/** The name of the database for WordPress */
	
	define('DB_NAME', 'yourdatabasename');
	
	/** MySQL database username */
	define('DB_USER', 'yourdbuser');
	
	/** MySQL database password */
	define('DB_PASSWORD', 'yourpassword');
	
	/** MySQL hostname */
	define('DB_HOST', 'localhost');
	/**
	 * For developers: WordPress debugging mode.
	 * * Change this to true to enable the display of notices during development.
	 * It is strongly recommended that plugin and theme developers use WP_DEBUG
	 * in their development environments.
	 */
	define('WP_DEBUG', true);
	
	//Security key settings
	define('AUTH_KEY',         'put your unique phrase here');
	define('SECURE_AUTH_KEY',  'put your unique phrase here');
	define('LOGGED_IN_KEY',    'put your unique phrase here');
	define('NONCE_KEY',        'put your unique phrase here');
	define('AUTH_SALT',        'put your unique phrase here');
	define('SECURE_AUTH_SALT', 'put your unique phrase here');
	define('LOGGED_IN_SALT',   'put your unique phrase here');
	define('NONCE_SALT',       'put your unique phrase here');
	
	/**
	 * WordPress Database Table prefix.
	 *
	 * You can have multiple installations in one database if you give each a unique
	 * prefix. Only numbers, letters, and underscores please!
	 */
	$table_prefix  = 'wp_';
```

上記のセキュリティ キーを設定することは、Web アプリをハッキングから守ることに役立つので、一意の値を使用します。前述のセキュリティ キーの文字列を生成する必要がある場合は、自動ジェネレーターに移動し、この[リンク](https://api.wordpress.org/secret-key/1.1/salt)を使用して新しいキー/値を作成します。

`wp-config.azure.php` に次のコードをコピーします。


```

	<?php
	// MySQL settings
	/** The name of the database for WordPress */
	
	define('DB_NAME', getenv('DB_NAME'));
	
	/** MySQL database username */
	define('DB_USER', getenv('DB_USER'));
	
	/** MySQL database password */
	define('DB_PASSWORD', getenv('DB_PASSWORD'));
	
	/** MySQL hostname */
	define('DB_HOST', getenv('DB_HOST'));
	
	/**
	* For developers: WordPress debugging mode.
	*
	* Change this to true to enable the display of notices during development.
	* It is strongly recommended that plugin and theme developers use WP_DEBUG
	* in their development environments.
	* Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
	* do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
	* with WP_DEBUG_LOG so that errors are not displayed on the page */
	
	*/
	define('WP_DEBUG', getenv('WP_DEBUG'));
	define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
	define('WP_DEBUG_DISPLAY',false);
	
	//Security key settings
	/** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
	define('AUTH_KEY' ,getenv('DB_AUTH_KEY'));
	define('SECURE_AUTH_KEY',  getenv('DB_SECURE_AUTH_KEY'));
	define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
	define('NONCE_KEY', getenv('DB_NONCE_KEY'));
	define('AUTH_SALT',  getenv('DB_AUTH_SALT'));
	define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
	define('LOGGED_IN_SALT',   getenv('DB_LOGGED_IN_SALT'));
	define('NONCE_SALT',   getenv('DB_NONCE_SALT'));
	
	/**
	* WordPress Database Table prefix.
	*
	* You can have multiple installations in one database if you give each a unique
	* prefix. Only numbers, letters, and underscores please!
	*/
	$table_prefix  = getenv('DB_PREFIX');
```

#### 相対パスの使用
最後に、WordPress アプリが相対パスを使用できるようにします。WordPress では、データベースに URL 情報を格納します。そのため、環境間でのコンテンツの移動が難しくなります。ローカルからステージまたはステージから運用環境に移動するたびに、データベースを更新する必要があるためです。環境間でデプロイするたびにデータベースのデプロイで問題が発生するリスクを軽減するには、[Relative Root リンク プラグイン](https://wordpress.org/plugins/root-relative-urls/)を使用します。このプラグインは、WordPress 管理者のダッシュボードを使用してインストールするか、[ここ](https://downloads.wordpress.org/plugin/root-relative-urls.zip)から手動でダウンロードします。

`wp-config.php` ファイルに、`That's all, stop editing!` コメントの前に次のエントリを追加します。

```

    define('WP_HOME', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_SITEURL', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_CONTENT_URL', '/wp-content');
	define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

WordPress の管理者用ダッシュボードの [`Plugins`] メニューでプラグインをアクティブにします。WordPress アプリの固定リンク設定を保存します。

#### 最終の `wp-config.php` ファイル
WordPress のコア更新プログラムはいずれも、`wp-config.php`、`wp-config.azure.php`、および `wp-config.local.php` ファイルに影響を与えません。最終的に、この `wp-config.php` ファイルは次のようになります。

```

	<?php
	/**
	 * The base configurations of the WordPress.
	 *
	 * This file has the following configurations: MySQL settings, Table Prefix,
	 * Secret Keys, and ABSPATH. You can find more information by visiting
	 *
	 * Codex page. You can get the MySQL settings from your web host.
	 *
	 * This file is used by the wp-config.php creation script during the
	 * installation. You don't have to use the web web app, you can just copy this file
	 * to "wp-config.php" and fill in the values.
	 *
	 * @package WordPress
	 */
	
	// Support multiple environments
	// set the config file based on current environment
	if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
	    $config_file = 'config/wp-config.local.php';
	}
	elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
	    $config_file = 'config/wp-config.azure.php';
	}
	
	
	$path = dirname(__FILE__) . '/';
	if (file_exists($path . $config_file)) {
	    // include the config file if it exists, otherwise WP is going to fail
	    require_once $path . $config_file;
	}
	
	/** Database Charset to use in creating database tables. */
	define('DB_CHARSET', 'utf8');
	
	/** The Database Collate type. Don't change this if in doubt. */
	define('DB_COLLATE', '');
	
	
	/* That's all, stop editing! Happy blogging. */
	
	define('WP_HOME', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_SITEURL', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_CONTENT_URL', '/wp-content');
	define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	
	/** Absolute path to the WordPress directory. */
	if ( !defined('ABSPATH') )
		define('ABSPATH', dirname(__FILE__) . '/');
	
	/** Sets up WordPress vars and included files. */
	require_once(ABSPATH . 'wp-settings.php');
```

#### ステージング環境のセットアップ
Azure Web 上で WordPress Web アプリが既に実行されている場合は、[Azure ポータル](https://portal.azure.com/)にログインして WordPress Web アプリに移動します。そうでない場合は、Marketplace から Web アプリを作成することができます。詳細については、[ここ](web-sites-php-web-site-gallery.md)をクリックしてください。**[設定]**、**[デプロイメント スロット]**、**[追加]** の順にクリックして、名前ステージでデプロイメント スロットを作成します。デプロイメント スロットは、上記で作成したプライマリ Web アプリと同じリソースを共有する別の Web アプリケーションです。

![ステージ デプロイメント スロットを作成する](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

別の MySQL データベース (たとえば、`wordpress-stage-db`) をリソース グループ `wordpressapp-group` に追加します。

 ![リソース グループに MySQL データベースを追加する](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

ステージ デプロイメント スロットの接続文字列を、新規に作成したデータベース `wordpress-stage-db` を指すように更新します。運用 Web アプリ `wordpressprodapp` とステージング Web アプリ `wordpressprodapp-stage` はそれぞれ別のデータベースを指す必要があるので注意してください。

#### 環境固有のアプリ設定の構成
開発者は、キー値の文字列ペアを、App Settings と呼ばれる Web アプリに関連付けられた構成情報の一部として Azure に格納することができます。実行時に、App Service Web Apps はこれらの値を自動的に取得し、Web アプリで実行されているコードから利用できるようにします。この機能は、パスワードを使用したデータベース接続文字列などの機密情報が `wp-config.php` などのファイルにクリア テキストとして出現しないので、セキュリティ上のメリットがあります。

以下に定義したこのプロセスは、WordPress アプリに関するファイルの変更とデータベースの変更の両方が含まれるので、更新を実行するときに役立ちます。

- WordPress バージョンのアップグレード
- 新しいプラグインの追加、プラグインの編集またはアップグレード
- 新しいテーマの追加、テーマの編集またはアップグレード

アプリ設定の構成:

- データベース情報
- WordPress のログ記録のオン/オフ
- WordPress のセキュリティ設定

![Wordpress Web アプリ用のアプリ設定](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

運用 Web アプリとステージ スロット用に次のアプリ設定が追加されていることを確認します。運用 Web アプリとステージング Web アプリはそれぞれ別のデータベースを使用するので注意してください。WP\_ENV を除くのすべての設定パラメーターの **[スロット設定]** チェック ボックスをオフにします。これにより、Web アプリの構成と、ファイルの内容およびデータベースがスワップされます。**[スロット設定]** が**オン**の場合、スワップ操作の実行時に Web アプリのアプリ設定と接続文字列の構成は環境間で移動されません。そのため、データベースの変更が存在していても、運用 Web アプリは中断されません。

WebMatrix または任意のツール (FTP、Git、PhpMyAdmin など) を使用して、ローカル開発環境 Web アプリをステージ Web アプリとデータベースにデプロイします。

![WordPress Web アプリの [Web Matrix 公開] ダイアログ ボックス](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

ステージング Web アプリを参照し、テストします。Web アプリのテーマが更新されるシナリオについて検討します。ここでは、ステージング Web アプリを扱います。

![スロットをスワップする前にステージング Web アプリを参照する](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)


 すべて問題なければ、ステージング Web アプリの **[スワップ]** ボタンをクリックして、コンテンツを運用環境に移動します。この場合、**スワップ**操作のたびに、環境間で Web アプリとデータベースをスワップします。

![Wordpress のプレビュー変更をスワップする](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

 > [AZURE.NOTE]
 ファイルのみをプッシュすればよいシナリオでは (データベースは更新されない)、スワップを実行する前に、Azure ポータルの Web アプリ設定ブレードで、データベースに関連するすべての*アプリ設定*と*接続文字列の設定*について、**[スロット設定]** を**オン**にします。この場合、DB\_NAME、DB\_HOST、DB\_PASSWORD、DB\_USER、既定の接続文字列設定は、**スワップ**の実行時に、プレビュー変更に表示されません。この時点で、**スワップ**操作を完了すると、WordPress Web アプリには更新されたファイル**だけ**が含まれます。

スワップを実行する前の運用 WordPress Web アプリを次に示します ![スロットをスワップする前の運用 Web アプリ](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

スワップ操作を実施したところ、運用 Web アプリでテーマが更新されました。

![スロットをスワップした後の運用 Web アプリ](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

**ロールバック**が必要な場合は、運用 Web アプリ設定に移動し、**[スワップ]** ボタンをクリックして、Web アプリとデータベースを運用環境からステージング スロットにスワップします。特定の時点で**スワップ**操作にデータベースの変更が追加された場合は、ステージング Web アプリに次回再デプロイするときに、ステージング Web アプリの現在のデータベース (前の運用データベースまたはステージ データベースと考えられます) にデータベースの変更をデプロイする必要があることに注意してください。

#### 概要
データベースを使用する任意のアプリケーションのプロセスを汎用化するには

1. ローカル環境にアプリケーションをインストールします。
2. 環境固有の構成を含めます (ローカルおよび Azure Web アプリケーション)。
3. App Service Web Apps で環境をセットアップします (ステージング、運用環境)。
4. 運用アプリケーションが Azure で既に実行されている場合は、運用コンテンツ (ファイル/コード、およびデータベース) をローカルおよびステージング環境と同期します。
5. ローカル環境でアプリケーションを開発します。
6. 運用 Web アプリをメンテナンス モードまたはロック モードに設定し、データベースのコンテンツを運用環境からステージングおよび開発環境へ同期します。
7. ステージング環境とテスト環境にデプロイします。
8. 運用環境にデプロイします。
9. 手順 4 ～ 6 を繰り返します。

### Umbraco
このセクションでは、Umbraco CMS でカスタム モジュールを使用して、複数の DevOps 環境間でデプロイする方法について説明します。この例では、複数の開発環境を管理するための別のアプローチを紹介します。

[Umbraco CMS](http://umbraco.com/) は、多くの開発者によって使用されている一般的な .NET CMS ソリューションの 1 つです。Umbraco CMS では、開発環境からステージング環境へ、そして運用環境へとデプロイするための [Courier2](http://umbraco.com/products/more-add-ons/courier-2) モジュールを提供します。Umbraco CMS Web アプリのローカル開発環境は、Visual Studio または WebMatrix を使用して簡単に作成することができます。

1. Visual Studio で Umbraco Web アプリを作成するには、[ここ](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget)をクリックします。
2. WebMatrix で Umbraco Web アプリを作成するには、[ここ](http://umbraco.com/help-and-support/video-tutorials/getting-started/working-with-webmatrix)をクリックします。

アプリケーションにおいて `install` フォルダーを削除することを忘れないでください。また、このフォルダーを、ステージ Web アプリまたは運用 Web アプリに決してアップロードしないでください。このチュートリアルでは、WebMatrix を使用します。

#### ステージング環境のセットアップ
Umbraco CMS の Web アプリが既にアップされ実行されている場合は、Umbraco CMS Web アプリ用に前述のデプロイメント スロットを作成します。そうでない場合は、Marketplace から Web アプリを作成することができます。

ステージ デプロイメント スロットの接続文字列を、新規に作成したデータベース **umbraco-stage-db** を指すように更新します。運用 Web アプリ (umbraositecms 1) と ステージング Web アプリ (umbracositecms-1-stage) は、それぞれ別のデータベースを指す**必要があります**。

![新しいステージング データベースでステージング Web アプリの接続文字列を更新する](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

デプロイメント スロット **ステージ**で **[発行設定の取得]** をクリックします。これにより、発行設定ファイルがダウンロードされます。このファイルには、Visual Studio または Webmatrix がアプリケーションをローカル開発 Web アプリから Azure Web アプリへ発行するのに必要とする情報がすべて格納されています。

 ![ステージング Web アプリの発行設定を取得する](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)

- **WebMatrix** または **Visual Studio** でローカル開発 Web アプリを開きます。このチュートリアルでは Web Matrix を使用します。そこで、まず、ステージング Web アプリの発行設定ファイルをインポートする必要があります。

![Web Matrix を使用して Umbraco の発行設定をインポートする](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

- ダイアログ ボックスで変更を確認し、ローカル Web アプリを Azure Web アプリ *umbracositecms-1-stage* にデプロイします。ファイルをステージング Web アプリに直接デプロイする場合は、`~/app_data/TEMP/` フォルダー内のファイルを除外します。これらのファイルは、ステージ Web アプリが最初に起動されたときに再生成されます。`~/app_data/umbraco.config` ファイルも再生成されるので除外します。

![Web Matrix での発行に関する変更を確認する](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

- Umbraco ローカル Web アプリがステージング Web アプリに正常に発行されたら、ステージング Web アプリを参照し、いくつかのテストを実行して問題を排除します。

#### Courier2 デプロイメント モジュールのセットアップ
[Courier2](http://umbraco.com/products/more-add-ons/courier-2) モジュールを使用すると、ステージング Web アプリから運用 Web アプリへ、右クリックのみで、コンテンツ、スタイル シート、開発モジュールなどをプッシュすることができます。これにより、更新プログラムをデプロイする場合に、手間をかけずに済むので、運用 Web アプリが中断されるリスクを減らすことができます。ドメイン `*.azurewebsites.net` とカスタム ドメイン (たとえば、http://abc.com) 用に Courier2 のライセンスを購入します。ライセンスを購入したら、ダウンロードしたライセンス (.LIC ファイル) を `bin` フォルダーに置きます。

![Bin フォルダーの下にライセンス ファイルをドロップする](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

[ここ](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/)から Courier2 パッケージをダウンロードします。ステージ Web アプリ (たとえば、http://umbracocms-site-stage.azurewebsites.net/umbraco) にログオンし、**[開発者]** メニューをクリックし、**[パッケージ]** を選択します。**[ローカル パッケージのインストール]** をクリックします。

![Umbraco パッケージ インストーラー](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

インストーラーを使用して courier2 パッケージをアップロードします。

![Courier モジュールのパッケージをアップロードする](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

構成を行うには、Web アプリの **Config** フォルダーにある courier.config ファイルを更新する必要があります。

```xml
<!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1.azurewebsites.net</url>
            <user>0</user>
            <!--<login>user@email.com</login> -->
            <!-- <password>user_password</password>-->
           <!-- <passwordEncoding>Clear</passwordEncoding>-->
           </repository>
  </repositories>
 ```

`<repositories>` で、運用サイトの URL とユーザー情報を入力します。既定の Umbraco メンバーシップ プロバイダーを使用している場合は、<user> セクションで管理ユーザーの ID を追加します。カスタム Umbraco メンバーシップ プロバイダーを使用している場合は、Courier2 モジュールへの `<login>`、`<password>` を使用し、運用サイトに接続する方法を理解します。詳細については、Courier モジュールの[ドキュメント](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation)を確認してください。

同様に、運用サイトで Courier モジュールをインストールし、ここで示すように、それぞれの courier.config ファイルでステージ Web アプリをポイントするように構成します。

```xml
  <!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  -->
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1-stage.azurewebsites.net</url>
            <user>0</user>
           </repository>
  </repositories>
```

Umbraco CMS Web アプリのダッシュボードで [Courier2] タブをクリックし、場所を選択します。`courier.config` で説明したように、リポジトリ名を確認する必要があります。運用 Web アプリとステージング Web アプリの両方で同じ操作を行います。

![宛先 Web アプリ リポジトリを表示する](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

次に、ステージング サイトから運用サイトに何らかのコンテンツをデプロイします。[コンテンツ] に移動し、既存のページを選択するか、または新しいページを作成します。Web アプリから既存のページを選択します。ページのタイトルが**[作業の開始 – 新規]** に変更されたら、**[保存して発行]** をクリックします。

![ページのタイトルを変更して公開する](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

今度は、変更されたページを選択し、*右クリック*して、すべてのオプションを表示します。**[Courier]** をクリックして、[デプロイメント] ダイアログを表示します。**[デプロイ]** をクリックして、デプロイを開始します。

![Courier モジュールのデプロイメント ダイアログ ボックス](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

変更を確認し、[続行] をクリックします。

![Courier モジュールのデプロイメント ダイアログ ボックスの確認対象の変更](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

デプロイメント ログに、デプロイが正常に完了したかどうかが示されます。

 ![Courier モジュールからのデプロイメント ログを表示する](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

運用 Web アプリを参照し、変更が反映されているかどうかを確認します。

 ![運用 Web アプリを参照する](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Courier の詳細な使用方法については、ドキュメントを参照してください。

#### Umbraco CMS バージョンのアップグレード方法

Courier では、Umbraco CMS のバージョン間のアップグレードでのデプロイを支援しません。Umbraco CMS のバージョンをアップグレードする場合は、カスタム モジュールまたはサード パーティ モジュールとの互換性、および Umbraco のコア ライブラリとの互換性が大丈夫かどうかを確認する必要があります。ベスト プラクティスを以下に示します。

1. アップグレードを実行する前に、Web アプリとデータベースを必ずバックアップしてください。Azure Web App では、バックアップ機能を使用して Web サイトの自動バックアップをセットアップできます。また、必要に応じて、復元機能を使用してサイトを復元することができます。詳細については、「[Web アプリのバックアップ方法](web-sites-backup.md)」および「[Web アプリの復元方法](web-sites-restore.md)」を参照してください。

2. 現在使用しているサードパーティ製のパッケージが、アップグレード先のバージョンと互換性があるかどうかを確認します。パッケージのダウンロード ページで、プロジェクトが Umbraco CMS のバージョンと互換性があることを確認します。

Web アプリをローカルでアップグレードする方法の詳細については、[ここ](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general)に示すガイドラインに従ってください。

ローカル開発サイトがアップグレードされたら、変更内容をステージング Web アプリに発行します。アプリケーションをテストし、結果がすべてが良好であれば、**[スワップ]** ボタンをクリックして、ステージング サイトを運用 Web アプリに**スワップ**します。**スワップ**操作の実行時には、Web アプリの構成で影響を受ける変更を確認できます。この **スワップ**操作を使用して、Web アプリとデータベースをスワップします。すなわち、スワップ後、運用 Web アプリは umbraco-stage-db データベースを指すようになり、ステージング Web アプリは umbraco-prod-db データベースを指すようになります。

![Umbraco CMS をデプロイするためのスワップ プレビュー](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

Web アプリとデータベースの両方をスワップすることのメリット:
1. アプリケーションに問題がある場合、別の**スワップ**操作で Web アプリの前のバージョンにロールバックすることができます。
2. アップグレードでは、ステージング Web アプリから運用 Web アプリおよびデータベースに、ファイルおよびデータベースをデプロイする必要があります。ファイルとデータベースをデプロイする場合は、問題となる可能性のある事柄が多数あります。スロットの**スワップ**機能を使用することにより、アップグレード中のダウンタイムを短縮し、変更のデプロイ時に障害が発生するリスクを軽減することができます。
3. [運用環境でのテスト](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)機能を使用して**A/B テスト**を実行することができます。

この例では、Umbraco Courier モジュールに類似したカスタム モジュールを作成して複数の環境でデプロイメントを管理する場合のプラットフォームの柔軟性について示しています。

## 参照
[Azure App Service を使用したアジャイル ソフトウェア開発](app-service-agile-software-development.md)

[Azure App Service の Web アプリのステージング環境を設定する](web-sites-staged-publishing.md)

[運用環境以外のデプロイメント スロットへの Web アクセスを禁止する方法](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

<!---HONumber=AcomDC_0615_2016-->
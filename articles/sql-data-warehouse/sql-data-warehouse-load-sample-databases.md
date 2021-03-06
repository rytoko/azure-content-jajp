<properties
   pageTitle="SQL Data Warehouse へのサンプル データのロード | Microsoft Azure"
   description="SQL Data Warehouse へのサンプル データのロード"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#SQL Data Warehouse へのサンプル データのロード

次の簡単な手順で Adventure Works サンプル データベースをロードしてクエリを実行します。これらのスクリプトでは、まず sqlcmd を使用して、テーブルとビューを作成する SQL を実行します。テーブルが作成されると、スクリプトは bcp を使用してデータを読み込みます。まだ sqlcmd と bcp をインストールしていない場合は、リンクに従って [bcp をインストール][]し、[sqlcmd をインストール][]します。

##サンプル データの読み込み

1. [SQL Data Warehouse の Adventure Works サンプル スクリプト][]の zip ファイルをダウンロードします。

2. ダウンロードした zip からローカル コンピューターのディレクトリにファイルを抽出します。

3. 抽出したファイル aw\_create.bat を編集し、ファイルの先頭にある以下の変数を設定します。"=" とパラメーターの間にスペースを入れないようにします。編集内容の例を次に示します。

    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```

4. Windows コマンド プロンプトから編集した aw\_create.bat を実行します。編集バージョンの aw\_create.bat を保存したディレクトリにいることを確認します。このスクリプトでは、次のことが行われます。
	* データベースに既に存在する Adventure Works のテーブルまたはビューを削除します
	* Adventure Works のテーブルとビューを作成します
	* bcp を使用して Adventure Works の各テーブルをロードします
	* Adventure Works の各テーブルの行数を検証します
	* Adventure Works の各テーブルのすべての列の統計情報を収集します


##サンプル データのクエリ

SQL Data Warehouse にサンプル データをロードしたら、いくつかのクエリをすぐに実行できます。クエリを実行するには、Azure SQL DW に新しく作成した Adventure Works データベースに Visual Studio と SSDT を使用して接続します (詳しくは、[Visual Studio を使用したクエリ][]に関するドキュメントを参照)。

従業員のすべての情報を取得する簡単な SELECT ステートメントの例:

```sql
SELECT * FROM DimEmployee;
```

GROUP BY などのコンストラクトを使用する、より複雑なクエリを実行して、日ごとの総売上金額の合計を参照する例:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

SELECT と WHERE 句を使用して、ある日付以前の注文をフィルター処理する例:

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

SQL Data Warehouse は、SQL Server がサポートするほぼすべての T-SQL 構造をサポートします。相違点については、[コードの移行][]ドキュメントを参照してください。

## 次のステップ
サンプル データをクエリしたので、SQL Data Warehouse の[開発][]、[ロード][]、[移行][]の方法を確認してください。

<!--Image references-->

<!--Article references-->
[移行]: sql-data-warehouse-overview-migrate.md
[開発]: sql-data-warehouse-overview-develop.md
[ロード]: sql-data-warehouse-overview-load.md
[Visual Studio を使用したクエリ]: sql-data-warehouse-query-visual-studio.md
[コードの移行]: sql-data-warehouse-migrate-code.md
[bcp をインストール]: sql-data-warehouse-load-with-bcp.md
[sqlcmd をインストール]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[SQL Data Warehouse の Adventure Works サンプル スクリプト]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip

<!---HONumber=AcomDC_0817_2016-->
<properties
   pageTitle="Azure Automation の Hybrid Runbook Worker | Microsoft Azure"
   description="この記事では、ローカル データ センター内のコンピューターで Runbook を実行できるようにする Azure Automation の機能である Hybrid Runbook Worker のインストールと使用について説明します。"
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="bwren" />

# Azure Automation の Hybrid Runbook Worker

Azure Automation の Runbook は、Azure クラウドで実行されるため、ローカル データ センター内のリソースにはアクセスできません。Azure Automation の Hybrid Runbook Worker 機能を使用すれば、ローカル リソースを管理するために、データ センターに配置されたマシンで Runbook を実行することができます。Runbook は Azure Automation で格納および管理されてから、1 つ以上のオンプレミス マシンに配信されます。

次の図にこの機能を示します。<br>

![Hybrid Runbook Worker の概要](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-overview.png)

Hybrid Runbook Worker として機能し、Azure Automation の Runbook を実行するように、データ センターの 1 つ以上のコンピューターを指定できます。各 worker には、Microsoft Operations Management Suite および Azure Automation Runbook 環境に接続できる Microsoft 管理エージェントが必要です。Operations Management Suite は、管理エージェントをインストールして管理し、worker の機能を監視するためにのみ使用されます。Runbook の配信と Runbook の実行指示は、Azure Automation によって行われます。

![Hybrid Runbook Worker のコンポーネント](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-components_b.png)

>[AZURE.NOTE] Operational Insights は Operations Management Suite への統合作業中であり、ポータルおよびドキュメントではどちらかの名前が使用されている場合があります。

Hybrid Runbook Worker をサポートするための受信ファイアウォールの要件はありません。ローカル コンピューター上のエージェントは、クラウドでの Azure Automation とのすべての通信を開始します。Runbook が開始されると、Azure Automation はエージェントによって取得される指示を作成します。次に、エージェントは、それを実行する前に、Runbook と任意のパラメーターをプル ダウンします。Azure Automation の Runbook で使用される[資産](http://msdn.microsoft.com/library/dn939988.aspx)も取得します。

>[AZURE.NOTE] Hybrid Runbook Worker は、現時点では [DSC 構成](automation-dsc-overview.md)をサポートしていません。


## Hybrid Runbook Worker のグループ

各 Hybrid Runbook Worker は、エージェントのインストール時に指定する Hybrid Runbook Worker グループのメンバーです。グループには単一のエージェントを含めることができますが、高可用性グループに複数のエージェントをインストールすることができます。

Hybrid Runbook Worker で Runbook を開始する場合は、実行されるグループを指定します。要求を処理するワーカーは、グループのメンバーが決定します。特定のワーカーを指定することはできません。

## Hybrid Runbook Worker の要件

Hybrid Runbook のジョブを実行するには、少なくとも 1 台のオンプレミス コンピューターを指定する必要があります。このコンピューターには次のものが必要です。

- Windows Server 2012 またはそれ以降
- Windows PowerShell 4.0 またはそれ以降
- 少なくとも 2 つのコアと 4 GB の RAM

Hybrid Worker では次の推奨事項を考慮してください。

- 高可用性のため、グループごとに複数の Hybrid Worker を指定します。
- Hybrid Worker は、Service Management Automation または System Center Orchestrator の Runbook サーバーと共存できます。
- ジョブが完了すると Azure Automation にジョブのデータが返送されるため、Automation アカウントのリージョン内またはその近くに物理的に存在するコンピューターを使用することを検討します。


>[AZURE.NOTE] Hybrid Runbook Worker Version 7.2.11136.0 では、現在、PowerShell スクリプトによるプロキシ サーバー経由の通信のみがサポートされています。PowerShell ワークフロー スクリプトのサポートは今後のリリースで提供される予定です。

### プロキシとファイアウォール設定の構成

オンプレミスの Hybrid Runbook Worker を Microsoft Operations Management Suite (OMS) サービスに接続して登録するには、その Hybrid Runbook Worker は、以下で説明するポート番号と URL にアクセスできる必要があります。OMS に接続するには、[Microsoft Monitoring Agent に必要なポートと URL](../log-analytics/log-analytics-proxy-firewall.md#configure-proxy-and-firewall-settings-with-the-microsoft-monitoring-agent) のほかに、このポートと URL が必要です。エージェントと OMS サービス間の通信にプロキシ サーバーを使用する場合、適切なリソースにアクセスできることを確認する必要があります。ファイアウォールを使用してインターネットへのアクセスを制限している場合は、アクセスを許可するようにファイアウォールを構成する必要があります。

Automation と通信するには、次の一覧に示すポートと URL が Hybrid Runbook Worker に必要です。

- ポート: 発信インターネット アクセスには TCP 443 のみが必要です
- グローバル URL: *.azure-automation.net

Automation アカウントが特定のリージョンに対して定義されており、そのリージョンのデータセンターとの通信を制限する必要がある場合は、次の表に示す各リージョンの DNS レコードを使用します。

|**[リージョン]**|**DNS レコード**|
|--------------|--------------|
|米国中南部|scus-jobruntimedata-prod-su1.azure-automation.net|
|米国東部 2|eus2-jobruntimedata-prod-su1.azure-automation.net|
|西ヨーロッパ|we-jobruntimedata-prod-su1.azure-automation.net|
|北ヨーロッパ|ne-jobruntimedata-prod-su1.azure-automation.net|
|カナダ中部|cc-jobruntimedata-prod-su1.azure-automation.net|
|東南アジア|sea-jobruntimedata-prod-su1.azure-automation.net|
|インド中部|cid-jobruntimedata-prod-su1.azure-automation.net|
|東日本|jpe-jobruntimedata-prod-su1.azure-automation.net|
|オーストラリア東南部|ase-jobruntimedata-prod-su1.azure-automation.net|


## Hybrid Runbook Worker のインストール

Hybrid Runbook Worker をインストールして構成する手順は次のとおりです。最初の 2 つのステップは Automation 環境に対して 1 回だけ実行し、残りのステップは worker コンピューターごとに繰り返します。

### 1\.Operations Management Suite のワークスペースを作成する

Operations Management Suite のワークスペースがまだない場合は、[ワークスペースのセットアップ](https://technet.microsoft.com/library/mt484119.aspx)に関するページの手順に従って作成します。既存のワークスペースがある場合は、それを使用できます。

### 手順 2.Operations Management Suite ワークスペースに Automation ソリューションを追加します。

ソリューションにより、Operations Management Suite に機能が追加されます。Automation ソリューションは、Hybrid Runbook Worker のサポートなど、Azure Automation 用の機能を追加します。ソリューションをワークスペースに追加すると、次の手順でインストールする worker コンポーネントがエージェント コンピューターに自動的にプッシュダウンされます。

「[To add a solution using the Solutions Gallery (ソリューション ギャラリーを使用してソリューションを追加するには)](../log-analytics/log-analytics-add-solutions.md)」の説明に従って、Operations Management Suite のワークスペースに **Automation** ソリューションを追加します。

### 3\.Microsoft Monitoring Agent をインストールする

Microsoft Monitoring Agent は Operations Management Suite にコンピューターを接続します。エージェントをオンプレミスのコンピューターにインストールし、ワークスペースに接続すると、Hybrid Runbook Worker に必要なコンポーネントが自動的にダウンロードされます。

「[Windows コンピューターを Log Analytics に接続する](../log-analytics/log-analytics-windows-agents.md)」の手順に従って、オンプレミスのコンピューターにエージェントをインストールします。コンピューターごとにこのプロセスを繰り返して、複数の worker を環境に追加できます。

エージェントが Operations Management Suite に正常に接続すると、Operations Management Suite の **[設定]** ウィンドウの **[接続されているソース]** タブにエージェントが表示されます。C:\\Program Files\\Microsoft Monitoring Agent\\Agent に **AzureAutomationFiles** という名前のフォルダーが作成されていることを調べて、エージェントが Automation ソリューションを正常にダウンロードしたことを確認できます。Hybrid Runbook Worker のバージョンを確認するには、C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\AzureAutomation\\ and note the \\<*バージョン*> サブフォルダーに移動します。


### 4\.Runbook 環境をインストールして、Azure Automation に接続する

エージェントを Operations Management Suite に追加すると、Automation ソリューションは、**Add-HybridRunbookWorker** コマンドレットを含む **HybridRegistration** PowerShell モジュールをプッシュダウンします。コンピューターに Runbook 環境をインストールして、Azure Automation に登録する場合は、このコマンドレットを使用します。

管理者モードで PowerShell セッションを開き、次のコマンドを実行してモジュールをインポートします。

	cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation<version>\HybridRegistration"
	Import-Module HybridRegistration.psd1

次に、以下の構文を使用して、**Add-HybridRunbookWorker** コマンドレットを実行します。

	Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

このコマンドレットに必要な情報は、Azure ポータルの **[キーの管理]** ブレードから取得できます。Automation アカウントの [要素] パネルにあるキー アイコンをクリックして、このブレードを開くことができます。

![Hybrid Runbook Worker の概要](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

- **Name** は、Hybrid Runbook Worker グループの名前です。Automation アカウントにこのグループが既に存在する場合は、現在のコンピューターがそれに追加されます。まだ存在しない場合は、追加されます。
- **EndPoint** は、**[キーの管理]** ブレードの **[URL]** フィールドです。
- **Token** は、**[キーの管理]** ブレードの **[プライマリ アクセス キー]** です。

インストールに関する詳細な情報を受け取るには、**Add-HybridRunbookWorker** で **-Verbose** スイッチを使用します。

### 5\.PowerShell モジュールをインストールする

Runbook は、Azure Automation 環境にインストールされているモジュールで定義されているアクティビティとコマンドレットをすべて使用できます。これらのモジュールはオンプレミス マシンに自動的に配置されないため、手動でインストールする必要があります。ただし、Azure モジュールは既定でインストールされ、すべての Azure サービスのコマンドレットと Azure Automation のアクティビティにアクセスできます。

Hybrid Runbook Worker 機能の主な目的はローカル リソースを管理することであるため、ほとんどの場合、これらのリソースをサポートするモジュールをインストールする必要があります。Windows PowerShell モジュールのインストールについては、「[モジュールのインストール](http://msdn.microsoft.com/library/dd878350.aspx)」を参照してください。

## Hybrid Runbook Worker の削除

オンプレミスのコンピューターで **Remove-HybridRunbookWorker** コマンドレットを実行することにより、そのコンピューターから Hybrid Runbook Worker を削除できます。削除処理の詳細なログを取得するには、**-Verbose** スイッチを使用します。

## Hybrid Runbook Worker での Runbook の開始

「[Azure Automation での Runbook の開始](automation-starting-a-runbook.md)」では、Runbook を開始するためのさまざまな方法を説明しています。Hybrid Runbook Worker は、Hybrid Runbook Worker グループの名前を指定できる **RunOn** オプションを追加します。グループが指定されている場合は、Runbook が取得され、そのグループ内のワーカーによって実行されます。このオプションが指定されていない場合は、Azure Automation で通常どおり実行されます。

Azure ポータルで Runbook を開始する際に、**Azure** または**ハイブリッド worker** を選択できる **[実行場所を選択して実行]** オプションが表示されます。**Hybrid Worker** を選択した場合は、ドロップダウン リストからグループを選択できます。

**RunOn** パラメーターを使用します。次のコマンドを使用し、Windows PowerShell を使用して MyHybridGroup という名前の Hybrid Runbook Worker グループで Test-Runbook という名前の Runbook を開始できます。

	Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

>[AZURE.NOTE] **RunOn** パラメーターは、バージョン 0.9.1 の Microsoft Azure PowerShell で **Start-AzureAutomationRunbook** コマンドレットに追加されました。以前のバージョンがインストールされている場合は、[最新バージョンをダウンロード](https://azure.microsoft.com/downloads/)する必要があります。Windows PowerShell から Runbook を開始するワークステーションでは、このバージョンをインストールするだけです。ワーカー コンピューターから Runbook を開始する場合を除き、そのコンピューターにインストールする必要はありません。Automation アカウントに最新バージョンの Azure Powershell をインストールする必要があるため、現時点ではある Runbook を別の Runbook の Hybrid Runbook Worker で開始することはできません。最新バージョンは Azure Automation で自動的に更新され、自動的にワーカーにすぐにプッシュダウンされます。

## Runbook のアクセス許可

Hybrid Runbook Worker で実行されている Runbook は Azure 外部のリソースにアクセスするため、[Azure のリソースへの認証に Runbook で通常使用される方法](automation-configuring.md#configuring-authentication-to-azure-resources)と同じものを使用することはできません。Runbook にローカル リソースに対して独自の認証機能を用意するか、すべての Runbook にユーザー コンテキストを提供する RunAs アカウントを指定することができます。

### Runbook の認証

既定で、Runbook はオンプレミス コンピューターのローカル システム アカウントのコンテキストで実行されるため、アクセスするリソースを独自に認証する必要があります。

資格情報を指定できるコマンドレットで Runbook の[資格情報](http://msdn.microsoft.com/library/dn940015.aspx)資産と[証明書](http://msdn.microsoft.com/library/dn940013.aspx)資産を使用することで、さまざまなリソースへの認証が可能になります。次の例は、コンピューターを再起動する Runbook の一部を示しています。資格情報資産から資格情報を取得し、変数資産からはコンピューター名を取得して、Restart-Computer コマンドレットでこれらの値を使用します。

	$Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
	$Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

	Restart-Computer -ComputerName $Computer -Credential $Cred

また、[PSCredential 共通パラメーター](http://technet.microsoft.com/library/jj129719.aspx)で指定された資格情報で別のコンピューター上でコード ブロックを実行できるようにする [InlineScript](automation-powershell-workflow.md#inline-script) を利用することもできます。

### RunAs アカウント

Runbook でローカル リソースへの独自の認証機能を用意するのではなく、ハイブリッド worker グループの **RunAs** アカウントを指定することができます。ローカル リソースに対するアクセス権を持つ[資格情報資産](automation-credentials.md)を指定すると、グループ内の Hybrid Runbook Worker で Runbook を実行するときに、その資格情報ですべての Runbook が実行されます。

資格情報のユーザー名は、次の形式にする必要があります。

- ドメイン\\ユーザー名
- username@domain
- ユーザー名 (オンプレミス コンピューターのローカルのアカウントの場合)


ハイブリッド worker グループの RunAs アカウントを指定するには、以下の手順を実行します。

1. ローカル リソースに対するアクセス権を持つ[資格情報資産](automation-credentials.md)を作成します。
2. Azure ポータルで Automation アカウントを開きます。
2. **[ハイブリッド Worker グループ]** タイルを選択し、グループを選択します。
3. **[すべての設定]**、**[ハイブリッド Worker グループの設定]** の順に選択します。
4. **[実行者]** を **[既定]** から **[カスタム]** に変更します。
5. 資格情報を選択し、**[保存]** をクリックします。


## Hybrid Runbook Worker の Runbook の作成

Azure Automation で実行される Runbook と、Hybrid Runbook Worker で実行される Runbook の構造に違いはありません。ただし、通常、Hybrid Runbook Worker の Runbook はデータ センター内のローカル リソースを管理しますが、Azure Automation の Runbook は Azure クラウド内のリソースを管理するため、ほとんどの場合、それぞれで使用する Runbook は大きく異なります。

Azure Automation で Hybrid Runbook Worker 用に Runbook を編集することはできますが、エディターで Runbook のテストを試行する場合は困難な場合があります。ローカル リソースにアクセスする PowerShell モジュールを Azure Automation 環境にインストールできない場合があります。その場合、テストは失敗します。必要なモジュールをインストールすれば、Runbook は実行されますが、ローカル リソースにアクセスして完全なテストを行うことはできません。

## Hybrid Runbook Worker での Runbook のトラブルシューティング

クラウドで実行される Runbook ジョブと同じように、[Runbook の出力とメッセージ](automation-runbook-output-and-messages.md)は Hybrid Worker から Azure Automation に送られます。他の Runbook と同じ方法で、Verbose および Progress ストリームも有効にできます。

ログは、各ハイブリッド worker の C:\\ProgramData\\Microsoft\\System Center\\Orchestrator\\7.2\\SMA\\Sandboxes にローカルに格納されます。

Runbook が正常に完了せず、ジョブの概要で状態が**中断**になっている場合は、トラブルシューティングの記事「[Hybrid Runbook Worker: Runbook ジョブが中断状態で終了する](automation-troubleshooting-hrw-runbook-terminates-suspended.md)」を参照してください。

## Service Management Automation との関係

[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) は、ローカル データ センターの Azure Automation でサポートされているものと同じ Runbook を実行できるようにする Microsoft Azure パック (WAP) のコンポーネントです。Azure Automation とは異なり、SMA には Runbook および SMA の構成を保持するためのデータベースと、Microsoft Azure パックの管理ポータルを含むローカル インストールが必要です。Azure Automation ではクラウドでこれらのサービスが提供されるため、必要なのはローカル環境での Hybrid Runbook Worker の維持のみです。

既存の SMA ユーザーの場合は、何も変更しなくても Hybrid Runbook Worker で使用する Azure Automation に Runbook を移動できます。ただし、「[Hybrid Runbook Worker の Runbook の作成](#creating-runbooks-for-hybrid-runbook-worker)」の説明に従って、リソースを独自に認証することが前提となります。SMA の Runbook は、Runbook の認証を提供できるワーカー サーバーのサービス アカウントのコンテキストで実行されます。

Hybrid Runbook Worker 機能を持つ Azure Automation と Service Management Automation のどちらがより要件に合っているかを判断するために、次の条件を使用できます。

- SMA には Microsoft Azure パックのローカル インストールが必要です。ローカル Runbook ワーカーにインストールされているエージェントのみを必要とする Azure Automation よりローカル リソースとメンテナンス コストがかかります。エージェントは Operations Management Suite によって管理され、メンテナンス コストがより削減されます。
- Azure Automation はその Runbook をクラウド内に格納し、オンプレミスの Hybrid Runbooks Worker に配信します。セキュリティ ポリシーでこの動作が許可されていない場合は、SMA を使用する必要があります。
- Microsoft Azure パックは無料でダウンロードできますが、Azure Automation ではサブスクリプション料がかかる可能性があります。
- Hybrid Runbook Worker 機能を持つ Azure Automation を使用することで、Azure Automation と SMA の両方を別々に管理するのではなく、1 つの場所でクラウド リソースとローカル リソースの Runbook を管理できるようになります。
- Azure Automation には、SMA で使用できないグラフィカル作成などの拡張機能があります。


## 次のステップ

- Runbook を開始するために使用できるさまざまな方法の詳細については、「[Azure Automation での Runbook の開始](automation-starting-a-runbook.md)」を参照してください
- テキスト エディターを使用し、Azure Automation で PowerShell Runbook と PowerShell ワークフロー Runbook を操作するためのさまざまな手順については、[Azure Automation での Runbook の編集](automation-edit-textual-runbook.md)に関するページをご覧ください

 

<!---HONumber=AcomDC_0817_2016-->
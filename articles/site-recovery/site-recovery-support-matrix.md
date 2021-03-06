<properties
	pageTitle="Azure Site Recovery のサポート マトリックス | Microsoft Azure"
	description="Azure Site Recovery でサポートされているオペレーティング システムとコンポーネントの概要について説明します"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="09/07/2016"
	ms.author="raynew"/>

# Azure Site Recovery のサポート マトリックス

この記事では、Azure Site Recovery のデプロイでサポートされているオペレーティング システムとコンポーネントの概要について説明します。サポートされているコンポーネントと前提条件の一覧は、デプロイに関する各記事のデプロイ シナリオごとに使用できますが、このトピックではその要約を示しています。

## 仮想化サーバーのサポートされているオペレーティング システム


**レプリケート** | **レプリケート対象** | **レプリケート先** | **ホスト OS**
---|---|---|--- 
**Hyper-V ホスト** | すべてのワークロード | Azure | Windows Server 2012 R2 (最新の更新プログラムをインストール済み)
**VMM クラウドの Hyper-V ホスト** | すべてのワークロード | Azure | Windows Server 2012 R2 (最新の更新プログラムをインストール済み)
**VMM クラウドの Hyper-V ホスト** | すべてのワークロード | セカンダリの VMM サイト | Windows Server 2012 以上 (最新の更新プログラムをインストール済み)
**VMware ホスト/vCenter** | すべてのワークロード | Azure | vCenter 5.5 または 6.0 (5.5 の機能のみをサポート) <br/><br/>vSphere 6.0、5.5、または 5.1 (最新の更新プログラムをインストール済み)
**VMware ホスト/vCenter** | すべてのワークロード | セカンダリの VMware サイト | vCenter 5.5 または 6.0 (5.5 の機能のみをサポート) <br/><br/>vSphere 6.0、5.5、または 5.1 (最新の更新プログラムをインストール済み)

## レプリケートされたコンピューターのサポートされる要件

**レプリケート** | **レプリケート対象** | **レプリケート先** | **ホスト OS**
---|---|---|--- 
**Hyper-V VM** | すべてのワークロード | Azure | [Azure がサポートする](https://technet.microsoft.com/library/cc794868.aspx)すべてのゲスト OS<br/><br/> VM は [Azure 要件](site-recovery-best-practices.md#azure-virtual-machine-requirements)を満たしている必要があります
**VMM クラウドの Hyper-V VM** | すべてのワークロード | Azure | [Azure がサポートする](https://technet.microsoft.com/library/cc794868.aspx)すべてのゲスト OS<br/><br/> VM は [Azure 要件](site-recovery-best-practices.md#azure-virtual-machine-requirements)を満たしている必要があります
**VMM クラウドの Hyper-V VM** | すべてのワークロード | セカンダリの VMM サイト | [Hyper-V がサポートする](https://technet.microsoft.com/library/mt126277.aspx)すべてのゲスト OS
**VMware VM** | Windows VM で実行されているすべてのワークロード | Azure | 64 ビット Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 以降<br/><br/> VM は [Azure 要件](site-recovery-best-practices.md#azure-virtual-machine-requirements)を満たしている必要があります
**VMware VM** | Linux VM で実行されているすべてのワークロード | Azure | Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> Centos 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 必要なストレージ: ファイル システム (EXT3、ETX4、ReiserFS、XFS)、マルチパス ソフトウェア デバイス マッパー (multipath)、ボリューム マネージャー (LVM2)。HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされていません。ReiserFS ファイルシステムは、SUSE Linux Enterprise Server 11 SP3 でのみサポートされています。<br/><br/> VM は [Azure 要件](site-recovery-best-practices.md#azure-virtual-machine-requirements)を満たしている必要があります
**VMware VM** | Windows VM で実行されているすべてのワークロード | セカンダリの VMware サイト | 64 ビット Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 以降
**VMware VM** | Linux VM で実行されているすべてのワークロード | セカンダリの VMware サイト | Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> Centos 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 必要なストレージ: ファイル システム (EXT3、ETX4、ReiserFS、XFS)、マルチパス ソフトウェア デバイス マッパー (multipath)、ボリューム マネージャー (LVM2)。HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされていません。ReiserFS ファイルシステムは、SUSE Linux Enterprise Server 11 SP3 でのみサポートされています。
**物理サーバー** | Windows で実行されているすべてのワークロード | Azure | 64 ビット Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 SP1 以降
**物理サーバー** | Linux で実行されているすべてのワークロード | Azure | Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> Centos 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 必要なストレージ: ファイル システム (EXT3、ETX4、ReiserFS、XFS)、マルチパス ソフトウェア デバイス マッパー (multipath)、ボリューム マネージャー (LVM2)。HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされていません。ReiserFS ファイルシステムは、SUSE Linux Enterprise Server 11 SP3 でのみサポートされています。
**物理サーバー** | Windows で実行されているすべてのワークロード | セカンダリ サイト | 64 ビット Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 SP1 以降
**物理サーバー** | Linux で実行されているすべてのワークロード | セカンダリ サイト | Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> Centos 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 必要なストレージ: ファイル システム (EXT3、ETX4、ReiserFS、XFS)、マルチパス ソフトウェア デバイス マッパー (multipath)、ボリューム マネージャー (LVM2)。HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされていません。ReiserFS ファイルシステムは、SUSE Linux Enterprise Server 11 SP3 でのみサポートされています。


## プロバイダーのバージョン

**名前** | **説明** | **最新バージョン** | **サポート** | **詳細**
---|---|---|---| ---
**Azure Site Recovery プロバイダー** | オンプレミスのサーバーと Azure/セカンダリ サイトの間の通信を調整します <br/><br/> オンプレミスの VMM サーバー、または Hyper-V サーバー (VMM サーバーがない場合) にインストールされます | 5\.1.1700 (ポータルから使用可能) | [最新の機能と修正](https://support.microsoft.com/kb/3155002)
**Azure Site Recovery の統合セットアップ (VMware から Azure)** | オンプレミスの VMware サーバーと Azure の間の通信を調整します <br/><br/> オンプレミスの VMware サーバーにインストールされます | 9\.3.4246.1 (ポータルから使用可能) | [最新の機能と修正](https://support.microsoft.com/kb/3155002)
**モビリティ サービス** | オンプレミスの VMware サーバー/物理サーバーと Azure/セカンダリ サイトの間のレプリケーションを調整します | 該当なし (ポータルから使用可能) | 各 VMware VM またはレプリケートする物理サーバーにインストールされます。**Microsoft Azure Recovery Services (MARS) エージェント** | Hyper-V VM と Azure の間のレプリケーションを調整します<br/><br/> オンプレミスの Hyper-V サーバーにインストールされます (VMM サーバーの有無にかかわらず) | 2\.0.8689.0 (ポータルから使用可能) | このエージェントは Azure Site Recovery と Azure Backup が使用します。[詳細情報](https://support.microsoft.com/ja-JP/kb/2997692)

## 次のステップ

[デプロイメントの準備をする](site-recovery-best-practices.md)

<!---HONumber=AcomDC_0907_2016-->
<properties 
    pageTitle="チュートリアル: Azure Active Directory と Mindflash の統合 | Microsoft Azure" 
    description="Azure Active Directory で Mindflash を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/08/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と Mindflash の統合
  
このチュートリアルでは、Azure と Mindflash の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Mindflash でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、Mindflash に割り当てた Azure AD ユーザーは、Mindflash 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Mindflash のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-mindflash-tutorial/IC787132.png "シナリオ")
##Mindflash のアプリケーション統合の有効化
  
このセクションでは、Mindflash のアプリケーション統合を有効にする方法について説明します。

###Mindflash のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-mindflash-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-mindflash-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-mindflash-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-mindflash-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Mindflash**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-mindflash-tutorial/IC787133.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Mindflash]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Mindflash](./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Mindflash に対する認証を行うことができるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **[Mindflash]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-mindflash-tutorial/IC787135.png "Configure Single Sign-On")

2.  **[ユーザーの Mindflash へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-mindflash-tutorial/IC787136.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページで、**[サインオン URL]** ボックスに、"*http://company.mindflash.com*" パターンの URL を入力し、**[次へ]** をクリックします。

    ![Configure App URL](./media/active-directory-saas-mindflash-tutorial/IC787137.png "アプリケーション URL の構成")

4.  **[Mindflash でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、コンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-mindflash-tutorial/IC787138.png "Configure Single Sign-On")

5.  Mindflash サポート チームに、メタデータ ファイルを送信します。

    >[AZURE.NOTE] シングル サインオンの構成は、Mindflash サポート チームが実行する必要があります。構成が完了すると、サポート チームから通知が届きます。

6.  Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-mindflash-tutorial/IC787139.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが Mindflash にログインできるようにするには、そのユーザーを Mindflash にプロビジョニングする必要があります。Mindflash の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1.  **Mindflash** の企業サイトに管理者としてログインします。

2.  **[ユーザーの管理]** に移動します。

    ![Manage Users](./media/active-directory-saas-mindflash-tutorial/IC787140.png "Manage Users")

3.  **[ユーザーの追加]** をクリックし、**[新規]** をクリックします。

4.  **[新しいユーザーの追加]** セクションで、次の手順に従います。

    ![新しいユーザーの追加](./media/active-directory-saas-mindflash-tutorial/IC787141.png "新しいユーザーの追加")

    1.  対応するテキストボックスに、プロビジョニングする有効な AAD アカウントの**名**、**姓**、**メール**を入力します。
    2.  **[追加]** をクリックします。

>[AZURE.NOTE]Mindflash から提供されている他の Mindflash ユーザー アカウント作成ツールや API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Mindflash に割り当てるには、次の手順に従います。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **Mindflash** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-mindflash-tutorial/IC787142.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-mindflash-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0713_2016-->
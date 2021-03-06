<properties
	pageTitle="Azure Active Directory プレビューで他のディレクトリまたはパートナー会社からユーザーを追加する | Microsoft Azure"
	description="Azure Active Directory で、外部ユーザーとゲスト ユーザーを含む、ユーザーの追加方法またはユーザー情報の変更方法を説明します。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand"/>

# Azure Active Directory プレビューで他のディレクトリまたはパートナー会社からユーザーを追加する

> [AZURE.SELECTOR]
- [Azure ポータル](active-directory-users-create-external-azure-portal.md)
- [Azure クラシック ポータル](active-directory-create-users-external.md)

この記事では、Azure Active Directory (Azure AD) プレビューの他のディレクトリまたはパートナー会社からユーザーを追加する方法について説明します。プレビューの機能については、[こちらの記事](active-directory-preview-explainer.md)をご覧ください。 組織内の新しいユーザーの追加、および Microsoft アカウントを持つユーザーの追加については、[Azure Active Directory への新しいユーザーの追加](active-directory-users-create-azure-portal.md)に関する記事をご覧ください。既定では、追加されたユーザーに管理者のアクセス許可はありませんが、ロールはいつでも割り当てることができます。

## ユーザーの追加

1.  ディレクトリの全体管理者であるアカウントで [Azure ポータル](https://portal.azure.com)にサインインします。

2.  **[その他のサービス]** を選択し、テキスト ボックスに「**ユーザーとグループ**」と入力して、**Enter** キーを押します。

    ![ユーザー管理を開く](./media/active-directory-users-create-external-azure-portal/create-users-user-management.png)

3.  **[ユーザーとグループ]** ブレードで、**[ユーザー]** を選択し、**[追加]** をクリックします。

    ![[追加] をクリックする](./media/active-directory-users-create-external-azure-portal/create-users-add-command.png)

4. **[ユーザー]** ブレードで、**[名前]** に表示名を入力し、**[ユーザー名]** にユーザーのサインイン名を入力します。

5. このプロセスの完了後、ユーザーに提供できるように、生成されたユーザー パスワードをコピーするか、メモしておきます。

6. 必要に応じて、**[プロファイル]** を選択して、ユーザーの姓、名、役職、部門名を追加します。

	![Opening the user profile](./media/active-directory-users-create-external-azure-portal/create-users-user-profile.png)

	- **[グループ]** を選択して、1 つ以上のグループにユーザーを追加します。

		![ユーザーをグループに追加する]](./media/active-directory-users-create-external-azure-portal/create-users-user-groups.png)

	- **[組織のロール]** を選択して、**[ロール]** 一覧のロールにユーザーを割り当てます。ユーザーおよび管理者のロールの詳細については、「[Azure AD での管理者ロールの割り当て](active-directory-assign-admin-roles.md)」を参照してください。

		![ユーザーをロールに割り当てる](./media/active-directory-users-create-external-azure-portal/create-users-assign-role.png)

7. **[作成]** を選択します。

8. ユーザーがサインインできるように、新しいユーザーに生成されたパスワードを安全に配布します。

> [AZURE.IMPORTANT] 組織が複数のドメインを使用している場合は、ユーザー アカウントを追加するときに起きる、次の問題について理解しておく必要があります。
>
> - 複数のドメインで同じユーザー プリンシパル名 (UPN) を持つユーザー アカウントを追加するには、たとえば**最初に** geoffgrisso@contoso.onmicrosoft.com を追加し、**それに続けて** geoffgrisso@contoso.com を追加します。
> - geoffgrisso@contoso.onmicrosoft.com を追加する前に geoffgrisso@contoso.com を追加**しないでください**。この順序は重要であり、なかなか元に戻すことができない場合があります。

オンプレミスの Active Directory サービスに ID が同期されているユーザーの情報を変更する場合、Azure クラシック ポータルでユーザー情報を変更することはできません。このユーザー情報を変更するには、オンプレミスの Active Directory 管理ツールを使用してください。


## 参照トピック

- [ユーザーの追加](active-directory-users-create-azure-portal.md)
- [新しい Azure ポータルでのユーザーのパスワードのリセット](active-directory-users-reset-password-azure-portal.md)
- [Azure AD でのロールへのユーザーの割り当て](active-directory-users-assign-role-azure-portal.md)
- [ユーザーの勤務先情報の変更](active-directory-users-work-info-azure-portal.md)
- [ユーザー プロファイルの管理](active-directory-users-profile-azure-portal.md)
- [Azure AD でのユーザーの削除](active-directory-users-delete-user-azure-portal.md)

<!---HONumber=AcomDC_0914_2016--->
---
title: "Azure Active Directory B2C: 拡張可能なポリシー フレームワーク | Microsoft Docs"
description: "Azure Active Directory B2C の拡張可能なポリシー フレームワークと、さまざまなポリシー タイプの作成方法に関するトピック。"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 0d453e72-7f70-4aa2-953d-938d2814d5a9
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c0a3bec8542cc5974516a8fb3deb14d23bf1fe2e


---
# <a name="azure-active-directory-b2c-extensible-policy-framework"></a>Azure Active Directory B2C: 拡張可能なポリシー フレームワーク
## <a name="the-basics"></a>基本
Azure Active Directory (Azure AD) B2C の拡張可能なポリシー フレームワークは、このサービスの中核となる強みです。 ポリシーには、サインアップ、サインイン、プロファイルの編集など、コンシューマーの ID エクスペリエンスが完全に記述されています。 たとえば、サインアップのポリシーを使用して次の設定を構成し、動作を制御できます。

* コンシューマーがアプリケーションにサインアップするために使用できるアカウント タイプ (Facebook などのソーシャル アカウント、または電子メール アドレスなどのローカル アカウント)。
* サインアップ中にコンシューマーから収集される属性 (名、郵便番号、靴のサイズなど)。
* 多要素認証の使用。
* すべてのサインアップ ページの外観。
* ポリシーの実行が完了したときにアプリケーションが受け取る情報 (要求内で要求として明示される)。

テナント内でさまざまな種類のポリシーを複数作成し、必要に応じてそれらをアプリケーションで使用できます。 ポリシーは、アプリケーション間で再利用することができます。 これにより、開発者はコードの変更を最小限に抑えるか、まったく変更を行わずに、コンシューマーの ID エクスペリエンスを定義および変更できます。

ポリシーは、簡単な開発者インターフェイスを使用して入手できます。 アプリケーションは標準 HTTP 認証要求 (要求でポリシー パラメーターを渡す) を使用してポリシーをトリガーし、カスタマイズされたトークンを応答として受け取ります。 たとえば、サインアップ ポリシーを呼び出す要求と、サインイン ポリシーを呼び出す要求の唯一の相違点は、"p" クエリ文字列パラメーターで使用されるポリシー名です。

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

ポリシー フレームワークの詳細については、こちらの [ブログ記事](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx)を参照してください。

## <a name="create-a-sign-up-policy"></a>サインアップ ポリシーを作成する
アプリケーションにサインアップできるようにするには、サインアップ ポリシーを作成する必要があります。 このポリシーは、サインアップ中のコンシューマーのエクスペリエンスと、サインアップが成功したときにアプリケーションが受け取るトークンのコンテンツを記述します。

1. [この手順に従って、Azure ポータルで B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. **[サインアップ ポリシー]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. **[名前]** によって、アプリケーションで使用されるサインアップ ポリシー名が決定されます。 たとえば、「SiUp」を入力します。
5. **[Identity providers (ID プロバイダー)]** をクリックし、[Email signup (電子メール サインアップ)] を選択します。 既に構成されている場合は、ソーシャル ID プロバイダーを選択することもできます。 **[OK]**をクリックします。
6. **[サインアップ属性]**をクリックします。 ここで、サインアップ中にコンシューマーから収集する属性を選択します。 たとえば、[市町村]、[表示名]、[郵便番号] などを選択します。 **[OK]**をクリックします。
7. **[アプリケーション クレーム]**をクリックします。 ここで、サインアップ エクスペリエンスの成功後にアプリケーションに戻されるトークンで返される要求を選択します。 たとえば、"表示名"、"ID プロバイダー"、"郵便番号"、"ユーザーが新規"、"ユーザーのオブジェクト ID" などを選択します。
8. **[作成]**をクリックします。 作成したポリシーが **[Sign-up policies (サインアップ ポリシー)]** ブレードに "**B2C_1_SiUp**" と表示されます (**B2C\_1\_** フラグメントが自動的に追加されます)。
9. **[B2C_1_SiUp]** をクリックしてポリシーを開きます。
10. **[アプリケーション]** ボックスの一覧で "Contoso B2C app" を選択し、**[応答 URL/リダイレクト URI]** ボックスの一覧で `https://localhost:44321/` を選択します。
11. **[今すぐ実行]**をクリックします。 新しいブラウザー タブが開き、アプリケーションへのサインアップのコンシューマー エクスペリエンスを確認できます。
    
    > [!NOTE]
    > ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
    > 
    > 

## <a name="create-a-sign-in-policy"></a>サインイン ポリシーを作成する
アプリケーションにサインインできるようにするには、サインイン ポリシーを作成する必要があります。 このポリシーは、サインイン中のコンシューマーのエクスペリエンスと、サインインが成功したときにアプリケーションが受け取るトークンのコンテンツを記述します。

1. [この手順に従って、Azure Portal で B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. **[サインイン ポリシー]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. **[名前]** によって、アプリケーションで使用されるサインイン ポリシー名が決定されます。 たとえば、「SiIn」を入力します。
5. **[Identity providers (ID プロバイダー)]** をクリックし、[Local Account SignIn (ローカル アカウント サインイン)] を選択します。 既に構成されている場合は、ソーシャル ID プロバイダーを選択することもできます。 **[OK]**をクリックします。
6. **[アプリケーション クレーム]**をクリックします。 ここで、サインイン エクスペリエンスの成功後にアプリケーションに戻されるトークンで返される要求を選択します。 たとえば、"表示名"、"ID プロバイダー"、"郵便番号"、および "ユーザーのオブジェクト ID" などを選択します。 **[OK]**をクリックします。
7. **[作成]**をクリックします。 作成したポリシーが**サインイン ポリシー** ブレードに "**B2C_1_SiIn**" と表示されます (**B2C\_1\_** フラグメントが自動的に追加されます)。
8. **[B2C_1_SiIn]** をクリックしてポリシーを開きます。
9. **[アプリケーション]** ボックスの一覧で "Contoso B2C app" を選択し、**[応答 URL/リダイレクト URI]** ボックスの一覧で `https://localhost:44321/` を選択します。
10. **[今すぐ実行]**をクリックします。 新しいブラウザー タブが開き、アプリケーションへのサインインのコンシューマー エクスペリエンスを確認できます。
    
    > [!NOTE]
    > ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
    > 
    > 

## <a name="create-a-sign-up-or-sign-in-policy"></a>サインアップまたはサインイン ポリシーを作成する
このポリシーは、1 つの構成でコンシューマーのサインアップ エクスペリエンスとサインイン エクスペリエンスの両方を処理します。 コンシューマーは、状況に応じて正しいパス (サインアップまたはサインイン) に誘導されます。 このポリシーはまた、サインアップまたはサインインが成功したときにアプリケーションが受け取るトークンのコンテンツを記述します。  サインアップまたはサインイン ポリシーのサンプル コードは、 [こちらで入手できます](active-directory-b2c-devquickstarts-web-dotnet-susi.md)。

1. [この手順に従って、Azure Portal で B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. **[Sign-up or sign-in policies (サインアップまたはサインイン ポリシー)]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. **[名前]** によって、アプリケーションで使用されるサインアップ ポリシー名が決定されます。 たとえば、「SiUpIn」と入力します。
5. **[Identity providers (ID プロバイダー)]** をクリックし、[Email signup (電子メール サインアップ)] を選択します。 既に構成されている場合は、ソーシャル ID プロバイダーを選択することもできます。 **[OK]**をクリックします。
6. **[サインアップ属性]**をクリックします。 ここで、サインアップ中にコンシューマーから収集する属性を選択します。 たとえば、[市町村]、[表示名]、[郵便番号] などを選択します。 **[OK]**をクリックします。
7. **[アプリケーション クレーム]**をクリックします。 ここで、サインアップまたはサインイン エクスペリエンスの成功後にアプリケーションに戻されるトークンで返される要求を選択します。 たとえば、"表示名"、"ID プロバイダー"、"郵便番号"、"ユーザーが新規"、"ユーザーのオブジェクト ID" などを選択します。
8. **[作成]**をクリックします。 作成したポリシーが **[Sign-up or sign-in policies (サインアップまたはサインイン ポリシー)]** ブレードに "**B2C_1_SiUpIn**" として表示されます (**B2C\_1\_** フラグメントが自動的に追加されます)。
9. **[B2C_1_SiUpIn]** をクリックしてポリシーを開きます。
10. **[アプリケーション]** ボックスの一覧で "Contoso B2C app" を選択し、**[応答 URL/リダイレクト URI]** ボックスの一覧で `https://localhost:44321/` を選択します。
11. **[今すぐ実行]**をクリックします。 新しいブラウザー タブが開き、構成したサインアップまたはサインインのコンシューマー エクスペリエンスを確認できます。
    
    > [!NOTE]
    > ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
    > 
    > 

## <a name="create-a-profile-editing-policy"></a>プロファイル編集ポリシーを作成する
アプリケーションでポリシーを編集できるようにするには、プロファイル編集ポリシーを作成する必要があります。 このポリシーは、プロファイル編集中のコンシューマーのエクスペリエンスと、正常に完了したときにアプリケーションが受け取るトークンのコンテンツを記述します。

1. [この手順に従って、Azure Portal で B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. **[プロファイル編集ポリシー]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. **[名前]** によって、アプリケーションで使用されるプロファイル編集ポリシー名が決定されます。 たとえば、「SiPe」を入力します。
5. **[ID プロバイダー]** をクリックし、[電子メール アドレス] を選択します。 既に構成されている場合は、ソーシャル ID プロバイダーを選択することもできます。 **[OK]**をクリックします。
6. **[プロファイルの属性]**をクリックします。 ここで、コンシューマーが表示および編集できる属性を選択します。 たとえば、[市町村]、[表示名]、[郵便番号] などを選択します。 **[OK]**をクリックします。
7. **[アプリケーション クレーム]**をクリックします。 ここで、プロファイル編集エクスペリエンスの成功後にアプリケーションに戻されるトークンで返される要求を選択します。 たとえば、"表示名" および "郵便番号" などを選択します。
8. **[作成]**をクリックします。 作成したポリシーが **[Profile editing policies (プロファイルの編集ポリシー)]** ブレードに "**B2C_1_SiPe**" として表示されます (**B2C\_1\_** フラグメントが自動的に追加されます)。
9. **[B2C_1_SiPe]** をクリックしてポリシーを開きます。
10. **[アプリケーション]** ボックスの一覧で "Contoso B2C app" を選択し、**[応答 URL/リダイレクト URI]** ボックスの一覧で `https://localhost:44321/` を選択します。
11. **[今すぐ実行]**をクリックします。 新しいブラウザー タブが開き、アプリケーションでプロファイル編集のコンシューマー エクスペリエンスを確認できます。
    
    > [!NOTE]
    > ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
    > 
    > 

## <a name="create-a-password-reset-policy"></a>パスワードのリセット ポリシーを作成する
アプリケーションで詳細なパスワード リセットを有効にするには、パスワードのリセット ポリシーを作成する必要があります。 [ここ](active-directory-b2c-reference-sspr.md) に示されているテナント全体のパスワード リセット オプションは、サインイン ポリシーにも適用可能であることに注意してください。 このポリシーは、パスワード リセット中のコンシューマーのエクスペリエンスと、正常に完了したときにアプリケーションが受け取るトークンのコンテンツを記述します。

1. [この手順に従って、Azure Portal で B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. **[Password reset policies (パスワードのリセット ポリシー)]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. **[名前]** によって、アプリケーションで使用されるパスワードのリセット ポリシー名が決まります。 たとえば、「SSPR」などと入力します。
5. **[Identity providers (ID プロバイダー)]** をクリックし、[Reset password using email address (電子メール アドレスを使用してパスワードをリセットする)] を選択します。 **[OK]**をクリックします。
6. **[アプリケーション クレーム]**をクリックします。 ここで、パスワード リセット エクスペリエンスの成功後にアプリケーションに戻されるトークンで返される要求を選択します。 たとえば、"ユーザーのオブジェクト ID" を選択します。
7. **[作成]**をクリックします。 作成したポリシーが **[Password reset policies (パスワードのリセット ポリシー)]** ブレードに "**B2C_1_SSPR**" として表示されます (**B2C\_1\_** フラグメントが自動的に追加されます)。
8. **[B2C_1_SSPR]** をクリックしてポリシーを開きます。
9. **[アプリケーション]** ボックスの一覧で "Contoso B2C app" を選択し、**[応答 URL/リダイレクト URI]** ボックスの一覧で `https://localhost:44321/` を選択します。
10. **[今すぐ実行]**をクリックします。 新しいブラウザー タブが開き、アプリケーションでパスワード リセットのコンシューマー エクスペリエンスを確認できます。
    
    > [!NOTE]
    > ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
    > 
    > 

## <a name="additional-resources"></a>その他のリソース
* [トークン、セッション、およびシングル サインオンの構成](active-directory-b2c-token-session-sso.md)。




<!--HONumber=Nov16_HO3-->



---
title: アプリを登録し、コードから SQL Database に接続するためのクライアント ID とキーを取得する | Microsoft Docs
description: コードから SQL Database にアクセスするためのクライアント ID とキーを取得します。
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''
tags: ''

ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/06/2016
ms.author: sstein

---
# コードから SQL Database に接続するためのクライアント ID とキーを取得する
コードから SQL Database を作成および管理するには、Azure リソースを作成したサブスクリプションと関連付けられている Azure Active Directory (AAD) ドメインにアプリを登録する必要があります。アプリケーションを登録すると、コードでアプリを認証するために必要なクライアント ID とキーが Azure で生成されます。詳細については、「[Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)」を参照してください。

## ネイティブ クライアント アプリケーションを登録してクライアント ID を取得する
新しいアプリケーションを作成し、登録するには、次の手順を実行します。

1. [クラシック ポータル](https://manage.windowsazure.com/)にログインします (現在、アプリケーションの登録は、クラシック ポータルで行う必要があります)。
2. メニューの **[Active Directory]** を選択します。
   
    ![AAD][1]
3. アプリケーションを認証するディレクトリを選択し、その**名前**をクリックします。
   
    ![ディレクトリ][4]
4. [ディレクトリ] ページで、**[アプリケーション]** をクリックします。
   
    ![アプリケーション][5]
5. **[追加]** をクリックして、新しいアプリケーションを作成します。
   
    ![アプリケーションの追加][6]
6. アプリの**名前**を指定し、**[ネイティブ クライアント アプリケーション]** を選択します。
   
    ![Add application][7]
7. **リダイレクト URI** を指定します。実際のエンドポイントである必要はありませんが、有効な URI である必要があります。
   
    ![Add application][8]
8. アプリの作成を完了し、**[構成]** をクリックして、**[クライアント ID]** (コードで必要になる値です) をコピーします。
   
    ![クライアント ID の取得][9]
9. ページを下にスクロールし、**[アプリケーションの追加]** をクリックします。
10. **[Microsoft Apps]** を選択します。
11. **[Microsoft Azure サービス管理 API]** を選択し、ウィザードを完了します。
12. **[他のアプリケーションに対するアクセス許可]** セクションで、**[Microsoft Azure サービス管理 API]** の **[デリゲートされたアクセス許可]** をクリックします。
13. **[Azure サービス管理 API へのアクセス]** を選択します。
    
     ![アクセス許可][2]
14. ページの下部にある **[保存]** をクリックします。

## Web アプリ (または Web API) を登録し、クライアント ID とキーを取得する
新しいアプリケーションを作成し、適切な Active Directory に登録するには、以下を実行します。

1. [クラシック ポータル](https://manage.windowsazure.com/)にログインします。
2. メニューの **[Active Directory]** を選択します。
   
    ![AAD][1]
3. アプリケーションを認証するディレクトリを選択し、その**名前**をクリックします。
   
    ![ディレクトリ][4]
4. [ディレクトリ] ページで、**[アプリケーション]** をクリックします。
   
    ![アプリケーション][5]
5. **[追加]** をクリックして、新しいアプリケーションを作成します。
   
    ![アプリケーションの追加][6]
6. アプリの **[名前]** を入力し、**[Web アプリケーションや Web API]** を選択します。
   
    ![Add application][10]
7. **[サインオン URL]** と **[アプリケーション ID/URI]** を入力します。実際のエンドポイントである必要はありませんが、有効な URI である必要があります。
   
    ![アプリケーションの追加][11]
8. アプリの作成を完了したら、**[構成]** をクリックします。
   
    ![configure][12]
9. **[キー]** セクションまでスクロールし、**[時間の選択]** リストで **[1 年]** を選択します。保存後はキー値が表示されるので、後で戻ってキーをコピーします。
   
    ![キーの有効期間の設定][13]
10. ページを下にスクロールし、**[アプリケーションの追加]** をクリックします。
11. **[Microsoft Apps]** を選択します。
12. **[Microsoft Azure サービス管理 API]** を選択し、ウィザードを完了します。
13. **[他のアプリケーションに対するアクセス許可]** セクションで、**[Microsoft Azure サービス管理 API]** の **[デリゲートされたアクセス許可]** をクリックします。
14. **[Azure サービス管理 API へのアクセス]** を選択します。
    
     ![アクセス許可][2]
15. ページの下部にある **[保存]** をクリックします。
16. 保存が完了したら、クライアント ID とキーを探して保存します。
    
     ![Web アプリ シークレット][14]

## ドメイン名を取得する
認証コードにはドメイン名が必要な場合があります。適切なドメイン名を指定する簡単な方法を以下に示します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 右上隅の自分の名前にマウスを合わせ、ポップアップ ウィンドウに表示されるドメインをメモしてください。
   
    ![ドメイン名の指定][3]

## コンソール アプリケーションの例
Visual Studio の[パッケージ マネージャー コンソール](http://docs.nuget.org/Consume/Package-Manager-Console) (**[ツール]**、**[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリック) で次のパッケージをインストールし、必要な管理ライブラリを入手します。

    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


**SqlDbAuthSample** というコンソール アプリを作成し、**Program.cs** の内容を次の内容に置き換えます。

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace SqlDbAuthSample
    {
    class Program
    {
        static void Main(string[] args)
        {
            token = GetAccessTokenforNativeClient();
            // token = GetAccessTokenUsingUserCredentials(new UserCredential("<email address>"));
            // token = GetAccessTokenForWebApp();

            Console.WriteLine("Signed in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Press Enter to continue...");
            Console.ReadLine();
        }


        // authentication variables (native)
        static string nclientId = "<your client id>";
        static string nredirectUri = "<your redirect URI>";
        static string ndomainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        private static AuthenticationResult GetAccessTokenforNativeClient()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + ndomainName /* Tenant ID or AAD domain */);

            token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    nclientId,
            new Uri(nredirectUri),
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }


        // authentication variables (web)
        static string wclientId = "<your client id>";
        static string wkey = "<your key>";
        static string wdomainName = "<i.e. microsoft.onmicrosoft.com>";

        private static AuthenticationResult GetAccessTokenForWebApp()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + wdomainName /* Tenant ID or AAD domain */);

            ClientCredential cc = new ClientCredential(wclientId, wkey);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                cc);

            return token;
        }


        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + ndomainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                nclientId /* application client ID from AAD*/,
                new Uri(nredirectUri) /* redirect URI */,
                PromptBehavior.Auto,
                new UserIdentifier(userCredential.UserName, UserIdentifierType.RequiredDisplayableId));

            return token;
        }
    }
    }


Azure AD 認証に関連した具体的なコード例については、MSDN の [SQL Server セキュリティに関するブログ](http://blogs.msdn.com/b/sqlsecurity/)を参照してください。

## 関連項目
* [C# を使用した SQL Database の作成](sql-database-get-started-csharp.md)
* [Azure Active Directory の認証を使用して SQL Database に接続する](sql-database-aad-authentication.md)

<!--Image references-->
[1]: ./media/sql-database-client-id-keys/aad.png
[2]: ./media/sql-database-client-id-keys/permissions.png
[3]: ./media/sql-database-client-id-keys/getdomain.png
[4]: ./media/sql-database-client-id-keys/aad2.png
[5]: ./media/sql-database-client-id-keys/aad-applications.png
[6]: ./media/sql-database-client-id-keys/add.png
[7]: ./media/sql-database-client-id-keys/add-application.png
[8]: ./media/sql-database-client-id-keys/add-application2.png
[9]: ./media/sql-database-client-id-keys/clientid.png
[10]: ./media/sql-database-client-id-keys/add-application-web.png
[11]: ./media/sql-database-client-id-keys/add-application-app-properties.png
[12]: ./media/sql-database-client-id-keys/configure.png
[13]: ./media/sql-database-client-id-keys/key-duration.png
[14]: ./media/sql-database-client-id-keys/web-secrets.png

<!---HONumber=AcomDC_0608_2016-->
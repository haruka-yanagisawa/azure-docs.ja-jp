---
title: Azure セキュリティ センターでのセキュリティに関する推奨事項の管理 | Microsoft Docs
description: このドキュメントでは、Azure セキュリティ センターでの推奨事項に従ってご使用の Azure のリソースを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2016
ms.author: terrylan

---
# Azure セキュリティ センターでのセキュリティに関する推奨事項の管理
このドキュメントでは、Azure セキュリティ センターでの推奨事項を使用して、ご使用の Azure のリソースを保護する方法について説明します。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。ステップ バイ ステップ ガイドではありません。
> 
> 

## セキュリティに関する推奨事項とは
セキュリティ センターは、Azure リソースのセキュリティの状態を定期的に分析します。セキュリティ センターでは、潜在的なセキュリティの脆弱性が特定されると、推奨事項が作成されます。推奨事項では、必要なコントロールを構成する手順を説明します。

## セキュリティに関する推奨事項の実装
### 推奨事項の設定
「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」では、次のことを学習します。

* セキュリティ ポリシーを構成する。
* データ収集を有効にする。
* セキュリティ ポリシーの一部として表示する推奨事項を選択する。

現在のポリシーの推奨事項は、システムの更新プログラム、基準規則、マルウェア対策プログラム、サブネットとネットワーク インターフェイス上の[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)、SQL データベースの監査、SQL データベースの透過的なデータ暗号化、Web アプリケーション ファイアウォールを軸として展開しています。[セキュリティ ポリシーの設定](security-center-policies.md)では、推奨事項の各オプションについて説明します。

### 推奨事項の監視
セキュリティ ポリシーを設定すると、セキュリティ センターではリソースのセキュリティの状態が分析され、潜在的な脆弱性が特定されます。**[セキュリティ センター]** ブレードの **[推奨事項]** タイルでは、Security Center で特定された推奨事項の総数がわかります。

![Recommendations tile][1]

各推奨事項の詳細を表示するには、以下の手順に従います。

1. **[セキュリティ センター]** ブレードの **[推奨事項]** タイルをクリックします。**[推奨事項]** ブレードが開きます。

推奨事項は表形式で表示されます。表の行はそれぞれ特定の推奨事項を表します。この表には次の列があります。

* **[説明]**: 推奨事項とそれに対応するために必要な処理について説明します。
* **[リソース]**: この推奨事項が適用されるリソースの一覧を表示します。
* **[状態]**: 推奨事項の現在の状態を示します。
  * **[オープン]**: 推奨事項にまだ対応してない。
  * **[処理中]**: 現在、リソースへの推奨事項の適用を進めており、ユーザーのアクションは不要。
  * **[解決済み]**: 推奨事項への対応が既に完了済み (この場合、行は淡色表示になる)。
* **[重大度]**: 特定の推奨事項の重大度を示します。
  * **[高]**: 重要なリソース (アプリケーション、VM、ネットワーク セキュリティ グループなど) に脆弱性が存在しており、注意が必要。
  * **[中]**: 脆弱性が存在しており、脆弱性を排除するかプロセスを完了するために重大ではない手順または追加の手順が必要。
  * **[低]**: 対処する必要はあるが、早急な注意を必要としない脆弱性が存在する (既定では、重要度の低い推奨事項は表示されないが、重要度の低い推奨事項にフィルターを適用すると表示できる)。

次の表を参考にすると、利用可能な推奨事項と、それを適用した場合のそれぞれの結果が理解しやすくなります。

> [!NOTE]
> Azure リソースの[クラシック デプロイ モデルと Resource Manager デプロイ モデル](../azure-classic-rm.md)について理解しておく必要があります。
> 
> 

| 推奨 | Description |
| --- | --- |
| [サブスクリプションのデータ収集の有効化](security-center-enable-data-collection.md) |各サブスクリプションおよびサブスクリプションのすべての仮想マシン (VM) に対して、セキュリティ ポリシーでデータ収集を有効にすることをお勧めします。 |
| [OS の脆弱性の修復](security-center-remediate-os-vulnerabilities.md) |OS の構成を推奨される構成規則 (パスワードの保存を許可しないなど) に合わせることをお勧めします。 |
| [システムの更新の適用](security-center-apply-system-updates.md) |システムの不足しているセキュリティ更新プログラムおよび重要な更新プログラムを VM にデプロイすることをお勧めします。 |
| [システムの更新後に再起動する](security-center-apply-system-updates.md#reboot-after-system-updates) |VM を再起動してシステムの更新プログラムの適用プロセスを完了するよう推奨します。 |
| [Web アプリケーション ファイアウォールの追加](security-center-add-web-application-firewall.md) |Web エンドポイントに Web アプリケーション ファイアウォール (WAF) をデプロイすることをお勧めします。セキュリティ センターで複数の Web アプリケーションを保護するには、対象のアプリケーションを既存の WAF デプロイに追加します。(リソース マネージャー デプロイ モデルを使用して作成した) WAF アプライアンスは、別の仮想ネットワークにデプロイする必要があります。(クラシック デプロイ モデルを使用して作成した) WAF アプライアンスは、ネットワーク セキュリティ グループの使用に限定されています。今後、このサポートは、全面的にカスタマイズされた WAF アプライアンスのデプロイ (クラシック) へと拡大される予定です。Security Center では、VM 上および App Service 環境 (ASE) 上の Web アプリケーションを対象とする攻撃から保護するための WAF をプロビジョニングするよう推奨します。ASE の詳細については、[App Service 環境のドキュメント](../app-service/app-service-app-service-environments-readme.md)をご覧ください。 |
| [アプリケーション保護を完了する](security-center-add-web-application-firewall.md#finalize-application-protection) |WAF の構成を完了するには、WAF アプライアンスにトラフィックを再ルーティングする必要があります。この推奨事項に従うと、必要なセットアップの変更が完了します。 |
| [次世代ファイアウォールの追加](security-center-add-next-generation-firewall.md) |セキュリティ保護を強化するために、Microsoft パートナーの次世代ファイアウォール (NGFW) を追加することをお勧めします。 |
| [NGFW 経由に限定したトラフィックのルーティング](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |VM への受信トラフィックを必ず NGFW 経由にするようにネットワーク セキュリティ グループ (NSG) の規則を構成することをお勧めします。 |
| [Endpoint Protection をインストールします](security-center-install-endpoint-protection.md) |マルウェア対策プログラムを VM (Windows VM のみ) にプロビジョニングすることをお勧めします。 |
| [Endpoint Protection の正常性アラートの解決](security-center-resolve-endpoint-protection-health-alerts.md) |Endpoint Protection のエラーを解決することをお勧めします。 |
| [サブネットまたは仮想マシンでのネットワーク セキュリティ グループの有効化](security-center-enable-network-security-groups.md) |サブネットまたは VM で NSG を有効にすることをお勧めします。 |
| [インターネットに接続するエンドポイント経由のアクセスの制限](security-center-restrict-access-through-internet-facing-endpoints.md) |NSG に着信トラフィックのルールを構成することをお勧めします。 |
| [サーバーの SQL 監査の有効化](security-center-enable-auditing-on-sql-servers.md) |Azure SQL サーバーの監査を有効にすることをお勧めします (Azure SQL サービスのみ。仮想マシンで実行されている SQL を除く)。 |
| [データベースの SQL 監査の有効化](security-center-enable-auditing-on-sql-databases.md) |Azure SQL データベースの監査を有効にすることをお勧めします (Azure SQL サービスのみ。仮想マシンで実行されている SQL を除く)。 |
| [SQL データベースでの透過的なデータ暗号化の有効化](security-center-enable-transparent-data-encryption.md) |SQL データベース (Azure SQL のサービスのみ) に対して暗号化を有効にすることをお勧めします。 |
| [VM エージェントの有効化](security-center-enable-vm-agent.md) |VM エージェントを必要とする VM を確認できます。パッチのスキャン、基準のスキャン、およびマルウェア対策プログラムをプロビジョニングするには、VM 上に VM エージェントをインストールする必要があります。既定では、Azure Marketplace からデプロイされた VM に VM エージェントがインストールされます。「[VM エージェントと拡張機能 – パート 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)」の記事には、VM エージェントのインストール方法が記載されています。 |
| [ディスク暗号化の適用](security-center-apply-disk-encryption.md) |Azure Disk Encryption を使用して VM ディスクを暗号化することをお勧めします (Windows VM および Linux VM)。VM 上の OS とデータ ボリュームの両方を暗号化することをお勧めします。 |
| [セキュリティの連絡先詳細の提供](security-center-provide-security-contact-details.md) |各サブスクリプションのセキュリティの連絡先情報を提供することをお勧めします。連絡先情報は、電子メール アドレスと電話番号です。セキュリティ チームがリソースの侵害に気付いた場合、この情報を使用してご連絡します。 |
| [OS バージョンの更新](security-center-update-os-version.md) |クラウド サービスのオペレーティング システム (OS) のバージョンを、ご利用の OS ファミリで利用できる最新のバージョンに更新するようお勧めします。Cloud Services の詳細については、[Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)に関するページをご覧ください。 |
| [脆弱性評価がインストールされていません](security-center-vulnerability-assessment-recommendations.md) |VM に脆弱性評価ソリューションをインストールすることをお勧めします。 |
| [脆弱性の修復](security-center-vulnerability-assessment-recommendations.md#review-recommendation) |VM にインストールされている脆弱性評価ソリューションによって検出された、システムとアプリケーションの脆弱性を確認できます。 |

推奨事項をフィルター処理し、無視することができます。

1. **[推奨事項]** ブレードで **[フィルター]** をクリックします。**[フィルター]** ブレードが開いたら、確認する重要度と状態の値を選択します。
   
    ![Filter recommendations][2]
2. 推奨事項が適用できないと判断した場合、その推奨事項を無視し、ビューから除外することができます。推奨事項を無視するには 2 つの方法があります。1 つは、項目を右クリックして **[無視]** を選択する方法です。もう 1 つは、項目の上にマウスを合わせ、右側に表示される 3 つの点をクリックして、**[無視]** を選択する方法です。**[フィルター]** をクリックして **[無視]** を選択すると、無視した推奨事項を表示できます。
   
    ![Dismiss recommendation][3]

### 推奨事項の適用
すべての推奨事項を確認したら、最初に適用する推奨事項を決めます。重要度の評価をメイン パラメーターとして使用して、最初にどの推奨事項を適用する必要があるか評価することをお勧めします。

前述の推奨事項の表で推奨事項を選択し、推奨事項を適用する方法の一例として手順を確認してください。

## 関連項目
このドキュメントでは、セキュリティ センターのセキュリティに関する推奨事項について説明しました。セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」 -- Azure リソースの正常性を監視する方法について説明しています。
* 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」 -- セキュリティの警告の管理と対応の方法について説明しています。
* 「[Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)」 -- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png

<!---HONumber=AcomDC_0928_2016-->
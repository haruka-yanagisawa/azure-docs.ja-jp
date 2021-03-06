---
title: "サンプルのインフラストラクチャによるチュートリアル | Microsoft Docs"
description: "Azure でのサンプルのインフラストラクチャのデプロイに関する主要な設計と実装のガイドラインについて説明します。"
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 281fc2c0-b533-45fa-81a3-728c0049c73d
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9e909f7b0c0abe9063322c40b6bdb11494952621


---
# <a name="example-azure-infrastructure-walkthrough"></a>サンプルの Azure インフラストラクチャによるチュートリアル
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

この記事では、サンプルのアプリケーション インフラストラクチャの構築について説明します。 ここでは、名前付け規則、可用性セット、仮想ネットワークおよびロード バランサーに関するガイドラインと意思決定のすべてをまとめたシンプルなオンライン ストア向けインフラストラクチャを設計し、実際に仮想マシン (VM) をデプロイする方法について説明します。

## <a name="example-workload"></a>ワークロードの例
Adventure Works Cycles では、以下の項目で構成されるオンライン ストア アプリケーションを Azure に構築する必要があります。

* Web 層でクライアント フロントエンドを実行する 2 台の nginx サーバー
* アプリケーション層でデータと注文を処理する 2 台の nginx サーバー
* シャード クラスターの一部であり、データベース層で製品データと注文を格納する 2 台の MongoDB サーバー
* 認証層の顧客アカウントとサプライヤー用の 2 つの Active Directory ドメイン コントローラー
* すべてのサーバーは次の 2 つのサブネットに配置されています。
  * フロント エンド サブネット: Web サーバー用 
  * バック エンド サブネット: アプリケーション サーバー、MongoDB クラスター、およびドメイン コントローラー用

![アプリケーション インフラストラクチャのさまざまなレベルの図](./media/virtual-machines-common-infrastructure-service-guidelines/example-tiers.png)

セキュリティで保護された着信 Web トラフィックについては、顧客がオンライン ストアを閲覧する際に Web サーバー間で負荷を分散する必要があります。 Web サーバーからの HTTP 要求の形式での注文処理トラフィックについては、アプリケーション サーバー間で負荷を分散する必要があります。 さらに、インフラストラクチャは高可用性対応で設計する必要があります。

結果として得られる設計には、次のものが組み込まれる必要があります。

* Azure サブスクリプションとアカウント
* 単一リソース グループ
* ストレージ アカウント
* 2 つのサブネットを含む仮想ネットワーク
* 似たロールを持つ VM のための可用性セット
* 仮想マシン

これらすべてで、次の名前付け規則を使用します。

* Adventure Works Cycles は、プレフィックスとして **[IT ワークロード]-[場所]-[Azure リソース]** を使用します。
  * たとえば、"**azos**" (Azure On-line Store) は IT ワークロード名であり、"**use**" (米国東部 2) は場所です。
* ストレージ アカウントは、adventureazosusesa**[説明]** を使用します。
  * "adventure" は一意性のためにプレフィックスに追加されており、ストレージ アカウント名ではハイフンを使用できません。
* 仮想ネットワークは、AZOS-USE-VN**[番号]** を使用します。
* 可用性セットは、azos-use-as-**[ロール]** を使用します。
* 仮想マシン名は、azos-use-vm-**[仮想マシン名]** を使用します。

## <a name="azure-subscriptions-and-accounts"></a>Azure サブスクリプションとアカウント
Adventure Works Cycles は、Adventure Works Enterprise Subscription という名前のエンタープライズ サブスクリプションを使用して、この IT ワークロードに対する課金を行います。

## <a name="storage-accounts"></a>ストレージ アカウント
Adventure Works Cycles は、次の 2 つのストレージ アカウントが必要であると判断しました。

* **adventureazosusesawebapp** : Web サーバー、アプリケーション サーバー、ドメイン コントローラーとそれらのデータ ディスクの Standard Storage 用。
* **adventureazosusesadbclust** : MongoDB シャード クラスター サーバーとそれらのデータ ディスクの Premium Storage 用。

## <a name="virtual-network-and-subnets"></a>仮想ネットワークとサブネット
仮想ネットワークを Adventure Work Cycles のオンプレミス ネットワークに常時接続している必要はないので、Adventure Work Cycles はクラウド専用の仮想ネットワークに決定しました。

Contoso は、Azure ポータルを使用して次の設定でクラウド専用仮想ネットワークを作成しました。

* 名前: AZOS-USE-VN01
* 場所: East US 2
* 仮想ネットワークのアドレス空間: 10.0.0.0/8
* 1 番目のサブネット:
  * 名前: FrontEnd
  * アドレス空間: 10.0.1.0/24
* 2 番目のサブネット:
  * 名前: BackEnd
  * アドレス空間: 10.0.2.0/24

## <a name="availability-sets"></a>可用性セット
オンライン ストアの 4 つの階層すべてで高可用性を維持するため、Adventure Works Cycles は次の 4 つの可用性セットを採用しました。

* **azos-use-as-web** : Web サーバー用
* **azos-use-as-app** : アプリケーション サーバー用
* **azos-use-as-db** : MongoDB シャード クラスター内のサーバー用
* **azos-use-as-dc** : ドメイン コントローラー用

## <a name="virtual-machines"></a>仮想マシン
Adventure Works Cycles は、各 Azure VM に対して次の名前を決定しました。

* **azos-use-vm-web01** : 1 番目の Web サーバー用
* **azos-use-vm-web02** : 2 番目の Web サーバー用
* **azos-use-vm-app01** : 1 番目のアプリケーション サーバー用
* **azos-use-vm-app02** : 2 番目のアプリケーション サーバー用
* **azos-use-vm-db01** : クラスター内の 1 番目の MongoDB サーバー用
* **azos-use-vm-db02** : クラスター内の 2 番目の MongoDB サーバー用
* **azos-use-vm-dc01** : 1 番目のドメイン コントローラー用
* **azos-use-vm-dc02** : 2 番目のドメイン コントローラー用

完成すると次のような構成になります。

![Azure にデプロイされた最終的なアプリケーション インフラストラクチャ](./media/virtual-machines-common-infrastructure-service-guidelines/example-config.png)

この構成には次のものが含まれます。

* クラウド専用仮想ネットワークと 2 つのサブネット (FrontEnd と BackEnd)
* 2 つのストレージ アカウント
* オンライン ストアの各階層に 1 つずつ、計 4 つの可用性セット
* 4 つの階層用の仮想マシン
* インターネットから Web サーバーへの HTTPS ベースの Web トラフィック用の外部負荷分散セット
* Web サーバーからアプリケーション サーバーへの暗号化されていない Web トラフィック用の内部負荷分散セット
* 単一リソース グループ

## <a name="next-steps"></a>次のステップ
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]




<!--HONumber=Nov16_HO3-->



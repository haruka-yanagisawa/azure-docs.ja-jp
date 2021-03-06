---
title: Service Fabric クラスターの容量計画 | Microsoft Docs
description: Service Fabric クラスターの容量計画に関する考慮事項。Nodetypes、持続性と信頼性レベル
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2016
ms.author: chackdan

---
# Service Fabric クラスターの容量計画に関する考慮事項
容量計画は、運用環境へのデプロイにおいて重要なステップとなります。ここでは、そのプロセスの一環として考慮すべき事柄をいくつか取り上げます。

* クラスターで最初に必要となるノード タイプの数
* ノード タイプごとの特性 (サイズ、プライマリ/非プライマリ、インターネット接続、VM 数など)
* クラスターの信頼性と耐久性の特徴

それぞれの項目について簡単に見ていきましょう。

## クラスターで最初に必要となるノード タイプの数
まず、これから作成するクラスターを何の目的に使用するのか、また、そのクラスターにどのようなアプリケーションをデプロイするのかを把握する必要があります。クラスターの目的が明確でないとすれば、容量計画プロセスに着手するのはおそらく時期尚早です。

クラスターで最初に必要となるノード タイプの数をはっきりさせましょう。ノード タイプはそれぞれ 1 つの仮想マシン スケール セットに対応付けられます。各ノードの種類は、個別にスケール アップまたはスケール ダウンすることができ、さまざまなセットのポートを開き、異なる容量のメトリックスを持つことができます。そのためノード タイプの数は、基本的に次の要因を考慮して決めることになります。

* アプリケーションに複数のサービスが存在するか、またその中に、外部に公開 (インターネットに接続) しなければならないサービスはあるか。 標準的なアプリケーションには、クライアントからの入力を受け取るフロントエンド ゲートウェイ サービスと、そのフロントエンド サービスと通信するバックエンド サービスが存在します。したがってこのケースでは、最終的に少なくとも 2 つのノード タイプが必要となります。
* アプリケーションの構成要素として、インフラストラクチャ ニーズの異なる複数のサービスが存在するか (大容量の RAM が必要、高い CPU 処理能力が必要など)。 たとえばデプロイするアプリケーションに、フロントエンド サービスとバックエンド サービスが含まれていると仮定します。フロントエンド サービスはバックエンド サービスよりも小さい VM (D2 など) で実行できますが、インターネットに対してポートを開放する必要があります。一方バックエンド サービスは計算負荷が高く、より大きな VM (D4、D6、D15 など) で実行する必要はありますが、インターネット接続は不要です。
  
  この場合、1 つのノード タイプにすべてのサービスをデプロイすることもできますが、Microsoft としては、2 つのノード タイプから成るクラスターにデプロイすることをお勧めします。そうすることでノード タイプごとに、インターネットの接続性や VM サイズなど異なる特性を持たせることができます。VM 数を個別に増減させることもできます。
* 将来を予測することはできないので、まずは把握している事実を踏まえ、アプリケーションの立ち上げ時に必要となるノード タイプの数を決めましょう。ノード タイプは後からいつでも追加または削除できます。Service Fabric クラスターには少なくとも 1 つのノード タイプが必要です。

## 各ノード タイプの特性
**ノード タイプ**は、Cloud Services のロールと同等のものと見なすことができます。ノードのタイプには、VM のサイズ、VM の数、プロパティが定義されています。Service Fabric クラスターで定義されているすべてのノード タイプは、個別の仮想マシン スケール セットとしてセットアップされます。VM スケール セットは、セットとして仮想マシンのコレクションをデプロイおよび管理するために使用できる Azure コンピューティング リソースです。ノード タイプはそれぞれ別々の VM スケール セットとして定義されているため、個別にスケールアップまたはスケールダウンすることができ、また異なるポートの組み合わせを開放し、異なる容量メトリックを割り当てることができます。

クラスターには複数のノード タイプを指定できますが、運用環境のワークロードに使用するクラスターの場合、プライマリ ノード タイプ、つまりポータルで最初に定義したノードには、少なくとも 5 つの VM が必要です (テスト環境のクラスターの場合は 3 つ以上)。Resource Manager テンプレートを使用してクラスターを作成する場合は、ノード タイプの定義に **is Primary** 属性が存在します。Service Fabric のシステム サービスは、プライマリ ノード タイプに配置されます。

### プライマリ ノード タイプ
1 つのクラスターに複数のノード タイプが存在する場合、そのいずれかをプライマリにする必要があります。プライマリ ノード タイプには、次の特徴があります。

* プライマリ ノード タイプの最小 VM サイズは、選択した耐久性レベルによって決まります。既定の耐久性レベルは Bronze です。耐久性レベルとは何か、どのようなプランがあるかについては、このページの下の方で説明しています。
* プライマリ ノード タイプの最低 VM 数は、選択した信頼性レベルによって決まります。既定の信頼性レベルは Silver です。信頼性レベルとは何か、どのようなプランがあるかについては、このページの下の方で説明しています。
* Service Fabric システム サービス (クラスター マネージャー サービス、イメージ ストア サービスなど) はプライマリ ノード タイプに置かれるので、クラスターの信頼性と耐久性は、プライマリ ノード タイプに選択した信頼性レベルのプランと耐久性レベルのプランによって決まります。

![ノードが 2 種類あるクラスターのスクリーン ショット][SystemServices]

### 非プライマリ ノード タイプ
クラスターに複数のノード タイプが存在するとき、プライマリ ノード タイプは 1 つだけで、それ以外はすべて非プライマリ ノード タイプです。非プライマリ ノード タイプには、次の特徴があります。

* このノード タイプの最小 VM サイズは、選択した耐久性レベルによって決まります。既定の耐久性レベルは Bronze です。耐久性レベルとは何か、どのようなプランがあるかについては、このページの下の方で説明しています。
* このノード タイプの最低 VM 数は 1 です。ただしこの数は、このノード タイプで実行するアプリケーション/サービスのレプリカ数に基づいて選ぶ必要があります。ノード タイプの VM 数は、クラスターのデプロイ後に増やすことができます。

## クラスターの耐久性の特徴
耐久性レベルは、ご利用の VM が、基になる Azure インフラストラクチャに対して持つ特権をシステムに表明する目的で使用します。プライマリ ノード タイプでは、Service Fabric がこの特権を使って、システム サービスやステートフル サービスのクォーラム要件に影響を及ぼす、VM レベルのインフラストラクチャ要求 (VM の再起動、VM の再イメージ化、VM の移行など) を一時停止させることができます。非プライマリ ノード タイプの場合も、Service Fabric がこの特権の下で、ノード内で実行されるステートフル サービスのクォーラム要件に影響を及ぼす、VM レベルのインフラストラクチャ要求 (VM の再起動、VM の再イメージ化、VM の移行など) を一時停止させることができます。

この特権は次のプランで表されます。

* Gold - 1 つの更新ドメインにつき 2 時間、インフラストラクチャ ジョブを一時停止させることができます。
* Silver - 1 つの更新ドメインにつき 30 分、インフラストラクチャ ジョブを一時停止させることができます (現在有効になっていません。有効にすると、単一コア以上のすべての Standard VM で使用できます)。
* Bronze - 特権はありません。既定のプランです。

## クラスターの信頼性の特徴
信頼性レベルは、クラスターのプライマリ ノード タイプで実行するシステム サービスのレプリカ数を設定する目的で使用します。レプリカ数が増えるほど、クラスター内のシステム サービスの信頼性が上がります。

信頼性レベルは、以下のプランから選ぶことができます。

* Platinum - ターゲット レプリカ セット数を 9 としてシステム サービスを実行します。
* Gold - ターゲット レプリカ セット数を 7 としてシステム サービスを実行します。
* Silver - ターゲット レプリカ セット数を 5 としてシステム サービスを実行します。
* Bronze - ターゲット レプリカ セット数を 3 としてシステム サービスを実行します。

> [!NOTE]
> 選択した信頼性レベルによって、プライマリ ノード タイプに必要な最小ノード数が決まります。信頼性レベルは、クラスターの最大サイズには何の影響も及ぼしません。たとえば、20 ノードのクラスターを Bronze の信頼性で実行することもできます。
> 
> 

 クラスターの信頼性レベルはいつでも変更できます。クラスターの信頼性レベルを変更すると、システム サービスのレプリカ セット数を変更するために必要なクラスターのアップグレードが開始されます。ノードの追加など、クラスターにさらに変更を行う場合は、このアップグレードが完了してからにしてください。アップグレードの進行状況を監視するには、Service Fabric Explorer を使用するか、[Get ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt126012.aspx) を実行します

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
容量計画が完了し、クラスターをセットアップしたら、以下のドキュメントをお読みください。

* [Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md)
* [Service Fabric の正常性モデルの概要](service-fabric-health-introduction.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png

<!---HONumber=AcomDC_0921_2016-->
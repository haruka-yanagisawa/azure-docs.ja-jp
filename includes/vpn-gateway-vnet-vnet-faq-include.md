* 仮想ネットワークが属している Azure リージョン (場所) は異なっていてもかまいません。
* クラウド サービスや負荷分散エンドポイントは、仮にそれらが相互に接続されていたとしても、仮想ネットワークの境界を越えることはできません。
* 複数の Azure Virtual Network を接続するときに、オンプレミスの VPN ゲートウェイは必要ありません (クロスプレミス接続が必要な場合を除く)。
* VNet 間接続によって仮想ネットワークを接続できます。 仮想ネットワーク内に存在しない仮想マシンやクラウド サービスを接続することはできません。
* VNet 間接続には、RouteBased VPN タイプの Azure VPN ゲートウェイが必要です (以前は「動的ルーティング」と呼ばれていました) 。 
* 仮想ネットワーク接続は、マルチサイト VPN と同時に使用することができます。1 つの仮想ネットワーク VPN ゲートウェイに最大 10 本 (既定/標準のゲートウェイ) または 30 本 (高性能ゲートウェイ) の VPN トンネルを確立し、他の仮想ネットワークまたはオンプレミス サイトに接続することが可能です。
* 仮想ネットワークのアドレス空間とオンプレミスのローカル ネットワーク サイトのアドレス空間とが重複しないようにする必要があります。 アドレス空間が重複していると、VNet 間接続の作成は失敗します。
* 一対の仮想ネットワーク間に冗長トンネルを確立することはできません。
* 仮想ネットワークのすべての VPN トンネルは、Azure VPN ゲートウェイ上の使用可能な帯域幅を共有し、Azure 内の同じ VPN ゲートウェイ アップタイム SLA を共有します。
* VNet 間のトラフィックは、インターネットではなく、Microsoft ネットワークで送信されます。
* 同じリージョン内の VNet 間トラフィックは双方向で無料です。リージョンを超えて送信される VNet 間トラフィックには、ソース リージョンに基づき、送信 VNet 内データ転送料金が課せられます。 詳細については、[料金のページ](https://azure.microsoft.com/pricing/details/vpn-gateway/)を参照してください。



<!--HONumber=Nov16_HO2-->



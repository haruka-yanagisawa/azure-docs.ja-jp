

カスタム スクリプト拡張機能は、導入以来、Windows VM と Linux VM でワークロードを構成するために広く使用されてきました。Azure リソース マネージャー テンプレートの導入により、VM をプロビジョニングするだけでなく、VM でワークロードも構成する単一のテンプレートを作成できるようになりました。

## Azure リソース マネージャーのテンプレートについて
Azure リソース マネージャー テンプレートでは、リソース間の依存関係を定義することで、JSON 言語で Azure IaaS インフラストラクチャを宣言によって指定できます。Azure リソース マネージャー テンプレートの概要については、次の記事をご覧ください。

* [リソース グループの概要](../articles/resource-group-overview.md)
* [Azure Powershell を使用したテンプレートのデプロイ](../articles/virtual-machines/virtual-machines-windows-ps-manage.md)

### 前提条件
1. お使いのオペレーティング システムの Azure コマンド ライン ツールを[ここ](https://azure.microsoft.com/downloads/)からダウンロードします。
2. スクリプトが既存の仮想マシンで実行される場合、VM で VM エージェントが有効になっていることを確認してください。そうでない場合、[Linux](../articles/virtual-machines/virtual-machines-linux-classic-manage extensions.md) または [Windows](../articles/virtual-machines/virtual-machines-windows-classic-manage extensions.md) のガイダンスに従って、VM エージェントをインストールしてください。
3. VM で実行するスクリプトを Azure Storage にアップロードします。スクリプトは、1 つか複数のストレージ コンテナーから取得できます。
4. スクリプトを GitHub アカウントにアップロードすることもできます。
5. スクリプトは、拡張機能によって起動されるエントリ スクリプトが、他のスクリプトを順に起動するように記述されている必要があります。

## カスタム スクリプト拡張機能の使用
テンプレートを使用したデプロイでは、Azure サービス管理 API で使用できるバージョンと同じバージョンのカスタム スクリプト拡張機能を使用します。拡張機能では、同じパラメーターとシナリオ (Azure ストレージ アカウントまたは Github へのファイルのアップロードなど) をサポートします。テンプレートで使用する際の重要な違いは、拡張機能のバージョンを majorversion.* 形式で指定するのではなく、正確なバージョンを指定する必要があることです。

<!---HONumber=AcomDC_0420_2016-->
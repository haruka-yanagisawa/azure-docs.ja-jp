| リソース | 無料 | Basic (プレビュー) | S1 | S2 |
| --- | --- | --- | --- | --- |
| サブスクリプション レベルごとの最大サービス数 <sup>1</sup> |1 |12 |12 |1 |
| レベルごとの最大スケール <sup>2</sup> |該当なし |3 SU (最大 3 レプリカと 1 パーティション) |36 SU |36 SU |

<sup>1</sup> サービスはそれぞれ特定の価格レベルでプロビジョニングされ、単一の Azure サブスクリプション内のレベルごとにプロビジョニングできるサービスの数には制限があります。プレビュー期間中、レベルは全額の 50% オフの導入価格で利用できます。

<sup>2</sup> スケール アウトの制限は、レベルあたりの検索ユニット (SU) の観点から定義されます。検索単位数は**レプリカ**または**パーティション**のいずれかの課金対象項目です。ストレージ、インデックス作成と、クエリ操作の両方が必要です。**Basic** と **Standard** それぞれで 3 または 36 ユニットの最大制限内で保持するレプリカとパーティションの有効な組み合わせについては、「[クエリとインデックスのワークロードに関するリソースレベルのスケール](../articles/search/search-capacity-planning.md)」を参照してください。 **Free** は複数のサブスクライバーによって使用される共有リソースに基づいているため、このレベルではスケール アウトは提供されていません。

<!---HONumber=AcomDC_0601_2016-->
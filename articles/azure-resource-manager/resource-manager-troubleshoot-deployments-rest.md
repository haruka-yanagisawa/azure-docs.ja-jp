---
title: "REST API でのデプロイ操作の表示 | Microsoft Docs"
description: "Azure Resource Manager REST API を使用して、リソース マネージャーのデプロイからの問題を検出する方法について説明します。"
services: azure-resource-manager,virtual-machines
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: af2abbef-5454-45d3-b5cf-4ae10347e630
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 06/13/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: f6e684b08ed481cdf84faf2b8426da72f98fc58c
ms.openlocfilehash: e26a41a38fb78c0e6e075f952e05f40d4e87f1e5


---
# <a name="view-deployment-operations-with-azure-resource-manager-rest-api"></a>Azure Resource Manager REST API でのデプロイ操作の表示
> [!div class="op_single_selector"]
> * [ポータル](resource-manager-troubleshoot-deployments-portal.md)
> * [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
> * [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
> * [REST API](resource-manager-troubleshoot-deployments-rest.md)
> 
> 

Azure にリソースをデプロイするときにエラーが発生した場合、実行したデプロイ操作に関して、より詳しい情報が必要になることがあります。 REST API では、エラーを見つけて、可能性のある修正を確認できるように、操作を提供します。

[!INCLUDE [resource-manager-troubleshoot-introduction](../../includes/resource-manager-troubleshoot-introduction.md)]

デプロイの前にテンプレートおよびインフラストラクチャを検証して、いくつかのエラーを回避できます。 後からトラブルシューティングに役立つと思われるデプロイメント中の追加の要求および応答の情報を記録することもできます。 検証、 ログ記録の要求および応答の情報については、「 [Azure リソース マネージャーのテンプレートを使用したリソース グループのデプロイ](resource-group-template-deploy-rest.md)」を参照してください。

## <a name="troubleshoot-with-rest-api"></a>REST API を使用したトラブルシューティング
1. [テンプレート デプロイを作成する](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate)操作でリソースをデプロイします。 デバッグに役立つ可能性のある情報を保持するには、JSON 要求の **debugSetting** プロパティを **requestContent** や **responseContent** に設定します。 
   
        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",      
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }
   
    既定では、**debugSetting** の値は、**none** に設定されます。 **debugSetting** の値を指定する場合、デプロイ時に渡している情報の種類を慎重に検討します。 要求または応答に関する情報をログ記録すると、デプロイ操作で取得される重要なデータを公開する可能性があります。 
2. [テンプレート デプロイに関する情報を取得する](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get)操作を行って、デプロイに関する情報を取得します。
   
        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
   
    応答では、特に **provisioningState**、**correlationId**、および **error** 要素に注意してください。 **correlationId** は、関連するイベントを追跡するために使用されます。また、問題のトラブルシューティングを行うためにテクニカル サポートと共に作業を行うときにも、役立つ場合があります。
   
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
            }  
          }
        }
3. [すべてのテンプレート デプロイ操作を一覧表示する](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List)操作を行って、デプロイ操作に関する情報を取得します。 
   
        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
   
    応答には、デプロイ時に **debugSetting** プロパティで指定した内容に基づいて、要求または応答の情報が含まれます。
   
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }
4. [サブスクリプションの管理イベントを一覧表示する](https://msdn.microsoft.com/library/azure/dn931934.aspx) 操作を行って、デプロイに関する監査情報からイベントを取得します。
   
        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}

## <a name="next-steps"></a>次のステップ
* 特定のデプロイ エラーの解決については、 [Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーの解決](resource-manager-common-deployment-errors.md)に関するページを参照してください。
* 他の種類のアクションを監視するために監査ログを使用する方法については、「 [Resource Manager の監査操作](resource-group-audit.md)」を参照してください。
* デプロイを実行する前に検証するには、「 [Azure Resource Manager のテンプレートを使用したリソースのデプロイ](resource-group-template-deploy.md)」を参照してください。




<!--HONumber=Nov16_HO3-->



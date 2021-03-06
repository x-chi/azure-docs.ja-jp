---
title: Azure Red Hat OpenShift 3.11 を使用してプライベート クラスターを作成する | Microsoft Docs
description: Azure Red Hat OpenShift 3.11 を使用してプライベート クラスターを作成する
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: ARO, OpenShift, プライベート クラスター, Rad Hat
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399268"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Azure Red Hat OpenShift 3.11 を使用してプライベート クラスターを作成する

> [!IMPORTANT]
> 現在、Azure Red Hat OpenShift (ARO) プライベート クラスターは、米国東部 2 のプライベート プレビューでのみご利用いただけます。 プライベート プレビューの受け入れは招待によってのみ可能です。 この機能を有効にする前に、必ずサブスクリプションを登録してください。

プライベート クラスターには、次のような利点があります。

* プライベート クラスターは、パブリック IP アドレスでクラスターのコントロール プレーン コンポーネント (API サーバーなど) を公開しません。
* プライベート クラスターの仮想ネットワークの構成は、お客様が行うことができます。これにより、ExpressRoute 環境など、他の仮想ネットワークとのピアリングが可能になるようにネットワークを設定できます。 また、仮想ネットワークにカスタム DNS を構成して、内部サービスと統合することもできます。

## <a name="before-you-begin"></a>開始する前に

> [!NOTE]
> この機能には、ARO HTTP API のバージョン 2019-10-27-preview が必要です。 これは、Azure CLI ではまだサポートされていません。

次の構成スニペットのフィールドは新しいものであり、クラスター構成に含める必要があります。 `managementSubnetCidr` はクラスターの仮想ネットワーク内にある必要があり、クラスターを管理するために Azure によって使用されます。

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

プライベート クラスターは、以下に示すサンプル スクリプトを使用してデプロイできます。 クラスターがデプロイされたら、`cluster get` コマンドを実行し、`properties.FQDN` プロパティを表示して、OpenShift API サーバーのプライベート IP アドレスを特定します。

クラスター仮想ネットワークは、変更できるように、アクセス許可を使用して作成されます。 その後、必要に応じて、仮想ネットワーク (ExpressRoute、VPN、仮想ネットワーク ピアリング) にアクセスするようにネットワークを設定できます。

クラスター仮想ネットワークで DNS ネーム サーバーを変更する場合は、VM を再イメージ化できるように、`properties.RefreshCluster` プロパティを `true` に設定して、クラスターで更新を実行する必要があります。 この更新により、新しいネーム サーバーを取得できるようになります。

## <a name="sample-configuration-scripts"></a>サンプル構成スクリプト

プライベート クラスターを設定してデプロイするには、このセクションのサンプル スクリプトを使用します。

### <a name="environment"></a>環境

独自の値を使用して、以下の環境変数を入力します。

> [!NOTE]
> 場所は、現在プライベート クラスターでサポートされている唯一の場所である `eastus2` に設定する必要があります。

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>private-cluster.json

上で定義した環境変数を使用して、プライベート クラスターが有効になっているクラスター構成の例を次に示します。

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>プライベート クラスターをデプロイする

上記のサンプル スクリプトを使用してプライベート クラスターを構成した後、次のコマンドを実行してプライベート クラスターをデプロイします。

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>次のステップ

OpenShift コンソールにアクセスする方法については、[Web コンソールのチュートリアル](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html)を参照してください。

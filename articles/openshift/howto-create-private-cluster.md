---
title: Skapa ett privat kluster med Azure Red Hat OpenShift 3,11 | Microsoft Docs
description: Skapa ett privat kluster med Azure Red Hat OpenShift 3,11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: Aro, OpenShift, privat kluster, Red Hat
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399424"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Skapa ett privat kluster med Azure Red Hat OpenShift 3,11

> [!IMPORTANT]
> Privata kluster i Azure Red Hat OpenShift (ARO) är för närvarande endast tillgängliga i privat för hands version i USA, östra 2. Godkännande av privat för hands version är endast efter inbjudan. Kom ihåg att registrera din prenumeration innan du försöker aktivera den här funktionen.

Privata kluster ger följande fördelar:

* Privata kluster exponerar inte komponenter i kluster kontroll planet (till exempel API-servrar) på en offentlig IP-adress.
* Det virtuella nätverket i ett privat kluster kan konfigureras av kunder, så att du kan konfigurera nätverk så att de tillåter peering med andra virtuella nätverk, inklusive ExpressRoute-miljöer. Du kan också konfigurera anpassad DNS på det virtuella nätverket för integrering med interna tjänster.

## <a name="before-you-begin"></a>Innan du börjar

> [!NOTE]
> Den här funktionen kräver version 2019-10-27 – för hands version av ARO HTTP-API. Det stöds ännu inte i Azure CLI.

Fälten i följande konfigurations kods tycke är nya och måste ingå i kluster konfigurationen. `managementSubnetCidr` måste ligga inom klustrets virtuella nätverk och används av Azure för att hantera klustret.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Ett privat kluster kan distribueras med hjälp av de exempel skript som anges nedan. När klustret har distribuerats kör du kommandot `cluster get` och visar egenskapen `properties.FQDN` för att fastställa den privata IP-adressen för OpenShift-API-servern.

Klustrets virtuella nätverk kommer att ha skapats med behörigheter så att du kan ändra det. Du kan sedan konfigurera nätverk för att få åtkomst till det virtuella nätverket (ExpressRoute, VPN, virtuell nätverks-peering) som krävs för dina behov.

Om du ändrar DNS-namnservrar på det virtuella kluster nätverket måste du utfärda en uppdatering på klustret med egenskapen `properties.RefreshCluster` inställd på `true` så att de virtuella datorerna kan avbildas. Den här uppdateringen gör att de kan hämta den nya namnservrar.

## <a name="sample-configuration-scripts"></a>Exempel på konfigurations skript

Använd exempel skripten i det här avsnittet för att konfigurera och distribuera ditt privata kluster.

### <a name="environment"></a>Miljö

Fyll i miljövariablerna nedan när du använder dina egna värden.

> [!NOTE]
> Platsen måste anges till `eastus2` eftersom detta är den enda plats som stöds för privata kluster.

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

### <a name="private-clusterjson"></a>privat-Cluster. JSON

Med de miljövariabler som definierats ovan är här en exempel kluster konfiguration med privat kluster aktiverat.

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

## <a name="deploy-a-private-cluster"></a>Distribuera ett privat kluster

När du har konfigurerat ditt privata kluster med exempel skripten ovan kör du följande kommando för att distribuera ditt privata kluster.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer åt OpenShift-konsolen finns i [genom gång av webb konsolen](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).

---
title: Skapa ett privat kluster med Azure Red Hat OpenShift 3.11 | Microsoft-dokument
description: Skapa ett privat kluster med Azure Red Hat OpenShift 3.11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, privat kluster, röd hatt
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399424"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Skapa ett privat kluster med Azure Red Hat OpenShift 3.11

> [!IMPORTANT]
> Privata kluster (Azure Red Hat OpenShift) är för närvarande endast tillgängliga i privat förhandsversion i östra USA 2. Accepterar privat förhandsgranskning sker endast genom inbjudan. Var noga med att registrera din prenumeration innan du försöker aktivera den här funktionen.

Privata kluster ger följande fördelar:

* Privata kluster exponerar inte klusterkontrollplankomponenter (till exempel API-servrar) på en offentlig IP-adress.
* Det virtuella nätverket i ett privat kluster kan konfigureras av kunder, så att du kan konfigurera nätverk för att tillåta peering med andra virtuella nätverk, inklusive ExpressRoute-miljöer. Du kan också konfigurera anpassad DNS i det virtuella nätverket för att integrera med interna tjänster.

## <a name="before-you-begin"></a>Innan du börjar

> [!NOTE]
> Den här funktionen kräver version 2019-10-27-förhandsvisning av ARO HTTP API. Det stöds ännu inte i Azure CLI.

Fälten i följande konfigurationskodavsnitt är nya och måste inkluderas i klusterkonfigurationen. `managementSubnetCidr`måste finnas i klustrets virtuella nätverk och används av Azure för att hantera klustret.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Ett privat kluster kan distribueras med hjälp av exempelskripten nedan. När klustret har `cluster get` distribuerats `properties.FQDN` kör du kommandot och visar egenskapen för att fastställa den privata IP-adressen för OpenShift API-servern.

Det virtuella klustret har skapats med behörigheter så att du kan ändra det. Du kan sedan konfigurera nätverk för att komma åt det virtuella nätverket (ExpressRoute, VPN, virtuell nätverks peering) som krävs för dina behov.

Om du ändrar DNS-namnservrarna i klustrets virtuella nätverk måste du `properties.RefreshCluster` utfärda `true` en uppdatering i klustret med egenskapen inställd på så att de virtuella datorerna kan göras om. Den här uppdateringen gör det möjligt för dem att plocka upp de nya namnservrarna.

## <a name="sample-configuration-scripts"></a>Exempel på konfigurationsskript

Använd exempelskripten i det här avsnittet för att konfigurera och distribuera ditt privata kluster.

### <a name="environment"></a>Miljö

Fyll i miljövariablerna nedan som med dina egna värden.

> [!NOTE]
> Platsen måste anges `eastus2` till eftersom det för närvarande är den enda plats som stöds för privata kluster.

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

### <a name="private-clusterjson"></a>privat-cluster.json

Med hjälp av de miljövariabler som definierats ovan är här en exempelklusterkonfiguration med privat kluster aktiverat.

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

När du har konfigurerat ditt privata kluster med exempelskripten ovan kör du följande kommando för att distribuera ditt privata kluster.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kommer åt OpenShift-konsolen finns i [Web Console Walkthrough](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).

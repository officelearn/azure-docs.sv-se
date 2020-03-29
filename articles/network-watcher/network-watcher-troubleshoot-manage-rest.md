---
title: Felsöka VNET Gateway och anslutningar – Azure REST API
titleSuffix: Azure Network Watcher
description: På den här sidan beskrivs felsÃ¶kning av virtual network gateways and connections with Azure Network Watcher using REST
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: ab9f7fd95d7081b66e05dfd3d6a5ef47eb3c4053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840680"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Felsöka virtual network gateway och anslutningar med Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [Powershell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher innehåller många funktioner när det gäller att förstå dina nätverksresurser i Azure. En av dessa funktioner är felsökning av resurser. Resursfelsökning kan anropas via portalen, PowerShell, CLI eller REST API. När network watcher anropas inspekterar den hälsotillståndet för en virtuell nätverksgateway eller en anslutning och returnerar dess resultat.

Den här artikeln tar dig igenom de olika hanteringsuppgifter som för närvarande är tillgängliga för resursfelsökning.

- [**Felsöka en virtuell nätverksgateway**](#troubleshoot-a-virtual-network-gateway)
- [**Felsöka en anslutning**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Innan du börjar

ARMclient används för att anropa REST API med PowerShell. ARMClient finns på chocolatey på [ARMClient på Chocolatey](https://chocolatey.org/packages/ARMClient)

Det här scenariot förutsätter att du redan har följt stegen i [Skapa en nätverksbevakare](network-watcher-create.md) för att skapa en Network Watcher.

En lista över gatewaytyper som stöds besöker [gatewaytyper som stöds](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Översikt

Felsökning av network watcher ger möjlighet att felsöka problem som uppstår med gateways och anslutningar för virtuella nätverk. När en begäran görs till resursfelsökningen frågar och inspekteras loggar. När inspektionen är klar returneras resultaten. Felsöknings-API-begäranden är tidskrävande begäranden, vilket kan ta flera minuter att returnera ett resultat. Loggar lagras i en behållare på ett lagringskonto.

## <a name="log-in-with-armclient"></a>Logga in med ARMClient

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Felsöka en virtuell nätverksgateway


### <a name="post-the-troubleshoot-request"></a>PUBLICERA felsökningsbegäran

I följande exempel frågar status för en virtuell nätverksgateway.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$vnetGatewayName = "ContosoVNETGateway"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @"
{
'TargetResourceId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/virtualNetworkGateways/${vnetGatewayName}',
'Properties': {
'StorageId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}',
'StoragePath': 'https://${storageAccountName}.blob.core.windows.net/${containerName}'
}
}
"@


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" $requestBody -verbose
```

Eftersom den här åtgärden är tidskrävande returneras URI:en för att fråga åtgärden och URI:n för resultatet i svarshuvudet enligt följande svar:

**Viktiga värden**

* **Azure-AsyncOperation** - Den här egenskapen innehåller URI för att fråga Async-felsökningsåtgärden
* **Plats** - Den här egenskapen innehåller URI där resultaten är när åtgärden är klar

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Fråga asynkronåtgärden för slutförande

Använd URI-åtgärderna för att fråga efter förloppet för åtgärden enligt följande exempel:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Medan åtgärden pågår visas **InProgress** enligt följande exempel:

```json
{
  "status": "InProgress"
}
```

När åtgärden är klar ändras statusändringarna till **Lyckades**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Hämta resultaten

När statusen har **returnerats anropar**du en GET-metod på åtgärdenResult URI för att hämta resultaten.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Följande svar är exempel på ett typiskt försämrat svar som returneras när du frågar resultatet av felsökning av en gateway. Se [Förstå resultaten](#understanding-the-results) för att få förtydligande om vad egenskaperna i svaret betyder.

```json
{
  "startTime": "2017-01-12T10:31:41.562646-08:00",
  "endTime": "2017-01-12T18:31:48.677Z",
  "code": "Degraded",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN Gateway"
        },
        {
          "actionText": "If your VPN gateway isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN gateway is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Felsöka anslutningar

I följande exempel frågar status för en anslutning.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$connectionName = "VNET2toVNET1Connection"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @{
"TargetResourceId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/connections/${connectionName}",
"Properties": {
"StorageId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}",
"StoragePath": "https://${storageAccountName}.blob.core.windows.net/${containerName}"
}

}
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 $requestBody"
```

> [!NOTE]
> Felsökningsåtgärden kan inte köras parallellt på en anslutning och motsvarande gateways. Åtgärden måste slutföras innan den körs på föregående resurs.

Eftersom detta är en tidskrävande transaktion returneras uri:n för att fråga åtgärden och URI för resultatet i svarshuvudet som visas i följande svar:

**Viktiga värden**

* **Azure-AsyncOperation** - Den här egenskapen innehåller URI för att fråga Async-felsökningsåtgärden
* **Plats** - Den här egenskapen innehåller URI där resultaten är när åtgärden är klar

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Fråga asynkronåtgärden för slutförande

Använd URI-åtgärderna för att fråga efter förloppet för åtgärden enligt följande exempel:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Medan åtgärden pågår visas **InProgress** enligt följande exempel:

```json
{
  "status": "InProgress"
}
```

När åtgärden är klar ändras statusen till **Lyckades**.

```json
{
  "status": "Succeeded"
}
```

Följande svar är exempel på ett typiskt svar som returneras när du frågar resultatet av felsökning av en anslutning.

### <a name="retrieve-the-results"></a>Hämta resultaten

När statusen har **returnerats anropar**du en GET-metod på åtgärdenResult URI för att hämta resultaten.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Följande svar är exempel på ett typiskt svar som returneras när du frågar resultatet av felsökning av en anslutning.

```json
{
  "startTime": "2017-01-12T14:09:19.1215346-08:00",
  "endTime": "2017-01-12T22:09:23.747Z",
  "code": "UnHealthy",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This 
is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN gateway"
        },
        {
          "actionText": "If your VPN Connection isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN Connection is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Förstå resultaten

Åtgärdstexten ger allmän vägledning om hur du löser problemet. Om en åtgärd kan vidtas för problemet får en länk ytterligare vägledning. Om det inte finns någon ytterligare vägledning, ger svaret url för att öppna ett supportärende.  Mer information om svarets egenskaper och vad som ingår finns i [översikt över felsökning](network-watcher-troubleshoot-overview.md) av Network Watcher

Instruktioner om hur du hämtar filer från azure storage-konton finns i [Komma igång med Azure Blob-lagring med .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som kan användas är Storage Explorer. Mer information om Storage Explorer finns här på följande länk: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Nästa steg

Om inställningarna har ändrats som stoppar VPN-anslutning läser du [Hantera nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md) för att spåra nätverkssäkerhetsgruppen och säkerhetsregler som kan vara i fråga.

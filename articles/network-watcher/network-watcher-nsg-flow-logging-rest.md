---
title: Hantera NSG-flödesloggar - Azure REST API
titleSuffix: Azure Network Watcher
description: På den här sidan beskrivs hur du hanterar flödesloggar för nätverkssäkerhetsgrupper i Azure Network Watcher med REST API
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7cc47414dc985f6fc2fff3c57d809f307b142e30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840935"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Konfigurera flödesloggar för nätverkssäkerhetsgruppen med REST API

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Nätverkssäkerhetsgruppsflödesloggar är en funktion i Network Watcher som gör att du kan visa information om inkommande och utgående IP-trafik via en nätverkssäkerhetsgrupp. Dessa flödesloggar är skrivna i json-format och visar utgående och inkommande flöden per regel, nätverkskortet som flödet gäller för, 5-tuppelinformation om flödet (Source/Destination IP, Source/Destination Port, Protocol) och om trafiken tilläts eller nekades.

## <a name="before-you-begin"></a>Innan du börjar

ARMclient används för att anropa REST API med PowerShell. ARMClient finns på chocolatey på [ARMClient på Chocolatey](https://chocolatey.org/packages/ARMClient)

Det här scenariot förutsätter att du redan har följt stegen i [Skapa en nätverksbevakare](network-watcher-create.md) för att skapa en Network Watcher.

> [!Important]
> För NETWORK Watcher REST API anropar resursgruppnamnet i begäran URI är resursgruppen som innehåller Network Watcher, inte de resurser som du utför diagnostikåtgärderna på.

## <a name="scenario"></a>Scenario

Scenariot som beskrivs i den här artikeln visar hur du aktiverar, inaktiverar och frågar flödesloggar med REST API. Mer information om flödesloggningar för nätverkssäkerhetsgruppen finns i [Flödesloggning av nätverkssäkerhetsgrupp - Översikt](network-watcher-nsg-flow-logging-overview.md).

I det här fallet kommer du att:

* Aktivera flödesloggar (version 2)
* Inaktivera flödesloggar
* Status för frågeflödesloggar

## <a name="log-in-with-armclient"></a>Logga in med ARMClient

Logga in för att armkla ientera dina Azure-autentiseringsuppgifter.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Registrera Insights-providern

För att flödesloggningen ska fungera måste **Microsoft.Insights-providern** vara registrerad. Om du är osäker på om **Microsoft.Insights-providern** är registrerad kör du följande skript.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Aktivera flödesloggar för nätverkssäkerhetsgruppen

Kommandot för att aktivera flödesloggar version 2 visas i följande exempel. För version 1 ersätter "version"-fältet med "1":

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Svaret som returneras från föregående exempel är följande:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="disable-network-security-group-flow-logs"></a>Inaktivera flödesloggar för nätverkssäkerhetsgruppen

Använd följande exempel för att inaktivera flödesloggar. Anropet är detsamma som att aktivera flödesloggar, förutom **att false** har angetts för den aktiverade egenskapen.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Svaret som returneras från föregående exempel är följande:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="query-flow-logs"></a>Frågeflödesloggar

Följande REST-anrop frågar status för flödesloggar i en nätverkssäkerhetsgrupp.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

Följande är ett exempel på svaret som returneras:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
   "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="download-a-flow-log"></a>Hämta en flödeslogg

Lagringsplatsen för en flödeslogg definieras när den skapas. Ett praktiskt verktyg för att komma åt dessa flödesloggar som sparats på ett lagringskonto är Microsoft Azure Storage Explorer, som kan hämtas här:https://storageexplorer.com/

Om ett lagringskonto anges sparas paketinsamlingsfiler i ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [visualiserar dina NSG-flödesloggar med PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Lär dig hur du [visualiserar dina NSG-flödesloggar med verktyg med öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

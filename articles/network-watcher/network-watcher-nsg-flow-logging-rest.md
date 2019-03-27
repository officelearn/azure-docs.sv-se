---
title: Hantera Nätverkssäkerhetsgrupp flödesloggar med Azure Network Watcher - REST API | Microsoft Docs
description: Den här sidan förklarar hur du hanterar flödesloggar för Nätverkssäkerhetsgruppen i Azure Network Watcher med REST API
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2ab25379-0fd3-4bfe-9d82-425dfc7ad6bb
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 4569ea0ff43993c4605c55c529dbcb951ef502ca
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485025"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Konfigurera någon Nätverkssäkerhetsgrupp flödesloggar med REST API

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Flödesloggar för Nätverkssäkerhetsgruppen är en funktion i Network Watcher där du kan visa information om ingående och utgående IP-trafik via en Nätverkssäkerhetsgrupp. Dessa flödesloggar skrivs i json-format och visa utgående och inkommande flöden på basis av per regel, nätverkskortet flödet gäller för 5-tuppel information om flödet (källa/mål-IP, källa/mål-Port, Protocol), och om trafik tillåts eller nekas.

## <a name="before-you-begin"></a>Innan du börjar

ARMclient används för att anropa REST-API med hjälp av PowerShell. ARMClient hittas på chocolatey på [ARMClient på Chocolatey](https://chocolatey.org/packages/ARMClient)

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Network Watcher](network-watcher-create.md) att skapa en Network Watcher.

> [!Important]
> För Network Watcher REST-API-anrop som resursgruppens namn i begärande-URI är resursgruppen som innehåller Network Watcher, inte resurserna du utför åtgärderna som diagnostiska på.

## <a name="scenario"></a>Scenario

Det scenario som beskrivs i den här artikeln visar hur du aktivera, inaktivera och fråga efter flödesloggar med hjälp av REST-API. Läs mer om Nätverkssäkerhetsgruppen flow loggings [flödesloggar för Nätverkssäkerhetsgruppen - översikt](network-watcher-nsg-flow-logging-overview.md).

I det här scenariot kommer du att:

* Aktivera flödesloggar (Version 2)
* Inaktivera flödesloggar
* Frågestatus flow loggar

## <a name="log-in-with-armclient"></a>Logga in med ARMClient

Logga in på armclient med dina autentiseringsuppgifter för Azure.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Registrera Insights-providern

För flow loggning för att fungera korrekt, den **Microsoft.Insights** providern måste vara registrerad. Om du inte är säker på att om den **Microsoft.Insights** providern är registrerad, kör följande skript.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Aktivera Nätverkssäkerhetsgrupp flödesloggar

Kommando för att aktivera flödet loggar version 2 visas i följande exempel. Ersätt versionsfältet med ”1” för version 1:

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

Svaret som returnerades från föregående exempel är följande:

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

## <a name="disable-network-security-group-flow-logs"></a>Inaktivera Nätverkssäkerhetsgrupp flödesloggar

Använd följande exempel för att inaktivera flödesloggar. Anropet är detsamma som att aktivera flödesloggar utom **FALSKT** har angetts för egenskapen aktiverad.

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

Svaret som returnerades från föregående exempel är följande:

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

## <a name="query-flow-logs"></a>Flödesloggar för fråga

Följande frågor för REST-anrop status för flödet loggar in på en Nätverkssäkerhetsgrupp.

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

Följande är ett exempel på svaret som returnerades:

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

## <a name="download-a-flow-log"></a>Ladda ned en flow-log

Lagringsplatsen för en flow-log definieras när du skapar. Ett praktiskt verktyg för att komma åt dessa flödesloggar som sparats i ett lagringskonto är Microsoft Azure Storage Explorer, som kan hämtas här:  https://storageexplorer.com/

Om ett lagringskonto anges sparas packet capture filer till ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [visualisera din NSG-flödesloggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Lär dig hur du [visualisera din NSG-flödesloggar med verktyg för öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

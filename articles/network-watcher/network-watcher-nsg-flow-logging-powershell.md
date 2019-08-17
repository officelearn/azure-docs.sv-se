---
title: Hantera flödes loggar för nätverks säkerhets grupper med Azure Network Watcher – PowerShell | Microsoft Docs
description: På den här sidan förklaras hur du hanterar flödes loggar för nätverks säkerhets grupper i Azure Network Watcher med PowerShell
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: c34ed9c5f1b5e422ba9e4e0b12fbaf833c8a4a7c
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563465"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Konfigurera flödes loggar för nätverks säkerhets grupper med PowerShell

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Flödes loggar för nätverks säkerhets grupper är en funktion i Network Watcher som gör att du kan visa information om inkommande och utgående IP-trafik via en nätverks säkerhets grupp. Dessa flödes loggar skrivs i JSON-format och visar utgående och inkommande flöden per regel, vilket nätverkskort flödet gäller för, 5-tuple-information om flödet (käll-/mål-IP, käll-och mål Port, protokoll) och om trafiken tillåts eller nekas.

## <a name="register-insights-provider"></a>Registrera Insights-providern

För att flödes loggningen ska fungera korrekt måste **Microsoft. Insights** -providern vara registrerad. Om du inte är säker på om **Microsoft. Insights** -providern är registrerad kör du följande skript.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Aktivera flödes loggar för nätverks säkerhets grupper och Trafikanalys

Kommandot för att aktivera flödes loggar visas i följande exempel:

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceid -WorkspaceLocation $workspaceRegion

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

Det lagrings konto som du anger kan inte ha nätverks regler som har kon figurer ATS för att begränsa nätverks åtkomsten till enbart Microsoft-tjänster eller vissa virtuella nätverk. Lagrings kontot kan vara i samma eller en annan Azure-prenumeration än NSG som du aktiverar flödes loggen för. Om du använder olika prenumerationer måste båda vara kopplade till samma Azure Active Directory-klient. Det konto som du använder för varje prenumeration måste ha de [behörigheter som krävs](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Inaktivera flödes loggar för Trafikanalys och nätverks säkerhets grupper

Använd följande exempel för att inaktivera trafik analyser och flödes loggar:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Hämta en flödes logg

Lagrings platsen för en flödes logg definieras när den skapas. Ett användbart verktyg för att komma åt dessa flödes loggar som sparas till ett lagrings konto är Microsoft Azure Storage Explorer, som kan hämtas här: https://storageexplorer.com/

Om ett lagrings konto har angetts sparas flödes logg filen på ett lagrings konto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
> [!IMPORTANT]
> För närvarande finns det ett problem där [nätverks säkerhets grupps flödes loggar (NSG)](network-watcher-nsg-flow-logging-overview.md) för Network Watcher inte automatiskt tas bort från Blob Storage utifrån inställningar för bevarande principer. Om du har en befintlig bevarande princip som inte är noll rekommenderar vi att du regelbundet tar bort de lagrings blobbar som håller på att kvarhållas för att undvika kostnader. Mer information om hur du tar bort NSG Flow logg Storage-bloggen finns i [ta bort NSG Flow logg Storage blobs](network-watcher-delete-nsg-flow-log-blobs.md).

Mer information om strukturen för logg besöks [logg översikt över nätverks säkerhets grupp](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [visualiserar dina NSG Flow-loggar med PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Lär dig hur du [visualiserar dina NSG Flow-loggar med verktyg med öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

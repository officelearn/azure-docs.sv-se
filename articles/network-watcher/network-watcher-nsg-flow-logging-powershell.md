---
title: Hantera NSG-flödesloggar - Azure PowerShell
titleSuffix: Azure Network Watcher
description: På den här sidan beskrivs hur du hanterar flödesloggar för nätverkssäkerhetsgrupper i Azure Network Watcher med PowerShell
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
ms.openlocfilehash: 9612afdb63c6988c0027f003caeacd456b5e50e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840952"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Konfigurera flödesloggar för nätverkssäkerhetsgrupp med PowerShell

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Nätverkssäkerhetsgruppsflödesloggar är en funktion i Network Watcher som gör att du kan visa information om inkommande och utgående IP-trafik via en nätverkssäkerhetsgrupp. Dessa flödesloggar är skrivna i json-format och visar utgående och inkommande flöden per regel, nätverkskortet som flödet gäller för, 5-tuppelinformation om flödet (Source/Destination IP, Source/Destination Port, Protocol) och om trafiken tilläts eller nekades.

## <a name="register-insights-provider"></a>Registrera Insights-providern

För att flödesloggningen ska fungera måste **Microsoft.Insights-providern** vara registrerad. Om du är osäker på om **Microsoft.Insights-providern** är registrerad kör du följande skript.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Aktivera flödesloggar för nätverkssäkerhetsgrupper och Traffic Analytics

Kommandot för att aktivera flödesloggar visas i följande exempel:

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
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

Det lagringskonto som du anger kan inte ha nätverksregler konfigurerade för det som begränsar nätverksåtkomsten till endast Microsoft-tjänster eller specifika virtuella nätverk. Lagringskontot kan finnas i samma, eller en annan Azure-prenumeration, än NSG som du aktiverar flödesloggen för. Om du använder olika prenumerationer måste de båda vara associerade till samma Azure Active Directory-klientorganisation. Kontot som du använder för varje prenumeration måste ha [de behörigheter som krävs](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Inaktivera flödesloggar för trafikanalys och nätverkssäkerhetsgrupp

Använd följande exempel för att inaktivera trafikanalys och flödesloggar:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Ladda ner en Flödeslogg

Lagringsplatsen för en flödeslogg definieras när den skapas. Ett praktiskt verktyg för att komma åt dessa flödesloggar som sparats på ett lagringskonto är Microsoft Azure Storage Explorer, som kan hämtas här:https://storageexplorer.com/

Om ett lagringskonto anges sparas flödesloggfiler i ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Mer information om loggens struktur finns i [Översikt över flödesloggen För nätverkssäkerhetsgrupp](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Efterföljande moment

Lär dig hur du [visualiserar dina NSG-flödesloggar med PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Lär dig hur du [visualiserar dina NSG-flödesloggar med verktyg med öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

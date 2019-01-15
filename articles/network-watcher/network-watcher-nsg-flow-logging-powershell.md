---
title: Hantera Network Security Group Flow loggar med Azure Network Watcher – PowerShell | Microsoft Docs
description: Den här sidan förklarar hur du hanterar Network Security Group Flow loggar i Azure Network Watcher med PowerShell
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 92bbcf1285449ac21cd038e1a6d27d2e3d43430b
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259741"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Konfigurera Network Security Group Flow loggar med PowerShell

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Flödesloggar för Nätverkssäkerhetsgruppen är en funktion i Network Watcher där du kan visa information om ingående och utgående IP-trafik via en Nätverkssäkerhetsgrupp. Dessa flödesloggar skrivs i json-format och visa utgående och inkommande flöden på basis av per regel, nätverkskortet flödet gäller för 5-tuppel information om flödet (källa/mål-IP, källa/mål-Port, Protocol), och om trafik tillåts eller nekas.

> [!NOTE] 
> Flödesloggar av version 2 är endast tillgängliga i regionen USA, västra centrala. Aktivera Version 2 kan-loggar i en region som stöds inte resultera i Version 1-loggarna för utdata till ditt lagringskonto.

## <a name="register-insights-provider"></a>Registrera Insights-providern

För flow loggning för att fungera korrekt, den **Microsoft.Insights** providern måste vara registrerad. Om du inte är säker på att om den **Microsoft.Insights** providern är registrerad, kör följande skript.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Aktivera Nätverkssäkerhetsgrupp flödesloggar och trafikanalys

Kommando för att aktivera flödesloggar visas i följande exempel:

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

Det lagringskonto som du anger inte får ha konfigurerat för den Nätverksregler som begränsar nätverksåtkomst till endast Microsoft-tjänster eller specifika virtuella nätverk. Lagringskontot kan vara i samma eller en annan Azure-prenumeration, än NSG: N som du aktiverar det flöde du vill ha. Om du använder olika prenumerationer kan vara de båda kopplade till samma Azure Active Directory-klient. Det konto som används för varje prenumeration måste ha den [behörighet](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Inaktivera Traffic Analytics och Flow för Network Security Group-loggar

Använd följande exempel för att inaktivera trafikanalys och strömma loggar:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Ladda ned en Flow-log

Lagringsplatsen för en flow-log definieras när du skapar. Ett praktiskt verktyg för att komma åt dessa flödesloggar som sparats i ett lagringskonto är Microsoft Azure Storage Explorer, som kan hämtas här:  http://storageexplorer.com/

Om ett lagringskonto anges sparas flow loggfiler till ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Information om strukturen i loggen på [Network Security Group Flow log översikt](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [visualisera din NSG-flödesloggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Lär dig hur du [visualisera din NSG-flödesloggar med verktyg för öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

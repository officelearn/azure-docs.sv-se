---
title: Skapa en instans av Azure Nätverksbevakaren | Microsoft Docs
description: Lär dig hur du aktiverar Nätverksbevakaren i en Azure-region.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 9d3e579cd58bc6c7d67b29998ea5a48a65548b0a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="create-an-azure-network-watcher-instance"></a>Skapa en instans av Azure Nätverksbevakaren

Nätverksbevakaren är en regionala tjänst som gör att du kan övervaka och diagnostisera villkor på nätverket scenariot nivå, till och från Azure. Scenariot nivån övervakning kan du diagnostisera problem på vyn för slutpunkt till slutpunkt. Diagnostiska nätverks- och visualiseringsverktyg som finns tillgängliga med Nätverksbevakaren hjälpa dig att förstå, diagnostisera och få insyn i nätverket i Azure.

## <a name="create-a-network-watcher-in-the-portal"></a>Skapa en Nätverksbevakaren i portalen

Gå till **alla tjänster** > **nätverk** > **nätverk Watcher**. Du kan välja alla prenumerationer som du vill aktivera Nätverksbevakaren för. Den här åtgärden skapar en Nätverksbevakaren i varje region som är tillgänglig.

![Skapa en nätverksbevakaren](./media/network-watcher-create/figure1.png)

När du aktiverar Nätverksbevakaren med hjälp av portalen namnet på instansen Nätverksbevakaren anges automatiskt *NetworkWatcher_region_name* där *region_name* motsvarar Azure-regionen där instansen är aktiverat. Till exempel en Nätverksbevakaren aktiverad i region Väst centrala USA heter *NetworkWatcher_westcentralus*.

Nätverksbevakaren instans skapas automatiskt i en resursgrupp med namnet *NetworkWatcherRG*. Resursgruppens namn skapas om den inte redan finns.

Om du vill anpassa namnet på en Nätverksbevakaren-instans och resursgruppen har placerats i, kan du använda Powershell, Azure CLI, REST-API eller ARMClient metoder som beskrivs i avsnitten som följer. I varje alternativ, måste resursgruppens namn finnas innan du skapar en Nätverksbevakaren i den.  

## <a name="create-a-network-watcher-with-powershell"></a>Skapa en Nätverksbevakaren med PowerShell

Om du vill skapa en instans av Nätverksbevakaren, kör du följande exempel:

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Skapa en Nätverksbevakaren med Azure CLI

Om du vill skapa en instans av Nätverksbevakaren, kör du följande exempel:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Skapa en Nätverksbevakaren med REST API

ARMclient används för att anropa REST-API med hjälp av PowerShell. ARMClient hittas på chocolatey på [ARMClient på Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Logga in med ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Skapa nätverksbevakaren

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>Nästa steg

Nu när du har en instans av Nätverksbevakaren Lär dig mer om funktionerna:

* [Topologi](network-watcher-topology-overview.md)
* [Paketinsamling](network-watcher-packet-capture-overview.md)
* [Verifiera IP-flöde](network-watcher-ip-flow-verify-overview.md)
* [Nästa hopp](network-watcher-next-hop-overview.md)
* [Säkerhetsgruppvy](network-watcher-security-group-view-overview.md)
* [NSG flödet loggning](network-watcher-nsg-flow-logging-overview.md)
* [Felsökning av virtuella nätverksgateway](network-watcher-troubleshoot-overview.md)

När en Nätverksbevakaren-instans kan aktivera du paketinsamling virtuella datorer. Mer information finns i avsnittet [skapar en avisering utlösta paketinsamling](network-watcher-alert-triggered-packet-capture.md)

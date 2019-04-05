---
title: Skapa en instans för Azure Network Watcher | Microsoft Docs
description: Lär dig hur du aktiverar Network Watcher i en Azure-region.
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
ms.openlocfilehash: 601a3f273a8da9100d24dfdbd13bd598b0e48884
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051571"
---
# <a name="create-an-azure-network-watcher-instance"></a>Skapa en Azure Network Watcher-instans

Network Watcher är en regional tjänst som hjälper dig att övervaka och diagnostisera villkor på nätverksnivå, till och från Azure. Scenariot på övervakning kan du diagnostisera problem på vyn för slutpunkt till slutpunkt. Nätverksdiagnostik- och visualiseringsverktyg för Network Watcher hjälper dig att förstå, diagnostisera och få information om ditt nätverk i Azure. Network Watcher aktiveras genom att skapa en Network Watcher-resurs. Den här resursen kan du använda Network Watcher-funktioner.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Network Watcher aktiveras automatiskt
När du skapar eller uppdaterar ett virtuellt nätverk i din prenumeration aktiveras Network Watcher automatiskt i din virtuella nätverksregion. Dina resurser påverkas inte av den automatiska aktiveringen av Network Watcher, och inga kostnader tillkommer.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Avstår från automatisk aktivering av Network Watcher
Om du vill välja bort automatisk aktivering av Network Watcher kan göra du det genom att köra följande kommandon:

> [!WARNING]
> Väljer ut av automatisk aktivering av Network Watcher är en permanent förändring. När du inte välja bort du kan anmäla sig utan [att kontakta supporten](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Skapa en Network Watcher i portalen

Gå till **alla tjänster** > **nätverk** > **Network Watcher**. Du kan välja de prenumerationer som du vill aktivera Network Watcher för. Den här åtgärden skapar en Network Watcher i varje region som är tillgänglig.

![Skapa en network watcher](./media/network-watcher-create/figure1.png)

När du aktiverar Network Watcher med hjälp av portalen, namnet på instansen av Network Watcher ställs automatiskt in *NetworkWatcher_region_name* där *region_name* motsvarar den Azure-regionen där instansen är aktiverat. Till exempel en Network Watcher aktiverat i regionen USA, västra centrala heter *NetworkWatcher_westcentralus*.

Network Watcher-instans skapas automatiskt i en resursgrupp med namnet *NetworkWatcherRG*. Resursgruppen skapas om den inte redan finns.

Om du vill anpassa namnet på en Network Watcher-instans och resursgruppen har placerats i kan du använda Powershell, Azure CLI, REST API eller ARMClient metoder som beskrivs i avsnitten som följer. I varje alternativ, måste resursgruppens namn finnas innan du skapar en Network Watcher i den.  

## <a name="create-a-network-watcher-with-powershell"></a>Skapa en Network Watcher med PowerShell

Om du vill skapa en instans av Network Watcher, kör du följande exempel:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Skapa en Network Watcher med Azure CLI

Om du vill skapa en instans av Network Watcher, kör du följande exempel:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Skapa en Network Watcher med REST-API

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

Nu när du har en instans av Network Watcher lär du dig mer om tillgängliga funktioner:

* [Topologi](network-watcher-topology-overview.md)
* [Paketinsamling](network-watcher-packet-capture-overview.md)
* [Kontrollera IP-flöde](network-watcher-ip-flow-verify-overview.md)
* [Nästa hopp](network-watcher-next-hop-overview.md)
* [Säkerhetsgruppvy](network-watcher-security-group-view-overview.md)
* [NSG-flödesloggar](network-watcher-nsg-flow-logging-overview.md)
* [Felsökning av virtuell nätverksgateway](network-watcher-troubleshoot-overview.md)

När en instans av Network Watcher är kan aktivera du infångade paket på virtuella datorer i. Att lära dig hur genom att läsa [skapar en avisering utlösta paketfångsten](network-watcher-alert-triggered-packet-capture.md)

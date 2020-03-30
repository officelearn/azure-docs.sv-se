---
title: Skapa en Azure Network Watcher-instans | Microsoft-dokument
description: Lär dig hur du aktiverar Network Watcher i en Azure-region.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 77812a3765a027152c957f6dbb7c9b3811a2278f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191182"
---
# <a name="create-an-azure-network-watcher-instance"></a>Skapa en Azure Network Watcher-instans

Network Watcher är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på nätverksscenarionivå i, till och från Azure. Med övervakning på scenarionivå kan du diagnostisera problem i slutet till slutet av vyn på nätverksnivå. Nätverksdiagnostik och visualiseringsverktyg som är tillgängliga med Network Watcher hjälper dig att förstå, diagnostisera och få insikter i nätverket i Azure. Network Watcher aktiveras genom att skapa en Network Watcher-resurs. Med den här resursen kan du använda Network Watcher-funktioner.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Network Watcher aktiveras automatiskt
När du skapar eller uppdaterar ett virtuellt nätverk i din prenumeration aktiveras Network Watcher automatiskt i din virtuella nätverksregion. Dina resurser påverkas inte av den automatiska aktiveringen av Network Watcher, och inga kostnader tillkommer.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Opt-out av Network Watcher automatisk aktivering
Om du vill välja bort automatisk aktivering av Network Watcher kan du göra det genom att köra följande kommandon:

> [!WARNING]
> Att välja bort automatisk aktivering av Network Watcher är en permanent ändring. När du har avanmält dig kan du inte anmäla dig utan [att kontakta supporten](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Skapa en nätverksbevakare i portalen

Navigera till **All Services** > **Networking** > **Network Watcher**. Du kan välja alla prenumerationer som du vill aktivera Network Watcher för. Den här åtgärden skapar en nätverksbevakare i alla regioner som är tillgängliga.

![skapa en nätverksbevakare](./media/network-watcher-create/figure1.png)

När du aktiverar Network Watcher med hjälp av portalen ställs namnet på Network Watcher-instansen automatiskt in *på NetworkWatcher_region_name* där *region_name* motsvarar azure-regionen där instansen är aktiverad. En Nätverksbevakare som är aktiverad i regionen Västra centrala USA heter till exempel *NetworkWatcher_westcentralus*.

Network Watcher-instansen skapas automatiskt i en resursgrupp med namnet *NetworkWatcherRG*. Resursgruppen skapas om den inte redan finns.

Om du vill anpassa namnet på en Network Watcher-instans och resursgruppen som den placeras i kan du använda Powershell, Azure CLI, REST API eller ARMClient-metoderna som beskrivs i de avsnitt som följer. I varje alternativ måste resursgruppen finnas innan du skapar en nätverksbevakare i den.  

## <a name="create-a-network-watcher-with-powershell"></a>Skapa en nätverksbevakare med PowerShell

Om du vill skapa en instans av Network Watcher kör du följande exempel:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Skapa en nätverksbevakare med Azure CLI

Om du vill skapa en instans av Network Watcher kör du följande exempel:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Skapa en nätverksbevakare med REST API

ARMclient används för att anropa REST API med PowerShell. ARMClient finns på chocolatey på [ARMClient på Chocolatey](https://chocolatey.org/packages/ARMClient)

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

## <a name="delete-a-network-watcher-in-the-portal"></a>Ta bort en nätverksbevakare i portalen

Navigera till **All Services** > **Networking** > **Network Watcher**.

Välj översiktsfliken om du inte redan är där. Använd listrutan för att välja den prenumeration som du vill inaktivera nätverksbevakaren i.
Expandera listan över regioner för din valda prenumeration genom att klicka på pilen. För en viss, använd 3 punkter till höger för att komma åt snabbmenyn.
Klicka på "Inaktivera nätverksbevakare" för att börja inaktivera. Du kommer att bli ombedd att bekräfta detta steg. Klicka på Ja för att fortsätta.
På portalen måste du göra detta individuellt för varje region i varje prenumeration.


## <a name="delete-a-network-watcher-with-powershell"></a>Ta bort en nätverksbevakare med PowerShell

Om du vill ta bort en instans av Network Watcher kör du följande exempel:

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>Nästa steg

Nu när du har en instans av Network Watcher kan du läsa mer om de funktioner som är tillgängliga:

* [Topologi](network-watcher-topology-overview.md)
* [Paketinsamling](network-watcher-packet-capture-overview.md)
* [Verifiera IP-flöde](network-watcher-ip-flow-verify-overview.md)
* [Nästa hopp](network-watcher-next-hop-overview.md)
* [Säkerhetsgruppvy](network-watcher-security-group-view-overview.md)
* [NSG-flödesloggning](network-watcher-nsg-flow-logging-overview.md)
* [Felsökning av virtuell nätverksgateway](network-watcher-troubleshoot-overview.md)

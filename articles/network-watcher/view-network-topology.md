---
title: Visa Azure virtuella nätverkets topologi | Microsoft Docs
description: Lär dig mer om att visa resurser i ett virtuellt nätverk och relationer mellan resurserna.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: jdial
ms.openlocfilehash: 6ef165ddc481bf84c6189635e36b97eb9518261e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077894"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Visa topologin för Azure-nätverk

Lär dig hur du visar resurser i ett virtuellt nätverk i Microsoft Azure och relationer mellan resurser i den här artikeln. Ett virtuellt nätverk innehåller till exempel undernät. Undernät innehålla resurser, till exempel Azure virtuella datorer (VM). Virtuella datorer har en eller flera nätverksgränssnitt. Varje undernät kan ha en nätverkssäkerhetsgrupp och en routingtabell som är kopplade till den. Topologi möjligheterna för Azure Nätverksbevakaren kan du visa alla resurser i ett virtuellt nätverk de resurser som är kopplade till resurser i ett virtuellt nätverk och relationer mellan resurserna.

Du kan använda den [Azure-portalen](#azure-portal), [Azure CLI](#azure-cli), eller [PowerShell](#powershell) att visa en topologi.

## <a name = "azure-portal"></a>Visa topologi - Azure-portalen

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som har nödvändiga [behörigheter](required-rbac-permissions.md).
2. Överst vänstra hörnet av portalen, Välj **alla tjänster**.
3. I den **alla tjänster** filtrera rutan, ange *Nätverksbevakaren*. När **Nätverksbevakaren** visas i sökresultaten väljer den.
4. Välj **topologi**. Generera en topologi kräver en nätverksbevakaren i samma region som det virtuella nätverket som du vill generera topologin för finns i. Om du inte har en nätverksbevakaren aktiverad i den region som det virtuella nätverket som du vill generera en topologi för skapas Övervakare nätverk automatiskt för dig i alla regioner. Övervakare nätverk skapas i en resursgrupp med namnet **NetworkWatcherRG**.
5. Välj en prenumeration, resursgrupp i ett virtuellt nätverk som du vill visa topologin för, och välj sedan det virtuella nätverket. I följande bild visas en topologi för ett virtuellt nätverk med namnet *MyVnet*, i resursgrupp med namnet *MyResourceGroup*:

    ![Visa topologi](./media/view-network-topology/view-topology.png)

    Som du ser i bilden innehåller tre undernät i det virtuella nätverket. Ett undernät har en virtuell dator som distribuerats i. Den virtuella datorn har ett nätverksgränssnitt som är kopplade till den och en offentlig IP-adress som är kopplad till den. Två undernät har en routingtabell som är kopplade till dem. Varje routningstabellen innehåller två vägar. Ett undernät har en nätverkssäkerhetsgrupp som är kopplade till den. Topologiinformation visas endast för resurser som är: – inom samma resursgrupp och region som den *myVnet* virtuellt nätverk. Till exempel en nätverkssäkerhetsgrupp som finns i en resursgrupp än *MyResourceGroup*, inte visas, även om nätverkssäkerhetsgruppen som är kopplad till ett undernät i den *MyVnet* virtuellt nätverk .
        -I, eller så är kopplade till resurser i den *myVnet* virtuellt nätverk. Till exempel en nätverkssäkerhetsgrupp som är inte kopplad till ett undernät eller nätverk gränssnitt i den *myVnet* virtuellt nätverk är inte visas, även om nätverkssäkerhetsgruppen i den *MyResourceGroup* resursgrupp.

    Topologi som visas i bilden är för det virtuella nätverket som skapas när du har distribuerat den **dirigera trafik via ett nätverk virtuell installation skriptexempel**, som du kan distribuera med den [Azure CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), eller [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Välj **hämta topologi** att hämta bilden som en redigerbar fil i svg-format.

Resurser som visas i diagrammet är en delmängd av nätverkskomponenter i det virtuella nätverket. Till exempel när en nätverkssäkerhetsgrupp visas visas säkerhetsregler i den inte i diagrammet. Även om inte differentierade i diagrammet, raderna som representerar ett av två relationer: *inneslutning* eller *associerade*. Om du vill se en fullständig lista över resurser i det virtuella nätverket och typen av relation mellan resurser generera topologi med [PowerShell](#powershell) eller [Azure CLI](#azure-cli).

## <a name = "azure-cli"></a>Visa topologi - Azure CLI

Du kan köra kommandon i de steg som följer:
- Azure-molnet Shell, genom att välja **prova** överst direkt av ett kommando. Azure Cloud-gränssnittet är ett kostnadsfritt interaktiva gränssnitt som har gemensamma Azure verktyg förinstallerat och konfigureras för användning med ditt konto.
- Genom att köra CLI från datorn. Om du kör CLI från datorn, steg i den här artikeln kräver Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt, måste du också köra `az login` att skapa en anslutning med Azure.

Det konto som du använder måste ha nödvändiga [behörigheter](required-rbac-permissions.md).

1. Om du redan har en nätverksbevakaren i samma region som det virtuella nätverket som du vill skapa en topologi för vidare till steg 3. Skapa en resursgrupp att innehålla en nätverksbevakaren med [az gruppen skapa](/cli/azure/group#az_group_create). I följande exempel skapas resursgruppen i den *eastus* region:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Skapa en nätverksbevakaren med [az nätverksbevakaren konfigurera](/cli/azure/network/watcher#az-network-watcher-configure). I följande exempel skapas en nätverksbevakaren i den *eastus* region:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Visa topologi med [az nätverkstopologi watcher visa-](/cli/azure/network/watcher#az-network-watcher-show-topology). I följande exempel visar topologin för en resursgrupp med namnet *MyResourceGroup*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Topologiinformation returneras bara för resurser som tillhör samma resursgrupp som det *MyResourceGroup* resursgrupp och samma region som nätverksbevakaren. Till exempel en nätverkssäkerhetsgrupp som finns i en resursgrupp än *MyResourceGroup*, inte visas, även om nätverkssäkerhetsgruppen som är kopplad till ett undernät i den *MyVnet* virtuellt nätverk .

  Lär dig mer om den [relationer](#relationhips) och [egenskaper](#properties) i returnerade utdata. Om du inte har ett befintligt virtuellt nätverk att visa en topologi för, kan du skapa en med hjälp av den [dirigera trafik via en virtuell nätverksenhet](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) skriptexempel. Om du vill visa ett diagram över topologi och ladda ned den i en redigerbar fil, använder den [portal](#azure-portal).

## <a name = "powershell"></a>Visa topologi - PowerShell

Du kan köra kommandon i de steg som följer:
- Azure-molnet Shell, genom att välja **prova** överst direkt av ett kommando. Azure Cloud-gränssnittet är ett kostnadsfritt interaktiva gränssnitt som har gemensamma Azure verktyg förinstallerat och konfigureras för användning med ditt konto.
- Genom att köra PowerShell från datorn. Om du kör PowerShell från datorn stegen i den här artikeln kräver version 5.7.0 eller senare av modulen AzureRm. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

Det konto som du använder måste ha nödvändiga [behörigheter](required-rbac-permissions.md).

1. Om du redan har en nätverksbevakaren i samma region som det virtuella nätverket som du vill skapa en topologi för vidare till steg 3. Skapa en resursgrupp att innehålla en nätverksbevakaren med [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). I följande exempel skapas resursgruppen i den *eastus* region:

    ```azurepowershell-interactive
    New-AzureRmResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Skapa en nätverksbevakaren med [ny AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher). I följande exempel skapas en nätverksbevakaren i eastus region:

    ```azurepowershell-interactive
    New-AzureRmNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Hämta en Nätverksbevakaren-instans med [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher). I följande exempel hämtas en nätverksbevakaren i östra USA:

    ```azurepowershell-interactive
    $nw = Get-AzurermResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzureRmNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Hämta en topologi med [Get-AzureRmNetworkWatcherTopology](/powershell/module/azurerm.network/get-azurermnetworkwatchertopology). I följande exempel hämtar en topologi för ett virtuellt nätverk i resursgruppen med namnet *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzureRmNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Topologiinformation returneras bara för resurser som tillhör samma resursgrupp som det *MyResourceGroup* resursgrupp och samma region som nätverksbevakaren. Till exempel en nätverkssäkerhetsgrupp som finns i en resursgrupp än *MyResourceGroup*, inte visas, även om nätverkssäkerhetsgruppen som är kopplad till ett undernät i den *MyVnet* virtuellt nätverk .

  Lär dig mer om den [relationer](#relationhips) och [egenskaper](#properties) i returnerade utdata. Om du inte har ett befintligt virtuellt nätverk att visa en topologi för, kan du skapa en med hjälp av den [dirigera trafik via en virtuell nätverksenhet](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) skriptexempel. Om du vill visa ett diagram över topologi och ladda ned den i en redigerbar fil, använder den [portal](#azure-portal).

## <a name="relationships"></a>Relationer

Alla resurser som returneras i en topologi ha något av följande typer av relationen till en annan resurs:

| Relationstyp | Exempel                                                                                                |
| ---               | ---                                                                                                    |
| Inneslutning       | Ett virtuellt nätverk innehåller ett undernät. Ett undernät innehåller ett nätverksgränssnitt.                            |
| Associerade        | Ett nätverksgränssnitt är kopplat till en virtuell dator. En offentlig IP-adress är kopplad till ett nätverksgränssnitt. |

## <a name="properties"></a>Egenskaper

Alla resurser som returneras i en topologi har följande egenskaper:

- **Namnet**: namnet på resursen
- **ID**: URI: N för resursen.
- **Plats**: Azure-region som resursen finns i.
- **Kopplingarna**: en lista över kopplingar till det refererade objektet. Varje association har följande egenskaper:
    - **AssociationType**: refererar till relationen mellan det underordnade objektet och överordnat. Giltiga värden är *innehåller* eller *associerade*.
    - **Namnet**: namnet på den refererade resursen.
    - **ResourceId**:-URI: N på den resurs som refereras i kopplingen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [diagnostisera trafik filter nätverksproblem till eller från en virtuell dator](diagnose-vm-network-traffic-filtering-problem.md) med hjälp av nätverket Watcher IP-flöde verifieringsfunktioner
- Lär dig hur du [diagnostisera trafik routning nätverksproblem från en virtuell dator](diagnose-vm-network-routing-problem.md) med nätverket Watcher nästa hopp-funktionen
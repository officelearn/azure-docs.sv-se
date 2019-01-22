---
title: Visa topologi för Azure-nätverk | Microsoft Docs
description: Lär dig mer om att visa resurser i ett virtuellt nätverk och relationerna mellan resurserna.
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
ms.openlocfilehash: 501659a93306342c7a212d135b4fdd89be096451
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428179"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Visa topologi för Azure-nätverk

I den här artikeln lär du dig visa resurser i ett virtuellt Microsoft Azure-nätverk och relationerna mellan resurserna. Till exempel innehåller ett virtuellt nätverk undernät. Undernät innehålla resurser, till exempel Azure-datorer (VM). Virtuella datorer har en eller flera nätverksgränssnitt. Varje undernät kan ha en nätverkssäkerhetsgrupp och en routningstabell som är associerade med den. Topologi-funktionen i Azure Network Watcher kan du visa alla resurser i ett virtuellt nätverk, de resurser som är kopplade till resurser i ett virtuellt nätverk och relationerna mellan resurserna.

Du kan använda den [Azure-portalen](#azure-portal), [Azure CLI](#azure-cli), eller [PowerShell](#powershell) att visa en topologi.

## <a name = "azure-portal"></a>Visa topologi - Azure-portalen

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som har nödvändiga [behörigheter](required-rbac-permissions.md).
2. På överst vänstra hörnet i portalen, väljer **alla tjänster**.
3. I den **alla tjänster** filtrera rutan, ange *Network Watcher*. Välj **Network Watcher** i sökresultatet.
4. Välj **topologi**. Generera en topologi kräver en network watcher i samma region som det virtuella nätverk som du vill generera topologi för finns i. Om du inte har en network watcher som aktiverats i den region som det virtuella nätverket som du vill generera en topologi för finns i, skapas automatiskt nätverksbevakare åt dig i alla regioner. Nätverksbevakare skapas i en resursgrupp med namnet **NetworkWatcherRG**.
5. Välj en prenumeration, resursgrupp för ett virtuellt nätverk som du vill visa topologi för, och välj sedan det virtuella nätverket. I följande bild visas en topologi för ett virtuellt nätverk med namnet *MyVnet*, i resursgruppen med namnet *MyResourceGroup*:

    ![Visa topologi](./media/view-network-topology/view-topology.png)

    Som du ser i bilden innehåller tre undernät i det virtuella nätverket. Ett undernät har en virtuell dator som distribuerats i. Den virtuella datorn har ett nätverksgränssnitt som är kopplade till den och en offentlig IP-adress som är associerade med den. De två undernäten har en associerad routningstabell. Varje routningstabellen innehåller två vägar. Ett undernät har en nätverkssäkerhetsgrupp som är associerade med den. Topologiinformation visas bara för resurser som är:
    
    - Inom samma resursgrupp och region som den *myVnet* virtuellt nätverk. Till exempel en nätverkssäkerhetsgrupp som finns i en resursgrupp än *MyResourceGroup*, inte visas, även om nätverkssäkerhetsgruppen är kopplad till ett undernät i den *MyVnet* virtuellt nätverk .
    - I, eller så är kopplade till resurser i, den *myVnet* virtuellt nätverk. Till exempel en nätverkssäkerhetsgrupp som inte är kopplad till ett undernät eller ett gränssnitt i den *myVnet* virtuellt nätverk är inte visas, även om nätverkssäkerhetsgruppen är i den *MyResourceGroup* resursgrupp.

  Topologi som visas i bilden är för det virtuella nätverket skapas när du har distribuerat den **dirigera trafik via ett nätverk virtuell nätverksinstallation – skriptexempel**, vilka du kan distribuera med hjälp av den [Azure CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), eller [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Välj **ladda ned topologi** ladda ned bilden som en redigerbara fil i svg-format.

Resurserna som visas i diagrammet är en delmängd av nätverkskomponenter i det virtuella nätverket. Till exempel när en nätverkssäkerhetsgrupp visas visas säkerhetsregler i den inte i diagrammet. Även om inte differentierade i diagrammet, utgör en av två relationer: *Inneslutning* eller *associerade*. Om du vill se en fullständig lista över resurser i det virtuella nätverket och typen av relation mellan resurser, generera topologi med [PowerShell](#powershell) eller [Azure CLI](#azure-cli).

## <a name = "azure-cli"></a>Visa topologi – Azure CLI

Du kan köra kommandon i stegen nedan:
- I Azure Cloud Shell, genom att välja **prova** längst upp höger på alla kommandon. Azure Cloud Shell är ett interaktivt gränssnitt som har vanliga Azure-verktyg förinstallerat och har konfigurerats för att använda med ditt konto.
- Genom att köra CLI från datorn. Om du kör CLI från datorn i den här artikeln kräver Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också behöva köra `az login` att skapa en anslutning till Azure.

Det konto som du använder måste ha nödvändiga [behörigheter](required-rbac-permissions.md).

1. Om du redan har en nätverksbevakare i samma region som det virtuella nätverket som du vill skapa en topologi för vidare till steg 3. Skapa en resursgrupp som innehåller en nätverksbevakare med [az gruppen skapa](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp i den *eastus* region:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Skapa en network watcher med [az network watcher konfigurera](/cli/azure/network/watcher#az-network-watcher-configure). I följande exempel skapas en network watcher i den *eastus* region:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Visa topologi med [az network watcher show-topology](/cli/azure/network/watcher#az-network-watcher-show-topology). Följande exempel visar topologin för en resursgrupp med namnet *MyResourceGroup*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Topologiinformation returneras bara för resurser som ligger inom samma resursgrupp som den *MyResourceGroup* resursgruppens namn och samma region som nätverksbevakaren. Till exempel en nätverkssäkerhetsgrupp som finns i en resursgrupp än *MyResourceGroup*, inte visas, även om nätverkssäkerhetsgruppen är kopplad till ett undernät i den *MyVnet* virtuellt nätverk .

  Läs mer om den [relationer](#relationhips) och [egenskaper](#properties) i returnerade utdata. Om du inte har ett befintligt virtuellt nätverk för att visa en topologi för kan du skapa en med den [dirigera trafik via en virtuell nätverksinstallation](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) skriptexempel. Om du vill visa ett diagram över topologi och ladda ned den i en fil som redigeras, Använd den [portal](#azure-portal).

## <a name = "powershell"></a>Visa topologi – PowerShell

Du kan köra kommandon i stegen nedan:
- I Azure Cloud Shell, genom att välja **prova** längst upp höger på alla kommandon. Azure Cloud Shell är ett interaktivt gränssnitt som har vanliga Azure-verktyg förinstallerat och har konfigurerats för att använda med ditt konto.
- Genom att köra PowerShell från datorn. Om du kör PowerShell från datorn i den här artikeln kräver version 5.7.0-installationsprogram eller senare av AzureRm-modulen. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

Det konto som du använder måste ha nödvändiga [behörigheter](required-rbac-permissions.md).

1. Om du redan har en nätverksbevakare i samma region som det virtuella nätverket som du vill skapa en topologi för vidare till steg 3. Skapa en resursgrupp som innehåller en nätverksbevakare med [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). I följande exempel skapas en resursgrupp i den *eastus* region:

    ```azurepowershell-interactive
    New-AzureRmResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Skapa en network watcher med [New AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher). I följande exempel skapas en network watcher i regionen eastus:

    ```azurepowershell-interactive
    New-AzureRmNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Hämta en Network Watcher-instans med [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher). I följande exempel hämtas en network watcher i regionen östra USA:

    ```azurepowershell-interactive
    $nw = Get-AzurermResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzureRmNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Hämta en topologi med [Get-AzureRmNetworkWatcherTopology](/powershell/module/azurerm.network/get-azurermnetworkwatchertopology). I följande exempel hämtas en topologi för ett virtuellt nätverk i resursgruppen med namnet *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzureRmNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Topologiinformation returneras bara för resurser som ligger inom samma resursgrupp som den *MyResourceGroup* resursgruppens namn och samma region som nätverksbevakaren. Till exempel en nätverkssäkerhetsgrupp som finns i en resursgrupp än *MyResourceGroup*, inte visas, även om nätverkssäkerhetsgruppen är kopplad till ett undernät i den *MyVnet* virtuellt nätverk .

  Läs mer om den [relationer](#relationhips) och [egenskaper](#properties) i returnerade utdata. Om du inte har ett befintligt virtuellt nätverk för att visa en topologi för kan du skapa en med den [dirigera trafik via en virtuell nätverksinstallation](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) skriptexempel. Om du vill visa ett diagram över topologi och ladda ned den i en fil som redigeras, Använd den [portal](#azure-portal).

## <a name="relationships"></a>Relationer

Alla resurser som returneras i en topologi ha någon av följande typer av relation till en annan resurs:

| Relationstyp | Exempel                                                                                                |
| ---               | ---                                                                                                    |
| Inneslutning       | Ett virtuellt nätverk innehåller ett undernät. Ett undernät innehåller ett nätverksgränssnitt.                            |
| Associerad        | Ett nätverksgränssnitt är associerad med en virtuell dator. En offentlig IP-adress är kopplad till ett nätverksgränssnitt. |

## <a name="properties"></a>Egenskaper

Alla resurser som returneras i en topologi har följande egenskaper:

- **Namn**: Namnet på resursen
- **ID**: URI för resursen.
- **Plats**: Azure-regionen som resursen finns i.
- **Associationer**: En lista över kopplingar till det refererade objektet. Varje association har följande egenskaper:
    - **AssociationType**: Refererar till förhållandet mellan ett underordnat objekt och överordnat. Giltiga värden är *innehåller* eller *associerade*.
    - **Namn**: Namnet på den refererade resursen.
    - **ResourceId**:-URI: N av resursen som refereras i kopplingen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [diagnostisera trafik filter nätverksproblem till eller från en virtuell dator](diagnose-vm-network-traffic-filtering-problem.md) med Network Watcher IP-flöde verifiera kapaciteten
- Lär dig hur du [diagnostisera ett trafik problem med nätverksroutning från en virtuell dator](diagnose-vm-network-routing-problem.md) med Network Watcher nästa hopp-funktionen

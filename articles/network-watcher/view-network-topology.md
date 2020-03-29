---
title: Visa Azure-topologi för virtuella nätverk | Microsoft-dokument
description: Lär dig hur du visar resurserna i ett virtuellt nätverk och relationerna mellan resurserna.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: damendo
ms.openlocfilehash: 675919db55932d3ccc04fd5397f6f673832b4900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840578"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Visa topologin för ett virtuellt Azure-nätverk

I den här artikeln får du lära dig hur du visar resurser i ett virtuellt Microsoft Azure-nätverk och relationerna mellan resurserna. Ett virtuellt nätverk innehåller till exempel undernät. Undernät innehåller resurser, till exempel Virtuella Azure-datorer (VM). Virtuella datorer har ett eller flera nätverksgränssnitt. Varje undernät kan ha en nätverkssäkerhetsgrupp och en vägtabell kopplad till sig. Med topologifunktionen i Azure Network Watcher kan du visa alla resurser i ett virtuellt nätverk, de resurser som är associerade till resurser i ett virtuellt nätverk och relationerna mellan resurserna.

Du kan använda [Azure-portalen,](#azure-portal) [Azure CLI](#azure-cli)eller [PowerShell](#powershell) för att visa en topologi.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Visa topologi – Azure-portal

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som har de [behörigheter](required-rbac-permissions.md)som krävs .
2. Högst upp, till vänster i portalen väljer du **Alla tjänster**.
3. I rutan Alla **tjänsters** filter anger du *Network Watcher*. Välj **Network Watcher** i sökresultatet.
4. Välj **Topologi**. Generera en topologi kräver en nätverk watcher i samma region som det virtuella nätverket som du vill generera topologin för finns i. Om du inte har aktiverat en nätverksbevakare i den region som det virtuella nätverk som du vill generera en topologi för finns i, skapas nätverksbevakare automatiskt åt dig i alla regioner. Nätverksbevakare skapas i en resursgrupp med namnet **NetworkWatcherRG**.
5. Välj en prenumeration, resursgruppen för ett virtuellt nätverk som du vill visa topologin för och välj sedan det virtuella nätverket. I följande bild visas en topologi för ett virtuellt nätverk med namnet *MyVnet*i resursgruppen *MyResourceGroup:*

    ![Visa topologi](./media/view-network-topology/view-topology.png)

    Som du kan se i föregående bild innehåller det virtuella nätverket tre undernät. Ett undernät har en virtuell dator som distribueras i det. Den virtuella datorn har ett nätverksgränssnitt kopplat till sig och en offentlig IP-adress som är kopplad till den. De andra två undernäten har en vägtabell kopplad till dem. Varje flödestabell innehåller två vägar. Ett undernät har en nätverkssäkerhetsgrupp kopplad till sig. Topologiinformation visas endast för resurser som är:
    
    - Inom samma resursgrupp och region som *myVnet* virtuella nätverk. En nätverkssäkerhetsgrupp som finns i en annan resursgrupp än *MyResourceGroup*visas till exempel inte, även om nätverkssäkerhetsgruppen är associerad med ett undernät i det virtuella *nätverket MyVnet.*
    - Inom, eller kopplat till resurser inom, *myVnet* virtuella nätverk. En nätverkssäkerhetsgrupp som inte är associerad med ett undernät eller ett nätverksgränssnitt i det virtuella *nätverket myVnet* visas till exempel inte, även om nätverkssäkerhetsgruppen finns i resursgruppen *MyResourceGroup.*

   Topologin som visas i bilden är för det virtuella nätverk som skapats efter distribution av **dirigera trafik genom ett nätverksskriptskript för virtuella enheter**, som du kan distribuera med Azure [CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)eller [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Välj **Hämta topologi för** att hämta bilden som en redigerbar fil, i svg-format.

De resurser som visas i diagrammet är en delmängd av nätverkskomponenterna i det virtuella nätverket. När en nätverkssäkerhetsgrupp till exempel visas visas inte säkerhetsreglerna i den i diagrammet. Även om linjerna inte är differentierade i diagrammet representerar de en av två relationer: *Inneslutning* eller *associerad*. Om du vill se den fullständiga listan över resurser i det virtuella nätverket och typen av relation mellan resurserna genererar du topologin med [PowerShell](#powershell) eller [Azure CLI](#azure-cli).

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Visa topologi - Azure CLI

Du kan köra kommandona i följande steg:
- I Azure Cloud Shell genom att välja **Prova det** längst upp till höger i alla kommandon. Azure Cloud Shell är ett kostnadsfritt interaktivt skal som har vanliga Azure-verktyg som är förinstallerade och konfigurerade för att användas med ditt konto.
- Genom att köra CLI från datorn. Om du kör CLI från datorn kräver stegen i den här artikeln Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du `az login` också köra för att skapa en anslutning med Azure.

Kontot som du använder måste ha de [behörigheter](required-rbac-permissions.md)som krävs .

1. Om du redan har en nätverksbevakare i samma region som det virtuella nätverk som du vill skapa en topologi för går du vidare till steg 3. Skapa en resursgrupp som innehåller en nätverksbevakare med [az-gruppge](/cli/azure/group). I följande exempel skapas resursgruppen i *regionen Eastus:*

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Skapa en nätverk watcher med [az nätverk watcher konfigurera](/cli/azure/network/watcher#az-network-watcher-configure). I följande exempel skapas en nätverksbevakare i *regionen Eastus:*

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Visa topologin med [az network watcher show-topologi](/cli/azure/network/watcher#az-network-watcher-show-topology). I följande exempel beskrivs topologin för en resursgrupp med namnet *MyResourceGroup:*

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Topologiinformation returneras endast för resurser som finns inom samma resursgrupp som resursgruppen *MyResourceGroup* och samma region som nätverksbevakaren. En nätverkssäkerhetsgrupp som finns i en annan resursgrupp än *MyResourceGroup*visas till exempel inte, även om nätverkssäkerhetsgruppen är associerad med ett undernät i det virtuella *nätverket MyVnet.*

   Läs mer om relationer och [egenskaper](#properties) i den returnerade utdata. Om du inte har ett befintligt virtuellt nätverk att visa en topologi för kan du skapa en med hjälp av exempel på [dirigeratrafik via ett nätverksskript.](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) Om du vill visa ett diagram över topologin och hämta den i en redigerbar fil använder du [portalen](#azure-portal).

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Visa topologi - PowerShell

Du kan köra kommandona i följande steg:
- I Azure Cloud Shell genom att välja **Prova det** längst upp till höger i alla kommandon. Azure Cloud Shell är ett kostnadsfritt interaktivt skal som har vanliga Azure-verktyg som är förinstallerade och konfigurerade för att användas med ditt konto.
- Genom att köra PowerShell från datorn. Om du kör PowerShell från datorn kräver den `Az` här artikeln Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

Kontot som du använder måste ha de [behörigheter](required-rbac-permissions.md)som krävs .

1. Om du redan har en nätverksbevakare i samma region som det virtuella nätverk som du vill skapa en topologi för går du vidare till steg 3. Skapa en resursgrupp som innehåller en nätverksbevakare med [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). I följande exempel skapas resursgruppen i *regionen Eastus:*

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Skapa en nätverk watcher med [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). I följande exempel skapas en nätverksbevakare i regionen Eastus:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Hämta en Network Watcher-instans med [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). I följande exempel hämtas en nätverksbevakare i regionen östra USA:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Hämta en topologi med [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). I följande exempel hämtas en topologi för ett virtuellt nätverk i resursgruppen *MyResourceGroup:*

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Topologiinformation returneras endast för resurser som finns inom samma resursgrupp som resursgruppen *MyResourceGroup* och samma region som nätverksbevakaren. En nätverkssäkerhetsgrupp som finns i en annan resursgrupp än *MyResourceGroup*visas till exempel inte, även om nätverkssäkerhetsgruppen är associerad med ett undernät i det virtuella *nätverket MyVnet.*

   Läs mer om relationer och [egenskaper](#properties) i den returnerade utdata. Om du inte har ett befintligt virtuellt nätverk att visa en topologi för kan du skapa en med hjälp av exempel på [dirigeratrafik via ett nätverksskript.](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) Om du vill visa ett diagram över topologin och hämta den i en redigerbar fil använder du [portalen](#azure-portal).

## <a name="relationships"></a>Relationer

Alla resurser som returneras i en topologi har någon av följande typer av relation till en annan resurs:

| Typ av relation | Exempel                                                                                                |
| ---               | ---                                                                                                    |
| Behållare       | Ett virtuellt nätverk innehåller ett undernät. Ett undernät innehåller ett nätverksgränssnitt.                            |
| Associerade        | Ett nätverksgränssnitt är associerat med en virtuell dator. En offentlig IP-adress är kopplad till ett nätverksgränssnitt. |

## <a name="properties"></a>Egenskaper

Alla resurser som returneras i en topologi har följande egenskaper:

- **Namn**: Namnet på resursen
- **Id**: Resursens URI.
- **Plats**: Azure-regionen som resursen finns i.
- **Associationer**: En lista över associationer till det refererade objektet. Varje association har följande egenskaper:
    - **AssociationType**: Refererar till relationen mellan det underordnade objektet och det överordnade objektet. Giltiga värden är *Innehåller* eller *Associerade*.
    - **Namn**: Namnet på den refererade resursen.
    - **ResourceId**: - URI för resursen som refereras i associationen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du diagnostiserar ett problem med [nätverkstrafikfilter till eller från en virtuell dator](diagnose-vm-network-traffic-filtering-problem.md) med hjälp av Network Watchers IP-flödeskontroll
- Lär dig hur du [diagnostiserar ett problem med nätverkstrafikroutning från en virtuell dator](diagnose-vm-network-routing-problem.md) med hjälp av Network Watchers nästa hoppkapacitet

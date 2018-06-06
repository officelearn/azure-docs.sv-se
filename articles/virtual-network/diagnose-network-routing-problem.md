---
title: Felsöka en virtuell dator i Azure routning problemet | Microsoft Docs
description: Lär dig mer om att diagnosticera problem virtuella routning genom att visa effektiva vägar för en virtuell dator.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: jdial
ms.openlocfilehash: 07352a5d7c8b465440efab17c654979662a95f8e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34702394"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnostisera problem virtuella Routning

I den här artikeln får lära du att diagnostisera routning problem genom att visa de vägar som gäller för ett nätverksgränssnitt i en virtuell dator (VM). Azure skapar flera standardvägar för varje undernät för virtuellt nätverk. Du kan åsidosätta Azures standardvägar genom att definiera vägar i en routningstabell och sedan associera routningstabellen till ett undernät. Kombinationen av vägar som du skapar, Azures standardvägar och alla vägar spridas från ditt lokala nätverk via en Azure VPN-gateway (om det virtuella nätverket är anslutet till det lokala nätverket) via border gateway protocol (BGP), är det effektiva vägar för alla nätverkskort i ett undernät. Om du inte är bekant med virtuella nätverk, nätverksgränssnittet eller Routning begrepp finns [översikt över virtuella nätverk](virtual-networks-overview.md), [nätverksgränssnittet](virtual-network-network-interface.md), och [routning: översikt](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Scenario

Du försöker ansluta till en virtuell dator, men om anslutningen misslyckas. Du kan visa gällande vägar för ett nätverksgränssnitt med hjälp av Azure för att avgöra varför du inte kan ansluta till den virtuella datorn, [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), eller [Azure CLI](#diagnose-using-azure-cli).

De steg som följer förutsätter att du har en befintlig virtuell dator att visa effektiva vägar för. Om du inte har en befintlig virtuell dator, först distribuera en [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM utföra åtgärder i den här artikeln med. Exemplen i den här artikeln gäller för en virtuell dator med namnet *myVM* med ett nätverksgränssnitt med namnet *myVMVMNic*. Den virtuella datorn och ett nätverksgränssnitt finns i en resursgrupp med namnet *myResourceGroup*, och finns i den *östra USA* region. Ändra värdena i instruktioner för den virtuella datorn som du felsöker problemet för.

## <a name="diagnose-using-azure-portal"></a>Diagnostisera med hjälp av Azure portal

1. Logga in på Azure [portal](https://portal.azure.com) med ett Azure-konto som har den [behörighet](virtual-network-network-interface.md#permissions).
2. Ange namnet på en virtuell dator som är i tillståndet körs i sökrutan överst på Azure-portalen. När namnet på den virtuella datorn visas i sökresultaten väljer du den.
3. Välj **diagnostisera och lösa problem**, och sedan under **rekommenderade steg**väljer **effektiva vägar** i objektet 7, som visas i följande bild:

    ![Visa effektiva flöden](./media/diagnose-network-routing-problem/view-effective-routes.png)

4. Faktiska vägar för ett nätverksgränssnitt med namnet **myVMVMNic** visas i följande bild:

     ![Visa effektiva flöden](./media/diagnose-network-routing-problem/effective-routes.png)

    Om det finns flera nätverksgränssnitt som är kopplade till den virtuella datorn kan visa du effektiva vägar för alla nätverksgränssnitt genom att välja den. Eftersom varje nätverksgränssnitt kan vara i ett annat undernät, kan varje nätverksgränssnitt ha olika effektiva vägar.

    I exemplet som visas i föregående bild är listade vägar standardvägar som Azure skapar för varje undernät. I listan har minst dessa vägar, men kan ha ytterligare vägar, beroende på funktioner som du har aktiverat för ditt virtuella nätverk, till exempel den som är peerkopplat med ett annat virtuellt nätverk eller anslutna till ditt lokala nätverk via en Azure VPN-gateway. Mer information om varje vägar och andra vägar som kan visas för din nätverksgränssnittet finns [trafikroutning för virtuella nätverk](virtual-networks-udr-overview.md). Om listan har ett stort antal vägar, kan det vara lättare att välja **hämta**, för att hämta en .csv-fil med i listan över vägar.

Även om effektiva vägar har visas via den virtuella datorn i föregående steg, du kan också visa effektiva vägar via ett:
- **Enskilda nätverksgränssnittet**: Lär dig hur du [visa ett nätverksgränssnitt](virtual-network-network-interface.md#view-network-interface-settings).
- **Enskilda routningstabellen**: Lär dig hur du [visa en routingtabell](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnostisera med hjälp av PowerShell

Du kan köra kommandon som visas i den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud-gränssnittet är ett kostnadsfritt interaktiva gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn, måste den *AzureRM* PowerShell-modul, version 6.0.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt, måste du också köra `Login-AzureRmAccount` att logga in på Azure med ett konto som har den [behörighet](virtual-network-network-interface.md#permissions).

Hämta de effektiva vägarna för ett nätverksgränssnitt med [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). I följande exempel hämtas effektiva vägar för ett nätverksgränssnitt med namnet *myVMVMNic*, som i en resursgrupp med namnet *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

För att förstå informationen som returneras i utdata, se [routning: översikt](virtual-networks-udr-overview.md). Utdata returneras bara om den virtuella datorn är i körläge. Om det finns flera nätverksgränssnitt som är kopplade till den virtuella datorn kan granska du effektiva vägar för varje nätverksgränssnitt. Eftersom varje nätverksgränssnitt kan vara i ett annat undernät, kan varje nätverksgränssnitt ha olika effektiva vägar. Om det fortfarande uppstår kommunikationsproblem, se [ytterligare diagnos](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverksgränssnitt, men du vet namnet på den virtuella datorn nätverksgränssnittet är ansluten till, returnera ID-numren för alla nätverksgränssnitt som är kopplad till en virtuell dator med följande kommandon:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Utdata som liknar följande exempel visas:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

I tidigare utdata nätverksgränssnittets namn är *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnostisera med Azure CLI

Du kan köra kommandon som visas i den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt, måste du också köra `az login` och logga in på Azure med ett konto som har den [behörighet](virtual-network-network-interface.md#permissions).

Hämta de effektiva vägarna för ett nätverksgränssnitt med [az nätverk nic visa-gällande--routningstabellen](/cli/azure/network/nic#az-network-nic-show-effective-route-table). I följande exempel hämtas effektiva vägar för ett nätverksgränssnitt med namnet *myVMVMNic* som finns på en resursgrupp med namnet *myResourceGroup*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

För att förstå informationen som returneras i utdata, se [routning: översikt](virtual-networks-udr-overview.md). Utdata returneras bara om den virtuella datorn är i körläge. Om det finns flera nätverksgränssnitt som är kopplade till den virtuella datorn kan granska du effektiva vägar för varje nätverksgränssnitt. Eftersom varje nätverksgränssnitt kan vara i ett annat undernät, kan varje nätverksgränssnitt ha olika effektiva vägar. Om det fortfarande uppstår kommunikationsproblem, se [ytterligare diagnos](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverksgränssnitt, men du vet namnet på den virtuella datorn nätverksgränssnittet är ansluten till, returnera ID-numren för alla nätverksgränssnitt som är kopplad till en virtuell dator med följande kommandon:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Lösa problem

Lösa problem med Routning vanligtvis består av:

- Lägger till en anpassad väg om du vill åsidosätta en Azures standardvägar. Lär dig hur du [lägga till en anpassad väg](manage-route-table.md#create-a-route).
- Ändra eller ta bort en anpassad väg som kan orsaka routning till ett oönskat plats. Lär dig hur du [ändra](manage-route-table.md#change-a-route) eller [ta bort](manage-route-table.md#delete-a-route) en anpassad väg.
- Se till att routingtabell som innehåller alla anpassade vägar som du har definierat är kopplad till nätverksgränssnittet är i undernätet. Lär dig hur du [associera en routingtabell till ett undernät](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Säkerställer att enheter som till exempel Azure VPN-gateway eller nätverket virtuella enheter du har distribuerat kan använda. Använd den [VPN diagnostik](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) möjligheterna för Nätverksbevakaren att identifiera eventuella problem med en Azure VPN-gateway.

Om det fortfarande uppstår problem, se [överväganden](#considerations) och [ytterligare diagnos](#additional-dignosis).

## <a name="considerations"></a>Överväganden

Tänk på följande när du felsöker problem:

- Routning är baserat på längsta prefixmatchning (LPM) bland vägar som du har definierat border gateway protocol (BGP) och system vägar. Om det finns fler än en väg med samma LPM-matchning så väljs en väg baserat på dess ursprung i den ordning som anges i [routning: översikt](virtual-networks-udr-overview.md#how-azure-selects-a-route). Du kan bara se effektiva vägar som är en LPM-matchning, baserat på de tillgängliga vägarna med effektiva vägar. Se hur vägarna som utvärderas för ett nätverksgränssnitt gör det mycket enklare att felsöka specifika vägar driftstörningar kommunikation från den virtuella datorn.
- Om du har definierat anpassade vägar till en virtuell nätverksenhet (NVA) med *virtuell installation* som nexthop-typen, kontrollera att IP-vidarebefordring är aktiverat på en NVA som tar emot trafiken eller paket tappas. Lär dig mer om [aktiverar IP-vidarebefordring för ett nätverksgränssnitt](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Dessutom måste operativsystem eller program i en NVA också kunna vidarebefordra nätverkstrafik och vara konfigurerad för att göra det.
- Om du har skapat en väg till 0.0.0.0/0 dirigeras all utgående internet-trafik till nästa hopp du angett, till exempel en NVA eller VPN-gateway. Skapa en sådan väg kallas ofta Tvingad tunneltrafik. Fjärranslutningar med RDP eller SSH protokoll från internet till den virtuella datorn kanske inte fungerar med den här vägen, beroende på hur trafiken hanteras i nästa hopp. Tvingad tunneltrafik kan aktiveras:
    - När du använder plats-till-plats-VPN genom att skapa en väg med ett nexthop-typ av *VPN-Gateway*. Lär dig mer om [konfigurera Tvingad tunneltrafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Om en 0.0.0.0/0 () annonseras standardvägen över BGP via en gateway för virtuellt nätverk när du använder en plats-till-plats-VPN eller ExpressRoute-krets. Lär dig mer om att använda BGP med en [plats-till-plats VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- För peering trafik i virtuella nätverk ska fungera korrekt, en systemväg med ett nexthop-typ av *VNet-Peering* måste finnas för det peered virtuella nätverket prefix intervall. Om sådan en väg finns inte, och virtuella nätverk peering länken **ansluten**:
    - Vänta några sekunder och försök igen. Om det är en nyetablerade peering länk tar ibland längre tid att sprida väga på alla nätverksgränssnitt i ett undernät. Mer information om virtuellt nätverk peering finns [peering översikt över virtuella nätverk](virtual-network-peering-overview.md) och [hantera virtuella nätverk peering](virtual-network-manage-peering.md).
    - Regler för nätverkssäkerhetsgrupper driftstörningar kommunikation. Mer information finns i [diagnostisera virtuella trafik filter nätverksproblem](diagnose-network-traffic-filter-problem.md).
- Även om Azure tilldelas standardvägar varje Azure nätverksgränssnitt, om du har flera nätverksgränssnitt som är kopplade till den virtuella datorn, är det primära nätverksgränssnittet tilldelad en standardväg (0.0.0.0/0) eller gateway i den Virtuella datorns operativsystem. Lär dig hur du skapar en standardväg för sekundära nätverksgränssnitt som är kopplad till en [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) VM. Lär dig mer om [primära och sekundära nätverksgränssnitt](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Ytterligare diagnos

* Kör en snabb test för att avgöra nästa hopptyp för trafik till en plats med den [nästa hopp](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) möjligheterna för Azure Nätverksbevakaren. Nästa hopp anger nästa hopptyp är för trafik till en angiven plats.
* Om det finns inga vägar som orsakar en virtuell dators nätverkskommunikation misslyckas, kan det bero på grund av brandvägg körs på operativsystemet för den virtuella datorn
* Om du är [Tvingad tunneltrafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) trafik till en lokal enhet via en VPN-gateway eller NVA kan du inte att kunna ansluta till en virtuell dator från internet, beroende på hur du har konfigurerat routning för enheter. Bekräfta att routning som du har konfigurerat för enheten dirigerar trafik till antingen ett offentligt eller privat IP-adress för den virtuella datorn.
* Använd den [anslutning felsöka](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) möjligheterna för Nätverksbevakaren att fastställa routning, filtrering och i OS orsakerna till utgående kommunikationsproblem.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om alla aktiviteter, egenskaper och inställningar för en [dirigera tabell och vägar](manage-route-table.md).
- Lär dig mer om alla [nästa hopp-typer, systemvägar och hur Azure väljs en väg](virtual-networks-udr-overview.md).

---
title: Diagnostisera ett problem med nätverksroutning för virtuella Azure-datorer | Microsoft Docs
description: Lär dig att diagnostisera problem med nätverksroutning virtuell dator genom att visa de effektiva vägarna för en virtuell dator.
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
ms.openlocfilehash: 695d5f1507f766cf0a2ad96d7dcd25f45f98c20e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994725"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnostisera problem med nätverksroutning virtuell dator

I den här artikeln lär du dig att diagnostisera problem med nätverksroutning genom att visa de vägar som gäller för ett nätverksgränssnitt på en virtuell dator (VM). Azure skapar flera standardvägar för varje undernät för virtuellt nätverk. Du kan åsidosätta Azures standardvägar dirigerar genom att definiera vägar i en routningstabell och associerar sedan routningstabellen till ett undernät. Kombinationen av vägar som du skapar och Azures standardvägar dirigerar alla vägar som sprids från ditt lokala nätverk via en Azure VPN-gateway (om det virtuella nätverket är anslutet till ditt lokala nätverk) via border gateway protocol (BGP), är det gällande routningar för alla nätverksgränssnitt i ett undernät. Om du inte är bekant med virtuella nätverk, nätverksgränssnitt eller Routning begrepp, se [översikt över virtuella nätverk](virtual-networks-overview.md), [nätverksgränssnittet](virtual-network-network-interface.md), och [routningsöversikten](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Scenario

Du försöker ansluta till en virtuell dator, men om anslutningen misslyckas. För att avgöra varför du inte kan ansluta till den virtuella datorn, kan du visa de effektiva vägarna för ett nätverksgränssnitt med Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), eller [Azure CLI](#diagnose-using-azure-cli).

Stegen nedan förutsätter att du har en befintlig virtuell dator att visa de effektiva vägarna för. Om du inte har en befintlig virtuell dator, först distribuera en [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM du utför uppgifterna i den här artikeln med. Exemplen i den här artikeln gäller för en virtuell dator med namnet *myVM* med ett nätverksgränssnitt med namnet *myVMVMNic*. Det virtuella datorn och ett nätverksgränssnittet finns i en resursgrupp med namnet *myResourceGroup*, och finns i den *USA, östra* region. Ändra värden i steg efter behov för den virtuella datorn som du felsöker problemet för.

## <a name="diagnose-using-azure-portal"></a>Diagnostisera med hjälp av Azure-portalen

1. Logga in på Azure [portal](https://portal.azure.com) med en Azure-konto som har den [behörighet](virtual-network-network-interface.md#permissions).
2. Ange namnet på en virtuell dator som är i körläge, i sökrutan högst upp på Azure-portalen. När namnet på den virtuella datorn visas i sökresultatet väljer du den.
3. Välj **diagnostisera och lösa problem**, och sedan under **rekommenderade steg**väljer **gällande routningar** i objektet 7, enligt följande bild:

    ![Visa effektiva vägar](./media/diagnose-network-routing-problem/view-effective-routes.png)

4. Effektiva vägar för ett nätverksgränssnitt med namnet **myVMVMNic** visas i följande bild:

     ![Visa effektiva vägar](./media/diagnose-network-routing-problem/effective-routes.png)

    Om det finns flera nätverksgränssnitt som är kopplade till den virtuella datorn kan visa du de effektiva vägarna för alla nätverksgränssnitt genom att välja den. Eftersom varje nätverksgränssnitt kan vara i ett annat undernät, kan varje nätverksgränssnitt har olika gällande routningar.

    I exemplet som visas i föregående bild, är de listade vägarna standardvägar som Azure skapar för varje undernät. Listan har minst dessa vägar, men kan ha ytterligare vägar, beroende på funktioner som du har aktiverat för det virtuella nätverket, till exempel den som peer-kopplat med ett annat virtuellt nätverk eller ansluten till ditt lokala nätverk via en Azure VPN-gateway. Mer information om var och en av vägarna och andra vägar som du kan se för nätverksgränssnittets finns [trafikdirigering i virtuella nätverk](virtual-networks-udr-overview.md). Om listan har ett stort antal vägar, kan det vara lättare att välja **hämta**att ladda ned en .csv-fil med listan över vägar.

Även om gällande routningar har visas via den virtuella datorn i föregående steg, du kan också visa effektiva vägar via ett:
- **Enskilda nätverksgränssnitt**: Lär dig hur du [visa ett nätverksgränssnitt](virtual-network-network-interface.md#view-network-interface-settings).
- **Enskilda routningstabellen**: Lär dig hur du [visa en routningstabell](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnostisera med hjälp av PowerShell

Du kan köra kommandon i den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn, måste den *AzureRM* PowerShell-modulen version 6.0.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` på datorn, hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också behöva köra `Login-AzureRmAccount` att logga in på Azure med ett konto som har den [behörighet](virtual-network-network-interface.md#permissions).

Hämta effektiva vägar för ett nätverksgränssnitt med [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). I följande exempel hämtas de effektiva vägarna för ett nätverksgränssnitt med namnet *myVMVMNic*, som i en resursgrupp med namnet *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Information om den information som returneras i utdata finns i [routningsöversikten](virtual-networks-udr-overview.md). Utdata returneras bara om den virtuella datorn är i körläge. Om det finns flera nätverksgränssnitt som är kopplade till den virtuella datorn, kan du granska de effektiva vägarna för varje nätverksgränssnitt. Eftersom varje nätverksgränssnitt kan vara i ett annat undernät, kan varje nätverksgränssnitt har olika gällande routningar. Om du fortfarande har ett kommunikationsproblem, se [ytterligare diagnos](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverksgränssnitt, men vet namnet på den virtuella datorn nätverksgränssnittet är ansluten till, returnerar ID: N för alla nätverksgränssnitt som är kopplade till en virtuell dator i följande kommandon:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Du får utdata som liknar följande exempel:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

I den föregående utdatan, namnet på nätverksgränssnittet som är *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnostisera med hjälp av Azure CLI

Du kan köra kommandon i den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också behöva köra `az login` och logga in på Azure med ett konto som har den [behörighet](virtual-network-network-interface.md#permissions).

Hämta effektiva vägar för ett nätverksgränssnitt med [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). I följande exempel hämtas de effektiva vägarna för ett nätverksgränssnitt med namnet *myVMVMNic* som finns i en resursgrupp med namnet *myResourceGroup*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Information om den information som returneras i utdata finns i [routningsöversikten](virtual-networks-udr-overview.md). Utdata returneras bara om den virtuella datorn är i körläge. Om det finns flera nätverksgränssnitt som är kopplade till den virtuella datorn, kan du granska de effektiva vägarna för varje nätverksgränssnitt. Eftersom varje nätverksgränssnitt kan vara i ett annat undernät, kan varje nätverksgränssnitt har olika gällande routningar. Om du fortfarande har ett kommunikationsproblem, se [ytterligare diagnos](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverksgränssnitt, men vet namnet på den virtuella datorn nätverksgränssnittet är ansluten till, returnerar ID: N för alla nätverksgränssnitt som är kopplade till en virtuell dator i följande kommandon:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Lösa ett problem

Lösa routningsproblem vanligtvis består av:

- Lägger till en anpassad väg om du vill åsidosätta en av Azures standardvägar. Lär dig hur du [lägga till en anpassad väg](manage-route-table.md#create-a-route).
- Ändra eller ta bort en anpassad väg som kan orsaka routning till en oönskad plats. Lär dig hur du [ändra](manage-route-table.md#change-a-route) eller [ta bort](manage-route-table.md#delete-a-route) en anpassad väg.
- Se till att routningstabellen som innehåller alla anpassade vägar som du har definierat är kopplad till det undernät som nätverksgränssnittet finns i. Lär dig hur du [associera en routningstabell till ett undernät](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Se till att enheter, till exempel Azure VPN gateway eller nätverket virtuella enheter du har distribuerat är operativt. Använd den [VPN-diagnostik](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kapaciteten i Network Watcher för att fastställa eventuella problem med en Azure VPN-gateway.

Om du fortfarande har problem, se [överväganden](#considerations) och [ytterligare diagnos](#additional-dignosis).

## <a name="considerations"></a>Överväganden

Tänk på följande när du felsöker problem:

- Routning är baserat på längsta prefix-matchningen (LPM) bland vägar som du har definierat, border gateway protocol (BGP), och olika vägar. Om det finns fler än en väg med samma LPM-matchning så väljs en väg baserat på dess ursprung i den ordning som anges i [routningsöversikten](virtual-networks-udr-overview.md#how-azure-selects-a-route). Du kan bara se effektiva vägar som är en LPM-matchning, baserat på alla tillgängliga vägar med gällande routningar. Se hur vägar utvärderas för ett nätverksgränssnitt gör det mycket enklare att felsöka specifika vägar som kan påverka kommunikation från den virtuella datorn.
- Om du har definierat anpassade vägar till en virtuell nätverksinstallation (NVA), med *virtuell installation* som nästa hopptyp, se till att IP-vidarebefordring är aktiverat på den NVA som tar emot trafiken eller ignoreras paketet. Läs mer om [aktiverar IP-vidarebefordring för ett nätverksgränssnitt](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Dessutom måste operativsystem eller program i NVA också kunna vidarebefordra nätverkstrafik och konfigureras för att göra detta.
- Om du har skapat en väg till 0.0.0.0/0, dirigeras all utgående internet-trafik till nästa hopp du angav, exempelvis genom en NVA- eller VPN-gateway. Skapa en sådan väg kallas ofta Tvingad tunneltrafik. Fjärranslutningar med protokoll som RDP eller SSH från internet till den virtuella datorn kanske inte fungerar med den här vägen, beroende på hur nästa hopp hanterar trafik. Tvingad tunneltrafik kan aktiveras:
    - När du använder plats-till-plats-VPN genom att skapa en väg med ett nexthop-typ för *VPN-Gateway*. Läs mer om [konfigurera Tvingad tunneltrafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Om en 0.0.0.0/0 (standardvägen) annonseras via BGP genom en gateway för virtuellt nätverk när du använder en plats-till-plats-VPN eller ExpressRoute-krets. Läs mer om hur du använder BGP med en [plats-till-plats VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- För peering trafik i virtuella nätverk ska fungera korrekt, en systemväg med ett nexthop-typ för *VNet-Peering* måste finnas för det peer-kopplade virtuella nätverket prefix intervall. Om en sådan väg finns inte och vnet-peeringlänk är **ansluten**:
    - Vänta några sekunder och försök igen. Om det är en nyetablerade peeringlänk tar ibland det längre tid att sprida vägar till alla nätverksgränssnitt i ett undernät. Läs mer om virtuell nätverkspeering i [peering översikt över virtuella nätverk](virtual-network-peering-overview.md) och [Hantera virtuell nätverkspeering](virtual-network-manage-peering.md).
    - Regler för nätverkssäkerhetsgrupper kan påverka kommunikation. Mer information finns i [diagnostisera problem med virtuella nätverk trafik filter](diagnose-network-traffic-filter-problem.md).
- Även om Azure tilldelar standardvägar till varje nätverksgränssnitt i Azure om du har flera nätverksgränssnitt som är kopplade till den virtuella datorn, har det primära nätverksgränssnittet tilldelats en standardväg (0.0.0.0/0) eller en gateway i den Virtuella datorns operativsystem. Lär dig hur du skapar en standardväg för sekundära nätverksgränssnitt som är kopplade till en [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) VM. Läs mer om [primära och sekundära nätverksgränssnitt](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Ytterligare diagnos

* För att köra ett snabbtest för att avgöra nästa hopptyp för trafik till en plats, använder den [nästa hopp](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) funktion i Azure Network Watcher. Nästa hopp berättar vad nästa hopptyp är för trafik till en angiven plats.
* Om det finns inga vägar som orsakar en virtuell dators nätverkskommunikation misslyckas, kan det bero på grund av brandväggsprogram som körs i den Virtuella datorns operativsystem
* Om du är [Tvingad tunneltrafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) trafik till en lokal enhet via en VPN-gateway eller NVA kan du inte att kunna ansluta till en virtuell dator från internet, beroende på hur du har konfigurerat routning för enheterna. Bekräfta att routning som du har konfigurerat för enheten dirigerar trafik till antingen en offentlig eller privat IP-adress för den virtuella datorn.
* Använd den [anslutningsfelsökning](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kapaciteten i Network Watcher för att fastställa routning, filtrering och i OS orsakerna till problem med utgående kommunikation.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om alla aktiviteter, egenskaper och inställningar för en [dirigera tabell och vägar](manage-route-table.md).
- Lär dig mer om alla [nexthop-typer, systemvägar och hur Azure väljer en väg](virtual-networks-udr-overview.md).

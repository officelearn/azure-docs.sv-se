---
title: Diagnostisera ett routningsproblem för en virtuell azure-dator | Microsoft-dokument
description: Lär dig hur du diagnostiserar ett routningsproblem för virtuella datorer genom att visa de effektiva vägarna för en virtuell dator.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: 13d74fbb4a7c133ca2365fd2cbfce4b3d2bea72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350623"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnostisera ett routningsproblem för den virtuella datorn

I den här artikeln får du lära dig att diagnostisera ett routningsproblem genom att visa de vägar som är effektiva för ett nätverksgränssnitt i en virtuell dator (VM). Azure skapar flera standardvägar för varje virtuellt nätverksundernät. Du kan åsidosätta Azures standardvägar genom att definiera vägar i en flödestabell och sedan associera flödestabellen till ett undernät. Kombinationen av vägar som du skapar, Azures standardvägar och alla vägar som sprids från ditt lokala nätverk via en Azure VPN-gateway (om ditt virtuella nätverk är anslutet till ditt lokala nätverk) via protokollet för gränsgateway (BGP) är effektiva vägar för alla nätverksgränssnitt i ett undernät. Om du inte är bekant med begrepp för virtuellt nätverk, nätverksgränssnitt eller routning läser du [Översikt över virtuellt nätverk,](virtual-networks-overview.md) [Nätverksgränssnitt](virtual-network-network-interface.md)och [Routning](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Scenario

Du försöker ansluta till en virtuell dator, men anslutningen misslyckas. För att avgöra varför du inte kan ansluta till den virtuella datorn kan du visa de effektiva vägarna för ett nätverksgränssnitt med [Azure-portalen,](#diagnose-using-azure-portal) [PowerShell](#diagnose-using-powershell)eller [Azure CLI](#diagnose-using-azure-cli).

De steg som följer förutsätter att du har en befintlig virtuell dator för att visa de effektiva vägarna för. Om du inte har en befintlig virtuell dator distribuerar du först en [Linux-](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-dator för att slutföra uppgifterna i den här artikeln med. Exemplen i den här artikeln är för en virtuell dator som heter *myVM* med ett nätverksgränssnitt som heter *myVMNic1*. Den virtuella datorn och nätverksgränssnittet finns i en resursgrupp med namnet *myResourceGroup*och finns i regionen *östra USA.* Ändra värdena i stegen, beroende på vad som är lämpligt, för den virtuella datorn som du diagnostiserar problemet för.

## <a name="diagnose-using-azure-portal"></a>Diagnostisera med Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Azure-konto som har [de nödvändiga behörigheterna](virtual-network-network-interface.md#permissions).
2. Högst upp i Azure-portalen anger du namnet på en virtuell dator som körs i sökrutan. När namnet på den virtuella datorn visas i sökresultaten markerar du det.
3. Under **Inställningar** till vänster väljer du **Nätverk**och navigerar till nätverksgränssnittsresursen genom att välja dess namn.
     ![Visa nätverksgränssnitt](./media/diagnose-network-routing-problem/view-nics.png)
4. Till vänster väljer du **Effektiva rutter**. De effektiva vägarna för ett nätverksgränssnitt med namnet **myVMNic1** visas i följande bild: ![Visa effektiva vägar](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Om det finns flera nätverksgränssnitt kopplade till den virtuella datorn kan du visa de effektiva vägarna för alla nätverksgränssnitt genom att välja det. Eftersom varje nätverksgränssnitt kan finnas i ett annat undernät kan varje nätverksgränssnitt ha olika effektiva vägar.

    I exemplet som visas i föregående bild är de angivna vägarna standardvägar som Azure skapar för varje undernät. Listan har åtminstone dessa vägar, men kan ha ytterligare vägar, beroende på funktioner som du kan ha aktiverat för ditt virtuella nätverk, till exempel att den är peered med ett annat virtuellt nätverk eller ansluten till ditt lokala nätverk via en Azure VPN-gateway. Mer information om var och en av rutterna och andra vägar som visas för nätverksgränssnittet finns i Routning av [virtuell nätverkstrafik](virtual-networks-udr-overview.md). Om listan har ett stort antal vägar kan det vara enklare att välja **Hämta**för att hämta en CSV-fil med listan över rutter.

Även om effektiva vägar visades via den virtuella datorn i föregående steg kan du också visa effektiva vägar via en:
- **Individuellt nätverksgränssnitt:** Lär dig hur du [visar ett nätverksgränssnitt](virtual-network-network-interface.md#view-network-interface-settings).
- **Individuell rutttabell:** Lär dig hur du [visar en rutttabell](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnostisera med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra de kommandon som följer i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett kostnadsfritt interaktivt skal. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell-modulen, version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du `Connect-AzAccount` också köra för att logga in på Azure med ett konto som har [de behörigheter](virtual-network-network-interface.md#permissions)som krävs .

Få effektiva vägar för ett nätverksgränssnitt med [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable). I följande exempel får du effektiva vägar för ett nätverksgränssnitt med namnet *myVMNic1*, det villikans i en resursgrupp med namnet *myResourceGroup:*

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Information om hur du förstår informationen som returneras i utdata finns i [Översikt över Routning](virtual-networks-udr-overview.md). Utdata returneras endast om den virtuella datorn är i körläge. Om det finns flera nätverksgränssnitt kopplade till den virtuella datorn kan du granska de effektiva vägarna för varje nätverksgränssnitt. Eftersom varje nätverksgränssnitt kan finnas i ett annat undernät kan varje nätverksgränssnitt ha olika effektiva vägar. Om du fortfarande har ett kommunikationsproblem läser du [ytterligare diagnoser](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverksgränssnitt, men vet namnet på den virtuella datorn som nätverksgränssnittet är kopplat till, returnerar följande kommandon ID:n för alla nätverksgränssnitt som är anslutna till en virtuell dator:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Du får utdata som liknar följande exempel:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

I föregående utdata är nätverksgränssnittsnamnet *myVMNic1*.

## <a name="diagnose-using-azure-cli"></a>Diagnostisera med Azure CLI

Du kan köra de kommandon som följer i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du `az login` också köra och logga in på Azure med ett konto som har [de nödvändiga behörigheterna](virtual-network-network-interface.md#permissions).

Få effektiva vägar för ett nätverksgränssnitt med [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). I följande exempel får du effektiva vägar för ett nätverksgränssnitt med namnet *myVMNic1* som finns i en resursgrupp med namnet *myResourceGroup:*

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Information om hur du förstår informationen som returneras i utdata finns i [Översikt över Routning](virtual-networks-udr-overview.md). Utdata returneras endast om den virtuella datorn är i körläge. Om det finns flera nätverksgränssnitt kopplade till den virtuella datorn kan du granska de effektiva vägarna för varje nätverksgränssnitt. Eftersom varje nätverksgränssnitt kan finnas i ett annat undernät kan varje nätverksgränssnitt ha olika effektiva vägar. Om du fortfarande har ett kommunikationsproblem läser du [ytterligare diagnoser](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverksgränssnitt, men vet namnet på den virtuella datorn som nätverksgränssnittet är kopplat till, returnerar följande kommandon ID:n för alla nätverksgränssnitt som är anslutna till en virtuell dator:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Lösa ett problem

Att lösa routningsproblem består vanligtvis av:

- Lägga till en anpassad väg för att åsidosätta en av Azures standardvägar. Läs om hur du [lägger till en anpassad rutt](manage-route-table.md#create-a-route).
- Ändra eller ta bort en anpassad väg som kan orsaka routning till en oönskad plats. Läs om hur du [ändrar](manage-route-table.md#change-a-route) eller [tar bort](manage-route-table.md#delete-a-route) en anpassad rutt.
- Se till att flödestabellen som innehåller anpassade vägar som du har definierat är kopplad till undernätet som nätverksgränssnittet finns i. Lär dig hur du [associerar en vägtabell till ett undernät](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Se till att enheter som Azure VPN-gateway eller virtuella nätverksinstallationer som du har distribuerat fungerar. Använd [funktionen VPN-diagnostik](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för Network Watcher för att ta reda på eventuella problem med en Azure VPN-gateway.

Om du fortfarande har kommunikationsproblem läser du [Överväganden](#considerations) och ytterligare diagnos.

## <a name="considerations"></a>Överväganden

Tänk på följande punkter vid felsökning av kommunikationsproblem:

- Routning baseras på LPM (Längst prefixmatchning) mellan vägar som du har definierat, BGP (Border Gateway Protocol) och systemvägar. Om det finns mer än en väg med samma LPM-matchning väljs en rutt baserat på dess ursprung i den ordning som anges i [översikten Fördirigering](virtual-networks-udr-overview.md#how-azure-selects-a-route). Med effektiva vägar kan du bara se effektiva vägar som är en LPM-matchning, baserat på alla tillgängliga vägar. Att se hur vägarna utvärderas för ett nätverksgränssnitt gör det mycket enklare att felsöka specifika vägar som kan påverka kommunikationen från den virtuella datorn.
- Om du har definierat anpassade vägar till en virtuell nätverksinstallation (NVA), med *virtuell installation* som nästa hopptyp, kontrollerar du att IP-vidarebefordran är aktiverad på nva som tar emot trafiken eller att paket tas bort. Läs mer om [hur du aktiverar IP-vidarebefordran för ett nätverksgränssnitt](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Dessutom måste operativsystemet eller programmet inom NVA också kunna vidarebefordra nätverkstrafik och konfigureras för att göra det.
- Om du har skapat en väg till 0.0.0.0/0 dirigeras all utgående internettrafik till nästa hopp som du har angett, till exempel till en NVA- eller VPN-gateway. Att skapa en sådan rutt kallas ofta påtvingad tunnel. Fjärranslutningar som använder RDP- eller SSH-protokollen från internet till den virtuella datorn kanske inte fungerar med den här vägen, beroende på hur nästa hopp hanterar trafiken. Forcerad tunnel kan aktiveras:
    - När du använder plats-till-plats-VPN, genom att skapa en rutt med en nästa hop typ av *VPN Gateway*. Läs mer om hur du [konfigurerar tvångstunneler](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Om en 0.0.0.0/0 (standardväg) annonseras via BGP via en virtuell nätverksgateway när du använder en plats-till-plats-VPN eller ExpressRoute-krets. Läs mer om hur du använder BGP med en [plats-till-plats-VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- För att virtuell nätverks peering-trafik ska fungera korrekt måste det finnas en systemväg med nästa hoptyp av *VNet-peering* för det peer-ade virtuella nätverkets prefixintervall. Om en sådan väg inte finns och den virtuella nätverks peering-länken är **ansluten:**
    - Vänta några sekunder och försök igen. Om det är en nyetablerad peering-länk tar det ibland längre tid att sprida vägar till alla nätverksgränssnitt i ett undernät. Mer information om virtuell nätverks peering finns i [Översikt över virtuell nätverks peering](virtual-network-peering-overview.md) och hantera virtuell nätverks [peering](virtual-network-manage-peering.md).
    - Regler för nätverkssäkerhetsgrupper kan påverka kommunikationen. Mer information finns i [Diagnostisera ett problem med nätverkstrafikfilter för virtuell dator](diagnose-network-traffic-filter-problem.md).
- Även om Azure tilldelar standardvägar till varje Azure-nätverksgränssnitt tilldelas endast det primära nätverksgränssnittet en standardväg (0.0.0.0/0) eller gateway inom den virtuella datorns operativsystem. Lär dig hur du skapar en standardväg för sekundära nätverksgränssnitt som är kopplade till en [Virtuell Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Linux.](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) Läs mer om [primära och sekundära nätverksgränssnitt](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Ytterligare diagnos

* Om du vill köra ett snabbtest för att fastställa nästa hopptyp för trafik som är avsedd för en plats använder du [funktionen Nästa hopp i](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure Network Watcher. Nästa hopp visar vad nästa hopptyp är för trafik som är avsedd för en angiven plats.
* Om det inte finns några vägar som gör att en virtuell dators nätverkskommunikation misslyckas kan problemet bero på att brandväggsprogram som körs i den virtuella datorns operativsystem
* Om du [tvingar tunneltrafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) till en lokal enhet via en VPN-gateway eller NVA kanske du inte kan ansluta till en virtuell dator från internet, beroende på hur du har konfigurerat routning för enheterna. Bekräfta att den routning som du har konfigurerat för enheten dirigerar trafik till antingen en offentlig eller privat IP-adress för den virtuella datorn.
* Använd [funktionen för anslutningsfelsökning](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i Network Watcher för att fastställa routning, filtrering och in-OS-orsaker till problem med utgående kommunikation.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om alla uppgifter, egenskaper och inställningar för en [rutttabell och rutter](manage-route-table.md).
- Lär dig mer om alla [nästa hopptyper, systemvägar och hur Azure väljer en väg](virtual-networks-udr-overview.md).

---
title: Skapa, ändra eller ta bort en Azure-vägtabell
titlesuffix: Azure Virtual Network
description: Lär dig hur du skapar, ändrar eller tar bort en rutttabell.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 79310ddf121d6ada10755b198b515fdc9c1114d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247069"
---
# <a name="create-change-or-delete-a-route-table"></a>Skapa, ändra eller ta bort en flödestabell

Azure dirigerar automatiskt trafik mellan Azure-undernät, virtuella nätverk och lokala nätverk. Om du vill ändra någon av Azures standardroutning gör du det genom att skapa en vägtabell. Om du inte har någon ny routning i virtuella nätverk kan du läsa mer om det i routning av [virtuell nätverkstrafik](virtual-networks-udr-overview.md) eller genom att slutföra en [självstudiekurs](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Innan du börjar

Om du inte har ett, konfigurera ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Slutför sedan en av dessa uppgifter innan du startar stegen i något avsnitt i den här artikeln:

- **Portalanvändare**: Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

- **PowerShell-användare:** Kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller kör PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Leta reda på listrutan **Välj miljö** i webbläsaren Azure Cloud Shell och välj sedan **PowerShell** om den inte redan är markerad.

    Om du kör PowerShell lokalt använder du Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az.Network` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Kör `Connect-AzAccount` också för att skapa en anslutning med Azure.

- **CLI-användare (Azure Command-line Interface)**: Kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller kör CLI från datorn. Använd Azure CLI version 2.0.31 eller senare om du kör Azure CLI lokalt. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Kör `az login` också för att skapa en anslutning med Azure.

Kontot som du loggar in på eller ansluter till Azure med måste tilldelas [rollen Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [Behörigheter](#permissions).

## <a name="create-a-route-table"></a>Skapa en routningstabell

Det finns en gräns för hur många vägtabeller du kan skapa per Azure-plats och prenumeration. Mer information finns i [Nätverksgränser - Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. På [Azure-portalmenyn](https://portal.azure.com) eller på **startsidan** väljer du **Skapa en resurs**.

1. I sökrutan anger du *rutttabell*. När **rutttabellen** visas i sökresultaten markerar du den.

1. På sidan **Rutttabell** väljer du **Skapa**.

1. I dialogrutan **Skapa flödestabell:**

    1. Ange ett **namn** för flödestabellen.
    1. Välj din **prenumeration**.
    1. Välj en befintlig **resursgrupp** eller välj **Skapa ny** om du vill skapa en ny resursgrupp.
    1. Välj en **plats**.
    1. Om du planerar att koppla vägtabellen till ett undernät i ett virtuellt nätverk som är anslutet till ditt lokala nätverk via en VPN-gateway och du inte vill sprida dina lokala vägar till nätverksgränssnitten i undernätet anger du **dirigerar dirigerar virtual network gateway** till **Inaktiverat**.

1. Välj **Skapa** om du vill skapa den nya rutttabellen.

### <a name="create-route-table---commands"></a>Skapa flödestabell - kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Visa rutttabeller

Gå till [Azure-portalen](https://portal.azure.com) för att hantera ditt virtuella nätverk. Sök efter och välj **Rutttabeller**. De flödestabeller som finns i din prenumeration visas.

### <a name="view-route-table---commands"></a>Visa rutttabell - kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az-nätverksvägtabelllista](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Få-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Visa information om en rutttabell

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera ditt virtuella nätverk. Sök efter och välj **Rutttabeller**.

1. Välj den rutttabell som du vill visa information om i flödestabellen.

1. På flödestabellsidan, under **Inställningar,** visar du rutterna i **flödestabellen** eller de undernät som **flödestabellen** är kopplad till.

Mer information om vanliga Azure-inställningar finns i följande information:

- [Aktivitetslogg](../azure-monitor/platform/platform-logs-overview.md)
- [Åtkomstkontroll (IAM)](../role-based-access-control/overview.md)
- [Taggar](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Lås](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automationsskript](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Visa information om rutttabell - kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk rutt-tabell visar](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Få-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Ändra en rutttabell

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera ditt virtuella nätverk. Sök efter och välj **Rutttabeller**.

1. Välj den rutttabell som du vill ändra i flödestabellen.

De vanligaste ändringarna är att [lägga](#create-a-route) till vägar, [ta bort](#delete-a-route) vägar, [associera](#associate-a-route-table-to-a-subnet) flödestabeller till undernät eller koppla [bort flödestabeller](#dissociate-a-route-table-from-a-subnet) från undernät.

### <a name="change-a-route-table---commands"></a>Ändra en rutttabell - kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [uppdatering av az-nätverksvägtabell](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

Du kan också associera en flödestabell till ett undernät. En flödestabell kan associeras till noll eller fler undernät. Eftersom flödestabeller inte är kopplade till virtuella nätverk måste du associera en flödestabell till varje undernät som du vill att flödestabellen ska associeras med. Azure dirigerar all trafik som lämnar undernätet baserat på vägar som du har skapat i [vägtabeller, standardvägar](virtual-networks-udr-overview.md#default)och vägar som sprids från ett lokalt nätverk, om det virtuella nätverket är anslutet till en Virtuell Azure-nätverksgateway (ExpressRoute eller VPN). Du kan bara associera en routningstabell till undernät i virtuella nätverk som finns på samma Azure-plats och i samma prenumeration som routningstabellen.

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera ditt virtuella nätverk. Sök efter och välj **Virtuella nätverk**.

1. I den virtuella nätverkslistan väljer du det virtuella nätverk som innehåller det undernät som du vill associera en vägtabell till.

1. I menyraden för virtuellt nätverk väljer du **Undernät**.

1. Markera det undernät som du vill koppla flödestabellen till.

1. Välj den flödestabell som du vill associera till undernätet i **tabellen Rutt.**

1. Välj **Spara**.

Om ditt virtuella nätverk är anslutet till en Azure VPN-gateway ska du inte associera en vägtabell till [gateway-undernätet](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) som innehåller en väg med målet *0.0.0.0/0*. Om du gör det så förhindrar du gatewayen från att fungera korrekt. Mer information om hur du använder *0.0.0.0/0* i en väg finns i [Routning av virtuell nätverkstrafik](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Associera en rutttabell - kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Avassociera en routningstabell från ett undernät

När du tar bort en vägtabell från ett undernät dirigerar Azure trafik baserat på dess [standardvägar](virtual-networks-udr-overview.md#default).

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera ditt virtuella nätverk. Sök efter och välj **Virtuella nätverk**.

1. I den virtuella nätverkslistan väljer du det virtuella nätverk som innehåller det undernät som du vill ta bort en vägtabell från.

1. I menyraden för virtuellt nätverk väljer du **Undernät**.

1. Markera det undernät som du vill ta bort från flödestabellen från.

1. Välj **Ingen**i **tabellen Rutt**.

1. Välj **Spara**.

### <a name="dissociate-a-route-table---commands"></a>Koppla bort en rutttabell - kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Ta bort en routningstabell

Du kan inte ta bort en vägtabell som är kopplad till några undernät. [Koppla bort](#dissociate-a-route-table-from-a-subnet) en routningstabell från alla undernät innan du försöker ta bort den.

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera dina vägtabeller. Sök efter och välj **Rutttabeller**.

1. Välj den rutttabell som du vill ta bort i flödestabellen.

1. Välj **Ta bort**och välj sedan **Ja** i bekräftelsedialogrutan.

### <a name="delete-a-route-table---commands"></a>Ta bort en vägtabell - kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk rutt-tabell ta bort](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Ta bort-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Skapa en väg

Det finns en gräns för hur många vägar per vägtabell som kan skapa per Azure-plats och prenumeration. Mer information finns i [Nätverksgränser - Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera dina vägtabeller. Sök efter och välj **Rutttabeller**.

1. Välj den rutttabell som du vill lägga till en väg till i flödestabellen.

1. Välj **Rutter** > **Lägg till**på menyraden för rutttabeller .

1. Ange ett unikt **flödesnamn** för rutten i flödestabellen.

1. Ange den **adressprefix**, i klasslös CIDR-notation (Inter-Domain Routing), som du vill dirigera trafik till. Prefixet kan inte dupliceras i mer än en väg i flödestabellen, även om prefixet kan finnas i ett annat prefix. Om du till exempel har definierat *10.0.0.0/16* som prefix i ett flöde kan du fortfarande definiera en annan väg med adressprefixet *10.0.0.0/22.* Azure väljer en väg för trafik baserat på längsta prefixmatchning. Mer information finns i [Hur Azure väljer en rutt](virtual-networks-udr-overview.md#how-azure-selects-a-route).

1. Välj en **nästa hopptyp**. Mer information om nästa hopptyper finns i [Routning av virtuell nätverkstrafik](virtual-networks-udr-overview.md).

1. Om du väljer en **nästa hoptyp** av **virtuell installation**anger du en IP-adress för **Nästa hoppadress**.

1. Välj **OK**.

### <a name="create-a-route---commands"></a>Skapa en rutt - kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Visa rutter

En flödestabell innehåller noll eller fler vägar. Mer information om informationen i listan när du visar rutter finns i [Routning av virtuell nätverkstrafik](virtual-networks-udr-overview.md).

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera dina vägtabeller. Sök efter och välj **Rutttabeller**.

1. Välj den rutttabell som du vill visa rutter för i flödestabellen.

1. I menyraden för rutttabell väljer du **Rutter** för att se listan över rutter.

### <a name="view-routes---commands"></a>Visa rutter - kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az-nätverksväg-tabell ruttlista](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Få-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Visa information om en väg

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera dina vägtabeller. Sök efter och välj **Rutttabeller**.

1. I flödestabelllistan väljer du den flödestabell som innehåller den rutt som du vill visa information om.

1. I menyraden för rutttabell väljer du **Rutter** för att se listan över rutter.

1. Välj den rutt som du vill visa information om.

### <a name="view-details-of-a-route---commands"></a>Visa information om en rutt - kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk rutt-tabell rutt visa](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Få-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Ändra en rutt

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera dina vägtabeller. Sök efter och välj **Rutttabeller**.

1. Välj den flödestabell som innehåller den rutt som du vill ändra i flödestabellen.

1. I menyraden för rutttabell väljer du **Rutter** för att se listan över rutter.

1. Välj den rutt som du vill ändra.

1. Ändra befintliga inställningar till de nya inställningarna och välj sedan **Spara**.

### <a name="change-a-route---commands"></a>Ändra en rutt - kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [uppdatering av az-nätverksvägtabellväg](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Ta bort en väg

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera dina vägtabeller. Sök efter och välj **Rutttabeller**.

1. I flödestabelllistan väljer du den flödestabell som innehåller den rutt som du vill ta bort.

1. I menyraden för rutttabell väljer du **Rutter** för att se listan över rutter.

1. Välj den rutt som du vill ta bort.

1. Välj **Ta bort**och välj sedan **Ja** i bekräftelsedialogrutan.

### <a name="delete-a-route---commands"></a>Ta bort en rutt - kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az-nätverksväg-tabell rutt ta bort](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Ta bort-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Visa effektiva rutter

De effektiva vägarna för varje VM-ansluten nätverksgränssnitt är en kombination av vägtabeller som du har skapat, Azures standardvägar och alla vägar som sprids från lokala nätverk via BGP (Border Gateway Protocol) via ett virtuellt Azure-nätverk Gateway. Det är praktiskt att förstå de effektiva vägarna för ett nätverksgränssnitt vid felsökning av routningsproblem. Du kan visa effektiva vägar för alla nätverksgränssnitt som är kopplade till en virtuell dator som körs.

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera dina virtuella datorer. Sök efter och välj **Virtuella datorer**.

1. I listan för virtuella datorer väljer du den virtuella dator som du vill visa effektiva vägar för.

1. Välj **Nätverk**i menyraden För virtuell dator.

1. Välj namnet på ett nätverksgränssnitt.

1. I menyraden för nätverksgränssnitt väljer du **Effektiva vägar**.

1. Granska listan över effektiva vägar för att se om det finns rätt väg för var du vill dirigera trafik till. Läs mer om nästa hopptyper som visas i den här listan i [Routning av virtuell nätverkstrafik](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Visa effektiva rutter - kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk nic visa-effektiv-rutt-tabell](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Validera routning mellan två slutpunkter

Du kan bestämma nästa hopptyp mellan en virtuell dator och IP-adressen för en annan Azure-resurs, en lokal resurs eller en resurs på Internet. Att fastställa Azures routning är användbart vid felsökning av routningsproblem. För att kunna utföra den här uppgiften måste du ha en befintlig nätverksbevakare. Om du inte har en befintlig nätverksbevakare skapar du en genom att slutföra stegen i [Skapa en Network Watcher-instans](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera dina nätverksbevakare. Sök efter och välj **Network Watcher**.

1. I nätverksbevakningens menyrad väljer du **Nästa hopp**.

1. I **Nätverket Watcher | Nästa hop** sida:

    1. Välj din **prenumeration** och **resursgruppen** för källdatorn som du vill validera routning från.

    1. Välj den **virtuella datorn** och **nätverksgränssnittet** som är kopplat till den virtuella datorn.
    
    1. Ange en **käll-IP-adress** som tilldelats det nätverksgränssnitt som du vill validera routning från.

    1. Ange en **mål-IP-adress** som du vill validera routning till.

1. Välj **Nästa hopp**.

Efter en kort väntan berättar Azure om nästa hopptyp och ID för den väg som dirigerade trafiken. Läs mer om nästa hopptyper som visas returnerade i [routning av virtuell nätverkstrafik](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Validera routning mellan två slutpunkter - kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk watcher show-next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Hämta-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter i flödestabeller och vägar måste ditt konto tilldelas [rollen Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder i följande tabell:

| Åtgärd                                                          |   Namn                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Läsa en rutttabell                                    |
| Microsoft.Network/routeTables/write                             |   Skapa eller uppdatera en flödestabell                        |
| Microsoft.Network/routeTables/delete                            |   Ta bort en routningstabell                                  |
| Microsoft.Network/routeTables/join/action                       |   Associera en routningstabell till ett undernät                   |
| Microsoft.Network/routeTables/routes/read                       |   Läsa en rutt                                          |
| Microsoft.Network/routeTables/routes/write                      |   Skapa eller uppdatera en rutt                              |
| Microsoft.Network/routeTables/routes/delete                     |   Ta bort en väg                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Hämta den effektiva vägtabellen för ett nätverksgränssnitt |
| Microsoft.Network/networkWatchers/nextHop/action                |   Hämtar nästa hopp från en virtuell dator                           |

## <a name="next-steps"></a>Nästa steg

- Skapa en vägtabell med Exempelskript för [PowerShell](powershell-samples.md) eller [Azure CLI](cli-samples.md) eller Azure Resource [Manager](template-samples.md)
- Skapa och tillämpa [Azure-principen](policy-samples.md) för virtuella nätverk

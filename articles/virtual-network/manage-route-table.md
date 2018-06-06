---
title: Skapa, ändra eller ta bort en Azure routningstabellen | Microsoft Docs
description: Lär dig mer om att skapa, ändra eller ta bort en routningstabell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 93ecd0264413e0eb719c9d33f0a0b756bcee6552
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726456"
---
# <a name="create-change-or-delete-a-route-table"></a>Skapa, ändra eller ta bort en routningstabell

Azure automatiskt dirigerar trafik mellan Azure undernät, virtuella nätverk och lokala nätverk. Om du vill ändra något av Azures standard routning kan göra du detta genom att skapa en routingtabell. Om du har använt routning i virtuella nätverk, kan du läsa mer om den i den [översikt över routning](virtual-networks-udr-overview.md) eller genom att slutföra en [kursen](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Innan du börjar

Utför följande uppgifter innan du slutför stegen i alla avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [ledigt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppna https://portal.azure.com, och logga in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudiekursen kräver Azure PowerShell-modulen version 5.7.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.
- Om du använder Azure-kommandoradsgränssnittet (CLI)-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här kursen kräver Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt, måste du också köra `az login` att skapa en anslutning med Azure.

Kontot du loggar in, eller Anslut till Azure med, måste vara tilldelade till den [network-deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas de åtgärder som anges i [behörigheter ](#permissions).

## <a name="create-a-route-table"></a>Skapa en routningstabell

Det finns en gräns för hur många routningstabeller som du kan skapa per Azure-plats och prenumeration. Läs mer i informationen om [begränsningar för Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. I det övre vänstra hörnet av portalen väljer **+ skapa en resurs för**.
2. Välj **nätverk**och välj **routningstabellen**.
3. Ange en **namn** routningstabellen, Välj din **prenumeration**, skapa en ny **resursgruppen**, eller välj en befintlig resursgrupp, Välj en **plats** och välj **skapa**. Den **inaktivera BGP-väg spridningen** alternativet förhindrar lokal vägar från att spridas via BGP till nätverksgränssnitt i alla undernät i vägtabellen är kopplad till. Om det virtuella nätverket inte är ansluten till en gateway för Azure-nätverk (VPN eller ExpressRoute) lämna alternativet *inaktiverade*.

**Kommandon**

- Azure CLI: [az nätverket routningstabellen skapa](/cli/azure/network/route-table/route#az_network_route_table_create)
- PowerShell: [nya AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)

## <a name="view-route-tables"></a>Visa vägtabeller

Skriv i sökrutan överst i portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den. Routningstabeller som finns i din prenumeration visas.

**Kommandon**

- Azure CLI: [az nätverket routningstabellen lista](/cli/azure/network/route-table/route#az_network_route_table_list)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="view-details-of-a-route-table"></a>Visa information om en routningstabell

1. Skriv i sökrutan överst i portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
2. Välj routningstabellen i listan som du vill visa information om. Under **inställningar**, du kan visa den **vägar** i routningstabellen och **undernät** routningstabellen är kopplad till.
3. Mer information om gemensamma inställningar för Azure finns följande information:
    *   [Aktivitetslogg](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Åtkomstkontroll (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Taggar](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Lås](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automation-skript](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Kommandon**

- Azure CLI: [az nätverket routningstabellen visa](/cli/azure/network/route-table/route#az_network_route_table_show)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="change-a-route-table"></a>Ändra en routningstabell

1. Skriv i sökrutan överst i portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
2. Välj routningstabellen som du vill ändra. De vanligaste förändringar är [att lägga till](#create-a-route) eller [tar bort](#delete-a-route) vägar och [associera](#associate-a-route-table-to-a-subnet) routningstabeller till, eller [dissociating](#dissociate-a-route-table-from-a-subnet) routningstabeller från undernät.

**Kommandon**

- Azure CLI: [az nätverket routningstabellen uppdatering](/cli/azure/network/route-table/route#az_network_route_table_update)
- PowerShell: [Set AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

Ett undernät kan ha noll eller en vägtabell associerad till den. En routingtabell kan vara kopplad till noll eller flera undernät. Eftersom vägtabeller inte är kopplad till virtuella nätverk, måste du associera en routningstabell för varje undernät som du vill routningstabellen som är kopplad till. Omdirigeras all trafik som lämnar undernätet baserat på vägar som du har skapat i vägtabeller, [standard vägar](virtual-networks-udr-overview.md#default), och vägar sprids från ett lokalt nätverk, om det virtuella nätverket är anslutet till ett virtuellt Azure-nätverk gateway ( ExpressRoute eller VPN om använder BGP med en VPN-gateway). Du kan bara koppla en routingtabell till undernät i virtuella nätverk som finns i samma Azure-plats och prenumeration som routningstabellen.

1. Skriv i sökrutan överst i portalen *virtuella nätverk* i sökrutan. När **virtuella nätverken** visas i sökresultaten, markerar du den.
2. Välj det virtuella nätverket i listan som innehåller det undernät som du vill koppla en routingtabell till.
3. Välj **undernät** under **inställningar**.
4. Välj du vill associera routningstabellen för undernätet.
5. Välj **routningstabellen**, Välj routningstabellen som du vill koppla till undernätet och välj sedan **spara**.

Om det virtuella nätverket är ansluten till en Azure VPN-gateway kan inte koppla en routingtabell till den [gatewayundernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) som innehåller en väg med 0.0.0.0/0 som mål. Gör detta kan inte gatewayen från att fungera korrekt. Mer information om hur du använder 0.0.0.0/0 i en väg finns [trafikroutning för virtuella nätverk](virtual-networks-udr-overview.md#default-route).

**Kommandon**

- Azure CLI: [az network vnet undernät uppdatering](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Koppla bort en routingtabell från ett undernät

När du koppla bort en routingtabell från ett undernät Azure dirigerar trafik baserat på dess [standard vägar](virtual-networks-udr-overview.md#default).

1. Skriv i sökrutan överst i portalen *virtuella nätverk* i sökrutan. När **virtuella nätverken** visas i sökresultaten, markerar du den.
2. Välj det virtuella nätverk som innehåller det undernät som du vill koppla bort en routingtabell från.
3. Välj **undernät** under **inställningar**.
4. Välj det undernät som du vill koppla bort routningstabellen från.
5. Välj **routningstabellen**väljer **ingen**och välj **spara**.

**Kommandon**

- Azure CLI: [az network vnet undernät uppdatering](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 

## <a name="delete-a-route-table"></a>Ta bort en routningstabell

Om en routingtabell är kopplad till undernät, kan inte tas bort. [Koppla bort](#dissociate-a-route-table-from-a-subnet) en routingtabell från alla undernät innan du försöker ta bort den.

1. Skriv i sökrutan överst i portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
2. Välj **...**  höger om du vill ta bort routningstabellen.
3. Välj **ta bort**, och välj sedan **Ja**.

**Kommandon**

- Azure CLI: [az nätverket routningstabellen ta bort](/cli/azure/network/route-table/route#az_network_route_table_delete)
- PowerShell: [ta bort AzureRmRouteTable](/powershell/module/azurerm.network/delete-azurermroutetable) 

## <a name="create-a-route"></a>Skapa en väg

Det finns en gräns för hur många vägar per vägtabell kan skapa per Azure-plats och prenumeration. Läs mer i informationen om [begränsningar för Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Skriv i sökrutan överst i portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
2. Välj i listan som du vill lägga till en väg till vägtabellen.
3. Välj **vägar**under **inställningar**.
4. Välj **+ Lägg till**.
5. Ange ett unikt **namn** för vägen i routningstabellen.
6. Ange den **adressprefixet**, i CIDR-notation som du vill dirigera trafik till. Prefixet får inte vara dubbletter i flera vägar i routningstabellen, även om prefixet som kan vara i ett annat prefix. Om du har definierat 10.0.0.0/16 som ett prefix i ett flöde, kan du fortfarande definiera en annan väg med adressprefixet 10.0.0.0/24. Azure väljs en väg för trafik baserat på längsta prefixmatchning. Läs mer om hur Azure väljs vägar i [routning: översikt](virtual-networks-udr-overview.md#how-azure-selects-a-route).
7. Välj en **nästa hopptyp**. En detaljerad beskrivning av alla hopptyper av nästa finns [routning: översikt](virtual-networks-udr-overview.md).
8. Ange en IP-adress för **för nästa hopp**. Du kan bara ange en adress om du har valt *virtuell installation* för **nästa hopptyp**.
9. Välj **OK**.

**Kommandon**

- Azure CLI: [az nätverksväg routningstabellen skapa](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_create)
- PowerShell: [nya AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)

## <a name="view-routes"></a>Visa flöden

En routningstabell innehåller noll eller flera vägar. Mer information om den information som visas när du visar vägar finns [routning: översikt](virtual-networks-udr-overview.md).

1. Skriv i sökrutan överst i portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
2. Välj i listan som du vill visa vägar för routningstabellen.
3. Välj **vägar** under **inställningar**.

**Kommandon**

- Azure CLI: [az nätverkslistan routningstabellen väg](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_list)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="view-details-of-a-route"></a>Visa information om en väg

1. Skriv i sökrutan överst i portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
2. Välj routningstabellen som du vill visa information om en väg för.
3. Välj **vägar**.
4. Välj den väg som du vill visa information om.

**Kommandon**

- Azure CLI: [az nätverket routningstabellen väg visa](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_show)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="change-a-route"></a>Ändra en väg

1. Skriv i sökrutan överst i portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
2. Välj routningstabellen som du vill ändra en väg för.
3. Välj **vägar**.
4. Välj den väg som du vill ändra.
5. Ändra befintliga inställningar till de nya inställningarna och välj sedan **spara**.

**Kommandon**

- Azure CLI: [az nätverket routningstabellen väg uppdatering](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_update)
- PowerShell: [Set AzureRmRouteConfig](/powershell/module/azurerm.network/set-azurermrouteconfig)

## <a name="delete-a-route"></a>Ta bort en väg

1. Skriv i sökrutan överst i portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
2. Välj routningstabellen som du vill ta bort en väg för.
3. Välj **vägar**.
4. Välj i listan över vägar **...**  till höger för vägen som du vill ta bort.
5. Välj **ta bort**och välj **Ja**.

**Kommandon**

- Azure CLI: [az datornätverket routningstabellen väg ta bort](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_delete)
- PowerShell: [ta bort AzureRmRouteConfig](/powershell/module/azurerm.network/remove-azurermrouteconfig)

## <a name="view-effective-routes"></a>Visa effektiva flöden

Effektiva vägar för varje nätverksgränssnitt som är kopplad till en virtuell dator är en kombination av routningstabeller som du har skapat, Azures standardvägar och alla vägar sprids från lokala nätverk via BGP via en gateway för virtuella Azure-nätverket. Så här fungerar effektivt vägar för ett nätverksgränssnitt är användbart när du felsöker problem med routning. Du kan visa de effektiva vägarna för alla nätverksgränssnitt som är kopplad till en aktiv virtuell dator.

1. Ange namnet på en virtuell dator som du vill visa effektiva vägar för i sökrutan överst i portalen. Om du inte vet namnet på en virtuell dator, ange *virtuella datorer* i sökrutan. När **virtuella datorer** visas i sökresultaten markerar du den och väljer en virtuell dator i listan.
2. Välj **nätverk** under **inställningar**.
3. Välj namnet på ett nätverksgränssnitt.
4. Välj **effektiva vägar** under **stöd + felsökning**.
5. Granska listan över effektiva vägar för att avgöra om rätt väg finns där du vill dirigera trafik till. Lär dig mer om nästa hopptyper som visas i listan i [routning: översikt](virtual-networks-udr-overview.md).

**Kommandon**

- Azure CLI: [az nätverk nic visa-gällande--routningstabellen](/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/remove-azurermrouteconfig) 

## <a name="validate-routing-between-two-endpoints"></a>Validera routning mellan två slutpunkter

Du kan fastställa nästa hopptyp mellan en virtuell dator och IP-adressen för en annan Azure-resurs, en lokal resurs eller en resurs på Internet. Bestämma Azure routning är användbart när du felsöker problem med routning. För att slutföra den här uppgiften, måste du ha en befintlig Nätverksbevakaren. Om du inte har en befintlig Nätverksbevakaren kan skapa en genom att slutföra stegen i [skapa en instans av Nätverksbevakaren](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Skriv i sökrutan överst i portalen *nätverksbevakaren* i sökrutan. När **Network Watcher** visas i sökresultatet väljer du posten.
2. Välj **nästa hopp** under **verktyg för NÄTVERKSDIAGNOSTIK**.
3. Välj din **prenumeration** och **resursgruppen** av den virtuella källdatorn som du vill validera routning från.
4. Välj den **virtuella**, **nätverksgränssnittet** kopplade till den virtuella datorn och **källans IP-adress** tilldelats nätverksgränssnittet som du vill validera Routning från.
5. Ange den **målets IP-adress** som du vill validera routning till den.
6. Välj **nästa hopp**.
7. Efter en kort vänta returneras informationen som talar om nästa hopptyp och ID för vägen som dirigeras trafiken. Lär dig mer om nästa hopptyper som du ser returneras i [routning: översikt](virtual-networks-udr-overview.md).

**Kommandon**

- Azure CLI: [az nätverk watcher Visa-nästa hopp](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_show_next_hop)
- PowerShell: [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 

## <a name="permissions"></a>Behörigheter

För att utföra aktiviteter på route-tabeller och vägar, ditt konto måste ha tilldelats den [network-deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller en [anpassade](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som tilldelas åtgärderna som anges i följande tabell:

| Åtgärd                                                          |   Namn                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Läs en routningstabell                                    |
| Microsoft.Network/routeTables/write                             |   Skapa eller uppdatera en routningstabell                        |
| Microsoft.Network/routeTables/delete                            |   Ta bort en routningstabell                                  |
| Microsoft.Network/routeTables/join/action                       |   Associera en routningstabell till ett undernät                   |
| Microsoft.Network/routeTables/routes/read                       |   Läs en väg                                          |
| Microsoft.Network/routeTables/routes/write                      |   Skapa eller uppdatera en väg                              |
| Microsoft.Network/routeTables/routes/delete                     |   Ta bort en väg                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Hämta den effektiva routningstabellen för ett nätverksgränssnitt |
| Microsoft.Network/networkWatchers/nextHop/action                |   Hämtar nästa hopp från en virtuell dator                           |

## <a name="next-steps"></a>Nästa steg

- Skapa en väg tabell med [PowerShell](powershell-samples.md) eller [Azure CLI](cli-samples.md) exempel på skript eller använda Azure [Resource Manager-mallar](template-samples.md)
- Skapa och använda [Azure princip](policy-samples.md) för virtuella nätverk

---
title: Skapa, ändra eller ta bort en Azure route-tabell
titlesuffix: Azure Virtual Network
description: Lär dig hur du skapar, ändrar eller tar bort en routningstabell.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: fe8ea4dfb4de45a1e09648ac51fe8d74f93a6b9e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280279"
---
# <a name="create-change-or-delete-a-route-table"></a>Skapa, ändra eller ta bort en routningstabell

Azure dirigerar automatiskt trafik mellan Azure-undernät, virtuella nätverk och lokala nätverk. Om du vill ändra någon av Azures standardroutning gör du det genom att skapa en routningstabell. Om du inte har använt routning i virtuella nätverk kan du lära dig mer om det i [operationsföljden](virtual-networks-udr-overview.md) eller genom att slutföra en [själv studie kurs](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande åtgärder innan du slutför stegen i något avsnitt i den här artikeln:

* Om du inte redan har ett Azure-konto kan du registrera dig för ett [kostnads fritt utvärderings konto](https://azure.microsoft.com/free).<br>
* Om du använder portalen öppnar du https://portal.azure.comoch loggar in med ditt Azure-konto.<br>
* Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Vanliga Azure-verktyg finns förinstallerade och har konfigurerats för användning med ditt konto. I den här självstudien krävs Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.<br>
* Om du använder kommando rads kommandon i Azure för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. I den här självstudien krävs Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.

Det konto som du loggar in på eller ansluter till Azure med måste tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [behörigheter](#permissions).

## <a name="create-a-route-table"></a>Skapa en routningstabell

Det finns en gräns för hur många routningstabeller du kan skapa per Azure-plats och prenumeration. Läs mer i informationen om [begränsningar för Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. I det övre vänstra hörnet i portalen väljer du **+ skapa en resurs**.
1. Välj **nätverk**och välj sedan **routningstabell**.
1. Ange ett **namn** för routningstabellen, välj din **prenumeration**, skapa en ny **resurs grupp**eller Välj en befintlig resurs grupp, Välj en **plats**och välj sedan **skapa**. Om du planerar att koppla routningstabellen till ett undernät i ett virtuellt nätverk som är anslutet till ditt lokala nätverk via en VPN-gateway och du inaktiverar den **virtuella nätverks-gatewayens väg spridning**, sprids inte dina lokala vägar till nätverks gränssnitten i under nätet.

### <a name="create-route-table---commands"></a>Skapa routningstabell – kommandon

* Azure CLI: [AZ Network Route-Table Create](/cli/azure/network/route-table/route)<br>
* PowerShell: [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>Visa routningstabeller

I rutan Sök högst upp i *portalen anger du routningstabeller i* sökrutan. När **routningstabeller** visas i Sök resultaten väljer du den. De routningstabeller som finns i din prenumeration visas.

### <a name="view-route-table---commands"></a>Visa väg tabell – kommandon

* Azure CLI: [AZ Network Route-Table List](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>Visa information om en routningstabell

1. I rutan Sök högst upp i *portalen anger du routningstabeller i* sökrutan. När **routningstabeller** visas i Sök resultaten väljer du den.
1. Välj routningstabellen i listan som du vill visa information om. Under **Inställningar**kan du Visa **vägarna** i routningstabellen och **under näten** som routningstabellen är kopplad till.
1. Mer information om vanliga Azure-inställningar finns i följande information:

    * [Aktivitetslogg](../azure-monitor/platform/platform-logs-overview.md)<br>
    * [Åtkomst kontroll (IAM)](../role-based-access-control/overview.md)<br>
    * [Taggar](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Hålls](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Automation-skript](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Visa information om routningstabellen – kommandon

* Azure CLI: [AZ Network Route-Table show](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>Ändra en routningstabell

1. I rutan Sök högst upp i *portalen anger du routningstabeller i* sökrutan. När **routningstabeller** visas i Sök resultaten väljer du den.
1. Välj den routningstabell som du vill ändra. De vanligaste ändringarna [lägger till](#create-a-route) eller [tar bort](#delete-a-route) vägar och [kopplar](#associate-a-route-table-to-a-subnet) routningstabeller till eller kopplar bort routningstabeller [från](#dissociate-a-route-table-from-a-subnet) undernät.

### <a name="change-a-route-table---commands"></a>Ändra en routningstabell – kommandon

* Azure CLI: [AZ Network Route-Table Update](/cli/azure/network/route-table/route)<br>
* PowerShell: [set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

Ett undernät kan ha ingen eller en associerad routningstabell. En routningstabell kan kopplas till inget eller flera undernät. Eftersom routningstabeller inte är kopplade till virtuella nätverk måste du associera en routningstabell till varje undernät som du vill associera routningstabellen till. All trafik som lämnar under nätet dirigeras utifrån vägar som du har skapat i routningstabeller, [standard vägar](virtual-networks-udr-overview.md#default)och vägar som har spridits från ett lokalt nätverk, om det virtuella nätverket är anslutet till en virtuell Azure-nätverksgateway (EXPRESSROUTE eller VPN). Du kan bara associera en routningstabell till undernät i virtuella nätverk som finns på samma Azure-plats och i samma prenumeration som routningstabellen.

1. Ange *virtuella nätverk* i sökrutan i rutan Sök högst upp i portalen. När **virtuella nätverk** visas i Sök resultaten väljer du det.
1. Välj det virtuella nätverk i listan som innehåller det undernät som du vill associera en routningstabell till.
1. Välj **undernät** under **Inställningar**.
1. Välj det undernät som du vill koppla routningstabellen till.
1. Välj **routningstabell, Välj**den routningstabell som du vill koppla till under nätet och välj sedan **Spara**.

Om ditt virtuella nätverk är anslutet till en Azure VPN-gateway, associerar du inte en routingtabell till det [gatewayundernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) som innehåller en väg med målet 0.0.0.0/0. Om du gör det så förhindrar du gatewayen från att fungera korrekt. Mer information om hur du använder 0.0.0.0/0 i en väg finns i [trafik dirigering i virtuella nätverk](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Koppla en routningstabell – kommandon

* Azure CLI: [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Avassociera en routningstabell från ett undernät

När du kopplar bort en routningstabell från ett undernät dirigerar Azure trafik baserat på dess [standard vägar](virtual-networks-udr-overview.md#default).

1. Ange *virtuella nätverk* i sökrutan i rutan Sök högst upp i portalen. När **virtuella nätverk** visas i Sök resultaten väljer du det.
1. Välj det virtuella nätverk som innehåller det undernät som du vill koppla en routningstabell från.
1. Välj **undernät** under **Inställningar**.
1. Välj det undernät som du vill koppla routningstabellen från.
1. Välj **routningstabell**, Välj **ingen**och välj sedan **Spara**.

### <a name="dissociate-a-route-table---commands"></a>Koppla bort en routningstabell – kommandon

* Azure CLI: [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>Ta bort en routningstabell

Om en routingtabell är kopplad till undernät kan den inte tas bort. [Koppla bort](#dissociate-a-route-table-from-a-subnet) en routningstabell från alla undernät innan du försöker ta bort den.

1. I rutan Sök högst upp i *portalen anger du routningstabeller i* sökrutan. När **routningstabeller** visas i Sök resultaten väljer du den.
1. Välj **...** på höger sida av den routningstabell som du vill ta bort.
1. Välj **ta bort**och välj sedan **Ja**.

### <a name="delete-a-route-table---commands"></a>Ta bort en routningstabell – kommandon

* Azure CLI: [AZ Network Route-Table Delete](/cli/azure/network/route-table/route)<br>
* PowerShell: [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>Skapa en väg

Det finns en gräns för hur många vägar per routningstabell som kan skapas per Azure-plats och prenumeration. Läs mer i informationen om [begränsningar för Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. I rutan Sök högst upp i *portalen anger du routningstabeller i* sökrutan. När **routningstabeller** visas i Sök resultaten väljer du den.
1. Välj routningstabellen i listan som du vill lägga till en väg till.
1. Välj **vägar**under **Inställningar**.
1. Välj **+ Lägg till**.
1. Ange ett unikt **namn** för vägen i routningstabellen.
1. Ange adressprefixet **, i CIDR-notation**, som du vill dirigera trafiken till. Prefixet kan inte dupliceras i mer än en väg i routningstabellen, även om prefixet som kan finnas i ett annat prefix. Om du till exempel har definierat 10.0.0.0/16 som ett prefix i en väg kan du fortfarande definiera en annan väg med adressprefixet 10.0.0.0/24. Azure väljer en väg för trafik baserat på den längsta prefix matchningen. Mer information om hur Azure väljer vägar finns i [Operationsföljd översikt](virtual-networks-udr-overview.md#how-azure-selects-a-route).
1. Välj en **nästa hopp typ**. En detaljerad beskrivning av alla typer av nästa hopp finns i [routning – översikt](virtual-networks-udr-overview.md).
1. Ange en IP-adress för **nästa hopp adress**. Du kan bara ange en adress om du har valt *virtuell* installation för **nästa hopp typ**.
1. Välj **OK**.

### <a name="create-a-route---commands"></a>Skapa en Route-kommandon

* Azure CLI: [AZ nätverks väg – tabell väg skapa](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>Visa vägar

En routningstabell innehåller noll eller flera vägar. Mer information om den information som visas när du visar vägar finns i [routning – översikt](virtual-networks-udr-overview.md).

1. I rutan Sök högst upp i *portalen anger du routningstabeller i* sökrutan. När **routningstabeller** visas i Sök resultaten väljer du den.
1. Välj routningstabellen från listan som du vill visa vägar för.
1. Välj **vägar** under **Inställningar**.

### <a name="view-routes---commands"></a>Visa vägar – kommandon

* Azure CLI: [AZ Network Route-Table Route List](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>Visa information om en väg

1. I rutan Sök högst upp i *portalen anger du routningstabeller i* sökrutan. När **routningstabeller** visas i Sök resultaten väljer du den.
1. Välj den routningstabell som du vill visa information om en väg för.
1. Välj **vägar**.
1. Välj den väg som du vill visa information om.

### <a name="view-details-of-a-route---commands"></a>Visa information om en Route-kommandon

* Azure CLI: [AZ Network Route-Table Route show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>Ändra en väg

1. I rutan Sök högst upp i *portalen anger du routningstabeller i* sökrutan. När **routningstabeller** visas i Sök resultaten väljer du den.
1. Välj den routningstabell som du vill ändra en väg för.
1. Välj **vägar**.
1. Välj den väg som du vill ändra.
1. Ändra de befintliga inställningarna till de nya inställningarna och välj sedan **Spara**.

### <a name="change-a-route---commands"></a>Ändra en Route-kommandon

* Azure CLI: [AZ nätverks väg – uppdatering av tabell väg](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>Ta bort en väg

1. I rutan Sök högst upp i *portalen anger du routningstabeller i* sökrutan. När **routningstabeller** visas i Sök resultaten väljer du den.
1. Välj den routningstabell som du vill ta bort en väg för.
1. Välj **vägar**.
1. I listan över vägar väljer du **...** på höger sida av den väg som du vill ta bort.
1. Välj **ta bort**och välj sedan **Ja**.

### <a name="delete-a-route---commands"></a>Ta bort ett Route-kommandon

* Azure CLI: [AZ Network Route-Table Route Delete](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>Visa effektiva vägar

De effektiva vägarna för varje nätverks gränssnitt som är kopplat till en virtuell dator är en kombination av väg tabeller som du har skapat, Azures standard vägar och alla vägar som har spridits från lokala nätverk via BGP via en virtuell Azure-nätverksgateway. Att förstå de effektiva vägarna för ett nätverks gränssnitt är till hjälp vid fel sökning av problem med Routning. Du kan visa effektiva vägar för alla nätverks gränssnitt som är kopplade till en virtuell dator som körs.

1. I rutan Sök högst upp i portalen anger du namnet på den virtuella dator som du vill visa effektiva vägar för. Om du inte känner till namnet på en virtuell dator anger du *virtuella datorer* i sökrutan. När **virtuella datorer** visas i Sök resultaten väljer du den och väljer en virtuell dator i listan.
1. Välj **nätverk** under **Inställningar**.
1. Välj ett nätverks gränssnitts namn.
1. Välj **effektiva vägar** under **support + fel sökning**.
1. Granska listan över effektiva vägar för att avgöra om rätt väg finns där du vill dirigera trafiken till. Läs mer om de typer av nästa hopp som du ser i den här listan i [Översikt över routning](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Visa effektiva vägar – kommandon

* Azure CLI: [AZ Network NIC show-effektiv-Route-Table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>Verifiera routning mellan två slut punkter

Du kan fastställa nästa hopp typ mellan en virtuell dator och IP-adressen för en annan Azure-resurs, en lokal resurs eller en resurs på Internet. Att fastställa Azures routning är användbart när du felsöker problem med Routning. Du måste ha en befintlig Network Watcher för att slutföra den här uppgiften. Om du inte har en befintlig Network Watcher skapar du en genom att följa stegen i [skapa en Network Watcher instans](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. I rutan Sök högst upp i portalen anger du *Network Watcher* i rutan Sök. När **Network Watcher** visas i sökresultatet väljer du posten.
1. Välj **nästa hopp** under **diagnostikverktyg för nätverk**.
1. Välj din **prenumeration** och **resurs gruppen** för den virtuella käll dator som du vill validera routning från.
1. Välj den **virtuella datorn**, **nätverks gränssnittet** som är kopplat till den virtuella datorn och **käll-IP-adress** som tilldelats till det nätverks gränssnitt som du vill verifiera routning från.
1. Ange **IP-adressen till målet** som du vill validera routning till.
1. Välj **nästa hopp**.
1. Efter en kort vänte tid returneras information som anger nästa hopp typ och ID för den väg som dirigerade trafiken. Läs mer om de typer av nästa hopp som du ser i [Översikt över routning](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Verifiera routning mellan två slut punkter – kommandon

* Azure CLI: [AZ Network Watcher show-Next-hop](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* PowerShell: [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter i routningstabeller och vägar måste ditt konto tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som har tilldelats lämpliga åtgärder i följande tabell:

| Åtgärd                                                          |   Namn                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft. Network/routeTables/Read                              |   Läsa en routningstabell                                    |
| Microsoft. Network/routeTables/Write                             |   Skapa eller uppdatera en routningstabell                        |
| Microsoft. Network/routeTables/Delete                            |   Ta bort en routningstabell                                  |
| Microsoft. Network/routeTables/JOIN/åtgärd                       |   Associera en routningstabell till ett undernät                   |
| Microsoft. Network/routeTables/routes/Read                       |   Läs en väg                                          |
| Microsoft. Network/routeTables/routes/Write                      |   Skapa eller uppdatera en väg                              |
| Microsoft. Network/routeTables/routes/Delete                     |   Ta bort en väg                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Hämta den effektiva routningstabellen för ett nätverks gränssnitt |
| Microsoft. Network/networkWatchers/nextHop/Action                |   Hämtar nästa hopp från en virtuell dator                           |

## <a name="next-steps"></a>Nästa steg

* Skapa en routningstabell med [PowerShell](powershell-samples.md) -eller [Azure CLI](cli-samples.md) -exempel skript eller med Azure [Resource Manager-mallar](template-samples.md)<br>
* Skapa och tillämpa [Azure policy](policy-samples.md) för virtuella nätverk

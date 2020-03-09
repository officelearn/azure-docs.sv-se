---
title: Lägga till, ändra eller ta bort ett Azure Virtual Network-undernät
titlesuffix: Azure Virtual Network
description: Lär dig hur du lägger till, ändrar eller tar bort ett virtuellt nätverks under nät i Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: de80094c3fd2df7d2f8b32d1e968e9bebea847a1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357842"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Lägga till, ändra eller ta bort ett virtuellt nätverks under nät

Lär dig hur du lägger till, ändrar eller tar bort ett virtuellt nätverks under nät. Alla Azure-resurser som distribueras till ett virtuellt nätverk distribueras till ett undernät i ett virtuellt nätverk. Om du inte har använt virtuella nätverk tidigare kan du lära dig mer om dem i det [virtuella nätverkets översikt](virtual-networks-overview.md) eller genom att slutföra en [själv studie kurs](quick-create-portal.md). Information om hur du skapar, ändrar eller tar bort ett virtuellt nätverk finns i [hantera ett virtuellt nätverk](manage-virtual-network.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande åtgärder innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto kan du registrera dig för ett [kostnads fritt utvärderings konto](https://azure.microsoft.com/free).
- Om du använder portalen öppnar du https://portal.azure.comoch loggar in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. I den här självstudien krävs Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder kommando rads kommandon i Azure för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. I den här självstudien krävs Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.

Det konto som du loggar in på eller ansluta till Azure med måste tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [behörigheter](#permissions).

## <a name="add-a-subnet"></a>Lägga till ett undernät

1. Ange *virtuella nätverk* i sökrutan i rutan Sök högst upp i portalen. När **virtuella nätverk** visas i Sök resultaten väljer du det.
2. I listan med virtuella nätverk väljer du det virtuella nätverk som du vill lägga till ett undernät i.
3. Under **INSTÄLLNINGAR** väljer du **Undernät**.
4. Välj **+ undernät**.
5. Ange värden för följande parametrar:
   - **Namn**: namnet måste vara unikt inom det virtuella nätverket. För maximal kompatibilitet med andra Azure-tjänster rekommenderar vi att du använder en bokstav som det första tecknet i namnet. Azure Application Gateway kommer till exempel inte att distribueras till ett undernät som har ett namn som börjar med en siffra.
   - **Adress intervall**: intervallet måste vara unikt inom adress utrymmet för det virtuella nätverket. Intervallet får inte överlappa andra under näts adress intervall inom det virtuella nätverket. Adress utrymmet måste anges med hjälp av CIDR-notering (Classless Inter-Domain routing). I ett virtuellt nätverk med adress utrymmet 10.0.0.0/16 kan du till exempel definiera ett undernäts adress utrymme på 10.0.0.0/24. Det minsta intervallet du kan ange är/29, vilket ger åtta IP-adresser för under nätet. Azure reserverar den första och sista adressen i varje undernät för protokoll avvikelse. Tre ytterligare adresser är reserverade för användning av Azure-tjänster. Det innebär att du kan definiera ett undernät med ett/29-adressintervall som resulterar i tre användbara IP-adresser i under nätet. Om du planerar att ansluta ett virtuellt nätverk till en VPN-gateway måste du skapa ett Gateway-undernät. Läs mer om [vissa adress intervall för gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Du kan ändra adress intervallet när under nätet har lagts till under vissa förhållanden. Information om hur du ändrar ett adress intervall för ett undernät finns i [ändra under näts inställningar](#change-subnet-settings).
   - **Nätverks säkerhets grupp**: du kan koppla noll eller en befintlig nätverks säkerhets grupp till ett undernät för att filtrera inkommande och utgående nätverks trafik för under nätet. Nätverks säkerhets gruppen måste finnas i samma prenumeration och på samma plats som det virtuella nätverket. Lär dig mer om [nätverks säkerhets grupper](security-overview.md) och [hur du skapar en nätverks säkerhets grupp](tutorial-filter-network-traffic.md).
   - **Routningstabell: du**kan koppla noll eller en befintlig routningstabell till ett undernät för att styra routning av nätverks trafik till andra nätverk. Routningstabellen måste finnas i samma prenumeration och på samma plats som det virtuella nätverket. Lär dig mer om [Azure-routning](virtual-networks-udr-overview.md) och [hur du skapar en](tutorial-create-route-table-portal.md) routningstabell
   - **Tjänst slut punkter:** Ett undernät kan ha noll eller flera tjänst slut punkter aktiverade för den. Om du vill aktivera en tjänst slut punkt för en tjänst väljer du den tjänst eller de tjänster som du vill aktivera tjänst slut punkter för från listan över **tjänster** . Platsen konfigureras automatiskt för en slut punkt. Som standard konfigureras tjänstens slut punkter för det virtuella nätverkets region. För att Azure Storage för att stödja regionala failover-scenarier konfigureras slut punkter automatiskt till [Azure-kopplade regioner](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
   
       Om du vill ta bort en tjänst slut punkt avmarkerar du den tjänst som du vill ta bort tjänstens slut punkt för. Om du vill veta mer om tjänst slut punkter och de tjänster som de kan aktive ras för, se [Översikt över tjänst slut punkter i virtuella nätverk](virtual-network-service-endpoints-overview.md). När du har aktiverat en tjänst slut punkt för en tjänst måste du också aktivera nätverks åtkomst för under nätet för en resurs som skapats med tjänsten. Om du till exempel aktiverar tjänstens slut punkt för *Microsoft. Storage*måste du också aktivera nätverks åtkomst till alla Azure Storage konton som du vill bevilja nätverks åtkomst till. Mer information om hur du aktiverar nätverks åtkomst till undernät som en tjänst slut punkt är aktive rad för finns i dokumentationen för den enskilda tjänsten som du har aktiverat tjänstens slut punkt för.

     Om du vill kontrol lera att en tjänst slut punkt är aktive rad för ett undernät visar du [effektiva vägar](diagnose-network-routing-problem.md) för alla nätverks gränssnitt i under nätet. När en slut punkt har kon figurer ATS visas en *standard* väg med adressprefix för tjänsten och en NextHopType av **VirtualNetworkServiceEndpoint**. Mer information om routning finns i [routning – översikt](virtual-networks-udr-overview.md).
   - **Under näts delegering:** Ett undernät kan ha noll till flera delegeringar aktiverade för det. Under näts delegering ger explicita behörigheter till tjänsten för att skapa tjänstespecifika resurser i under nätet med en unik identifierare när tjänsten distribueras. Om du vill delegera för en tjänst väljer du den tjänst som du vill delegera till från listan **tjänster** .

6. Om du vill lägga till under nätet i det virtuella nätverk som du har valt väljer du **OK**.

**Kommandon**

- Azure CLI: [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet)
- PowerShell: [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Ändra under näts inställningar

1. Ange *virtuella nätverk* i sökrutan i rutan Sök högst upp i portalen. När **virtuella nätverk** visas i Sök resultaten väljer du det.
2. I listan med virtuella nätverk väljer du det virtuella nätverk som innehåller det undernät som du vill ändra inställningarna för.
3. Under **INSTÄLLNINGAR** väljer du **Undernät**.
4. I listan över undernät väljer du det undernät som du vill ändra inställningarna för. Du kan ändra följande inställningar:

    - **Adress intervall:** Om inga resurser har distribuerats i under nätet kan du ändra adress intervallet. Om det finns några resurser i under nätet måste du antingen flytta resurserna till ett annat undernät eller ta bort dem från under nätet först. De steg du tar för att flytta eller ta bort en resurs varierar beroende på resursen. Läs dokumentationen för varje resurs typ som du vill flytta eller ta bort om du vill lära dig att flytta eller ta bort resurser i undernät. Se begränsningarna för **adress intervall** i steg 5 i [Lägg till ett undernät](#add-a-subnet).
    - **Användare**: du kan styra åtkomsten till under nätet genom att använda inbyggda roller eller dina egna anpassade roller. Mer information om hur du tilldelar roller och användare åtkomst till under nätet finns i [använda roll tilldelning för att hantera åtkomst till dina Azure-resurser](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Nätverks säkerhets grupp** och **routningstabell**: se steg 5 i [Lägg till ett undernät](#add-a-subnet).
    - **Tjänst slut punkter**: se tjänst slut punkter i steg 5 i [Lägg till ett undernät](#add-a-subnet). När du aktiverar en tjänst slut punkt för ett befintligt undernät kontrollerar du att inga kritiska aktiviteter körs på någon resurs i under nätet. Tjänst slut punkter växlar vägar på varje nätverks gränssnitt i under nätet från att använda standard väg med adressprefixet *0.0.0.0/0* och nästa hopp typ av *Internet*, för att använda en ny väg med adressprefix för tjänsten och en nästa hopp typ för *VirtualNetworkServiceEndpoint*. Under växeln kan alla öppna TCP-anslutningar avslutas. Tjänstens slut punkt aktive ras inte förrän trafik flöden till tjänsten för alla nätverks gränssnitt har uppdaterats med den nya vägen. Mer information om routning finns i [routning – översikt](virtual-networks-udr-overview.md).
    - **Under näts delegering:** Se tjänst slut punkter i steg 5 i [Lägg till ett undernät](#add-a-subnet). Under näts delegering kan ändras till noll eller flera delegeringar som är aktiverade för den. Om en resurs för en tjänst redan har distribuerats i under nätet går det inte att lägga till eller ta bort under nät delegering förrän alla resurser för tjänsten tas bort. Om du vill delegera för en annan tjänst väljer du den tjänst som du vill delegera till från listan **tjänster** .
5. Välj **Spara**.

**Kommandon**

- Azure CLI: [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Ta bort ett undernät

Du kan bara ta bort ett undernät om det inte finns några resurser i under nätet. Om det finns resurser i under nätet måste du ta bort de resurser som finns i under nätet innan du kan ta bort under nätet. De steg du tar för att ta bort en resurs varierar beroende på resursen. Läs dokumentationen för varje resurs typ som du vill ta bort för att lära dig hur du tar bort resurser i undernät.

1. Ange *virtuella nätverk* i sökrutan i rutan Sök högst upp i portalen. När **virtuella nätverk** visas i Sök resultaten väljer du det.
2. I listan med virtuella nätverk väljer du det virtuella nätverk som innehåller det undernät som du vill ta bort.
3. Under **INSTÄLLNINGAR** väljer du **Undernät**.
4. I listan över undernät väljer du **...** , till höger, för det undernät som du vill ta bort
5. Välj **ta bort**och välj sedan **Ja**.

**Kommandon**

- Azure CLI: [AZ Network VNet Subnet Delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter på undernät måste ditt konto tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som har tilldelats lämpliga åtgärder i följande tabell:

|Åtgärd                                                                   |   Namn                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft. Network/virtualNetworks/subnets/Read                           |   Läs ett undernät för virtuellt nätverk              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Skapa eller uppdatera ett virtuellt nätverks under nät  |
|Microsoft. Network/virtualNetworks/subnets/Delete                         |   Ta bort ett undernät för virtuellt nätverk            |
|Microsoft. Network/virtualNetworks/subnets/Join/Action                    |   Anslut till ett virtuellt nätverk                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Aktivera en tjänst slut punkt för ett undernät     |
|Microsoft. Network/virtualNetworks/subnets/virtualMachines/Read           |   Hämta de virtuella datorerna i ett undernät       |

## <a name="next-steps"></a>Nästa steg

- Skapa ett virtuellt nätverk och undernät med [PowerShell](powershell-samples.md) -eller [Azure CLI](cli-samples.md) -exempel skript eller med hjälp av Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tillämpa [Azure policy](policy-samples.md) för virtuella nätverk

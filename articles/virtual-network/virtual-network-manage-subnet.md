---
title: Lägga till, ändra eller ta bort ett virtuellt Azure-nätverksundernät
titlesuffix: Azure Virtual Network
description: Lär dig mer om att lägga till, ändra eller ta bort ett virtuellt nätverksundernät i Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: b4fd3d7cd1d25415ba5938586aee448bdc512aa7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58100470"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Lägga till, ändra eller ta bort ett virtuellt nätverksundernät

Lär dig mer om att lägga till, ändra eller ta bort ett virtuellt nätverksundernät. Alla Azure-resurser som distribueras till ett virtuellt nätverk har distribuerats i ett undernät inom ett virtuellt nätverk. Om du är nybörjare på virtuella nätverk kan du läsa mer om dem i den [översikt över virtuella nätverk](virtual-networks-overview.md) eller genom att slutföra en [självstudien](quick-create-portal.md). För att skapa, ändra, eller ta bort ett virtuellt nätverk kan du läsa [hantera ett virtuellt nätverk](manage-virtual-network.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande uppgifter innan du slutför stegen i ett avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppnar du https://portal.azure.com, och logga in med ditt Azure-konto.
- Om du utför uppgifterna i den här artikeln med hjälp av PowerShell-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell-Modulversion 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du utför uppgifterna i den här artikeln med hjälp av Azure-kommandoradsgränssnittet (CLI)-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här självstudien krävs Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också behöva köra `az login` att skapa en anslutning till Azure.

Kontot du loggar in på eller ansluta till Azure med, måste tilldelas den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas de åtgärder som anges i [behörigheter ](#permissions).

## <a name="add-a-subnet"></a>Lägga till ett undernät

1. Ange i sökrutan överst på portalen *virtuella nätverk* i sökrutan. När **virtuella nätverk** visas i sökresultaten, markerar du den.
2. Välj det virtuella nätverket som du vill lägga till ett undernät till i listan med virtuella nätverk.
3. Under **INSTÄLLNINGAR** väljer du **Undernät**.
4. Välj **+ undernät**.
5. Ange värden för följande parametrar:
   - **Namn**: Namnet måste vara unikt inom det virtuella nätverket. För maximal kompatibilitet med andra Azure-tjänster rekommenderar vi med en bokstav som det första tecknet i namnet. Azure Application Gateway inte kommer att exempelvis distribuera i ett undernät som har ett namn som börjar med en siffra.
   - **Adressintervall**: Intervallet måste vara unikt inom adressutrymmet för det virtuella nätverket. Intervallet får inte överlappa med andra undernät adressintervall inom det virtuella nätverket. Adressutrymmet måste anges med hjälp av Classless Inter-Domain Routing CIDR-notering. Du kan till exempel definiera ett adressutrymme för undernätet 10.0.0.0/24 i ett virtuellt nätverk med adressutrymmet 10.0.0.0/16. Den minsta intervall som du kan ange är /29, vilket möjliggör åtta IP-adresser för undernätet. Azure reserverar de första och sista adressen i varje undernät för protokollöverensstämmelse. Tre ytterligare adresser är reserverade för användning av Azure-tjänsten. Därför kan definiera ett undernät med ett/29 åtgärda resulterar i tre användbara IP-adresser i undernätet. Om du planerar att ansluta ett virtuellt nätverk till en VPN-gateway, måste du skapa ett gateway-undernät. Läs mer om [specifik adressintervallet överväganden för gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Du kan ändra adressintervallet när undernätet har lagts till, vissa villkor. Läs hur du ändrar ett adressintervall för undernätet i [ändra undernätsinställningar](#change-subnet-settings).
   - **Nätverkssäkerhetsgrupp**: Du kan associera noll eller en befintlig nätverkssäkerhetsgrupp till ett undernät för att filtrera inkommande och utgående nätverkstrafik för undernätet. Nätverkssäkerhetsgruppen måste finnas i samma prenumeration och plats som det virtuella nätverket. Läs mer om [nätverkssäkerhetsgrupper](security-overview.md) och [hur du skapar en nätverkssäkerhetsgrupp](tutorial-filter-network-traffic.md).
   - **Routningstabellen**: Du kan associera noll eller en befintlig routningstabellen till ett undernät för att styra trafikdirigering till andra nätverk. Routningstabellen måste finnas i samma prenumeration och plats som det virtuella nätverket. Läs mer om [Azure routning](virtual-networks-udr-overview.md) och [så här skapar du en routningstabell](tutorial-create-route-table-portal.md)
   - **Tjänstslutpunkter:** Ett undernät kan ha noll eller flera aktiverat Tjänsteslutpunkter för den. Om du vill aktivera en tjänstslutpunkt för en tjänst, Välj den eller de tjänster som du vill aktivera Tjänsteslutpunkter för från den **Services** lista. Platsen konfigureras automatiskt för en slutpunkt. Tjänstslutpunkter konfigureras som standard för det virtuella nätverkets region. För Azure Storage, för att stödja regionala redundansscenarier konfigureras slutpunkter automatiskt till [parade Azure-regioner](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
   - **Undernät delegering:** Ett undernät kan ha noll till flera delegeringar aktiverat. Undernät delegering ger behörighet till tjänsten för att skapa tjänstspecifika resurser i undernätet med hjälp av en unik identifierare när du distribuerar tjänsten. Om du vill delegera för en tjänst, markera den tjänst som du vill delegera till från den **Services** lista.

       Om du vill ta bort en tjänstslutpunkt, avmarkerar du tjänsten som du vill ta bort tjänstens slutpunkt för. Läs mer om tjänstslutpunkter och de tjänster som de kan aktiveras för i [virtuella nätverk översikt över tjänstslutpunkter](virtual-network-service-endpoints-overview.md). När du har aktiverat en tjänstslutpunkt för en tjänst måste du också aktivera nätverksåtkomst för undernätet för en resurs som skapas med tjänsten. Exempel: Om du aktiverar tjänstslutpunkten för *Microsoft.Storage*, måste du också aktivera nätverksåtkomst till alla Azure Storage-konton som du vill bevilja åtkomst till. Mer information om hur du aktiverar åtkomst till undernät som en tjänstslutpunkt är aktiverad för finns i dokumentationen för den enskilda tjänsten aktiverad tjänstslutpunkt för.

     För att verifiera att en tjänstslutpunkt har aktiverats för ett undernät kan visa den [gällande routningar](diagnose-network-routing-problem.md) för alla nätverksgränssnitt i undernätet. När en slutpunkt har konfigurerats kan du se en *standard* väg med adressprefixet för tjänsten och nextHopType **VirtualNetworkServiceEndpoint**. Mer information om routning finns [routningsöversikten](virtual-networks-udr-overview.md).
6. Om du vill lägga till undernätet till det virtuella nätverket som du har valt, Välj **OK**.

**Kommandon**

- Azure CLI: [skapa az network vnet-undernät](/cli/azure/network/vnet/subnet)
- PowerShell: [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Ändra inställningar för undernät

1. Ange i sökrutan överst på portalen *virtuella nätverk* i sökrutan. När **virtuella nätverk** visas i sökresultaten, markerar du den.
2. Välj det virtuella nätverket som innehåller det undernät du vill ändra inställningarna för listan över virtuella nätverk.
3. Under **INSTÄLLNINGAR** väljer du **Undernät**.
4. Välj det undernät du vill ändra inställningarna för i listan med undernät. Du kan ändra följande inställningar:

    - **Adressintervall:** Om inga resurser har distribuerats i undernätet, kan du ändra adressintervallet. Om det finns några resurser i undernätet, måste du antingen flytta resurserna till ett annat undernät eller ta bort dem från undernätet först. Vilka steg du utför för att flytta eller ta bort en resurs kan variera beroende på resurs. Läs i dokumentationen för varje resurstyp som du vill flytta eller ta bort information om hur du flytta eller ta bort resurser som finns i undernät. Se begränsningarna för **adressintervall** i steg 5 i [lägga till ett undernät](#add-a-subnet).
    - **Användare**: Du kan styra åtkomsten till undernätet med hjälp av inbyggda roller eller dina egna anpassade roller. Läs mer om att tilldela roller och användare åtkomst till undernätet i [använda rolltilldelning för att hantera åtkomst till dina Azure-resurser](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Nätverkssäkerhetsgrupp** och **routningstabellen**: Se steg 5 i [lägga till ett undernät](#add-a-subnet).
    - **Tjänstslutpunkter**: Se tjänstslutpunkter i steg 5 i [lägga till ett undernät](#add-a-subnet). När du aktiverar en tjänstslutpunkt för ett befintligt undernät, se till att inga kritiska uppgifter körs på alla resurser i undernätet. Tjänstslutpunkter växla vägar på varje nätverksgränssnitt i undernätet från att använda standardvägen med den *0.0.0.0/0* -prefix och nexthop-typen *Internet*, med en ny väg med den -adressprefix för tjänsten och en nästa hopptyp av *VirtualNetworkServiceEndpoint*. Under växeln avslutas eventuella öppna TCP-anslutningar. Tjänsteslutpunkt aktiveras inte förrän trafikflöden till tjänsten för alla nätverksgränssnitt har uppdaterats med den nya vägen. Mer information om routning finns [routningsöversikten](virtual-networks-udr-overview.md).
    - **Undernät delegering:** Se tjänstslutpunkter i steg 5 i [lägga till ett undernät](#add-a-subnet). Undernät delegering kan ändras till noll eller flera delegeringar aktiverat. Om en resurs för en tjänst har redan distribuerats i undernätet, undernät delegering kan inte tas bort förrän alla resurser för tjänsten har tagits bort. Om du vill delegera för en annan tjänst, markera den tjänst som du vill delegera till från den **Services** lista.
5. Välj **Spara**.

**Kommandon**

- Azure CLI: [az network vnet-undernät update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Ta bort ett undernät

Du kan ta bort ett undernät om det finns inga resurser i undernätet. Om det finns resurser i undernätet, måste du ta bort de resurser som finns i undernät innan du kan ta bort undernätet. Vilka steg du utför för att ta bort en resurs kan variera beroende på resurs. Om du vill lära dig mer om att ta bort resurser som finns i undernät, Läs i dokumentationen för varje resurstyp som du vill ta bort.

1. Ange i sökrutan överst på portalen *virtuella nätverk* i sökrutan. När **virtuella nätverk** visas i sökresultaten, markerar du den.
2. Välj det virtuella nätverket som innehåller det undernät som du vill ta bort från listan över virtuella nätverk.
3. Under **INSTÄLLNINGAR** väljer du **Undernät**.
4. Välj i listan med undernät **...** , till höger för undernätet du vill ta bort
5. Välj **ta bort**, och välj sedan **Ja**.

**Kommandon**

- Azure CLI: [az network vnet ta bort](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Behörigheter

För att utföra uppgifter på undernät måste ditt konto tilldelas till den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassade](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som tilldelas åtgärderna som visas i följande tabell:

|Åtgärd                                                                   |   Namn                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Läsa ett virtuellt nätverksundernät              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Skapa eller uppdatera ett virtuellt nätverksundernät  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Ta bort ett virtuellt nätverksundernät            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Ansluta till ett virtuellt nätverk                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Aktivera en tjänstslutpunkt för ett undernät     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Hämta de virtuella datorerna i ett undernät       |

## <a name="next-steps"></a>Nästa steg

- Skapa ett virtuellt nätverk och undernät med [PowerShell](powershell-samples.md) eller [Azure CLI](cli-samples.md) exempel på skript eller genom att använda Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tillämpa [Azure policy](policy-samples.md) för virtuella nätverk

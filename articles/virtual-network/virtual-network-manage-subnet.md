---
title: Lägga till, ändra eller ta bort ett virtuellt Azure-nätverksundernät
titlesuffix: Azure Virtual Network
description: Lär dig hur du lägger till, ändrar eller tar bort ett virtuellt nätverksundernät i Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: e5b78969f6b4315bb02b3f4152c6eeab94adddb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246950"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Lägga till, ändra eller ta bort ett virtuellt nätverksundernät

Lär dig hur du lägger till, ändrar eller tar bort ett virtuellt nätverksundernät. Alla Azure-resurser som distribueras till ett virtuellt nätverk distribueras till ett undernät i ett virtuellt nätverk. Om du inte har tidigare i virtuella nätverk kan du läsa mer om dem i [översikten över virtuella nätverk](virtual-networks-overview.md) eller genom att slutföra en [snabbstart](quick-create-portal.md). Mer information om hur du hanterar ett virtuellt nätverk finns i [Skapa, ändra eller ta bort ett virtuellt nätverk](manage-virtual-network.md).

## <a name="before-you-begin"></a>Innan du börjar

Om du inte har ett, konfigurera ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Slutför sedan en av dessa uppgifter innan du startar stegen i något avsnitt i den här artikeln: 

- **Portalanvändare**: Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

- **PowerShell-användare:** Kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller kör PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Leta reda på listrutan **Välj miljö** i webbläsaren Azure Cloud Shell och välj sedan **PowerShell** om den inte redan är markerad.

    Om du kör PowerShell lokalt använder du Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az.Network` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Kör `Connect-AzAccount` också för att skapa en anslutning med Azure.

- **CLI-användare (Azure Command-line Interface)**: Kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller kör CLI från datorn. Använd Azure CLI version 2.0.31 eller senare om du kör Azure CLI lokalt. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Kör `az login` också för att skapa en anslutning med Azure.

Kontot som du loggar in på, eller ansluter till Azure med, måste tilldelas [rollen Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [Behörigheter](#permissions).

## <a name="add-a-subnet"></a>Lägga till ett undernät

1. Gå till [Azure-portalen](https://portal.azure.com) för att visa dina virtuella nätverk. Sök efter och välj **Virtuella nätverk**.

2. Markera namnet på det virtuella nätverk som du vill lägga till ett undernät i.

3. Välj **Undernät** > **undernät i** **Inställningar**.

4. Ange värden för följande inställningar i dialogrutan **Lägg till undernät:**

    | Inställning | Beskrivning |
    | --- | --- |
    | **Namn** | Namnet måste vara unikt inom det virtuella nätverket. För maximal kompatibilitet med andra Azure-tjänster rekommenderar vi att du använder en bokstav som det första tecknet i namnet. Azure Application Gateway distribueras till exempel inte till ett undernät som har ett namn som börjar med ett nummer. |
    | **Adressintervall** | <p>Intervallet måste vara unikt inom adressutrymmet för det virtuella nätverket. Intervallet kan inte överlappa med andra undernätsadressintervall i det virtuella nätverket. Adressutrymmet måste anges med hjälp av CIDR-notation (Classless Inter-Domain Routing).</p><p>I ett virtuellt nätverk med adressutrymme *10.0.0.0/16*kan du till exempel definiera ett undernätsadressutrymme *på 10.0.0.0/22*. Det minsta område du kan ange är */29*, som innehåller åtta IP-adresser för undernätet. Azure reserverar den första och sista adressen i varje undernät för protokollkonformance. Ytterligare tre adresser är reserverade för Azure-tjänstanvändning. Därför resulterar en definition av ett undernät med ett */29-adressintervall* i tre användbara IP-adresser i undernätet.</p><p>Om du planerar att ansluta ett virtuellt nätverk till en VPN-gateway måste du skapa ett gateway-undernät. Läs mer om [specifika överväganden om adressintervall för gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Du kan ändra adressintervallet när undernätet har lagts till under särskilda förhållanden. Mer information om hur du ändrar ett undernätsadressintervall finns i [Ändra inställningar för undernät](#change-subnet-settings).</p> |
    | **Säkerhetsgrupp för nätverk** | Om du vill filtrera inkommande och utgående nätverkstrafik för undernätet kan du koppla en befintlig nätverkssäkerhetsgrupp till ett undernät. Nätverkssäkerhetsgruppen måste finnas på samma prenumeration och plats som det virtuella nätverket. Läs mer om [nätverkssäkerhetsgrupper](security-overview.md) och [hur du skapar en nätverkssäkerhetsgrupp](tutorial-filter-network-traffic.md). |
    | **Routningstabell** | Om du vill styra nätverkstrafikroutning till andra nätverk kan du eventuellt associera en befintlig vägtabell till ett undernät. Flödestabellen måste finnas på samma prenumeration och plats som det virtuella nätverket. Läs mer om [Azure-routning](virtual-networks-udr-overview.md) och [hur du skapar en vägtabell](tutorial-create-route-table-portal.md). |
    | **Tjänstslutpunkter** | <p>Ett undernät kan eventuellt ha en eller flera tjänstslutpunkter aktiverade för det. Om du vill aktivera en tjänstslutpunkt för en tjänst väljer du den eller de tjänster som du vill aktivera tjänstslutpunkter för från listan **Tjänster.** Azure konfigurerar platsen automatiskt för en slutpunkt. Som standard konfigurerar Azure tjänstslutpunkterna för det virtuella nätverkets region. Azure konfigurerar automatiskt slutpunkter till [Azure-parade regioner](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) för Azure Storage för nationella redundansscenarier.</p><p>Om du vill ta bort en tjänstslutpunkt avmarkerar du den tjänst som du vill ta bort tjänstslutpunkten för. Mer information om tjänstslutpunkter och vilka tjänster de kan aktiveras för finns i slutpunkter för [virtuella nätverkstjänster.](virtual-network-service-endpoints-overview.md) När du har aktiverat en tjänstslutpunkt för en tjänst måste du också aktivera nätverksåtkomst för undernätet för en resurs som skapats med tjänsten. Om du till exempel aktiverar tjänstslutpunkten för **Microsoft.Storage**måste du också aktivera nätverksåtkomst till alla Azure Storage-konton som du vill bevilja nätverksåtkomst till. Om du vill aktivera nätverksåtkomst till undernät som en tjänstslutpunkt är aktiverad för läser du dokumentationen för den enskilda tjänst som du aktiverade tjänstslutpunkten för.</p><p>Om du vill verifiera att en tjänstslutpunkt är aktiverad för ett undernät visar du de [effektiva vägarna](diagnose-network-routing-problem.md) för alla nätverksgränssnitt i undernätet. När du konfigurerar en slutpunkt visas en *standardväg* med tjänstens adressprefix och en nästa hoptyp av **VirtualNetworkServiceEndpoint**. Mer information om routning finns i [Routning av virtuell nätverkstrafik](virtual-networks-udr-overview.md).</p> |
    | **Delegering av undernät** | Ett undernät kan eventuellt ha en eller flera delegationer aktiverade för det. Undernätsdelegering ger explicit behörighet till tjänsten för att skapa tjänstspecifika resurser i undernätet med hjälp av en unik identifierare under tjänstdistribution. Om du vill delegera för en tjänst väljer du den tjänst som du vill delegera till i listan **Tjänster.** |

5. Om du vill lägga till undernätet i det virtuella nätverk som du har markerat väljer du **OK**.

### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Ändra inställningar för undernät

1. Gå till [Azure-portalen](https://portal.azure.com) för att visa dina virtuella nätverk. Sök efter och välj **Virtuella nätverk**.

2. Markera namnet på det virtuella nätverk som innehåller det undernät som du vill ändra.

3. Välj **Undernät i** **Inställningar**.

4. Markera det undernät som du vill ändra inställningar för i listan över undernät.

5. Ändra någon av följande inställningar på undernätssidan:

    | Inställning | Beskrivning |
    | --- | --- |
    | **Adressintervall** | Om inga resurser distribueras i undernätet kan du ändra adressintervallet. Om det finns resurser i undernätet måste du antingen flytta resurserna till ett annat undernät eller ta bort dem från undernätet först. Vilka åtgärder du vidtar för att flytta eller ta bort en resurs varierar beroende på resursen. Om du vill veta hur du flyttar eller tar bort resurser som finns i undernät läser du dokumentationen för var och en av dessa resurstyper. Se begränsningarna för **adressintervall** i steg 4 i [Lägg till ett undernät](#add-a-subnet). |
    | **Användare** | Du kan styra åtkomsten till undernätet med hjälp av inbyggda roller eller egna anpassade roller. Mer information om hur du tilldelar roller och användare att komma åt undernätet finns i [Lägga till en rolltilldelning](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment). |
    | **Nätverkssäkerhetsgrupp** och **Routningstabell** | Se steg 4 [i Lägg till ett undernät](#add-a-subnet). |
    | **Tjänstslutpunkter** | <p>Se tjänstslutpunkter i steg 4 [i Lägg till ett undernät](#add-a-subnet). När du aktiverar en tjänstslutpunkt för ett befintligt undernät, se till att inga kritiska aktiviteter körs på någon resurs i undernätet. Tjänstslutpunkter växlar vägar i alla nätverksgränssnitt i undernätet. Tjänstslutpunkterna går från att använda standardvägen med prefixet *0.0.0.0/0/0* och nästa hopptyp av *Internet*till att använda en ny väg med tjänstens adressprefix och en nästa hopptyp för *VirtualNetworkServiceEndpoint*.</p><p>Under växeln kan alla öppna TCP-anslutningar avslutas. Tjänstslutpunkten är inte aktiverad förrän trafiken flödar till tjänsten för alla nätverksgränssnitt uppdateras med den nya vägen. Mer information om routning finns i [Routning av virtuell nätverkstrafik](virtual-networks-udr-overview.md).</p> |
    | **Delegering av undernät** | Se tjänstslutpunkter i steg 4 [i Lägg till ett undernät](#add-a-subnet). Undernätsdelegering kan ändras till noll eller flera delegeringar som är aktiverade för den. Om en resurs för en tjänst redan har distribuerats i undernätet kan det inte läggas till eller tas bort undernätsdelegering förrän alla resurser för tjänsten har tagits bort. Om du vill delegera för en annan tjänst väljer du den tjänst som du vill delegera till i listan **Tjänster.** |

6. Välj **Spara**.

### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Ta bort ett undernät

Du kan bara ta bort ett undernät om det inte finns några resurser i undernätet. Om resurserna finns i undernätet måste du ta bort dessa resurser innan du kan ta bort undernätet. Vilka åtgärder du vidtar för att ta bort en resurs varierar beroende på resursen. Om du vill veta hur du tar bort resurser som finns i undernät läser du dokumentationen för var och en av dessa resurstyper.

1. Gå till [Azure-portalen](https://portal.azure.com) för att visa dina virtuella nätverk. Sök efter och välj **Virtuella nätverk**.

2. Markera namnet på det virtuella nätverk som innehåller det undernät som du vill ta bort.

3. Välj **Undernät i** **Inställningar**.

4. Markera det undernät som du vill ta bort i listan över undernät.

5. Välj **Ta bort**och välj sedan **Ja** i bekräftelsedialogrutan.

### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk vnet undernät ta bort](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Ta bort-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter i undernät måste ditt konto tilldelas [rollen Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder i följande tabell:

|Åtgärd                                                                   |   Namn                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Läsa ett virtuellt nätverksundernät              |
|Microsoft.Network/virtualNetworks/undernät/skrivning                          |   Skapa eller uppdatera ett virtuellt nätverksundernät  |
|Microsoft.Network/virtualNetworks/subnät/borttagning                         |   Ta bort ett virtuellt nätverksundernät            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Gå med i ett virtuellt nätverk                     |
|Microsoft.Network/virtualNetworks/subnät/joinViaServiceEndpoint/åtgärd  |   Aktivera en tjänstslutpunkt för ett undernät     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read Microsoft.Network/virtualNetworks/subnets/virtualMachines/read Microsoft.Network/virtualNetworks/subnets/virtualMachines/read Microsoft.           |   Hämta de virtuella datorerna i ett undernät       |

## <a name="next-steps"></a>Nästa steg

- Skapa ett virtuellt nätverk och undernät med [powershell-](powershell-samples.md) eller [Azure CLI-exempelskript](cli-samples.md) eller använda Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tillämpa [Azure-principen](policy-samples.md) för virtuella nätverk

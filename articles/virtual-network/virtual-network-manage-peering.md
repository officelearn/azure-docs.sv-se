---
title: Skapa, ändra eller ta bort en Azure virtuell nätverkspeering | Microsoft Docs
description: Lär dig mer om att skapa, ändra eller ta bort vnet-peering.
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
ms.author: jdial;anavin
ms.openlocfilehash: 1b9807b587b6b52594133e8c792c72b21e8bd4ea
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503629"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Skapa, ändra eller ta bort en virtuell nätverkspeering

Lär dig mer om att skapa, ändra eller ta bort vnet-peering. Virtuell nätverkspeering gör att du kan ansluta virtuella nätverk via Azures stamnätverk. När de virtuella nätverken hanteras fortfarande som separata resurser. Om du inte har använt det virtuella nätverkets peering kan du läsa mer om den i den [översikt över peerkoppling av virtuella nätverk](virtual-network-peering-overview.md) eller genom att slutföra en [självstudien](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Innan du börjar

Utför följande uppgifter innan du slutför stegen i ett avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppnar https://portal.azure.com, och logga in med ett konto som har den [behörighet](#permissions) att arbeta med peer-kopplingar.
- Om du utför uppgifterna i den här artikeln med hjälp av PowerShell-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudiekursen kräver Azure PowerShell-modulen version 5.7.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också behöva köra `Connect-AzureRmAccount` med ett konto som har den [behörighet](#permissions) att arbeta med peering, om du vill skapa en anslutning till Azure.
- Om du utför uppgifterna i den här artikeln med hjälp av Azure-kommandoradsgränssnittet (CLI)-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här självstudien krävs Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också behöva köra `az login` med ett konto som har den [behörighet](#permissions) att arbeta med peering, om du vill skapa en anslutning till Azure.

Kontot du loggar in på eller ansluta till Azure med, måste tilldelas den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas de åtgärder som anges i [behörigheter ](#permissions).

## <a name="create-a-peering"></a>Skapa en peerkoppling

Innan du skapar en peering måste du bekanta dig med den [krav och begränsningar](#requirements-and-contstraints) och [behörighet](#permissions).

1. I sökrutan högst upp på Azure-portalen anger *virtuella nätverk* i sökrutan. När **virtuella nätverk** visas i sökresultaten, markerar du den. Markera inte **virtuella nätverk (klassiska)** om den visas i listan som du inte kan skapa en peer-kopplingen från ett virtuellt nätverk som distribueras via den klassiska distributionsmodellen.
2. Välj det virtuella nätverket i listan som du vill skapa en peerkoppling för.
3. Välj det virtuella nätverket som du vill skapa en peerkoppling för i listan med virtuella nätverk.
4. Under **inställningar**väljer **Peerings**.
5. Välj **+ Lägg till**. 
6. <a name="add-peering"></a>Ange eller Välj värden för följande inställningar:
    - **Namn:** namnet för peering måste vara unikt inom det virtuella nätverket.
    - **Distributionsmodell för virtuellt nätverk:** väljer du vilken distribution modellera det virtuella nätverket som du vill peerkoppla till har distribuerats via.
    - **Jag vet mitt resurs-ID:** om du har läsbehörighet till det virtuella nätverket som du vill peerkoppla till lämnar den här kryssrutan avmarkerad. Markera den här kryssrutan om du inte har läsbehörighet till det virtuella nätverket eller en prenumeration som du vill peerkoppla till. Ange fullständigt resurs-ID för det virtuella nätverket som du vill peerkoppla till i den **resurs-ID** rutan som visades när du har markerat kryssrutan. Resurs-ID du anger måste vara för ett virtuellt nätverk som finns i samma, eller [stöds olika](#requirements-and-constraints) Azure [region](https://azure.microsoft.com/regions) som den här virtuella nätverket. Fullständigt resurs-ID som liknar /subscriptions/<Id>/resourceGroups/ < resource-group-name > /providers/Microsoft.Network/virtualNetworks/ < virtual-network-name >. Du kan hämta resurs-ID för ett virtuellt nätverk genom att visa egenskaperna för ett virtuellt nätverk. Läs hur du visar egenskaperna för ett virtuellt nätverk i [hantera virtuella nätverk](manage-virtual-network.md#view-virtual-networks-and-settings).
    - **Prenumeration:** väljer den [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) för det virtuella nätverket som du vill peerkoppla till. En eller flera prenumerationer visas, beroende på hur många prenumerationer ditt konto har läsbehörighet till. När du har markerat den **resurs-ID** markerar du kryssrutan den här inställningen är inte tillgänglig.
    - **Virtuellt nätverk:** Välj det virtuella nätverk som du vill peerkoppla till. Du kan välja ett virtuellt nätverk som skapats via antingen Azure-distributionsmodellen. Om du vill välja ett virtuellt nätverk i en annan region måste du välja ett virtuellt nätverk i en [region som stöds](#cross-region). Du måste ha läsbehörighet till det virtuella nätverket att vara synliga i listan. Om ett virtuellt nätverk i listan, men nedtonade, kan det vara eftersom adressutrymmet för det virtuella nätverket överlappar adressutrymmet för det här virtuella nätverket. Om virtuellt nätverk adressutrymmen överlappar varandra, kan de inte peerkopplas. När du har markerat den **resurs-ID** markerar du kryssrutan den här inställningen är inte tillgänglig.
    - **Tillåt åtkomst till virtuellt nätverk:** Välj **aktiverad** (standard) om du vill aktivera kommunikation mellan de två virtuella nätverken. Om du aktiverar kommunikation mellan virtuella nätverk kan resurser som är anslutna till virtuella nätverken kan kommunicera med varandra med samma bandbredd och latens som om de var anslutna till samma virtuella nätverk. All kommunikation mellan resurser i de två virtuella nätverken är via Azure privat nätverk. Den **VirtualNetwork** tjänsttagg för nätverkssäkerhetsgrupper omfattar det virtuella nätverket och peer-kopplade virtuella nätverket. Läs mer om network security group tjänsttaggar i [nätverkssäkerhetsöversikt](security-overview.md#service-tags). Välj **inaktiverad** om du inte vill att trafik kan flöda till peerkopplade virtuella nätverk. Du kan välja **inaktiverad** om du har peer-kopplade ett virtuellt nätverk med ett annat virtuellt nätverk, men ibland vill du inaktivera trafikflödet mellan de två virtuella nätverken. Du kan hitta aktivering/inaktivering är mycket enklare än att ta bort och återskapa peer-kopplingar. När den här inställningen inaktiveras trafiken inte mellan peerkopplade virtuella nätverk.
    - **Tillåt vidarebefordrad trafik:** den här kryssrutan för att tillåta trafik *vidarebefordras* genom ett nätverks virtuella installation i ett virtuellt nätverk (som inte kommer från det virtuella nätverket) till flödet för att det här virtuella nätverket via en peer-koppling . Anta exempelvis att tre virtuella nätverk som heter Spoke1 och Spoke2 Hub. En peer-koppling finns mellan varje eker-nätverk och det virtuella navnätverket, men peerings finns inte mellan virtuella ekernätverk. En virtuell nätverksinstallation distribueras i det virtuella navnätverket och användardefinierade vägar tillämpas till varje eker-nätverk som dirigerar trafik mellan undernät via den virtuella nätverksinstallationen. Om den här kryssrutan inte är markerad för peering mellan varje eker-nätverk och det virtuella navnätverket trafiken inte mellan virtuella ekernätverk eftersom hubben är vidarebefordrar trafik mellan virtuella nätverk. Med att aktivera den här funktionen kan vidarebefordrad trafik via peer-kopplingen, skapas inte någon användardefinierade vägar eller virtuella nätverksinstallationer. Användardefinierade vägar och virtuella nätverksinstallationer skapas separat. Lär dig mer om [användardefinierade vägar](virtual-networks-udr-overview.md#user-defined). Du behöver inte markera den här inställningen om trafik vidarebefordras mellan virtuella nätverk via Azure VPN Gateway.
    - **Tillåt gatewayöverföring:** den här kryssrutan om du har en virtuell nätverksgateway som är kopplade till det här virtuella nätverket och vill tillåta trafik från det peer-kopplade virtuella nätverket kan flöda via gatewayen. Det här virtuella nätverket kan exempelvis vara kopplad till ett lokalt nätverk via en virtuell nätverksgateway. Det kan vara en ExpressRoute eller VPN-gateway. Markera den här rutan tillåter trafik från det peer-kopplade virtuella nätverket kan flöda via gatewayen ansluten till det här virtuella nätverket till det lokala nätverket. Om du markerar kryssrutan kan inte det peer-kopplade virtuella nätverket ha en konfigurerad gateway. Peer-kopplade virtuella nätverket måste ha den **Använd fjärrgateway** kryssrutan markerad när du konfigurerar peer-kopplingen från det virtuella nätverket till det här virtuella nätverket. Om du lämnar den här kryssrutan avmarkerad (standard), nätverkstrafiken från peer-kopplade virtuella nätverket fortfarande flöden till det här virtuella nätverket, men det går inte att flöda via en virtuell nätverksgateway som är kopplade till det här virtuella nätverket. Om peer-kopplingen ligger mellan ett virtuellt nätverk (Resource Manager) och ett virtuellt nätverk (klassisk), måste gatewayen vara i det virtuella nätverket (Resource Manager). Du kan inte aktivera det här alternativet om du peer virtuellt nätverk i olika regioner.
    
        Förutom vidarebefordrar trafik till ett lokalt nätverk, kan en VPN-gateway vidarebefordra nätverkstrafik mellan virtuella nätverk som peer-kopplade med det virtuella nätverket som gatewayen är i, utan att de virtuella nätverken behöva peer-kopplas med varandra. Med hjälp av en VPN-gateway för att vidarebefordra trafik är användbart när du vill använda en VPN-gateway i ett nav (se NAV och ekrar exemplet som beskrivs för **Tillåt vidarebefordrad trafik**) virtuellt nätverk kan dirigera trafik mellan virtuella ekernätverk som inte är peer-kopplas med varandra. Läs mer om att tillåta användning av en gateway för överföring i [konfigurera en VPN-gateway för överföring i en virtuell nätverkspeering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Det här scenariot kräver implementera användardefinierade vägar som anger den virtuella nätverksgatewayen som nästa hopptyp. Lär dig mer om [användardefinierade vägar](virtual-networks-udr-overview.md#user-defined). Du kan bara ange en VPN-gateway som nexthop-typen i en användardefinierad väg, du kan inte ange en ExpressRoute-gateway som nästa hopptyp i en användardefinierad väg. Du kan inte aktivera det här alternativet om du peer virtuellt nätverk i olika regioner.

    - **Använd fjärrgateway:** den här kryssrutan om du vill tillåta trafik från det här virtuella nätverket kan flöda via en virtuell nätverksgateway kopplade till det virtuella nätverket du peer med. Det virtuella nätverket som du peer-med har exempelvis en VPN-gateway som är ansluten som aktiverar kommunikation till ett lokalt nätverk.  Markera den här rutan tillåter trafik från det här virtuella nätverket kan flöda via VPN-gateway som är kopplade till det peer-kopplade virtuella nätverket. Om du markerar kryssrutan det peer-kopplade virtuella nätverket måste ha en virtuell nätverksgateway som är kopplade till den och måste ha den **Tillåt gatewayöverföring** kryssrutan är markerad. Om du lämnar du rutan avmarkerad (standard), trafiken från det peer-kopplade virtuella nätverket kan fortfarande flöda till den här virtuella nätverket, men det går inte att flow via en virtuell nätverksgateway kopplade till den här virtuella nätverket. 
    Endast en peering för det här virtuella nätverket kan ha den här inställningen är aktiverad.

        Du kan inte använda fjärrgatewayer om du redan har en konfigurerad gateway i det virtuella nätverket. Du kan inte aktivera det här alternativet om du peer virtuellt nätverk i olika regioner. Mer information om hur du använder en gateway för överföring finns [konfigurera en VPN-gateway för överföring i vnet-peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

7. Välj **OK** att lägga till peer-kopplingen till det virtuella nätverket som du har valt.

Stegvisa instruktioner för att implementera peering mellan virtuella nätverk i olika prenumerationer och distributionsmodeller finns i [nästa steg](#next-steps). 


### <a name="commands"></a>Kommandon

- **Azure CLI**: [skapa az network vnet-peering](/cli/azure/network/vnet/peering#create)
- **PowerShell**: [lägga till AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Visa eller ändra peering-inställningar

Innan du ändrar en peer-koppling, kan du bekanta dig med den [krav och begränsningar](#requirements-and-contstraints) och [behörighet](#permissions).

1. Ange i sökrutan överst på portalen *virtuella nätverk* i sökrutan. När **virtuella nätverk** visas i sökresultaten, markerar du den. Markera inte **virtuella nätverk (klassiska)** om den visas i listan som du inte kan skapa en peer-kopplingen från ett virtuellt nätverk som distribueras via den klassiska distributionsmodellen.
2. Välj det virtuella nätverket i listan som du vill ändra peering-inställningar för.
3. Välj det virtuella nätverket som du vill ändra peering-inställningar för listan över virtuella nätverk.
4. Under **inställningar**väljer **Peerings**.
5. Välja den peering som du vill visa eller ändra inställningar för.
6. Ändra lämpliga inställningar. Läs mer om alternativen för varje inställning i [steg 6](#add-peering) för Create som en peer-koppling. 
7. Välj **Spara**.

**Kommandon**

- **Azure CLI**: [az network vnet peering list](/cli/azure/network/vnet/peering#az_network_vnet_peering_list) att lista peerings för ett virtuellt nätverk, [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show) att visa inställningarna för en specifik peering och [az nätverket vnet peering update](/cli/azure/network/vnet/peering#az_network_vnet_peering_update) att ändra peering-inställningar. |
- **PowerShell**: [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) att hämta peering visningsinställningarna och [Set-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering) att ändra inställningarna.

## <a name="delete-a-peering"></a>Ta bort en peer-koppling

Innan du tar bort en peer-koppling, se till att ditt konto har de [behörighet](#permissions).

När en peering tas bort, flödar inte längre trafik från ett virtuellt nätverk till det peer-kopplade virtuella nätverket. När virtuella nätverk som distribuerats via Resource Manager har peerkopplats kan har varje virtuellt nätverk den en peer-koppling till det virtuella nätverket. Om du tar bort peer-kopplingen från ett virtuellt nätverk inaktiveras kommunikation mellan virtuella nätverk, tas inte bort peer-kopplingen från det virtuella nätverket. Peering-statusen för peering som finns i det virtuella nätverket är **frånkopplad**. Du kan inte återskapa peeringen tills du återskapa peer-kopplingen i det första virtuella nätverket och peering-statusen för båda virtuella nätverken ändringar *ansluten*. 

Om du vill att virtuella nätverk kan kommunicera ibland, men inte alltid, i stället för att ta bort en peer-koppling, du kan ange den **Tillåt åtkomst till virtuellt nätverk** att ställa in **inaktiverad** i stället. Att lära dig hur du läser steg 6 i den [skapar en peering](#create-peering) i den här artikeln. Du kanske inaktivera och aktivera nätverksåtkomst som är enklare än att ta bort och återskapa peer-kopplingar.

1. Ange i sökrutan överst på portalen *virtuella nätverk* i sökrutan. När **virtuella nätverk** visas i sökresultaten, markerar du den. Markera inte **virtuella nätverk (klassiska)** om den visas i listan som du inte kan skapa en peer-kopplingen från ett virtuellt nätverk som distribueras via den klassiska distributionsmodellen.
2. Välj det virtuella nätverket i listan som du vill ta bort en peerkoppling för.
3. Välj det virtuella nätverket som du vill ta bort en peer-koppling för i listan med virtuella nätverk.
4. Under **inställningar**väljer **Peerings**.
5. På höger sida av peering som du vill ta bort, väljer **...** väljer **ta bort**och välj sedan **Ja** att ta bort peer-kopplingen från det första virtuella nätverket.
6. Slutföra de föregående stegen för att ta bort peer-kopplingen från det virtuella nätverket i peer-kopplingen.

**Kommandon**

- **Azure CLI**: [az network vnet peering delete](/cli/azure/network/vnet/peering#az_network_vnet_peering_delete)
- **PowerShell**: [Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Krav och begränsningar 

- <a name="cross-region"></a>Du kan peer-koppla virtuella nätverk i samma region eller olika regioner. Följande begränsningar gäller inte när båda virtuella nätverken finns i den *samma* region, men gäller när de virtuella nätverken är globalt peer-kopplade: 
    - De virtuella nätverken kan finnas i alla regioner för offentliga Azure-molnet, men inte i nationella Azure-moln.
    - Resurser i ett virtuellt nätverk kan inte kommunicera med IP-adressen för en Azure intern belastningsutjämnare i det peer-kopplade virtuella nätverket. Belastningsutjämnaren och de resurser som kommunicerar med det måste finnas i samma virtuella nätverk.
    - Du kan inte Använd fjärrgateway eller Tillåt gatewayöverföring. Om du vill använda fjärrgatewayer eller Tillåt gatewayöverföring, måste båda virtuella nätverken i peer-kopplingen finnas i samma region. 
- De virtuella nätverken kan finnas i samma eller olika prenumerationer. När du peerkoppla virtuella nätverk i olika prenumerationer, måste båda prenumerationerna associeras till samma Azure Active Directory-klient. Om du inte redan har en AD-klient, kan du snabbt [skapar ett](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Du kan använda en [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) att ansluta två virtuella nätverk som finns i olika prenumerationer som är kopplade till olika Active Directory-klienter.
- Virtuella nätverk som peer-du måste ha icke-överlappande IP-adressutrymmen.
- Du kan inte lägga till adressintervall till eller ta bort-adressintervall från adressutrymmet för ett virtuellt nätverk när ett virtuellt nätverk är peerkopplat med ett annat virtuellt nätverk. Om du vill lägga till eller ta bort adressintervall, ta bort peer-kopplingen, lägga till eller ta bort adressintervallen, sedan återskapa peeringen. Om du vill lägga till adressintervall till eller ta bort-adressintervall från virtuella nätverk, se [hantera virtuella nätverk](manage-virtual-network.md).
- Peerkoppling kan upprättas mellan två virtuella nätverk som distribueras via Resource Manager eller ett virtuellt nätverk som distribuerats via Resource Manager med ett virtuellt nätverk som distribueras via den klassiska distributionsmodellen. Du kan inte peerkoppla två virtuella nätverk som skapats via den klassiska distributionsmodellen. Om du inte är bekant med Azures distributionsmodeller läsa den [distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikeln. Du kan använda [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) för att ansluta två virtuella nätverk som har skapats via den klassiska distributionsmodellen.
- Vid peer-koppling av två virtuella nätverk som skapas via Resource Manager måste en peer-koppling konfigureras för varje virtuellt nätverk i peer-kopplingen. Du ser något av följande typer för peering-status: 
    - *Initierad:* när du skapar peer-kopplingen till det andra virtuella nätverket från det första virtuella nätverket, peering-statusen är *initierad*. 
    - *Ansluten:* när du skapar peer-kopplingen från det andra virtuella nätverket till det första virtuella nätverket, peering-statusen är *ansluten*. Om du visar peering-statusen för det första virtuella nätverket ändras statusen från finns i *initierad* till *ansluten*. Peer-kopplingen är inte upprättad förrän peering-statusen för båda virtuella nätverken är *ansluten*.
- När peer-ett virtuellt nätverk som skapats via Resource Manager med ett virtuellt nätverk som skapats via den klassiska distributionsmodellen måste konfigurera du bara en peering för det virtuella nätverket som distribueras via Resource Manager. Du kan inte konfigurera peering för ett virtuellt nätverk (klassisk) eller mellan två virtuella nätverk som distribueras via den klassiska distributionsmodellen. När du skapar peer-kopplingen från det virtuella nätverket (Resource Manager) till det virtuella nätverket (klassisk) peering-statusen är *uppdaterar*, snart ändras till *ansluten*.
- En peer-koppling upprättas mellan två virtuella nätverk. Peerkopplingar är inte transitiva. Om du skapar peer-kopplingar mellan:
    - VirtualNetwork1 & VirtualNetwork2
    - VirtualNetwork2 & VirtualNetwork3

  Det finns ingen peering mellan VirtualNetwork1 och VirtualNetwork3 via VirtualNetwork2. Om du vill skapa en virtuell nätverkspeering mellan VirtualNetwork1 och VirtualNetwork3 måste du skapa en peering mellan VirtualNetwork1 och VirtualNetwork3.
- Du kan inte matcha namn i peer-kopplade virtuella nätverk som använder standard namnmatchning i Azure. Om du vill matcha namnen på andra virtuella nätverk, måste du använda [Azure DNS för privata domäner](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller en anpassad DNS-server. Läs hur du ställer in en egen DNS-server i [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Resurser i peer-kopplade virtuella nätverk i samma region kan kommunicera med varandra med samma bandbredd och latens som om de befann sig i samma virtuella nätverk. Varje virtuell datorstorlek har sin egen maximal nätverksbandbredd men. Läs mer om nätverkets maximala bandbredd för olika virtuella datorstorlekar i [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) storlekar för virtuella datorer.
- Ett virtuellt nätverk kan peerkopplas till ett annat virtuellt nätverk och också vara anslutna till ett annat virtuellt nätverk med en Azure virtuell nätverksgateway. När virtuella nätverk är anslutna via peering och en gateway, flödar trafiken mellan de virtuella nätverken genom peering-konfigurationen i stället för gatewayen.
- En nominell avgift tas ut för ingående och utgående trafik som använder virtuell nätverks-peering. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Behörigheter

De konton som du använder för att arbeta med peerkoppling måste tilldelas följande roller:

- [Network deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): för ett virtuellt nätverk som distribuerats via Resource Manager.
- [Klassisk Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): för ett virtuellt nätverk som distribueras via den klassiska distributionsmodellen.

Om ditt konto inte har tilldelats till en av rollerna som tidigare, så måste de tilldelas en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas åtgärderna som krävs i följande tabell:

| Åtgärd | Namn |
|---|---|
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Krävs för att skapa en peering från virtuella nätverket A till virtuellt nätverk B. virtuella måste nätverket A vara ett virtuellt nätverk (Resource Manager)                            |
| Microsoft.Network/virtualNetworks/peer/action                   | Krävs för att skapa en peering från virtuella nätverket B (Resource Manager) till virtuella nätverket A                                                                                |
| Microsoft.ClassicNetwork/virtualNetworks/peer                   | Krävs för att skapa en peering från virtuella nätverket B (klassisk) till virtuella nätverket A                                                                                    |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Läsa en virtuell nätverkspeering   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Ta bort en virtuell nätverkspeering |

## <a name="next-steps"></a>Nästa steg

* Peering för virtuellt nätverk skapas mellan virtuella nätverk som skapats via samma eller olika distributionsmodeller som finns i samma eller olika prenumerationer. Genomför en självstudiekurs för något av följande scenarier:

    |Azure-distributionsmodell             | Prenumeration  |
    |---------                          |---------|
    |Båda Resource Manager              |[Samma](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Olika](create-peering-different-subscriptions.md)|
    |En Resource Manager, en klassisk  |[Samma](create-peering-different-deployment-models.md)|
    |                                   |[Olika](create-peering-different-deployment-models-subscriptions.md)|

* Lär dig hur du skapar en [nätverkstopologi med nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
* Skapa en virtuell nätverkspeering med hjälp av [PowerShell](powershell-samples.md) eller [Azure CLI](cli-samples.md) exempel på skript eller genom att använda Azure [Resource Manager-mallar](template-samples.md)
* Skapa och tillämpa [Azure policy](policy-samples.md) för virtuella nätverk

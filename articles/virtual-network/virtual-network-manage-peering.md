---
title: Skapa, ändra eller ta bort en Virtuell Azure-nätverks peering | Microsoft-dokument
description: Lär dig hur du skapar, ändrar eller tar bort en virtuell nätverks peering.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: 97acac61d0397a4e13fb64d39a6aba92e4de2afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123309"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Skapa, ändra eller ta bort en virtuell nätverks peering

Lär dig hur du skapar, ändrar eller tar bort en virtuell nätverks peering. Med virtuell nätverks peering kan du ansluta virtuella nätverk i samma region och mellan regioner (kallas även Global VNet Peering) via Azure-stamnätet. När de virtuella nätverken har peerats hanteras de fortfarande som separata resurser. Om du inte har tidigare information om virtuell nätverks peering kan du läsa mer om det i [översikten över virtuella nätverks peering](virtual-network-peering-overview.md) eller genom att fylla i en [självstudiekurs](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Slutför följande uppgifter innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto registrerar du dig för ett [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder https://portal.azure.comportalen öppnar du och loggar in med ett konto som har [de behörigheter som krävs](#permissions) för att arbeta med peerings.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kör du antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du `Connect-AzAccount` också köra med ett konto som har [de behörigheter](#permissions) som krävs för att arbeta med peering för att skapa en anslutning med Azure.
- Om du använder CLI-kommandon (Azure Command-line interface) för att slutföra uppgifter i den här artikeln, kör du antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här självstudien kräver Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du `az login` också köra med ett konto som har [de behörigheter](#permissions) som krävs för att arbeta med peering för att skapa en anslutning till Azure.

Kontot som du loggar in på, eller ansluter till Azure med, måste tilldelas [rollen nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas lämpliga åtgärder som anges i [Behörigheter](#permissions).

## <a name="create-a-peering"></a>Skapa en peering

Innan du skapar en peering, bekanta dig med de krav och begränsningar och [nödvändiga behörigheter](#permissions).

1. Ange *virtuella nätverk* i sökrutan i sökrutan högst upp på Azure-portalen. När **virtuella nätverk** visas i sökresultaten markerar du det. Välj inte **Virtuella nätverk (klassiska)** om det visas i listan, eftersom du inte kan skapa en peering från ett virtuellt nätverk som distribueras via den klassiska distributionsmodellen.
2. Markera det virtuella nätverket i listan som du vill skapa en peering för.
3. Under **INSTÄLLNINGAR**väljer du **Peerings**.
4. Välj **+ Lägg till**. 
5. <a name="add-peering"></a>Ange eller välj värden för följande inställningar:
    - **Namn:** Namnet på peer-datorn måste vara unikt i det virtuella nätverket.
    - **Distributionsmodell för virtuellt nätverk:** Välj vilken distributionsmodell det virtuella nätverk som du vill peer med har distribuerats via.
    - **Jag vet mitt resurs-ID:** Om du har läsbehörighet till det virtuella nätverk som du vill peer med lämnar du den här kryssrutan avmarkerad. Markera den här rutan om du inte har läsbehörighet till det virtuella nätverk eller den prenumeration som du vill peer med. Ange det fullständiga resurs-ID:t för det virtuella nätverk som du vill peera med i rutan **Resurs-ID** som visades när du markerade rutan. Det resurs-ID som du anger måste vara för ett virtuellt nätverk som finns i samma eller [stöds olika](#requirements-and-constraints) [Azure-regioner](https://azure.microsoft.com/regions) som det här virtuella nätverket. Det fullständiga resurs-ID:et liknar `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>`. Du kan hämta resurs-ID:t för ett virtuellt nätverk genom att visa egenskaperna för ett virtuellt nätverk. Mer information om hur du visar egenskaperna för ett virtuellt nätverk finns i [Hantera virtuella nätverk](manage-virtual-network.md#view-virtual-networks-and-settings). Om prenumerationen är associerad till en annan Azure Active Directory-klient än prenumerationen med det virtuella nätverket som du skapar peering från, lägger du först till en användare från varje klient som [gästanvändare](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) i den motsatta klienten.
    - **Prenumeration:** Välj [prenumerationen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) för det virtuella nätverk som du vill peer med. En eller flera prenumerationer visas, beroende på hur många prenumerationer ditt konto har läsbehörighet till. Om du har markerat kryssrutan **Resurs-ID** är den här inställningen inte tillgänglig.
    - **Virtuellt nätverk:** Välj det virtuella nätverk som du vill peer med. Du kan välja ett virtuellt nätverk som skapats via antingen Azure-distributionsmodell. Om du vill välja ett virtuellt nätverk i en annan region måste du välja ett virtuellt nätverk i en [region som stöds](#cross-region). Du måste ha läsåtkomst till det virtuella nätverket för att det ska vara synligt i listan. Om ett virtuellt nätverk visas, men är nedtonat, kan det bero på att adressutrymmet för det virtuella nätverket överlappar adressutrymmet för det virtuella nätverket. Om virtuella nätverksadressutrymmen överlappar varandra kan de inte peereras. Om du har markerat kryssrutan **Resurs-ID** är den här inställningen inte tillgänglig.
    - **Tillåt virtuell nätverksåtkomst:** Välj **Aktiverad** (standard) om du vill aktivera kommunikation mellan de två virtuella nätverken. Om du aktiverar kommunikation mellan virtuella nätverk kan resurser som är anslutna till antingen virtuella nätverk kommunicera med varandra med samma bandbredd och svarstid som om de var anslutna till samma virtuella nätverk. All kommunikation mellan resurser i de två virtuella nätverken är över Azure privata nätverk. **VirtualNetwork-tjänsttaggen** för nätverkssäkerhetsgrupper omfattar det virtuella nätverket och det peer-virtuella nätverket. Mer information om tjänsttaggar för nätverkssäkerhetsgrupp finns i [Översikt över nätverkssäkerhetsgrupper](security-overview.md#service-tags). Välj **Inaktiverad** om du inte vill att trafiken ska flöda till det peer-inkopplade virtuella nätverket. Du kan välja **Inaktiverat** om du har peered ett virtuellt nätverk med ett annat virtuellt nätverk, men ibland vill inaktivera trafikflödet mellan de två virtuella nätverken. Det är bekvämare att aktivera/inaktivera än att ta bort och återskapa peerings. När den här inställningen är inaktiverad flödar inte trafiken mellan de peer-ed virtuella nätverken.
    - **Tillåt vidarebefordrad trafik:** Markera den här rutan om du vill att trafik *som vidarebefordras* av en virtuell nätverksinstallation i ett virtuellt nätverk (som inte kommer från det virtuella nätverket) ska kunna flöda till det här virtuella nätverket via en peering. Tänk dig till exempel tre virtuella nätverk som heter Spoke1, Spoke2 och Hub. Det finns en peering mellan varje virtuellt nätverk för eker och virtuellt hubbnätverk, men peerings finns inte mellan ekrar virtuella nätverk. En virtuell nätverksinstallation distribueras i det virtuella navnätverket och användardefinierade vägar tillämpas på varje eker virtuellt nätverk som dirigerar trafik mellan undernäten via den virtuella nätverksinstallationen. Om den här kryssrutan inte är markerad för peering mellan varje eker virtuellt nätverk och navet virtuellt nätverk, trafiken inte flöde mellan eker virtuella nätverk eftersom navet inte vidarebefordrar trafiken mellan de virtuella nätverken. När du aktiverar den här funktionen kan den vidarebefordrade trafiken via peer-datorn skapas inte några användardefinierade vägar eller virtuella nätverksinstallationer. Användardefinierade vägar och virtuella nätverksinstallationer skapas separat. Läs mer om [användardefinierade vägar](virtual-networks-udr-overview.md#user-defined). Du behöver inte kontrollera den här inställningen om trafik vidarebefordras mellan virtuella nätverk via en Azure VPN Gateway.
    - **Tillåt gatewaytransitering:** Markera den här rutan om du har en virtuell nätverksgateway kopplad till det här virtuella nätverket och vill tillåta att trafik från det peer-kopplade virtuella nätverket flödar genom gatewayen. Det här virtuella nätverket kan till exempel kopplas till ett lokalt nätverk via en virtuell nätverksgateway. Gatewayen kan vara en ExpressRoute- eller VPN-gateway. Om du markerar den här rutan kan trafiken från det peer-anpassade virtuella nätverket flöda genom den gateway som är kopplad till det här virtuella nätverket till det lokala nätverket. Om du markerar den här rutan kan det peer-nätverket inte ha en gateway konfigurerad. Det peer-nätverket måste ha kryssrutan **Använd fjärrgateways markerad** när peering-peering från det andra virtuella nätverket konfigureras till det här virtuella nätverket. Om du lämnar den här rutan avmarkerad (standard) flödar trafiken från det peer-kopplade virtuella nätverket fortfarande till det här virtuella nätverket, men kan inte flöda genom en virtuell nätverksgateway som är ansluten till det här virtuella nätverket. Om peering är mellan ett virtuellt nätverk (Resource Manager) och ett virtuellt nätverk (klassiskt), måste gatewayen vara i det virtuella nätverket (Resource Manager).

       Förutom att vidarebefordra trafik till ett lokalt nätverk kan en VPN-gateway vidarebefordra nätverkstrafik mellan virtuella nätverk som är peered med det virtuella nätverket gatewayen är i, utan att de virtuella nätverken behöver peered med varandra. Att använda en VPN-gateway för att vidarebefordra trafik är användbart när du vill använda en VPN-gateway i ett nav (se hubben och ekerexempel som beskrivs för **Tillåt vidarebefordrad trafik) virtuellt**nätverk för att dirigera trafik mellan eker virtuella nätverk som inte är peered med varandra. Mer information om hur du tillåter användning av en gateway för överföring finns i [Konfigurera en VPN-gateway för överföring i en virtuell nätverks peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Det här scenariot kräver implementering av användardefinierade vägar som anger den virtuella nätverksgatewayen som nästa hopptyp. Läs mer om [användardefinierade vägar](virtual-networks-udr-overview.md#user-defined). Du kan bara ange en VPN-gateway som nästa hopptyp i en användardefinierad väg, du kan inte ange en ExpressRoute-gateway som nästa hopptyp i en användardefinierad väg.

    - **Använda fjärrgateways:** Markera den här rutan om du vill att trafik från det här virtuella nätverket ska kunna flöda genom en virtuell nätverksgateway som är ansluten till det virtuella nätverk som du peering med. Det virtuella nätverket som du peering med har till exempel en VPN-gateway ansluten som möjliggör kommunikation till ett lokalt nätverk.  Om du markerar den här rutan kan trafik från det här virtuella nätverket flöda genom VPN-gatewayen som är ansluten till det peer-kopplade virtuella nätverket. Om du markerar den här rutan måste det peer-kopplade virtuella nätverket ha en virtuell nätverksgateway kopplad till sig och ha kryssrutan **Tillåt gateway-transitering** markerad. Om du lämnar den här rutan avmarkerad (standard) kan trafiken från det peer-kopplade virtuella nätverket fortfarande flöda till det här virtuella nätverket, men kan inte flöda genom en virtuell nätverksgateway som är ansluten till det här virtuella nätverket.
    
      Endast en peering för det här virtuella nätverket kan ha den här inställningen aktiverad.

      Du kan inte använda fjärrgateways om du redan har en gateway konfigurerad i det virtuella nätverket. Mer information om hur du använder en gateway för överföring finns i [Konfigurera en VPN-gateway för överföring i en virtuell nätverks peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        
    > [!NOTE]
    > Om du använder en virtuell nätverksgateway för att skicka lokal trafik transitivt till ett peered-virtuellt nätverk, måste det peered VNet IP-intervallet för den lokala VPN-enheten ställas in på "intressant" trafik. Annars kan dina lokala resurser inte kommunicera med resurser i det peer-nätverket.

6. Välj **OK** om du vill lägga till peering-datorn i det virtuella nätverk som du har valt.

Stegvisa instruktioner för implementering av peering mellan virtuella nätverk i olika prenumerationer och distributionsmodeller finns i [nästa steg](#next-steps).

### <a name="commands"></a>Kommandon

- **Azure CLI**: [az nätverk vnet peering skapa](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Visa eller ändra peering-inställningar

Innan du ändrar en peering, bekanta dig med de krav och begränsningar och [nödvändiga behörigheter](#permissions).

1. Ange *virtuella nätverk* i sökrutan i sökrutan högst upp på portalen. När **virtuella nätverk** visas i sökresultaten markerar du det. Välj inte **Virtuella nätverk (klassiska)** om det visas i listan, eftersom du inte kan skapa en peering från ett virtuellt nätverk som distribueras via den klassiska distributionsmodellen.
2. Välj det virtuella nätverket i listan som du vill ändra peering-inställningarna för.
3. Under **INSTÄLLNINGAR**väljer du **Peerings**.
4. Välj den peering du vill visa eller ändra inställningar för.
5. Ändra lämplig inställning. Läs om alternativen för varje inställning i [steg 5](#add-peering) i Skapa en peering.
6. Välj **Spara**.

**Kommandon**

- **Azure CLI**: [az network vnet peering list](/cli/azure/network/vnet/peering) to list peerings for a virtual network, az network [vnet peering show](/cli/azure/network/vnet/peering) to show settings for a specific peering, and az network [vnet peering update](/cli/azure/network/vnet/peering) to change peering settings.|
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) för att hämta visningsinställningar och [Set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) för att ändra inställningar.

## <a name="delete-a-peering"></a>Ta bort en peer-peering

Innan du tar bort en peering, se till att ditt konto har [de nödvändiga behörigheterna](#permissions).

När en peering tas bort flödar inte längre trafik från ett virtuellt nätverk till det peer-inkopplade virtuella nätverket. När virtuella nätverk som distribueras via Resource Manager peeras har varje virtuellt nätverk en peering till det andra virtuella nätverket. Även om du tar bort peering från ett virtuellt nätverk inaktiverar kommunikationen mellan de virtuella nätverken, tas inte peering från det andra virtuella nätverket bort. Peering-statusen för peering som finns i det andra virtuella nätverket är **frånkopplad**. Du kan inte återskapa peering tills du återskapar peering i det första virtuella nätverket och peering-status för båda virtuella nätverk ändras till *Ansluten*.

Om du vill att virtuella nätverk ska kommunicera ibland, men inte alltid, i stället för att ta bort en peering, kan du ange inställningen **Tillåt virtuell nätverksåtkomst** till **Inaktiverad** i stället. Om du vill veta mer läser du steg 6 i avsnittet Skapa en peering i den här artikeln. Det kan vara enklare att inaktivera och aktivera nätverksåtkomst än att ta bort och återskapa peerings.

1. Ange *virtuella nätverk* i sökrutan i sökrutan högst upp på portalen. När **virtuella nätverk** visas i sökresultaten markerar du det. Välj inte **Virtuella nätverk (klassiska)** om det visas i listan, eftersom du inte kan skapa en peering från ett virtuellt nätverk som distribueras via den klassiska distributionsmodellen.
2. Markera det virtuella nätverket i listan som du vill ta bort en peering för.
3. Under **INSTÄLLNINGAR**väljer du **Peerings**.
4. På höger sida av peering du vill ta bort, välj **...**, välj **Ta bort**och välj sedan **Ja** för att ta bort peering från det första virtuella nätverket.
5. Slutför föregående steg för att ta bort peering från det andra virtuella nätverket i peering.

**Kommandon**

- **Azure CLI**: [az network vnet peering delete](/cli/azure/network/vnet/peering) Azure CLI : az network vnet peering delete Azure CLI : az network vnet peering delete Azure CLI
- **PowerShell**: [Ta bort-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Krav och begränsningar

- <a name="cross-region"></a>Du kan peer virtuella nätverk i samma region eller olika regioner. Peering virtuella nätverk i olika regioner kallas också *Global VNet Peering*. 
- När du skapar en global peering kan de peered virtuella nätverken finnas i alla Offentliga Azure-molnregioner eller Kinamolnregioner eller molnregioner för myndigheter. Du kan inte kika över moln. Ett virtuella nätverk i Azure-offentligt moln kan till exempel inte peered till ett virtuella nätverk i Azure China-molnet.
- Resurser i ett virtuellt nätverk kan inte kommunicera med IP-adressen i klientdelen för en intern Basic-lastbalanserare i ett globalt peerkopplat virtuellt nätverk. Stöd för grundläggande lastbalanserare finns bara inom samma region. Stöd för Standard Load Balancer finns för både VNet-peering och global VNet-peering. Tjänster som använder en grundläggande belastningsutjämnare som inte fungerar över Global VNet Peering dokumenteras [här.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Du kan använda fjärrgateways eller tillåta gatewaytransit i globalt peered virtuella nätverk och lokalt peered virtuella nätverk.
- De virtuella nätverken kan finnas i samma eller olika prenumerationer. När du peer virtuella nätverk i olika prenumerationer, kan båda prenumerationerna associeras till samma eller olika Azure Active Directory-klient. Om du inte redan har en AD-klient kan du [skapa en](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant). Stöd för peering över virtuella nätverk från prenumerationer som är associerade till olika Azure Active Directory-klienter är inte tillgängligt i Portal. Du kan använda CLI, PowerShell eller Mallar.
- De virtuella nätverk som du peer måste ha icke-överlappande IP-adressutrymmen.
- Du kan inte lägga till adressintervall i eller ta bort adressintervall från ett virtuellt nätverks adressutrymme när ett virtuellt nätverk har peerats med ett annat virtuellt nätverk. Om du vill lägga till eller ta bort adressintervall tar du bort peering-, lägger till eller tar bort adressintervallen och återskapar sedan peer-datorn. Mer om du vill lägga till adressintervall i eller ta bort adressintervall från virtuella nätverk finns i [Hantera virtuella nätverk](manage-virtual-network.md).
- Du kan peer två virtuella nätverk som distribueras via Resource Manager eller ett virtuellt nätverk som distribueras via Resource Manager med ett virtuellt nätverk distribueras via den klassiska distributionsmodellen. Du kan inte peer två virtuella nätverk som skapats via den klassiska distributionsmodellen. Om du inte är bekant med Azure-distributionsmodeller läser du artikeln [Förstå Azure-distributionsmodeller.](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Du kan använda [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) för att ansluta två virtuella nätverk som har skapats via den klassiska distributionsmodellen.
- Vid peer-koppling av två virtuella nätverk som skapas via Resource Manager måste en peer-koppling konfigureras för varje virtuellt nätverk i peer-kopplingen. Du ser någon av följande typer för peering-status: 
  - *Initierad:* När du skapar peering till det andra virtuella nätverket från det första virtuella nätverket *initieras*peering-statusen . 
  - *Ansluten:* När du skapar peering från det andra virtuella nätverket till det första virtuella nätverket är dess peering-status *Ansluten*. Om du visar peering-status för det första virtuella nätverket visas dess status ändrad från *Initierad* till *Ansluten*. Peering har inte upprättats förrän peering-status för båda virtuella nätverks peerings är *Ansluten*.
- När du peering ett virtuellt nätverk som skapats via Resource Manager med ett virtuellt nätverk som skapats via den klassiska distributionsmodellen, konfigurerar du bara en peering för det virtuella nätverket som distribueras via Resource Manager. Du kan inte konfigurera peering för ett virtuellt nätverk (klassiskt) eller mellan två virtuella nätverk som distribueras via den klassiska distributionsmodellen. När du skapar peering från det virtuella nätverket (Resource Manager) till det virtuella nätverket (Classic), är peering status *Uppdatera*, sedan kort ändringar *till Ansluten*.
- En peering upprättas mellan två virtuella nätverk. Peerings är inte transitiva. Om du skapar peerings mellan:
  - VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3

  Det finns ingen peering mellan VirtualNetwork1 och VirtualNetwork3 via VirtualNetwork2. Om du vill skapa en virtuell nätverks peering mellan VirtualNetwork1 och VirtualNetwork3 måste du skapa en peering mellan VirtualNetwork1 och VirtualNetwork3.
- Du kan inte matcha namn i peered virtuella nätverk med standard Azure-namnmatchning. Om du vill matcha namn i andra virtuella nätverk måste du använda [Azure DNS för privata domäner](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller en anpassad DNS-server. Mer information om hur du konfigurerar en egen DNS-server finns i [Namnmatchning med din egen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Resurser i peered virtuella nätverk i samma region kan kommunicera med varandra med samma bandbredd och svarstid som om de vore i samma virtuella nätverk. Varje virtuell datorstorlek har sin egen maximala nätverksbandbredd dock. Mer information om maximal nätverksbandbredd för olika storlekar på virtuella datorer finns i Storlekar på [virtuella datorer](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i Windows eller [Linux.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Ett virtuellt nätverk kan peered till ett annat virtuellt nätverk, och även anslutas till ett annat virtuellt nätverk med en Azure virtuellt nätverk gateway. När virtuella nätverk ansluts via både peering och en gateway flödar trafiken mellan de virtuella nätverken via peering-konfigurationen i stället för gatewayen.
- Vpn-klienter från punkt till plats måste hämtas igen efter att virtuell nätverks peering har konfigurerats för att säkerställa att de nya vägarna hämtas till klienten.
- En nominell avgift tas ut för ingående och utgående trafik som använder virtuell nätverks-peering. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Behörigheter

De konton som du använder för att arbeta med virtuell nätverks peering måste tilldelas följande roller:

- [Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): För ett virtuellt nätverk som distribueras via Resource Manager.
- [Klassisk nätverksdeltagare:](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)För ett virtuellt nätverk som distribueras via den klassiska distributionsmodellen.

Om ditt konto inte har tilldelats någon av de föregående rollerna måste det tilldelas en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas nödvändiga åtgärder från följande tabell:

| Åtgärd                                                          | Namn |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Krävs för att skapa en peering från virtuellt nätverk A till virtuellt nätverk B. Virtuellt nätverk A måste vara ett virtuellt nätverk (Resource Manager)          |
| Microsoft.Network/virtualNetworks/peer/action                   | Krävs för att skapa en peering från virtuellt nätverk B (Resource Manager) till virtuellt nätverk A                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | Krävs för att skapa en peering från virtuellt nätverk B (klassisk) till virtuellt nätverk A                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Läsa en virtuell nätverks peering   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Ta bort en virtuell nätverks peering |

## <a name="next-steps"></a>Nästa steg

- Peering för virtuellt nätverk skapas mellan virtuella nätverk som skapats via samma eller olika distributionsmodeller som finns i samma eller olika prenumerationer. Genomför en självstudiekurs för något av följande scenarier:

  |Azure-distributionsmodell             | Prenumeration  |
  |---------                          |---------|
  |Båda Resource Manager              |[Samma](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Olika](create-peering-different-subscriptions.md)|
  |En Resource Manager, en klassisk  |[Samma](create-peering-different-deployment-models.md)|
  |                                   |[Olika](create-peering-different-deployment-models-subscriptions.md)|

- Lär dig hur du skapar en [nätverkstopologi med nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Skapa en virtuell nätverks peering med [PowerShell-](powershell-samples.md) eller [Azure CLI-exempelskript](cli-samples.md) eller använda Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tillämpa [Azure-principen](policy-samples.md) för virtuella nätverk

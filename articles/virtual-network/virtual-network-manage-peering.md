---
title: Skapa, ändra eller ta bort ett virtuellt Azure-nätverk som peering | Microsoft Docs
description: Lär dig mer om att skapa, ändra eller ta bort ett virtuellt nätverk-peering.
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
ms.openlocfilehash: 5cd486d9953979fe8aa9c8354c5fad5a9e11af1e
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Skapa, ändra eller ta bort ett virtuellt nätverk-peering

Lär dig mer om att skapa, ändra eller ta bort ett virtuellt nätverk-peering. Peering virtuellt nätverk kan du ansluta virtuella nätverk via Azure stamnät nätverket. När peerkoppla, hanteras virtuella nätverk fortfarande som separata resurser. Om du inte är bekant med virtuella nätverk peering rekommenderar vi att läsa den [peering översikt över virtuella nätverk](virtual-network-peering-overview.md) och slutföra de [skapa virtuellt nätverk peering självstudiekursen](tutorial-connect-virtual-networks-portal.md), innan du slutför uppgifterna i den här artikeln.

VNET-peering i samma region är allmänt sett tillgängligt. Peering virtuella nätverk i olika regioner är för närvarande under förhandsgranskning. Se [virtuella nätverk uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network) för tillgängliga regioner. Du måste [registrera prenumerationen för förhandsgranskningen](tutorial-connect-virtual-networks-powershell.md#register).

> [!WARNING]
> VNET-peering som skapats i det här scenariot kanske inte har samma tillgänglighet och pålitlighet som scenarier i en allmänt tillgänglig version. VNET-peering kan ha begränsad kapacitet och kanske inte är tillgänglig i alla Azure-regioner. Du hittar aktuell information om tillgänglighet och status för den här funktionen på [sidan med Azure Virtual Network-uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network).
>

## <a name="before-you-begin"></a>Innan du börjar

Utför följande uppgifter innan du slutför stegen i alla avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [ledigt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppna https://portal.azure.com, och logga in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här kursen kräver Azure PowerShell Modulversion 5.2.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.
- Om du använder Azure-kommandoradsgränssnittet (CLI)-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här kursen kräver Azure CLI version 2.0.26 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt, måste du också köra `az login` att skapa en anslutning med Azure.

## <a name="create-a-peering"></a>Skapa en peering

>[!NOTE]
>Innan du skapar en peering, se till att du har bekantat dig med de [kraven och begränsningarna](#requirements-and-constraints) och [nödvändiga behörigheter](#permissions).
>

1. Skriv i sökrutan överst i portalen *virtuella nätverk* i sökrutan. När **virtuella nätverken** visas i sökresultaten väljer den. Välj inte **virtuella nätverk (klassiskt)** om den visas i listan som du inte kan skapa en peering från ett virtuellt nätverk som distribuerats via den klassiska distributionsmodellen.
2. Välj det virtuella nätverket i listan som du vill skapa en peering för.
3. Välj det virtuella nätverket som du vill skapa en peering för i listan över virtuella nätverk.
4. Under **inställningar**väljer **Peerkopplingar**.
5. Välj **+ Lägg till**. 
6. <a name="add-peering"></a>Ange eller Välj värden för följande inställningar:
    - **Namn:** namn för peering måste vara unika inom det virtuella nätverket.
    - **Distributionsmodell för virtuellt nätverk:** väljer vilka distribution modellerar det virtuella nätverket som du vill peer med har distribuerats via.
    - **Jag vet mitt resurs-ID:** om du har läsbehörighet till det virtuella nätverket som du vill peer med lämnar den här kryssrutan avmarkerad. Om du inte har läsbehörighet till virtuella nätverk eller en prenumeration som du vill att peer-koppla med den här kryssrutan. Ange fullständiga resurs-ID för det virtuella nätverket som du vill peer med i den **resurs-ID** som visades när du har markerat kryssrutan. Resurs-ID som du anger måste vara för ett virtuellt nätverk som finns i samma Azure [region](https://azure.microsoft.com/regions) som den här virtuella nätverket. Om du vill välja ett virtuellt nätverk i en annan region [registrera prenumerationen för förhandsgranskningen.](tutorial-connect-virtual-networks-portal.md) Fullständiga resurs-ID som liknar /subscriptions/<Id>/resourceGroups/ < resursgruppnamn > /providers/Microsoft.Network/virtualNetworks/ <-namn virtuellt nätverk->. Du kan hämta resurs-ID för ett virtuellt nätverk genom att visa egenskaperna för ett virtuellt nätverk. Information om hur du visar egenskaperna för ett virtuellt nätverk finns [hantera virtuella nätverk](manage-virtual-network.md#view-virtual-networks-and-settings).
    - **Prenumerationen:** markerar du den [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) för det virtuella nätverket som du vill att peer-koppla med. En eller flera prenumerationer visas, beroende på hur många prenumerationer ditt konto har läsbehörighet till. När du har markerat den **resurs-ID** och den här inställningen är inte tillgänglig.
    - **Virtuellt nätverk:** Välj det virtuella nätverk som du vill att peer-koppla med. Du kan välja ett virtuellt nätverk som skapats via antingen Azure distributionsmodell. Om du vill välja ett virtuellt nätverk i en annan region [registrera prenumerationen för förhandsgranskningen.](tutorial-connect-virtual-networks-portal.md) Du måste ha läsbehörighet till det virtuella nätverket att vara synliga i listan. Om ett virtuellt nätverk i listan, men nedtonad, kanske eftersom adressutrymmet för det virtuella nätverket överlappar adressutrymmet för det här virtuella nätverket. Om virtuella adressutrymmen överlappar varandra, kan de inte peerkopplas. När du har markerat den **resurs-ID** och den här inställningen är inte tillgänglig.
    - **Tillåt åtkomst till virtuella nätverk:** Välj **aktiverad** (standard) om du vill aktivera kommunikation mellan de två virtuella nätverk. Om du aktiverar kommunikation mellan virtuella nätverk kan resurser som är anslutna till något virtuellt nätverk att kommunicera med varandra med samma bandbredd och svarstid som om de är anslutna till samma virtuella nätverk. All kommunikation mellan resurser i de två virtuella nätverk är över Azure privata nätverk. Den **VirtualNetwork** Standardtaggen för nätverkssäkerhetsgrupper omfattar virtuella nätverk och peerkoppla virtuella nätverk. Mer information om network security group standardtaggar den [Network security groups översikt](virtual-networks-nsg.md#default-tags) artikel.  Välj **inaktiverad** om du inte vill att trafiken kan flöda till peered virtuellt nätverk. Du kan välja **inaktiverad** om du har ett virtuellt nätverk med ett annat virtuellt nätverk är peerkopplat, men ibland vill inaktivera trafikflödet mellan de två virtuella nätverk. Du kan hitta aktivering/inaktivering av är mycket enklare än att ta bort och återskapa peerkopplingar. När den här inställningen inaktiveras trafiken inte mellan peered virtuella nätverk.
    - **Tillåt vidarebefordrad trafik:** den här kryssrutan för att tillåta trafik *vidarebefordras* av en virtuell nätverksenhet i ett virtuellt nätverk (som inte kommer från det virtuella nätverket) till flödet till den här virtuella nätverk via en peering . Anta till exempel att tre virtuella nätverk som heter Spoke1, Spoke2 och hubb. En peering finns mellan varje ekrar virtuella nätverket och det virtuella nätverket Hub, men finns inte peerkopplingar mellan eker virtuella nätverk. En virtuell nätverksenhet har distribuerats i det virtuella nätverket hubb och användardefinierade vägar tillämpas på varje ekrar virtuella nätverk som vidarebefordra trafik mellan undernät med virtuell nätverksenhet. Om den här kryssrutan inte är markerad för peering mellan varje ekrar virtuella nätverket och det virtuella nätverket hubb trafiken inte mellan de virtuella nätverken ekrar eftersom navet är vidarebefordrar trafik mellan virtuella nätverk. När du aktiverar den här funktionen kan vidarebefordrad trafik via peering, skapar inte någon användardefinierade vägar eller virtuella nätverksenheter. Användardefinierade vägar och virtuella nätverksenheter skapas separat. Lär dig mer om [användardefinierade vägar](virtual-networks-udr-overview.md#user-defined). Du behöver inte kontrollera den här inställningen om trafik vidarebefordras mellan virtuella nätverk via en Azure VPN-Gateway.
    - **Tillåt gateway överföring:** den här kryssrutan om du har en virtuell nätverksgateway som är kopplade till det här virtuella nätverket och vill tillåta trafik från peered virtuellt nätverk att strömma genom gatewayen. Det här virtuella nätverket kan till exempel kopplas till ett lokalt nätverk via en virtuell nätverksgateway. Gatewayen kan vara en ExpressRoute- eller VPN-gateway. Markera den här rutan tillåter trafik från peered virtuellt nätverk att strömma genom den gateway som är kopplade till det här virtuella nätverket till det lokala nätverket. Om du markerar kryssrutan kan inte peered virtuellt nätverk ha en gateway som konfigurerats. Peered virtuella nätverket måste ha den **använder fjärråtkomst gateways** markerad när du ställer in peering från det virtuella nätverket till den här virtuella nätverket. Om du lämnar den här kryssrutan avmarkerad (standard), trafik från peerkoppla virtuella nätverk fortfarande flödena till den här virtuella nätverket, men det går inte att flöda genom en virtuell nätverksgateway som är kopplade till det här virtuella nätverket. 
    
    Förutom vidarebefordrar trafik till ett lokalt nätverk, kan en VPN-gateway vidarebefordra trafik mellan virtuella nätverk som är peerkopplat med det virtuella nätverket som den är i, utan de virtuella nätverken behöva peerkopplat med varandra. Detta är användbart när du vill använda en VPN-gateway i ett nav (se exemplet NAV och ekrar beskrivs för **Tillåt vidarebefordrad trafik**) virtuella nätverk för att vidarebefordra trafik mellan eker virtuella nätverk som inte är peerkopplat med varandra. Lär dig mer om [virtuella nätverksgatewayerna](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti). Det här scenariot måste implementera användardefinierade vägar som anger virtuella nätverks-gatewayen som nexthop-typen. Lär dig mer om [användardefinierade vägar](virtual-networks-udr-overview.md#user-defined). Du kan bara ange en VPN-gateway som en nästa hopptyp i en användardefinierad väg, du kan inte ange en ExpressRoute-gateway som nexthop-typen i en användardefinierad väg.

        You cannot enable this option if you're peering a virtual network (Resource Manager) with a virtual network (classic). Though the traffic flows between the two virtual networks, the virtual network (classic) traffic cannot flow through a network gateway attached to the virtual network (Resource Manager). 

    - **Använd remote gateways:** den här kryssrutan om du vill tillåta trafik från det här virtuella nätverket att strömma genom en virtuell nätverksgateway kopplad till det virtuella nätverket du peering med. Har till exempel det virtuella nätverket som du peering med en VPN-gateway som är ansluten som aktiverar kommunikation till ett lokalt nätverk.  Markera den här rutan tillåter trafik från det här virtuella nätverket att strömma genom VPN-gateway som är kopplat till peered virtuellt nätverk. Om du markerar kryssrutan peered virtuella nätverk måste ha en virtuell nätverksgateway som kopplats till den och måste ha den **Tillåt gateway överföring** kryssrutan är markerad. Om du lämnar den här kryssrutan avmarkerad (standard), trafik från det virtuella nätverket peered fortfarande kan flöda till det här virtuella nätverket, men kan flödet genom en virtuell nätverksgateway kopplas till den här virtuella nätverket. 
Endast en peering för den här virtuella nätverket kan ha den här inställningen är aktiverad.
Du kan inte använda den här inställningen om du redan har en gateway som konfigurerats i det virtuella nätverket.
        Du kan inte aktivera det här alternativet om du peering ett virtuellt nätverk (Resource Manager) med ett virtuellt nätverk (klassiskt). Även om trafiken flödar mellan de två virtuella nätverk, kan inte virtuella nätverk (Resource Manager) trafiken via en nätverksgateway som är kopplade till det virtuella nätverket (klassiskt).

7. Klicka på den **OK** för att lägga till undernätet till det virtuella nätverket som du har valt.

### <a name="commands"></a>Kommandon

- Azure CLI: [az network vnet-peering skapa](/cli/azure/network/vnet/peering#create)
- PowerShell: [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering)

### <a name="scenarios"></a>Scenarier

Peering för virtuellt nätverk skapas mellan virtuella nätverk som skapats via samma eller olika distributionsmodeller som finns i samma eller olika prenumerationer. Utför en stegvis självstudiekurs för något av följande scenarier:
 
|Azure-distributionsmodell  | Prenumeration  |
|---------|---------|
|Båda Resource Manager |[Samma](tutorial-connect-virtual-networks-portal.md)|
| |[Olika](create-peering-different-subscriptions.md)|
|En Resource Manager, en klassisk     |[Samma](create-peering-different-deployment-models.md)|
| |[Olika](create-peering-different-deployment-models-subscriptions.md)|

## <a name="view-or-change-peering-settings"></a>Visa eller ändra peering inställningar

1. Skriv i sökrutan överst i portalen *virtuella nätverk* i sökrutan. När **virtuella nätverken** visas i sökresultaten väljer den. Välj inte **virtuella nätverk (klassiskt)** om den visas i listan som du inte kan skapa en peering från ett virtuellt nätverk som distribuerats via den klassiska distributionsmodellen.
2. Välj det virtuella nätverket i listan som du vill ändra peering-inställningar för.
3. Välj det virtuella nätverket som du vill ändra peering inställningar för listan över virtuella nätverk.
4. Under **inställningar**väljer **Peerkopplingar**.
5. Klicka på peering som du vill visa eller ändra inställningar för.
6. Ändra inställningarna. Läs mer om alternativen för varje inställning i [steg 6](#add-peering) skapa en peering. 

    >[!NOTE]
    >Innan du skapar en peering, se till att du har bekantat dig med de [kraven och begränsningarna](#requirements-and-constraints) och [nödvändiga behörigheter](#permissions).
    >

7. Klicka på **Spara**.

**Kommandon**

Azure CLI: [az vnet peering nätverkslistan](/cli/azure/network/vnet/peering#az_network_vnet_peering_list) till listan peerkopplingar för ett virtuellt nätverk [az network vnet-peering visa](/cli/azure/network/vnet/peering#az_network_vnet_peering_show) att visa inställningar för en specifik peering och [az network vnet-peering uppdatering ](/cli/azure/network/vnet/peering#az_network_vnet_peering_update) ändra peering inställningar. |
- PowerShell: [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) att hämta vyn peering inställningar och [Set AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering) att ändra inställningarna.

## <a name="delete-a-peering"></a>Ta bort en peering

När en peering tas bort flödar inte längre trafik från ett virtuellt nätverk till peered virtuellt nätverk. När virtuella nätverk som distribuerats via Resource Manager är peerkopplat varje virtuella nätverk som har en peering till det virtuella nätverket. Om du tar bort peering från ett virtuellt nätverk inaktiveras kommunikation mellan virtuella nätverk, tas inte bort peering från det virtuella nätverket. Peering status för peering som finns i det virtuella nätverket är **frånkopplad**. Du kan inte återskapa peering tills du skapa peerkopplingen på nytt i det första virtuella nätverket och peering statusen för både virtuella nätverk ändringar *ansluten*. 

Om du vill att virtuella nätverk ska kunna kommunicera ibland, men inte alltid, i stället för att ta bort en peering kan du ange den **Tillåt åtkomst till virtuella nätverk** till **inaktiverad** i stället. Mer information hur, läsa steg 6 i den [skapa en peering](#create-peering) i den här artikeln. Du kanske inaktivera och aktivera nätverksåtkomst som är enklare än tas bort och återskapas peerkopplingar.

1. Skriv i sökrutan överst i portalen *virtuella nätverk* i sökrutan. När **virtuella nätverken** visas i sökresultaten väljer den. Välj inte **virtuella nätverk (klassiskt)** om den visas i listan som du inte kan skapa en peering från ett virtuellt nätverk som distribuerats via den klassiska distributionsmodellen.
2. Välj det virtuella nätverket i listan som du vill ta bort en peering för.
3. Välj det virtuella nätverket som du vill ta bort en peering för i listan över virtuella nätverk.
4. Under **inställningar**väljer **Peerkopplingar**.
5. Välj höger för peering som du vill ta bort **...** väljer **ta bort**och välj **Ja** att ta bort peering från det första virtuella nätverket.
6. Slutföra de föregående stegen för att ta bort peering från andra virtuella nätverket i peering.

**Kommandon**

- Azure CLI: [az network vnet peering ta bort](/cli/azure/network/vnet/peering#az_network_vnet_peering_delete)
- PowerShell: [Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Krav och begränsningar 

- De virtuella nätverken peer-du måste ha icke-överlappande IP-adressutrymmen.
- Du kan inte lägga till-adressintervall till eller ta bort adressintervall adressutrymmet för ett virtuellt nätverk när ett virtuellt nätverk är peerkopplat med ett annat virtuellt nätverk. Om du vill lägga till eller ta bort adressintervall, ta bort peering, lägga till eller ta bort adressintervallen, sedan skapa peerkopplingen på nytt. Om du vill lägga till-adressintervall till eller ta bort adressintervall från virtuella nätverk, se [hantera virtuella nätverk](manage-virtual-network.md).
- Du kan peer-två virtuella nätverk som distribuerats via Hanteraren för filserverresurser eller ett virtuellt nätverk som distribuerats via Resource Manager med ett virtuellt nätverk som distribuerats via den klassiska distributionsmodellen. Du kan inte peer-två virtuella nätverk som skapats via den klassiska distributionsmodellen. Om du inte är bekant med Azure distributionsmodeller läsa den [förstå Azure distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikel. Du kan använda en [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) för att ansluta två virtuella nätverk som har skapats via den klassiska distributionsmodellen.
- Vid peer-koppling av två virtuella nätverk som skapas via Resource Manager måste en peer-koppling konfigureras för varje virtuellt nätverk i peer-kopplingen. 
    - *Initierade:* när du skapar peering till det andra virtuella nätverket från det första virtuella nätverket är peering status *initierade*. 
    - *Ansluten:* när du skapar peering från andra virtuella nätverket till det första virtuella nätverket peering statusen är *ansluten*. Om du visa peering status för det första virtuella nätverket visas dess status har ändrats från *initierade* till *ansluten*. Peering har upprättas inte förrän peering status för båda peerkopplingar mellan virtuella nätverk är *ansluten*.
- När peering ett virtuellt nätverk som skapats via Resource Manager med ett virtuellt nätverk som skapats via den klassiska distributionsmodellen konfigurera du bara en peering för det virtuella nätverket som distribuerats via Resource Manager. Du kan inte konfigurera peering för ett virtuellt nätverk (klassiskt) eller mellan två virtuella nätverk som distribuerats via den klassiska distributionsmodellen. När du skapar peering från det virtuella nätverket (Resource Manager) till det virtuella nätverket (klassisk) peering status är *uppdatering*, strax ändras till *ansluten*.
- En peering upprättas mellan två virtuella nätverk. Peerkopplingar är inte transitiva. Om du skapar peerkopplingar mellan:
    - VirtualNetwork1 & VirtualNetwork2
    - VirtualNetwork2 & VirtualNetwork3

  Det finns ingen peering mellan VirtualNetwork1 och VirtualNetwork3 via VirtualNetwork2. Om du vill skapa ett virtuellt nätverk peering mellan VirtualNetwork1 och VirtualNetwork3, måste du skapa en peering mellan VirtualNetwork1 och VirtualNetwork3.
- Du kan inte matcha namn i peerkoppla virtuella nätverk med hjälp av standard-Azure namnmatchning. Om du vill matcha namnen på andra virtuella nätverk, måste du använda en anpassad DNS-server. Mer information om hur du ställer in DNS-servern, läsa den [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) artikel.
- Resurser i båda virtuella nätverken i peering kan kommunicera med varandra med samma bandbredd och svarstid som om de befann sig i samma virtuella nätverk. Storlek på varje virtuell dator har sin egen maximal nätverksbandbredd men. Om du vill veta mer om nätverkets maximala bandbredd för olika virtuella datorstorlekar läser du artiklarna om virtuella datorstorlekar i [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Du kan peer-virtuella nätverk som distribuerats via Resource Manager som ingår i samma eller olika prenumerationer.
- Du kan peer-virtuella nätverk som distribuerats via olika distributionsmodeller som är i samma eller olika prenumerationer. 
- Prenumerationer som båda virtuella nätverken i måste vara kopplad till samma Azure Active Directory-klient. Om du inte redan har en AD-klient, kan du snabbt [skapar du en](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json##create-a-new-azure-ad-tenant). Du kan använda en [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) att ansluta två virtuella nätverk som finns i olika prenumerationer som är kopplade till olika Active Directory-klienter.
- Ett virtuellt nätverk kan peerkoppla till ett annat virtuellt nätverk och också vara ansluten till ett annat virtuellt nätverk med en gateway för virtuella Azure-nätverket. När virtuella nätverk är anslutna via peering och en gateway, flödar trafik mellan virtuella nätverk via peering konfigurationen i stället för gatewayen.
- En nominell avgift tas ut för ingående och utgående trafik som använder virtuell nätverks-peering. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Behörigheter

De konton som du använder för att skapa ett virtuellt nätverk som peering måste ha rollen eller nödvändiga behörigheter. Till exempel om du peering två virtuella nätverk som heter myVnetA och myVnetB, måste ditt konto tilldelas rollen följande minsta eller behörigheter för varje virtuellt nätverk:
    
|Virtuellt nätverk|Distributionsmodell|Roll|Behörigheter|
|---|---|---|---|
|myVnetA|Resource Manager|[Nätverksdeltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klassisk|[Klassisk nätverksdeltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Gäller inte|
|myVnetB|Resource Manager|[Nätverksdeltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klassisk|[Klassisk nätverksdeltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Lär dig mer om [inbyggda roller](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) och tilldela särskilda behörigheter till [anpassade roller](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (enbart Resource Manager).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar en [nätverkstopologi med nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)

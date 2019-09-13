---
title: Konfigurera och verifiera VNet- eller VPN-anslutningar
description: Steg för steg-anvisningar för att konfigurera och validera olika distributioner av Azure VPN och VNet
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: fc4b649ce8d082d8d854c4c19b617c088ff3141c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901898"
---
# <a name="configure-and-validate-vnet-or-vpn-connections"></a>Konfigurera och verifiera VNet- eller VPN-anslutningar

Den här guidade genom gången innehåller stegvisa instruktioner för att konfigurera och validera olika distributioner av Azure VPN och VNet i scenarier som, till exempel transit routning, VNet-till-VNet, BGP, flera platser, punkt-till-plats och så vidare.

Med Azure VPN-gatewayer kan du ordna nästan alla typer av anslutna topologier för Virtual Network (VNet) i Azure: du kan ansluta virtuella nätverk över flera regioner mellan VNet-typer (Azure Resource Manager vs. Klassisk) i Azure eller med en lokal hybrid miljö, i olika prenumerationer och så vidare. 

## <a name="scenario-1-vnet-to-vnet-vpn-connection"></a>Scenario 1: VPN-anslutning mellan virtuella nätverk

Att ansluta ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) via VPN liknar att ansluta ett VNet till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att tillhandahålla en säker tunnel med **IPSec/IKE**. De virtuella nätverken kan finnas i samma eller olika regioner och i samma eller olika prenumerationer.

![AVBILDNING](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Bild 1 – VNet-till-VNet med IPsec

Om dina virtuella nätverk finns i samma region kan det vara bättre att ansluta dem med VNet-peering, som inte använder en VPN-gateway, ökar data flödet och minskar svars tiden, väljer **Konfigurera och validera VNet-peering** för att konfigurera en VNet-peering anslutningen.

Om dina virtuella nätverk båda har skapats med hjälp av distributions modellen för Azure Resource Manager väljer du **Konfigurera och validera ett Resource Manager VNet till en Resource Manager VNET-anslutning** för att konfigurera en VPN-anslutning.

Om en av virtuella nätverk har skapats med hjälp av den klassiska Azure-distributions modellen skapas den andra av Resource Manager, väljer du **Konfigurera och validerar ett klassiskt VNet till en Resource Manager VNET-anslutning** för att konfigurera en VPN-anslutning.

### <a name="configuration-1-configure-vnet-peering-for-two-vnets-in-the-same-region"></a>Konfiguration 1: Konfigurera VNet-peering för två virtuella nätverk i samma region

Innan du börjar implementera Azure VNet-peering ska du kontrol lera att du uppfyller följande krav för att konfigurera VNet-peering:

* Peerkopplade virtuella nätverk måste finnas i samma Azure-region.
* Peer-kopplat virtuella nätverk måste ha icke överlappande IP-adressutrymme.
* Peer-kopplingarna i virtuella nätverk upprättas mellan två virtuella nätverk. Det finns ingen härledd transitiv relation mellan peer-kopplingar. Om exempelvis VNetA peerkopplas till VNetB och VNetB peerkopplas till VNetC, är VNetA *inte* peerkopplat till VNetC.

När du uppfyller kraven kan du följa [själv studie kursen skapa en virtuell nätverks-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) för att skapa och konfigurera VNet-peering.

Använd följande metoder för att kontrol lera konfigurationen av VNet-peering:

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett konto som har de [roller och behörigheter](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)som krävs.
2. Skriv *virtuella nätverk*i rutan som innehåller text *Sök resurserna* överst i Azure Portal. När **virtuella nätverk** visas i Sök resultaten klickar du på det.
3. I bladet **virtuella nätverk** som visas klickar du på det virtuella nätverk som du vill skapa en peering för.
4. I fönstret som visas för det virtuella nätverk som du har valt klickar du på **peering** i avsnittet **Inställningar** .
5. Klicka på den peering som du vill kontrol lera konfigurationen.

![AVBILDNING](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Med hjälp av Azure PowerShell kör du kommandot [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) för att hämta det virtuella nätverkets peering, till exempel:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connection-type-1-connect-a-resource-manager-vnet-to-anther-resource-manager-vnet-azure-resource-manager-to-azure-resource-manager"></a>Anslutnings typ 1: Ansluta ett virtuellt Resource Manager-nätverk till anther Resource Manager VNet (Azure Resource Manager till Azure Resource Manager)

Du kan konfigurera en anslutning från ett Resource Manager VNet till ett annat Resource Manager VNet direkt eller konfigurera anslutningen med IPsec.

### <a name="configuration-2-configure-vpn-connection-between-resource-manager-vnets"></a>Konfiguration 2: Konfigurera VPN-anslutning mellan Resource Manager-virtuella nätverk

Information om hur du konfigurerar en anslutning mellan Resource Manager-virtuella nätverk utan IPsec finns i [Konfigurera en VNet-till-VNet VPN gateway-anslutning med hjälp av Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

Om du vill konfigurera en anslutning med IPsec mellan två Resource Manager-virtuella nätverk följer du stegen 1-5 i [skapa en plats-till-plats-anslutning i Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) för varje VNet.

> [!Note]
> De här stegen fungerar endast för olika VNet i samma prenumeration. Om dina VNet finns i olika prenumerationer, måste du använda PowerShell för att ansluta. Mer information finns i [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)-artikeln.

### <a name="validate-vpn-connection-between-resource-manager-vnets"></a>Verifiera VPN-anslutning mellan Resource Manager-virtuella nätverk

![AVBILDNING](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Bild 4 – klassisk VNet-anslutning till Azure Resource Manager VNet

Följ anvisningarna för att kontrol lera att VPN-anslutningen är korrekt konfigurerad:

> [!Note]
> Antalet efter virtuella nätverks komponenter, till exempel "VNet 1" i stegen nedan, motsvarar talen i bild 4.

1. Sök efter överlappande adress utrymmen i den anslutna virtuella nätverk.

   > [!Note]
   > Det går inte att överlappa adress utrymmen i VNet 1 och VNET 6. 

2. Verifiera att Azure Resource Manager VNet 1-adressintervall definieras korrekt i **Connection-objekt** 4.
3. Verifiera att Azure Resource Manager VNet 6-adressintervall definieras korrekt i **Connection-objekt** 3.
4. Kontrol lera att de i förväg delade nycklarna matchar både anslutnings objekt 3 och 4.
5. Verifiera att Azure Resource Manager VNet Gateway 2 VIP definieras korrekt i Connection- **objekt** 4.
6. Kontrol lera att Azure Resource Manager VNet Gateway 5 VIP definieras korrekt i **Connection-objektet** 3.

### <a name="connection-type-2-connect-a-classic-vnet-to-a-resource-manager-vnet"></a>Anslutnings typ 2: Ansluta ett klassiskt virtuellt nätverk till ett virtuellt nätverk för resurshanteraren

Du kan skapa en anslutning mellan virtuella nätverk som finns i olika prenumerationer och i olika regioner. Du kan också ansluta virtuella nätverk som redan har anslutningar till lokala nätverk, förutsatt att du har konfigurerat Gateway-typen som Route-baserad.

Om du vill konfigurera en anslutning mellan ett klassiskt VNet och ett Resource Manager VNet, se [ansluta virtuella nätverk från olika distributions modeller med hjälp av Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal) för mer information.

![AVBILDNING](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Bild 5 – klassisk VNet-anslutning till Azure Resource Manager VNet

Följ anvisningarna för att kontrol lera konfigurationen när du ansluter ett klassiskt VNet till ett Azure Resource Manager VNet:

> [!Note]
> Antalet efter virtuella nätverks komponenter som "VNet 1" i stegen nedan motsvarar talen i bild 5.

1. Sök efter överlappande adress utrymmen i den anslutna virtuella nätverk.

   > [!Note]
   > Det går inte att överlappa adress utrymmen i VNet 1 och VNET 6

2. Verifiera att Azure Resource Manager VNet 6-adressintervall definieras korrekt i den klassiska lokala nätverks definitionen 3.
3. Kontrol lera att det klassiska VNet 1-adressintervallet definieras korrekt i Azure Resource Manager **Connection-objekt** 4.
4. Kontrol lera att den klassiska VNet Gateway 2 VIP definieras korrekt i Azure Resource Manager **Connection-objekt** 4.
5. Verifiera att Azure Resource Manager VNet Gateway 5 VIP definieras korrekt i den klassiska **lokala nätverks definitionen** 3.
6. Kontrol lera att de i förväg delade nycklarna matchar både anslutna virtuella nätverk:
   1. Klassisk VNet-lokal nätverks definition 3
   2. Azure Resource Manager VNet – anslutnings objekt 4

## <a name="scenario-2-point-to-site-vpn-connection"></a>Scenario 2: Punkt-till-plats-VPN-anslutning

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. En punkt-till-plats-anslutning är användbar när du vill ansluta till ditt virtuella nätverk från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett virtuellt nätverk. P2S VPN-anslutningen initieras från klientdatorn med hjälp av den inbyggda Windows VPN-klienten. Anslutande klienter använder certifikat för autentisering.

![AVBILDNING](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Bild 2 – punkt-till-plats-anslutning

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet. P2S skapar en VPN-anslutning över SSTP (Secure Socket Tunneling Protocol). Du kan ansluta en punkt-till-plats-anslutning till ett VNet med hjälp av ett annat distributions verktyg och distributions modeller:

* [Konfigurera en punkt-till-plats-anslutning till ett VNet med Azure-portalen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurera en punkt-till-plats-anslutning till ett VNet med hjälp av Azure Portal (klassisk)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Konfigurera en punkt-till-plats-anslutning till ett VNet med PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connections"></a>Verifiera dina punkt-till-plats-anslutningar

Artikeln [fel sökning: Problem](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) med Azure punkt-till-plats-anslutningen går igenom vanliga problem med punkt-till-plats-anslutningar.

## <a name="scenario-3-multi-site-vpn-connection"></a>Scenario 3: VPN-anslutning för flera platser

Du kan lägga till en plats-till-plats-anslutning (S2S) till ett VNet som redan har en S2S-anslutning, punkt-till-plats-anslutning eller VNet-till-VNet-anslutning. den här typen av anslutning kallas ofta för en konfiguration **med flera platser** . 

![AVBILDNING](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Bild 3 – anslutning till flera platser

Azure fungerar för närvarande med två distributions modeller: Resource Manager och klassisk. De två modellerna är inte helt kompatibla med varandra. Om du vill konfigurera anslutningar för **flera platser** med olika modeller kan du läsa följande artiklar:

* [Lägga till en plats-till-plats-anslutning till ett VNet med en befintlig VPN gateway-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Lägga till en plats-till-plats-anslutning till ett VNet med en befintlig VPN gateway-anslutning (klassisk)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> De här stegen gäller inte för ExpressRoute och sambefintliga anslutningar från plats till plats. Mer information om sambefintliga anslutningar finns i [ExpressRoute/S2S-befintliga anslutningar](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

## <a name="scenario-4-configure-transit-routing"></a>Scenario 4: Konfigurera överförings dirigering

Transitiv routning är ett särskilt routningsgränssnitt där du ansluter flera nätverk i en topologi för seriekopplade-kedja. Den här dirigeringen aktiverar resurser i virtuella nätverk i båda ändar av "kedjan" för att kommunicera med varandra via virtuella nätverk. Utan transitiv routning kan inte nätverk eller enheter som peer-kopplas via en hubb komma åt varandra.

### <a name="configuration-1-configure-transit-routing-in-a-point-to-site-connection"></a>Konfiguration 1: Konfigurera transit routning i en punkt-till-plats-anslutning

I det här scenariot konfigurerar du en plats-till-plats-VPN-anslutning mellan VNetA och VNetB. du kan också konfigurera en plats-till-plats-VPN för klienten för att ansluta till gatewayen för VNetA. Sedan vill du aktivera transit routning för punkt-till-plats-klienter för att ansluta till VNetB, som passerar genom VNetA. Det här scenariot stöds när BGP är aktiverat på plats-till-plats-VPN mellan VNetA och VNetB. Mer information finns i [om punkt-till-plats-VPN-routning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing).

### <a name="configuration-2-configure-transit-routing-in-an-expressroute-connection"></a>Konfiguration 2: Konfigurera överförings dirigering i en ExpressRoute-anslutning

Med Microsoft Azure ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet via en dedikerad privat anslutning från en anslutningsleverantör. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och Dynamics 365.  Mer information finns i [Översikt över ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![AVBILDNING](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

Bild 6 – ExpressRoute ' privat peering '-anslutning till Azure virtuella nätverk

> [!Note]
> Vi rekommenderar att om VNetA och VNetB finns i samma naturpolitisk region som du [länkar båda virtuella nätverk till ExpressRoute-kretsen](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) i stället för att konfigurera en transitiv routning. Om dina virtuella nätverk finns i olika politiska regioner kan du också länka dem till din krets direkt om du har [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium). 

Om du har ExpressRoute och plats-till-plats-samexistens stöds inte transit routning. Mer information finns i [Konfigurera ExpressRoute-och plats-till-plats-sambefintliga anslutningar för mer information](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Om du har aktiverat ExpressRoute för att ansluta dina lokala nätverk till ett virtuellt Azure-nätverk kan du aktivera VNet-peering mellan den virtuella nätverk som du vill ha en transitiv routning. Om du vill tillåta att dina lokala nätverk ansluter till fjärrnätverket måste du konfigurera [peering för virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity). 

> [!Note]
> VNet-peering är endast tillgängligt för virtuella nätverk i samma region.

Följ instruktionerna nedan för att kontrol lera om du har konfigurerat överförings vägen för VNet-peering:

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett konto som har de [roller och behörigheter](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)som krävs.
2. [Skapa en peering mellan VNet a och B](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) som i diagrammet tidigare (bild 6). 
3. I fönstret som visas för det virtuella nätverk som du har valt klickar du på **peering** i avsnittet **Inställningar** .
4. Klicka på peering som du vill visa och **Konfigurera** för att kontrol lera att du har aktiverat **Tillåt Gateway-överföring** på den VNetA som är ansluten till ExpressRoute-kretsen och **Använd fjärrgateway** på fjärr VNetB som inte är ansluten till ExpressRoute krets.

### <a name="configuration-3-configure-transit-routing-in-a-vnet-peering-connection"></a>Konfiguration 3: Konfigurera överförings dirigering i en VNet-peering-anslutning

När virtuella nätverk har peer-kopplats kan du också konfigurera gatewayen i det peer-kopplade virtuella nätverket som en överföringspunkt till det lokala nätverket. Om du vill konfigurera överförings vägen i VNet-peering, kontrollerar [du virtuella nätverk-till-virtuella-nätverksanslutningar](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json).

> [!Note]
> Gateway-överföring stöds inte i peering-relationen mellan virtuella nätverk som skapats via olika distributions modeller. Båda de virtuella nätverken i peering-relationen måste ha skapats via resurshanteraren för att en gatewayöverföring ska fungera.

Följ instruktionerna nedan för att kontrol lera om du har konfigurerat överförings vägen för VNet-peering:

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett konto som har de [roller och behörigheter](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)som krävs.
2. Skriv *virtuella nätverk*i rutan som innehåller text Sök resurserna överst i Azure Portal. När **virtuella nätverk** visas i Sök resultaten klickar du på det.
3. I bladet **virtuella nätverk** som visas klickar du på det virtuella nätverk som du vill kontrol lera peering-inställningen.
4. I fönstret som visas för det virtuella nätverk som du har valt klickar du på **peering** i avsnittet **Inställningar** .
5. Klicka på peering som du vill visa och kontrol lera att du har aktiverat **Tillåt Gateway-överföring** och **Använd fjärr-Gateway** under **konfiguration**.

![AVBILDNING](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configuration-4-configure-transit-routing-in-a-vnet-to-vnet-connection"></a>Konfiguration 4: Konfigurera överförings dirigering i en VNet-till-VNet-anslutning

Om du vill konfigurera transit routning mellan virtuella nätverk måste du aktivera BGP på alla mellanliggande VNet-till-VNet-anslutningar med hjälp av distributions modellen för Resource Manager och PowerShell. Instruktioner finns i [så här konfigurerar du BGP på Azure VPN-gatewayer med hjälp av PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Det går att använda en överförings trafik via Azure VPN-gatewayen med hjälp av den klassiska distributions modellen, men den förlitar sig på statiska definierade adress utrymmen i nätverks konfigurations filen. BGP stöds inte ännu med virtuella Azure-nätverk och VPN-gatewayer med hjälp av den klassiska distributions modellen. Utan BGP är manuell definiering av överförings adress utrymmen fel känsligt och rekommenderas inte.

> [!Note]
> Klassiska VNet-till-VNet-anslutningar konfigureras med hjälp av Azure Portal (klassisk) eller med hjälp av en nätverks konfigurations fil på den klassiska portalen. Du kan inte skapa eller ändra ett klassiskt virtuellt nätverk via Azure Resource Manager distributions modell eller Azure Portal. Mer information om transit routning för klassisk virtuella nätverk finns i [hubben & eker, seriekopplade-Chain och fullständig VNet-topologier i Azure arm med VPN (v1)](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

### <a name="configuration-5-configure-transit-routing-in-a-site-to-site-connection"></a>Konfiguration 5: Konfigurera överförings dirigering i en plats-till-plats-anslutning

Om du vill konfigurera överförings dirigeringen mellan ditt lokala nätverk och ett VNet med en plats-till-plats-anslutning måste du aktivera BGP på alla mellanliggande plats-till-plats-anslutningar med hjälp av distributions modellen för Resource Manager och PowerShell, se [så här konfigurerar du BGP på Azure VPN-gatewayer med hjälp av PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) för instruktioner.

Det går att använda en överförings trafik via Azure VPN-gatewayen med hjälp av den klassiska distributions modellen, men den förlitar sig på statiska definierade adress utrymmen i nätverks konfigurations filen. BGP stöds inte ännu med virtuella Azure-nätverk och VPN-gatewayer med hjälp av den klassiska distributions modellen. Utan BGP är manuell definiering av överförings adress utrymmen fel känsligt och rekommenderas inte.

> [!Note]
> Klassiska plats-till-plats-anslutningar konfigureras med hjälp av Azure Portal (klassisk) eller med hjälp av en nätverks konfigurations fil på den klassiska portalen. Du kan inte skapa eller ändra ett klassiskt virtuellt nätverk via Azure Resource Manager distributions modell eller Azure Portal. Mer information om transit routning för klassisk virtuella nätverk finns i [hubben & eker, seriekopplade-Chain och fullständig VNet-topologier i Azure arm med VPN (v1)](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

## <a name="scenario-5-configure-bgp-for-a-vpn-gateway"></a>Scenario 5: Konfigurera BGP för en VPN-gateway

BGP är det standard routningsprotokoll som används i Internet för att utbyta Routning och tillgänglighets information mellan två eller flera nätverk. När BGP används i Azure Virtual Networks-kontexter, aktiverar BGP Azure VPN-gatewayer och dina lokala VPN-enheter, som kallas BGP-peers eller grannar. De utbyter "vägar" som kommer att informera båda gatewayerna om tillgänglighet och tillgänglighet för dessa prefix för att gå igenom de gatewayer eller routrar som ingår. BGP kan också möjliggöra överföringsroutning mellan flera nätverk genom att sprida vägar som BGP-gatewayen får information om från en BGP-peer till alla andra BGP-peers. Mer information finns i [Översikt över BGP med Azure VPN-gatewayer](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>Konfigurera BGP för en VPN-anslutning

Information om hur du konfigurerar en VPN-anslutning som använder BGP finns i [så här konfigurerar du BGP på Azure VPN-gatewayer med hjälp av PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

> [!Note]
> 1. Aktivera BGP på Virtual Network Gateway genom att skapa ett AS-nummer för det. Basic gateways stöder inte BGP. Om du vill kontrol lera SKU: n för gatewayen går du till översikts avsnittet på bladet VPN Gateway i Azure Portal. Om SKU: n är **Basic**måste du ändra SKU: n (ändra[storlek på gatewayen](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)) till **VpnGw1** SKU. Genom att kontrol lera SKU: n kan du göra 20-30 minuter stillestånds tid. Så snart gatewayen har rätt SKU kan du lägga till som kan läggas till via [set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell-kommandot-let. När du har konfigurerat AS-numret kommer en BGP peer-IP för gatewayen att tillhandahållas automatiskt.
> 2. LocalNetworkGateway måste anges **manuellt** med ett as-nummer och en BGP-peer-adress. Du kan ange **ASN** -och **-BgpPeeringAddress-** värden med hjälp av antingen [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) eller [set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell kommando-let. Vissa tal är reserverade för Azure och du kan inte använda dem som beskrivs i [om BGP med azure VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview#bgp-faq).
> 3. Slutligen måste objektet ha BGP aktiverat. Du kan ange värdet **EnableBGP** till `$True` genom [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) eller [set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>Verifiera BGP-konfigurationen

Du kan kontrol lera om BGP har kon figurer ATS korrekt genom att `get-AzureRmVirtualNetworkGateway` köra `get-AzureRmLocalNetworkGateway`cmdleten och sedan Observera BGP-relaterade utdata i BgpSettingsText-delen. Exempel: BgpSettingsText:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="scenario-6-highly-available-active-active-vpn-connection"></a>Scenario 6: Aktiv-aktiv VPN-anslutning med hög tillgänglighet

De viktigaste skillnaderna mellan de aktiva och aktiva standby-gatewayerna:

* Du måste skapa två Gateway-IP-konfigurationer med två offentliga IP-adresser
* Du måste ange flaggan *EnableActiveActiveFeature*
* Gateway-SKU: n måste vara VpnGw1, VpnGw2, VpnGw3

För att uppnå hög tillgänglighet för anslutningar mellan olika platser och VNet-till-VNet bör du distribuera flera VPN-gatewayer och upprätta flera parallella anslutningar mellan dina nätverk och Azure. En översikt över anslutnings alternativ och topologi finns i [hög tillgängliga anslutningar mellan olika platser och VNET-till-VNET-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable).

Om du vill skapa aktiva, aktiva och VNet-till-VNet-anslutningar, följer du anvisningarna i [Konfigurera Active-Active-aktiva S2S VPN-anslutningar med Azure VPN-gatewayer](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) för att konfigurera Azure VPN gateway i aktivt/aktivt läge.

> [!Note]  
> 1. När du lägger till adresser i den lokala Nätverksgatewayen för BGP aktiverat, *lägger du endast till/32-adresserna för BGP-peer-adresserna i*det aktiva till aktivt läge. Om du lägger till fler adresser betraktas de som statiska vägar och prioriteras framför BGP-vägar.
> 2. Du måste använda olika BGP-ASN: er för dina lokala nätverk som ansluter till Azure. (Om de är samma måste du ändra ditt VNet ASN om din lokala VPN-enhet redan använder ASN för att peer-koppla med andra BGP-grannar.)

## <a name="scenario-7-change-an-azure-vpn-gateway-type-after-deployment"></a>Scenario 7: Ändra en Azure VPN gateway-typ efter distributionen

Du kan inte ändra en Azure VNet Gateway-typ från principbaserad till Route-baserad eller det andra sättet direkt. Du måste ta bort gatewayen, efter att IP-adressen och PSK-won'tbe (i förväg delad nyckel) bevaras. Sedan kan du skapa en ny Gateway av önskad typ. Följ stegen nedan om du vill ta bort och skapa en gateway:

1. Ta bort alla anslutningar som är associerade med den ursprungliga gatewayen.
2. Ta bort gatewayen med hjälp av Azure Portal, PowerShell eller klassisk PowerShell: 
   * [Ta bort en virtuell nätverksgateway med hjälp av Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Ta bort en virtuell nätverksgateway med PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Ta bort en virtuell nätverksgateway med PowerShell (klassisk)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Följ stegen i [skapa VPN-gatewayen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#a-namevnetgatewaya4-create-the-vpn-gateway) för att skapa den nya gatewayen av önskad typ och slutför VPN-installationen.

> [!Note]
> Processen tar cirka 60 minuter.

## <a name="next-steps"></a>Nästa steg

* [Felsökning av anslutningsproblem mellan virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)


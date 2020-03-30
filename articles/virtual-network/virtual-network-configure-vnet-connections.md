---
title: Konfigurera och validera virtuella nätverks- eller VPN-anslutningar
description: Steg-för-steg-vägledning för att konfigurera och validera olika Azure VPN- och virtuella nätverksdistributioner
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
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099062"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Konfigurera och validera virtuella nätverks- eller VPN-anslutningar

Den här genomgången ger steg-för-steg-vägledning för att konfigurera och validera olika Azure VPN- och virtuella nätverksdistributioner. Scenarier inkluderar transitroutning, nätverks-till-nätverk-anslutningar, BGP (Border Gateway Protocol), anslutningar på flera platser och point-to-site-anslutningar.

Azure VPN-gateways möjliggör flexibilitet i att ordna nästan alla typer av ansluten virtuell nätverkstopologi i Azure. Du kan till exempel ansluta virtuella nätverk:

- I olika regioner.
- Mellan virtuella nätverkstyper (Azure Resource Manager kontra classic).
- Inom Azure eller i en lokal hybridmiljö.
- I olika abonnemang. 

## <a name="network-to-network-vpn-connection"></a>VPN-anslutning mellan nätverk och nätverk

Att ansluta ett virtuellt nätverk till ett annat virtuellt nätverk (nätverk-till-nätverk) via VPN liknar att ansluta ett virtuellt nätverk till en lokal plats. Båda anslutningstyperna använder en VPN-gateway för att tillhandahålla en säker tunnel genom IPsec och IKE. De virtuella nätverken kan finnas i samma eller olika regioner och i samma eller olika prenumerationer.

![Nätverks-till-nätverk-anslutning med IPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Om dina virtuella nätverk finns i samma region kanske du vill överväga att ansluta dem med hjälp av virtuell nätverks peering. Virtuell nätverks peering använder inte en VPN-gateway. Det ökar dataflödet och minskar svarstiden. Om du vill konfigurera en virtuell nätverks peering-anslutning väljer du **Konfigurera och valideraR VNet-peering**.

Om dina virtuella nätverk skapades via Azure Resource Manger-distributionsmodellen väljer du **Konfigurera och validera ett Resurshanterarens virtuella nätverk till en Resource Manager-VNet-anslutning** för att konfigurera en VPN-anslutning.

Om ett av de virtuella nätverken skapades via azure classic deployment model och den andra skapades via Resource Manager väljer du **Konfigurera och validera ett klassiskt virtuellt nätverk till en Resource Manager VNet-anslutning** för att konfigurera en VPN-anslutning.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Konfigurera virtuell nätverks peering för två virtuella nätverk i samma region

Innan du börjar implementera och konfigurera Azure-peering för virtuella nätverk ska du se till att du uppfyller följande förutsättningar:

* Peerkopplade virtuella nätverk måste finnas i samma Azure-region.
* De peer-inrade virtuella nätverken måste ha IP-adressutrymmen som inte överlappar varandra.
* Peer-kopplingarna i virtuella nätverk upprättas mellan två virtuella nätverk. Det finns ingen härledd transitiv relation mellan peerings. Om VNetA till exempel är peered med VNetB och VNetB är peered med VNetC, är VNetA *inte* peered med VNetC.

När du uppfyller kraven kan du följa [Självstudiekurs: Anslut virtuella nätverk med virtuell nätverks peering med hjälp av Azure-portalen](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) för att skapa och konfigurera peering.

Så här kontrollerar du peering-konfigurationen:

1. Logga in på [Azure-portalen](https://portal.azure.com/) med hjälp av ett konto som har nödvändiga [roller och behörigheter](virtual-network-manage-peering.md#permissions).
2. Skriv **virtuella nätverk**i rutan som innehåller texten **Sökresurser** högst upp i portalen . När **virtuella nätverk** visas i sökresultaten markerar du det.
3. I bladet **Virtuella nätverk** som visas väljer du det virtuella nätverk som du vill skapa en peering för.
4. Välj **Peerings** i avsnittet **Inställningar** i fönstret som visas för det virtuella nätverket.
5. Välj en peering och visa konfigurationsresultaten.

![Val för att kontrollera konfigurationen för virtuell nätverks peering](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
För Azure PowerShell kör du kommandot [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) för att hämta den virtuella nätverks peering. Här är ett exempel:

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

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Ansluta ett virtuellt resurshanterare-nätverk till ett annat virtuellt resurshanteraren-nätverk

Du kan konfigurera en anslutning från ett virtuellt resurshanterarenätverk till ett annat virtuellt resurshanterarenätverk direkt. Eller så kan du konfigurera anslutningen med hjälp av IPsec.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Konfigurera en VPN-anslutning mellan virtuella Resurshanterarens virtuella nätverk

Information om hur du konfigurerar en anslutning mellan virtuella Resource Manager-nätverk utan IPsec finns i [Konfigurera en VPN-gateway-anslutning för nätverk med hjälp av Azure-portalen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

Om du vill konfigurera en anslutning med IPsec mellan två virtuella Resource Manager-nätverk följer du steg 1 till 5 i [Skapa en plats-till-plats-anslutning i Azure-portalen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) för varje virtuellt nätverk.

> [!Note]
> De här stegen fungerar bara för virtuella nätverk i samma prenumeration. Om dina virtuella nätverk finns i olika prenumerationer måste du använda PowerShell för att upprätta anslutningen. Mer information finns i [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)-artikeln.

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Verifiera VPN-anslutningen mellan virtuella Nätverk för Resource Manager

![Klassisk virtuell nätverksanslutning till ett virtuellt Azure Resource Manager-nätverk](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Följ dessa instruktioner om du vill kontrollera att VPN-anslutningen är korrekt konfigurerad.

> [!Note] 
> Siffrorna efter virtuella nätverkskomponenter i dessa steg motsvarar siffrorna i föregående diagram.

1. Kontrollera att det inte finns några överlappande adressutrymmen i de anslutna virtuella nätverken.
2. Kontrollera att adressintervallet för det virtuella nätverket Azure Resource Manager (1) har definierats korrekt i **anslutningsobjektinstansen** (4).
3. Kontrollera att adressintervallet för det virtuella nätverket Azure Resource Manager (6) har definierats korrekt i **anslutningsobjektinstansen** (3).
4. Kontrollera att de fördelade nycklarna matchar på anslutningsobjekten.
5. Kontrollera att Azure Resource Manager virtuella nätverksgateway VIP (2) har definierats korrekt i **connection-objektinstansen** (4).
6. Kontrollera att Azure Resource Manager virtuella nätverksgateway VIP (5) har definierats korrekt i **connection-objektinstansen** (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Ansluta ett klassiskt virtuellt nätverk till ett virtuellt resurshanterarenätverk

Du kan skapa en anslutning mellan virtuella nätverk som finns i olika prenumerationer och i olika regioner. Du kan också ansluta virtuella nätverk som redan har anslutningar till lokala nätverk, så länge du har konfigurerat gatewaytypen som vägbaserad.

Information om hur du konfigurerar en anslutning mellan ett klassiskt virtuellt nätverk och ett virtuellt resurshanterarenätverk finns i [Ansluta virtuella nätverk från olika distributionsmodeller med hjälp av Azure-portalen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal).

![Klassisk virtuell nätverksanslutning till ett virtuellt Azure Resource Manager-nätverk](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Om du vill kontrollera konfigurationen när du ansluter ett klassiskt virtuellt nätverk till ett virtuellt Azure Resource Manager-nätverk följer du dessa instruktioner.

> [!Note] 
> Siffrorna efter virtuella nätverkskomponenter i dessa steg motsvarar siffrorna i föregående diagram. 

1. Kontrollera att det inte finns några överlappande adressutrymmen i de anslutna virtuella nätverken.
2. Kontrollera att adressintervallet för det virtuella nätverket Azure Resource Manager (6) har definierats korrekt i den klassiska definitionen av lokalt nätverk (3).
3. Kontrollera att adressintervallet för det klassiska virtuella nätverket (1) definieras korrekt i Azure Resource Manager **Connection-objektinstansen** (4).
4. Kontrollera att den klassiska VIP-nätverksgatewayen för virtuellt nätverk (2) har definierats korrekt i azure Resource Manager **Connection-objektinstansen** (4).
5. Kontrollera att Azure Resource Manager virtuella nätverksgateway (5) definieras korrekt i den klassiska **local network definition-instansen** (3).
6. Kontrollera att de fördelade nycklarna matchar i båda anslutna virtuella nätverk:
   - Klassiskt virtuellt nätverk: **Definition av lokalt nätverk** (3)
   - Virtuellt nätverk i Azure Resource Manager: **Anslutningsobjekt** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Skapa en vpn-anslutning från punkt till plats

Med en punkt-till-plats-konfiguration *(P2S* i följande diagram) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. Point-to-site-anslutningar är användbara när du vill ansluta till det virtuella nätverket från en fjärrplats, till exempel hemifrån eller en konferens. De är också användbara när du bara har ett fåtal klienter som behöver ansluta till ett virtuellt nätverk. 

VPN-anslutningen mellan punkt och plats initieras från klientdatorn via den inbyggda Windows VPN-klienten. Anslutande klienter använder certifikat för autentisering.

![Punkt-till-plats-anslutning](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Point-to-site-anslutningar kräver inte en VPN-enhet. De skapar VPN-anslutningen över Secure Socket Tunneling Protocol (SSTP). Du kan ansluta en punkt-till-plats-anslutning till ett virtuellt nätverk med hjälp av olika distributionsverktyg och distributionsmodeller:

* [Konfigurera en point-to-site-anslutning till ett virtuellt nätverk med hjälp av Azure-portalen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurera en point-to-site-anslutning till ett virtuellt nätverk med hjälp av Azure-portalen (klassisk)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>Verifiera din punkt-till-plats-anslutning

Artikeln [Felsökning: Azure point-to-site-anslutningsproblem](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) går igenom vanliga problem med point-to-site-anslutningar.

## <a name="create-a-multisite-vpn-connection"></a>Skapa en VPN-anslutning på flera plats

Du kan lägga till en plats-till-plats-anslutning *(S2S* i följande diagram) i ett virtuellt nätverk som redan har en anslutning från plats till plats, punkt-till-plats-anslutning eller nätverks-till-nätverksanslutning. Den här typen av anslutning kallas ofta för en *konfiguration på flera enheter.* 

![Anslutning på flera enheter](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure fungerar för nuvarande med två distributionsmodeller: Resource Manager och klassisk. De två modellerna är inte helt kompatibla med varandra. Så här konfigurerar du en multisite-anslutning med olika modeller:

* [Lägga till en plats-till-plats-anslutning till ett virtuellt nätverk med en befintlig VPN-gatewayanslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Lägga till en plats-till-plats-anslutning till ett virtuellt nätverk med en befintlig VPN-gatewayanslutning (klassisk)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> Stegen i dessa artiklar gäller inte för Azure ExpressRoute och samtidiga anslutningskonfigurationer från plats till plats. Mer information finns i [ExpressRoute och samtidiga anslutningar](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)från plats till plats .

## <a name="configure-transit-routing"></a>Konfigurera transitroutning

Transitroutning är ett specifikt routningsscenario där du ansluter flera nätverk i en daisy-chain-topologi. Den här routningen gör det möjligt för resurser i virtuella nätverk i vardera änden av kedjan att kommunicera med varandra via virtuella nätverk däremellan. Utan transitroutning kan nätverk eller enheter som peer-data från ett nav inte nå varandra.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Konfigurera transitroutning i en punkt-till-plats-anslutning

Föreställ dig ett scenario där du vill konfigurera en plats-till-plats VPN-anslutning mellan VNetA och VNetB. Du vill också konfigurera en punkt-till-plats-VPN för klienten att ansluta till gatewayen för VNetA. Sedan vill du aktivera transitroutning för point-to-site-klienter för att ansluta till VNetB, som passerar via VNetA. 

Det här scenariot stöds när BGP är aktiverat på plats-till-plats-VPN mellan VNetA och VNetB. Mer information finns i [Om punkt-till-plats VPN-routning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing).

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Konfigurera transitroutning i en ExpressRoute-anslutning

Med Azure ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet via en dedikerad privat anslutning från en anslutningsleverantör. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och Dynamics 365. Mer information finns i [Översikt över ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![ExpressRoute privat peering-anslutning till virtuella Azure-nätverk](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Vi rekommenderar att du, om VNetA och VNetB befinner sig i samma geopolitiska region, [länkar båda virtuella nätverken till ExpressRoute-kretsen](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) i stället för att konfigurera transitroutning. Om dina virtuella nätverk finns i olika geopolitiska regioner kan du också länka dem till din krets direkt om du har [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium). 

Om du har ExpressRoute och samlevnad från plats till plats stöds inte transitroutning. Mer information finns i [Konfigurera ExpressRoute och plats-till-plats med PowerShell](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Om du har aktiverat ExpressRoute för att ansluta dina lokala nätverk till ett virtuellt Azure-nätverk kan du aktivera peering mellan de virtuella nätverk där du vill ha transitroutning. Om du vill att lokala nätverk ska kunna ansluta till det virtuella fjärrnätverket måste du konfigurera [virtuell nätverks peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity). 

> [!Note]
> Virtuell nätverks peering är endast tillgängligt för virtuella nätverk i samma region.

Så här kontrollerar du om du har konfigurerat transitroutning för virtuell nätverks peering:

1. Logga in på [Azure-portalen](https://portal.azure.com/) med hjälp av ett konto som har nödvändiga [roller och behörigheter](virtual-network-manage-peering.md#permissions).
2. [Skapa en peering mellan VNetA och VNetB](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) som visas i det tidigare diagrammet. 
3. Välj **Peerings** i avsnittet **Inställningar** i fönstret som visas för det virtuella nätverket.
4. Markera den peering du vill visa. Välj sedan **Konfiguration** för att verifiera att du har aktiverat **Tillåt gatewaytransit i** VNetA-nätverket som är anslutet till ExpressRoute-kretsen och Använd **fjärrgateway** på det fjärr-VNetB-nätverk som inte är anslutet till ExpressRoute-kretsen.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Konfigurera transitroutning i en virtuell nätverks peering-anslutning

När virtuella nätverk har peer-kopplats kan du också konfigurera gatewayen i det peer-kopplade virtuella nätverket som en överföringspunkt till det lokala nätverket. Om du vill konfigurera en transitväg i virtuella nätverks peering läser du [Nätverks-till-nätverk-anslutningar](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json).

> [!Note]
> Gateway-överföring stöds inte i peering-relationen mellan virtuella nätverk som skapas via olika distributionsmodeller. Båda virtuella nätverken i peering-relationen måste ha skapats via Resource Manager för att gatewaytransiteringen ska fungera.

Så här kontrollerar du om du har konfigurerat en transitväg för virtuell nätverks peering:

1. Logga in på [Azure-portalen](https://portal.azure.com/) med hjälp av ett konto som har nödvändiga [roller och behörigheter](virtual-network-manage-peering.md#permissions).
2. Skriv **virtuella nätverk**i rutan som innehåller texten **Sökresurser** högst upp i portalen . När **virtuella nätverk** visas i sökresultaten markerar du det.
3. I bladet **Virtuella nätverk** som visas väljer du det virtuella nätverk som du vill kontrollera peering-inställningen för.
4. Välj **Peerings** i avsnittet **Inställningar** i fönstret som visas för det virtuella nätverk som du har valt.
5. Markera den peering som du vill visa. Verifiera att du har aktiverat **Tillåt gatewaytransit och** **Använd fjärrgateways** under **Konfiguration**.

![Val för kontroll av att du har konfigurerat en transitväg för virtuell nätverks peering](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Konfigurera transitroutning i en nätverks-till-nätverksanslutning

Om du vill konfigurera transitroutning mellan virtuella nätverk måste du aktivera BGP för alla mellanliggande nätverks-till-nätverksanslutningar med hjälp av Resurshanterarens distributionsmodell och PowerShell. Instruktioner finns i [Så här konfigurerar du BGP på Azure VPN-gateways med PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Transittrafik via Azure VPN-gateways är möjlig via den klassiska distributionsmodellen, men som är beroende av statiskt definierade adressutrymmen i nätverkskonfigurationsfilen. BGP stöds ännu inte med virtuella Azure-nätverk och VPN-gateways via den klassiska distributionsmodellen. Utan BGP är det felbenägna att manuellt definiera transitadressutrymmen, och vi rekommenderar det inte.

> [!Note]
> Du konfigurerar klassiska nätverks-till-nätverk-anslutningar med hjälp av den klassiska Azure-portalen eller med hjälp av en nätverkskonfigurationsfil i den klassiska portalen. Du kan inte skapa eller ändra ett klassiskt virtuellt nätverk via Distributionsmodellen för Azure Resource Manager eller Azure-portalen. Mer information om transitroutning för klassiska virtuella nätverk finns i [bloggen Microsoft Developer](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Konfigurera transitroutning i en anslutning från plats till plats

Om du vill konfigurera transitroutning mellan det lokala nätverket och ett virtuellt nätverk med en anslutning från plats till plats måste du aktivera BGP på alla mellanliggande plats-till-plats-anslutningar med hjälp av Resurshanterarens distributionsmodell och PowerShell. Se [Så här konfigurerar du BGP på Azure VPN-gateways med hjälp av PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) för instruktioner.

Transittrafik via Azure VPN-gateways är möjlig via den klassiska distributionsmodellen, men som är beroende av statiskt definierade adressutrymmen i nätverkskonfigurationsfilen. BGP stöds ännu inte med virtuella Azure-nätverk och VPN-gateways via den klassiska distributionsmodellen. Utan BGP är det felbenägna att manuellt definiera transitadressutrymmen, och vi rekommenderar det inte.

> [!Note]
> Du konfigurerar klassiska plats-till-plats-anslutningar med hjälp av den klassiska Azure-portalen eller med hjälp av en nätverkskonfigurationsfil i den klassiska portalen. Du kan inte skapa eller ändra ett klassiskt virtuellt nätverk via Distributionsmodellen för Azure Resource Manager eller Azure-portalen. Mer information om transitroutning för klassiska virtuella nätverk finns i [bloggen Microsoft Developer](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

## <a name="configure-bgp-for-a-vpn-gateway"></a>Konfigurera BGP för en VPN-gateway

BGP är standard routningsprotokollet som används på Internet för att utbyta routning och information om räckviddsbarhet mellan två eller flera nätverk. När BGP används i samband med virtuella Azure-nätverk aktiveras Azure VPN-gateways och dina lokala VPN-enheter, så kallade BGP-peer-datorer eller grannar. De utbyter "rutter" som kommer att informera båda gateways om tillgänglighet och nåbarhet för dessa prefix att gå igenom gateways eller routrar inblandade. 

BGP kan också aktivera transitroutning mellan flera nätverk genom att sprida vägar som en BGP-gateway lär sig från en BGP-peer till alla andra BGP-peer-datorer. Mer information finns i [Översikt över BGP med Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>Konfigurera BGP för en VPN-anslutning

Information om hur du konfigurerar en VPN-anslutning som använder BGP finns i [Så här konfigurerar du BGP på Azure VPN-gateways med PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Aktivera BGP på den virtuella nätverksgatewayen genom att skapa ett autonomt systemnummer (AS) för den. Grundläggande gateways stöder inte BGP. Om du vill kontrollera SKU för gatewayen går du till avsnittet **Översikt för** **bladet VPN Gateway** i Azure-portalen. Om din SKU är **Basic**måste du ändra SKU (se [Ändra storlek på gatewayen)](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)till **VpnGw1**. 

Om du kontrollerar SKU orsaka 20 till 30 minuters driftstopp. Så fort gatewayen har rätt SKU kan du lägga till AS-numret med hjälp av [Commandlet set-AzureRmVirtualNetworkGateway.](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) När du har konfigurerat AS-numret tillhandahålls en BGP-peer-IP för gatewayen automatiskt.

Du måste manuellt `LocalNetworkGateway` ange ett AS-nummer och en BGP-peer-adress. Du kan `ASN` ange `-BgpPeeringAddress` värden och värden med hjälp av antingen [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) eller [Commandlet Set-AzureRmLocalNetworkGateway.](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) Vissa AS-nummer är reserverade för Azure och du kan inte använda dem enligt beskrivningen i [Om BGP med Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md#faq).

Anslutningsobjektet måste ha BGP aktiverat. Du kan `-EnableBGP` ange `$True` värdet till via [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) eller [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>Verifiera BGP-konfigurationen

Om du vill kontrollera om BGP är `get-AzureRmVirtualNetworkGateway` korrekt `get-AzureRmLocalNetworkGateway` konfigurerat kan du köra kommandona och kommandona. Då kommer du att märka BGP-relaterade utdata i delen. `BgpSettingsText` Ett exempel:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Skapa en aktiv/aktiv VPN-anslutning med högtillgänge

De viktigaste skillnaderna mellan aktiva/aktiva och aktiva/standby-gateways är:

* Du måste skapa två gateway IP-konfigurationer med två offentliga IP-adresser.
* Du måste ange flaggan **EnableActiveActiveFeature.**
* Gatewayen SKU måste vara **VpnGw1**, **VpnGw2**eller **VpnGw3**.

För att uppnå hög tillgänglighet för anslutning mellan lokala och nätverk till nätverk bör du distribuera flera VPN-gateways och upprätta flera parallella anslutningar mellan dina nätverk och Azure. En översikt över anslutningsalternativ och topologi finns i Anslutning [mellan lokala och nätverksanslutningar](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)som är tillgängliga för alla tillgängliga nätverk och nätverk.

Om du vill skapa aktiva/aktiva korslokala anslutningar och nätverks-till-nätverk-anslutningar följer du instruktionerna i [Konfigurera aktiva/aktiva S2S VPN-anslutningar med Azure VPN-gateways](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) för att konfigurera en Azure VPN-gateway i aktivt/aktivt läge.

> [!Note]  
> * När du lägger till adresser i den lokala nätverksgatewayen för aktivt/aktivt läge för BGP *lägger du bara till /32-adresserna för BGP-peer-datorerna*. Om du lägger till fler adresser betraktas de som statiska vägar och har företräde framför BGP-vägar.
> * Du måste använda olika BGP AS-nummer för lokala nätverk som ansluter till Azure. (Om de är desamma måste du ändra ditt virtuella nätverks-AS-nummer om din lokala VPN-enhet redan använder ASN för att peer med andra BGP-grannar.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Ändra en Azure VPN-gatewaytyp efter distribution

Du kan inte ändra en Azure-typ av virtuell nätverksgateway från principbaserad till vägbaserad eller åt annat håll direkt. Du måste först ta bort gatewayen. Därefter bevaras inte IP-adressen och den fördelade nyckeln. Sedan kan du skapa en ny gateway av önskad typ. 

Så här tar du bort och skapar en gateway:

1. Ta bort alla anslutningar som är associerade med den ursprungliga gatewayen.
2. Ta bort gatewayen med hjälp av Azure-portalen, PowerShell eller klassiska PowerShell: 
   * [Ta bort en virtuell nätverksgateway med hjälp av Azure-portalen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Ta bort en virtuell nätverksgateway med PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Ta bort en virtuell nätverksgateway med PowerShell (klassiskt)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Följ stegen i [Skapa VPN-gatewayen](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway) för att skapa den nya gatewayen av önskad typ och slutföra VPN-installationen.

> [!Note]
> Denna process tar cirka 60 minuter.

## <a name="next-steps"></a>Nästa steg

* [Felsökning av anslutningsproblem mellan virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)


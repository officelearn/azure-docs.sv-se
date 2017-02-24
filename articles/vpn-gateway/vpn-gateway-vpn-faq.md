---
title: "Vanliga frågor och svar om Azure VPN Gateway | Microsoft Docs"
description: "Vanliga frågor och svar om VPN Gateway. Vanliga frågor och svar om Microsoft Azure Virtual Network-anslutningar på flera platser, konfiguration av hybridanslutningar och VPN-gateways."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: 6ce36765-250e-444b-bfc7-5f9ec7ce0742
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 15ac382f72cab455246ffcc05f08c8aba5876c8f
ms.openlocfilehash: c90bb4f41661aedec2bde53abe035fe9bcc80320


---
# <a name="vpn-gateway-faq"></a>Vanliga frågor och svar om VPN Gateway
## <a name="connecting-to-virtual-networks"></a>Ansluta till virtuella nätverk
### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Kan jag ansluta till virtuella nätverk i olika Azure-regioner?
Ja. Faktum är att det inte finns någon regionbegränsning. Ett virtuellt nätverk kan ansluta till ett annat virtuellt nätverk i samma region eller i en annan Azure-region. 

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Kan jag ansluta till virtuella nätverk i olika prenumerationer?
Ja.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Kan jag ansluta till flera platser från en enda virtuellt nätverk?
Du kan ansluta till flera platser med hjälp av Windows PowerShell och Azure REST-API:er. Se [Multisite- och VNet-till-VNet-anslutning](#V2VMulti), avsnittet Vanliga frågor och svar.

### <a name="what-are-my-cross-premises-connection-options"></a>Vilka alternativ finns det för min anslutning till flera platser?
Följande anslutningar mellan flera platser stöds:

* [Plats-till-plats](vpn-gateway-howto-site-to-site-resource-manager-portal.md) – VPN-anslutning via IPsec (IKE v1 och IKE v2). Den här typen av anslutning kräver en VPN-enhet eller RRAS.
* [Punkt-till-plats](vpn-gateway-howto-point-to-site-resource-manager-portal.md) – VPN-anslutning över SSTP (Secure Socket Tunneling Protocol). Den här anslutningen kräver inte någon VPN-enhet.
* [VNet-till-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) – Den här typen av anslutning är samma som en plats-till-plats-konfiguration. VNet-till-VNet är en VPN-anslutning via IPsec (IKE v1 och IKE v2). Den kräver inte någon VPN-enhet.
* [Multisite](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) – Det här är en variant av en plats-till-plats-konfiguration som gör att du kan ansluta flera lokala platser till ett virtuellt nätverk.
* [ExpressRoute](../expressroute/expressroute-introduction.md) – ExpressRoute är en direkt anslutning till Azure från ditt WAN, i stället för över offentligt Internet. Mer information finns i [Teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md) och [Vanliga frågor och svar om ExpressRoute](../expressroute/expressroute-faqs.md).

Mer information om VPN-gatewayanslutningar finns i [Om VPN Gateway](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Vad är skillnaden mellan en plats-till-plats-anslutning och en punkt-till-plats-anslutning?
**Plats-till-plats**-konfigurationer sker mellan en lokal plats och Azure. Detta innebär att du kan ansluta mellan datorer i dina lokaler till valfri virtuell dator eller rollinstans i det virtuella nätverket, beroende på hur du väljer att konfigurera routningen. Det är ett bra alternativ för att få en anslutning mellan flera platser som alltid är tillgänglig och den passar bra för hybridkonfigurationer. Den här typen av anslutning bygger på en IPsec VPN-installation (maskinvaru- eller programinstallation), som måste distribueras i utkanten av nätverket.  För att skapa den här typen av anslutning måste du ha den maskinvara som krävs för VPN och en extern IPv4-adress.

Med **punkt-till-plats**-konfigurationer kan du ansluta från en enda dator varifrån som helst till något som finns i ditt virtuella nätverk. Den använder Windows som ingår i VPN-klienten. Som en del av punkt-till-plats-konfigurationen kan du installera ett certifikat och ett VPN-klientkonfigurationspaket, som innehåller inställningar för att datorn ska kunna ansluta till en virtuell dator eller rollinstans i det virtuella nätverket. Detta är användbart om du vill ansluta till ett virtuellt nätverk som inte finns lokalt. Det är också ett bra alternativ om du inte har tillgång till VPN-maskinvara eller en extern IPv4-adress. Båda krävs för en plats-till-plats-anslutning.

Du kan konfigurera ditt virtuella nätverk till att använda både plats-till-plats och punkt-till-plats samtidigt, förutsatt att du skapar din plats-till-plats-anslutning med en routningsbaserad VPN-typ för din gateway. Routningsbaserade VPN-typer kallas för dynamiska gateways i den klassiska distributionsmodellen.

## <a name="virtual-network-gateways"></a>Virtuella nätverksgatewayer

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>Är en VPN-gateway en virtuell nätverksgateway?
En VPN-gateway är en typ av virtuell nätverksgateway. En VPN-gateway skickar krypterad trafik mellan det virtuella nätverket och lokal plats via en offentlig anslutning. Du kan också använda en VPN-gateway för att skicka trafik mellan virtuella nätverk. När du skapar en VPN-gateway använder du värdet ”Vpn” för -GatewayType. Mer information finns i [Om konfigurationsinställningar för VPN-gateway](vpn-gateway-about-vpn-gateway-settings.md).

### <a name="what-is-a-policy-based-static-routing-gateway"></a>Vad är en principbaserad gateway (statisk routning)?
Principbaserade gateways implementerar principbaserade VPN:er. Principbaserade VPN:er krypterar och dirigerar paket via IPsec-tunnlar, baserat på kombinationerna av adressprefix mellan ditt lokala nätverk och Azure VNet. Principen (eller trafikväljaren) definieras vanligtvis som en åtkomstlista i VPN-konfigurationen.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Vad är en routningsbaserad gateway (dynamisk routning)?
Routningsbaserade gateways implementerar routningsbaserade VPN:er. Routningsbaserade VPN:er använder ”vägar” i IP-vidarebefordringen eller i routningstabellen för att dirigera paket till sina respektive tunnelgränssnitt. Tunnelgränssnitten krypterar eller dekrypterar sedan paketen in och ut från tunnlarna. Principen eller trafikväljaren för routningsbaserade VPN:er konfigureras som alla-till-alla (eller jokertecken).

### <a name="do-i-need-a-gatewaysubnet"></a>Behöver jag ett gatewayundernät?
Ja. Gatewayundernätet innehåller de IP-adresser som gatewaytjänsterna för virtuella nätverk använder. Om du vill konfigurera en virtuell nätverksgateway måste du först skapa ett gatewayundernät för det virtuella nätverket. Alla gatewayundernät måste ha namnet ”GatewaySubnet” för att fungera korrekt. Ge inte något annat namn till gateway-undernätet. Och distribuera inte virtuella datorer eller något annat till gateway-undernätet.

När du skapar gatewayundernätet anger du det antal IP-adresser som undernätet innehåller. IP-adresserna i gatewayundernätet allokeras till gatewaytjänsten. Vissa konfigurationer kräver att fler IP-adresser allokeras till gatewaytjänsterna än andra. Du måste kontrollera att gatewayundernätet innehåller tillräckligt med IP-adresser för att hantera framtida tillväxt och eventuella ytterligare nya anslutningskonfigurationer. Även om det är möjligt att skapa ett gatewayundernät som är så litet som /29, rekommenderar vi således att du skapar ett gatewayundernät på /28 eller större (/28, /27, /26 osv.). Granska kraven för den konfiguration som du vill skapa och kontrollera att gatewayundernätet som du har kommer att uppfylla dessa krav.

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Kan jag distribuera virtuella datorer eller rollinstanser till mitt gateway-undernät?
Nej.

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Kan jag få min IP-adress för VPN-gatewayen innan jag skapar den?
Nej. Du måste skapa din gateway innan du kan hämta IP-adressen. IP-adressen ändras om du tar bort och återskapar din VPN-gateway.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Hur blir min VPN-tunnel autentiserad?
Azure VPN använder PSK-autentisering (I förväg delad nyckel). Vi kan generera en i förväg delad nyckel (PSK) när vi skapar VPN-tunneln. Du kan byta den automatiskt genererade PSK:n mot din egen med PowerShell-cmdleten Set Pre-Shared Key eller REST-API.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Kan jag använda API:n Set Pre-Shared Key till att konfigurera min principbaserade gateway-VPN (statisk routning)?
Ja, både API:n och PowerShell-cmdleten Set Pre-Shared Key kan användas för att konfigurera Azures principbaserade (statiska) VPN:er och routningsbaserade (dynamisk) routning-VPN:er.

### <a name="can-i-use-other-authentication-options"></a>Kan jag använda andra autentiseringsalternativ?
Vi är begränsade till att använda PSK (I förväg delad nyckel) vid autentisering.


### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Hur anger jag vilken trafik som ska passera VPN-gatewayen?

####<a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager
* PowerShell: Använd ”AddressPrefix” för att ange trafik för den lokala nätverksgatewayen.
* Azure Portal: Gå till den lokala nätverksgatewayen > Konfiguration > Adressutrymme.

####<a name="classic-deployment-model"></a>Klassisk distributionsmodell
* Azure Portal: Gå till det klassiska virtuella nätverket > VPN-anslutningar > Plats-till-plats-VPN-anslutningar > Namn på lokal plats > Lokal plats > Adressutrymme för klient. 
* Klassisk portal: Lägg till varje intervall som du vill skicka via gatewayen för det virtuella nätverket på sidan Nätverk under Lokala nätverk. 

### <a name="can-i-configure-forced-tunneling"></a>Kan jag konfigurera tvingad tunneltrafik?
Ja. Se [Konfigurera tvingad tunneltrafik](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Kan jag installera min egen VPN-server i Azure och använda den för att ansluta till mitt lokala nätverk?
Ja, du kan distribuera egna VPN-gatewayer eller servrar i Azure, antingen från Azure Marketplace eller genom att skapa egna VPN-routrar. Du måste konfigurera användardefinierade vägar i det virtuella nätverket, så att trafiken dirigeras korrekt mellan de lokala nätverken och de virtuella undernätverken.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Varför är vissa portar öppna på min VPN-gateway?
De krävs för Azures infrastrukturkommunikation. De är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat kommer externa entiteter, inklusive kunderna till dessa gateways, inte kunna orsaka någon effekt på dessa slutpunkter.

En VPN-gateway är i grunden en multihomed-enhet med ett nätverkskort för kundens privata nätverk och ett nätverkskort som är riktat mot ett offentligt nätverk. Azure-infrastrukturens entiteter kan inte använda kundens privata nätverk av kompatibilitetsskäl, så de måste använda offentliga slutpunkter för infrastrukturkommunikationen. De offentliga slutpunkterna genomsöks regelbundet av Azures säkerhetsgranskning.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Mer information om gateway-typer, krav och dataflöde
Mer information finns i [Om konfigurationsinställningar för VPN-gateway](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="site-to-site-connections-and-vpn-devices"></a>Plats-till-plats-anslutningar och VPN-enheter
### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>Vad bör jag tänka på när jag väljer en VPN-enhet?
Vi har verifierat en uppsättning VPN-standardenheter för plats-till-plats tillsammans med våra enhetsleverantörer. En lista med kända kompatibla VPN-enheter, deras konfigurationsanvisningar eller exempel, samt specifikationer för enheten finns [här](vpn-gateway-about-vpn-devices.md). Alla enheter i enhetsfamiljerna som är kompatibla bör fungera tillsammans med Virtual Network. Om du behöver hjälp med att konfigurera din VPN-enhet kan du se enhetens konfigurationsexempel eller den länk som leder till lämplig enhetsfamilj.

### <a name="what-do-i-do-if-i-have-a-vpn-device-that-isnt-in-the-known-compatible-device-list"></a>Vad gör jag om jag har en VPN-enhet som inte finns med i listan över kända kompatibla enheter?
Om inte din enhet anges som en känd kompatibel VPN-enhet och du vill använda den till VPN-anslutningen måste du kontrollera att den uppfyller IPsec/IKE-konfigurationsalternativen och parametrarna som visas [här](vpn-gateway-about-vpn-devices.md). Enheter som uppfyller minimikraven bör fungera med VPN-gatewayer. Kontakta enhetstillverkaren för ytterligare information om support och konfiguration.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Varför stängs min principbaserade VPN-tunnel när trafiken är inaktiv?
Detta är ett förväntat beteende för principbaserade VPN-gatewayer (även kallat statisk routning). När trafiken i tunneln varit inaktiv i mer än 5 minuter kommer tunneln att stängas. När trafik börjar flöda i båda riktningarna, upprättas tunneln på nytt omedelbart.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Kan jag använda programvaru-VPN:er för att ansluta till Azure?
Vi har stöd för Routning och fjärråtkomst (RRAS) i Windows Server 2012 för plats-till-plats-konfiguration mellan flera platser.

Andra VPN-programlösningar bör fungera med vår gateway så länge de uppfyller branschens standardimplementeringar för IPsec. Kontakta leverantören av programvaran för konfigurations- och supportinstruktioner.

## <a name="a-namep2sapoint-to-site-connections"></a><a name="P2S"></a>Punkt-till-plats-anslutningar

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="a-namev2vmultiavnet-to-vnet-and-multi-site-connections"></a><a name="V2VMulti"></a>Anslutning mellan virtuella nätverk och flera platser

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Kan jag använda Azures VPN-gateway till att överföra trafik mellan mina lokala platser eller till ett annat virtuellt nätverk?

**Distributionsmodell för Resource Manager**<br>
Ja. Mer information finns i avsnittet om [BGP](#bgp).

**Klassisk distributionsmodell**<br>
Överföringstrafik via Azures VPN-gateway är möjlig med den klassiska distributionsmodellen, men den förlitar sig på statiska definierade adressutrymmen i nätverkskonfigurationsfilen. BGP stöds inte ännu med virtuella Azure-nätverk och VPN-gatewayer som använder den klassiska distributionsmodellen. Utan BGP är manuellt definierade överföringsadressutrymmen mycket felbenägna och rekommenderas inte.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Genererar Azure samma i förväg delade IPsec/IKE-nyckel för alla mina VPN-anslutningar för samma virtuella nätverk?
Nej, Azure genererar som standard olika nycklar för olika VPN-anslutningar. Du kan dock använda REST-API:n eller PowerShell-cmdleten Set VPN Gateway Key för att ange det nyckelvärde som du föredrar. Nyckeln MÅSTE vara en alfanumerisk sträng med 1–128 tecken.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Får jag mer bandbredd med fler plats-till-plats-VPN:er än med ett enda virtuellt nätverk?
Nej, alla VPN-tunnlar, inklusive punkt-till-plats-VPN:er, delar samma Azure VPN-gateway och tillgänglig bandbredd.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Kan jag konfigurera flera tunnlar mellan mitt virtuella nätverk och min lokala plats med hjälp av multisite-VPN?
Ja, men du måste konfigurera BGP för båda tunnlarna till samma plats.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Kan jag använda punkt-till-plats-VPN med mitt virtuella nätverk med flera VPN-tunnlar?
Ja, VPN:er för punkt-till-plats (P2S) kan användas med VPN-gateways som ansluter till flera lokala platser och andra virtuella nätverk.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Kan jag ansluta ett virtuellt nätverk med IPsec-VPN:er till min ExpressRoute-krets?
Ja, det stöds. Mer information finns i [Konfigurera ExpressRoute och VPN-anslutningar för plats till plats som kan samexistera](../expressroute/expressroute-howto-coexist-classic.md)

## <a name="a-namebgpabgp"></a><a name="bgp"></a>BGP
[!INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]

## <a name="cross-premises-connectivity-and-vms"></a>Anslutning på flera platser och virtuella datorer 
### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Om min virtuella dator finns i ett virtuellt nätverk och jag har en anslutning mellan flera platser, hur ansluter jag då till den virtuella datorn?
Du har några alternativ att välja mellan. Om du har aktiverat RDP och har skapat en slutpunkt, kan du ansluta till den virtuella datorn genom att använda VIP. I detta fall anger du VIP:en och den port som du vill ansluta till. Du måste konfigurera porten på den virtuella datorn för trafiken. Vanligtvis går du till den klassiska Azure-portalen och sparar inställningarna för RDP-anslutningen till datorn. Inställningarna innehåller den anslutningsinformation som krävs.

Om du har ett virtuellt nätverk med flera platsanslutningar konfigurerade, kan du ansluta till den virtuella datorn genom att använda intern DIP eller en privat IP-adress. Du kan också ansluta till den virtuella datorn med intern DIP från en annan virtuell dator som finns i samma virtuella nätverk. Du kan inte använda RDP till den virtuella datorn med hjälp av DIP om du ansluter från en plats utanför ditt virtuella nätverk. Om du till exempel har konfigurerat ett virtuellt nätverk från punkt till plats och du inte har upprättat någon anslutning från datorn, kan du inte ansluta till den virtuella datorn med DIP.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Om min virtuella dator finns i ett virtuellt nätverk med flera platsanslutningar, går då all trafik från min virtuella dator via den anslutningen?
Nej. Bara den trafik som har ett mål-IP som finns i det virtuella nätverkets lokala nätverks-IP-adressintervall som du har angett passerar den virtuella nätverksgatewayen. Trafik med ett mål-IP som finns i det virtuella nätverket stannar kvar i det virtuella nätverket. Annan trafik skickas via belastningsutjämnaren till offentliga nätverk. Om tvingad tunneltrafik används skickas den via Azures VPN-gateway. Om du felsöker är det viktigt att kontrollera att du har angett alla de intervall i det lokala nätverket som du vill skicka via gatewayen. Kontrollera att de lokala nätverksadressintervallen inte överlappar något av adressintervallen i det virtuella nätverket. Dessutom bör du kontrollera att DNS-servern som du använder matchar namnet på rätt IP-adress.

## <a name="virtual-network-faq"></a>Vanliga frågor och svar om Virtual Network
Du kan se mer information om virtuella nätverk i [Vanliga frågor och svar om Virtual Network](../virtual-network/virtual-networks-faq.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om VPN-gateway finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).
* Mer information om konfigurationsinställningar för VPN-gateway finns i [Om konfigurationsinställningar för VPN-gateway](vpn-gateway-about-vpn-gateway-settings.md).


<!--HONumber=Feb17_HO3-->



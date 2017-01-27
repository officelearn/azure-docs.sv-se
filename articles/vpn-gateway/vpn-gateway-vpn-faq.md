---
title: "Vanliga frågor och svar om virtuella nätverk i VPN Gateway | Microsoft Docs"
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
ms.date: 01/10/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2dda1cd384cf365504811a260872703f2c5c484e
ms.openlocfilehash: ccb0dc6172b234412558b9175f3872d690d4ea3a


---
# <a name="vpn-gateway-faq"></a>Vanliga frågor och svar om VPN Gateway
## <a name="connecting-to-virtual-networks"></a>Ansluta till virtuella nätverk
### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Kan jag ansluta till virtuella nätverk i olika Azure-regioner?
Ja. Faktum är att det inte finns någon regionbegränsning. Ett virtuellt nätverk kan ansluta till ett annat virtuellt nätverk i samma region eller i en annan Azure-region. 

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Kan jag ansluta till virtuella nätverk i olika prenumerationer?
Ja.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Kan jag ansluta till flera platser från en enda virtuellt nätverk?
Du kan ansluta till flera platser med hjälp av Windows PowerShell och Azure REST-API:er. Se [Multisite- och VNet-till-VNet-anslutning](#multi-site-and-vnet-to-vnet-connectivity), avsnittet Vanliga frågor och svar.

## <a name="what-are-my-cross-premises-connection-options"></a>Vilka alternativ finns det för min anslutning till flera platser?
Följande anslutningar mellan flera platser stöds:

* [Plats-till-plats](vpn-gateway-site-to-site-create.md) – VPN-anslutning via IPsec (IKE v1 och IKE v2). Den här typen av anslutning kräver en VPN-enhet eller RRAS.
* [Punkt-till-plats](vpn-gateway-point-to-site-create.md) – VPN-anslutning över SSTP (Secure Socket Tunneling Protocol). Den här anslutningen kräver inte någon VPN-enhet.
* [VNet-till-VNet](virtual-networks-configure-vnet-to-vnet-connection.md) – Den här typen av anslutning är samma som en plats-till-plats-konfiguration. VNet-till-VNet är en VPN-anslutning via IPsec (IKE v1 och IKE v2). Den kräver inte någon VPN-enhet.
* [Multisite](vpn-gateway-multi-site.md) – Det här är en variant av en plats-till-plats-konfiguration som gör att du kan ansluta flera lokala platser till ett virtuellt nätverk.
* [ExpressRoute](../expressroute/expressroute-introduction.md) – ExpressRoute är en direkt anslutning till Azure från ditt WAN, i stället för över offentligt Internet. Mer information finns i [Teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md) och [Vanliga frågor och svar om ExpressRoute](../expressroute/expressroute-faqs.md).

Mer information om anslutningar finns i [Om VPN Gateway](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Vad är skillnaden mellan en plats-till-plats-anslutning och en punkt-till-plats-anslutning?
Med **plats-till-plats**-anslutningar kan du ansluta mellan datorer i dina lokaler till valfri virtuell dator eller rollinstans i det virtuella nätverket, beroende på hur du väljer att konfigurera routning. Det är ett bra alternativ för att få en anslutning mellan flera platser som alltid är tillgänglig och den passar bra för hybridkonfigurationer. Den här typen av anslutning bygger på en IPsec VPN-installation (maskinvaru- eller programinstallation), som måste distribueras i utkanten av nätverket.  För att skapa den här typen av anslutning måste du ha den maskinvara som krävs för VPN och en extern IPv4-adress.

Med **punkt-till-plats**-anslutningar kan du ansluta från en enda dator varifrån som helst till något som finns i ditt virtuella nätverk. Den använder Windows som ingår i VPN-klienten. Som en del av punkt-till-plats-konfigurationen kan du installera ett certifikat och ett VPN-klientkonfigurationspaket, som innehåller inställningar för att datorn ska kunna ansluta till en virtuell dator eller rollinstans i det virtuella nätverket. Detta är användbart om du vill ansluta till ett virtuellt nätverk som inte finns lokalt. Det är också ett bra alternativ om du inte har tillgång till VPN-maskinvara eller en extern IPv4-adress. Båda krävs för en plats-till-plats-anslutning.

Du kan konfigurera ditt virtuella nätverk till att använda både plats-till-plats och punkt-till-plats samtidigt, förutsatt att du skapar din plats-till-plats-anslutning med en routningsbaserad VPN-typ för din gateway. Routningsbaserade VPN-typer kallas för dynamiska gateways i den klassiska distributionsmodellen.

### <a name="what-is-expressroute"></a>Vad är ExpressRoute?
Med ExpressRoute kan du skapa privata anslutningar mellan Microsofts datacenter och infrastruktur som finns lokalt eller i en samplaceringsmiljö. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, t.ex. Microsoft Azure och Office 365 i en ExpressRoute-partners samplaceringsmiljö, eller ansluta direkt till Azure från ditt befintliga WAN-nätverk (t.ex ett MPLS VPN som tillhandahålls av en nätverkstjänstleverantör).

ExpressRoute-anslutningar har bättre säkerhet, är tillförlitligare, har större bandbredd och kortare svarstider än normala anslutningar över Internet. I vissa fall kan användningen av ExpressRoute-anslutningar för att överföra data mellan ditt lokala nätverk och Azure även ge betydande kostnadsfördelar. Om du redan har skapat en anslutning för flera platser från ditt lokala nätverk till Azure, kan du migrera till en ExpressRoute-anslutning samtidigt som det virtuella nätverket är oförändrat.

Se [Vanliga frågor och svar om ExpressRoute](../expressroute/expressroute-faqs.md) för mer information.

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

## <a name="point-to-site-connections"></a>Punkt-till-plats-anslutningar
### <a name="what-operating-systems-can-i-use-with-point-to-site"></a>Vilka operativsystem kan jag använda med punkt-till-plats?
Följande operativsystem stöds:

* Windows 7 (32-bitars och 64-bitars)
* Windows Server 2008 R2 (endast 64-bitars)
* Windows 8 (32-bitars och 64-bitars)
* Windows 8.1 (32-bitars och 64-bitars)
* Windows Server 2012 (endast 64-bitars)
* Windows Server 2012 R2 (endast 64-bitars)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>Kan jag använda alla VPN-programklienter för punkt-till-plats som har stöd för SSTP?
Nej. Stödet är enbart begränsat till de Windows-operativsystemversioner som anges ovan.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Hur många slutpunkter på VPN-klienter kan jag ha i min punkt-till-plats-konfiguration?
Vi stöder att upp till 128 VPN-klienter kan ansluta till ett virtuellt nätverk på samma gång.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Kan jag använda min egen interna PKI-rotcertifikatutfärdare för punkt-till-plats-anslutningen?
Ja. Tidigare kunde bara självsignerade rotcertifikat användas. Du kan fortfarande överföra 20 rotcertifikat.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Kan jag korsa proxyservrar och brandväggar med punkt-till-plats-funktionen?
Ja. Vi använder SSTP (Secure Socket Tunneling Protocol) för tunneltrafik genom brandväggar. Den här tunneln visas som en HTTPs-anslutning.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Om jag startar om en klientdator som konfigurerats för punkt-till-plats, kommer då VPN att återansluta automatiskt?
Som standard kommer klientdatorn inte återupprätta VPN-anslutningen automatiskt.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Har punkt-till-plats stöd för automatisk återanslutning och DDNS på VPN-klienterna?
Automatisk återanslutning och DDNS stöds för närvarande inte i punkt-till-plats-VPN:er.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Kan jag ha plats-till-plats- och punkt-till-plats-konfigurationer samtidigt på samma virtuella nätverk?
Ja. Båda dessa lösningar fungerar om du har en routningsbaserad VPN-typ för din gateway. I den klassiska distributionsmodellen måste du ha en dynamisk gateway. Vi har inte stöd för punkt-till-plats för VPN-gateways med statisk routning eller gateways som använder cmdleten `-VpnType PolicyBased`.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Kan jag konfigurera en punkt-till-plats-klient så att den ansluter till flera virtuella nätverk samtidigt?
Ja, det kan du. Men de virtuella nätverken får inte ha några överlappande IP-prefix, och adressutrymmen för punkt-till-plats får inte överlappa mellan de virtuella nätverken.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Hur högt dataflöde kan jag förvänta mig vid anslutningar för plats-till-plats eller punkt-till-plats?
Det är svårt att bibehålla ett exakt dataflöde i VPN-tunnlarna. IPsec och SSTP är kryptografifrekventa VPN-protokoll. Dataflödet är också begränsat av svarstiden och bandbredden mellan din lokala plats och Internet.

## <a name="gateways"></a>Gateways
### <a name="what-is-a-policy-based-static-routing-gateway"></a>Vad är en principbaserad gateway (statisk routning)?
Principbaserade gateways implementerar principbaserade VPN:er. Principbaserade VPN:er krypterar och dirigerar paket via IPsec-tunnlar, baserat på kombinationerna av adressprefix mellan ditt lokala nätverk och Azure VNet. Principen (eller trafikväljaren) definieras vanligtvis som en åtkomstlista i VPN-konfigurationen.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Vad är en routningsbaserad gateway (dynamisk routning)?
Routningsbaserade gateways implementerar routningsbaserade VPN:er. Routningsbaserade VPN:er använder ”vägar” i IP-vidarebefordringen eller i routningstabellen för att dirigera paket till sina respektive tunnelgränssnitt. Tunnelgränssnitten krypterar eller dekrypterar sedan paketen in och ut från tunnlarna. Principen eller trafikväljaren för routningsbaserade VPN:er konfigureras som alla-till-alla (eller jokertecken).

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Kan jag få min IP-adress för VPN-gatewayen innan jag skapar den?
Nej. Du måste skapa din gateway innan du kan hämta IP-adressen. IP-adressen ändras om du tar bort och återskapar din VPN-gateway.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Hur blir min VPN-tunnel autentiserad?
Azure VPN använder PSK-autentisering (I förväg delad nyckel). Vi kan generera en i förväg delad nyckel (PSK) när vi skapar VPN-tunneln. Du kan byta den automatiskt genererade PSK:n mot din egen med PowerShell-cmdleten Set Pre-Shared Key eller REST-API.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Kan jag använda API:n Set Pre-Shared Key till att konfigurera min principbaserade gateway-VPN (statisk routning)?
Ja, både API:n och PowerShell-cmdleten Set Pre-Shared Key kan användas för att konfigurera Azures principbaserade (statiska) VPN:er och routningsbaserade (dynamisk) routning-VPN:er.

### <a name="can-i-use-other-authentication-options"></a>Kan jag använda andra autentiseringsalternativ?
Vi är begränsade till att använda PSK (I förväg delad nyckel) vid autentisering.

### <a name="what-is-the-gatewaysubnet-and-why-is-it-needed"></a>Vad är ”GatewaySubnet” och varför behövs det?
Vi har en gateway-tjänst som vi kör för att aktivera anslutning mellan flera platser.

Om du vill konfigurera en VPN-gateway, måste du först skapa ett gateway-undernät för ditt VNet. Alla gateway-undernät måste ha namnet GatewaySubnet för att fungera korrekt. Ge inte något annat namn till gateway-undernätet. Och distribuera inte virtuella datorer eller något annat till gateway-undernätet.

Den minsta storleken för gateway-undernätet beror helt på den konfiguration som du vill skapa. Även om det är möjligt att skapa ett gateway-undernät som är så litet som /29 för vissa konfigurationer, rekommenderar vi att du skapar ett gateway-undernät på /28 eller större (/28, /27, /26, etc.).

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Kan jag distribuera virtuella datorer eller rollinstanser till mitt gateway-undernät?
Nej.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Hur anger jag vilken trafik som ska passera VPN-gatewayen?

####<a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager
* PowerShell: Använd ”AddressPrefix” för att ange trafik för den lokala nätverksgatewayen.
* Azure Portal: Gå till den lokala nätverksgatewayen > Konfiguration > Adressutrymme.

####<a name="classic-deployment-model"></a>Klassisk distributionsmodell
* Azure Portal: Gå till det klassiska virtuella nätverket > VPN-anslutningar > Plats-till-plats-VPN-anslutningar > Namn på lokal plats
> Lokal plats > Adressutrymme för klient. 
* Den klassiska Azure-portalen: Lägg till varje intervall som du vill skicka via gatewayen för ditt virtuella nätverk på sidan Nätverk under Lokala nätverk. 

### <a name="can-i-configure-forced-tunneling"></a>Kan jag konfigurera tvingad tunneltrafik?
Ja. Se [Konfigurera tvingad tunneltrafik](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Kan jag installera min egen VPN-server i Azure och använda den för att ansluta till mitt lokala nätverk?
Ja, du kan distribuera egna VPN-gatewayer eller servrar i Azure, antingen från Azure Marketplace eller genom att skapa egna VPN-routrar. Du måste konfigurera användardefinierade vägar i det virtuella nätverket, så att trafiken dirigeras korrekt mellan ditt lokala nätverk och ditt virtuella undernätverk.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Varför är vissa portar öppna på min VPN-gateway?
De krävs för Azures infrastrukturkommunikation. De är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat kommer externa entiteter, inklusive kunderna till dessa gateways, inte kunna orsaka någon effekt på dessa slutpunkter.

En VPN-gateway är i grunden en multihomed-enhet med ett nätverkskort för kundens privata nätverk och ett nätverkskort som är riktat mot ett offentligt nätverk. Azure-infrastrukturens entiteter kan inte använda kundens privata nätverk av kompatibilitetsskäl, så de måste använda offentliga slutpunkter för infrastrukturkommunikationen. De offentliga slutpunkterna genomsöks regelbundet av Azures säkerhetsgranskning.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Mer information om gateway-typer, krav och dataflöde
Mer information finns i [Om VPN Gateway-inställningar](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="multi-site-and-vnet-to-vnet-connectivity"></a>Multisite- och VNet-till-VNet-anslutning
### <a name="which-type-of-gateways-can-support-multi-site-and-vnet-to-vnet-connectivity"></a>Vilken typ av gateways har stöd för multisite- och VNet-till-VNet-anslutningar?
Endast routningsbaserade VPN:er (dynamisk routning).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Kan jag ansluta ett VNet med en routningsbaserad VPN-typ till ett annat VNet med en policybaserad VPN-typ?
Nej, båda virtuella nätverken MÅSTE använda routningsbaserade VPN-anslutningar (dynamisk routning).

### <a name="is-the-vnet-to-vnet-traffic-secure"></a>Är VNet-till-VNet-trafiken säker?
Ja, den är skyddad med IPsec/IKE-kryptering.

### <a name="does-vnet-to-vnet-traffic-travel-over-the-azure-backbone"></a>Transporteras VNet-till-VNet-trafiken via Azures stamnät?
Ja, den här trafiken passerar Azures stamnät. Den går inte över Internet.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Hur många lokala platser och virtuella nätverk kan ett virtuellt nätverk ansluta till?
Max. 10 kombinerat för dynamiska routnings-gateways Basic och Standard, samt 30 för VPN-gatewayer med hög kapacitet.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Kan jag använda punkt-till-plats-VPN med mitt virtuella nätverk med flera VPN-tunnlar?
Ja, VPN:er för punkt-till-plats (P2S) kan användas med VPN-gateways som ansluter till flera lokala platser och andra virtuella nätverk.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Kan jag konfigurera flera tunnlar mellan mitt virtuella nätverk och min lokala plats med hjälp av multisite-VPN?
Ja, men du måste konfigurera BGP för båda tunnlarna till samma plats.

### <a name="can-there-be-overlapping-address-spaces-among-the-connected-virtual-networks-and-on-premises-local-sites"></a>Kan det finnas överlappande adressutrymmen i anslutna virtuella nätverk och lokala platser?
Nej. Överlappande adressutrymmen innebär att överföringen av nätverkskonfigurationsfilen eller ”Skapa virtuella nätverk” misslyckas.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Får jag mer bandbredd med fler plats-till-plats-VPN:er än med ett enda virtuellt nätverk?
Nej, alla VPN-tunnlar, inklusive punkt-till-plats-VPN:er, delar samma Azure VPN-gateway och tillgänglig bandbredd.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Kan jag använda Azures VPN-gateway till att överföra trafik mellan mina lokala platser eller till ett annat virtuellt nätverk?

####<a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager
Ja. Mer information finns i avsnittet om [BGP](#bgp).

####<a name="classic-deployment-model"></a>Klassisk distributionsmodell
Överföringstrafik via Azures VPN-gateway är möjlig med den klassiska distributionsmodellen, men den förlitar sig på statiska definierade adressutrymmen i nätverkskonfigurationsfilen. BGP stöds inte ännu med virtuella Azure-nätverk och VPN-gatewayer som använder den klassiska distributionsmodellen. Utan BGP är manuellt definierade överföringsadressutrymmen mycket felbenägna och rekommenderas inte.<br>

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Genererar Azure samma i förväg delade IPsec/IKE-nyckel för alla mina VPN-anslutningar för samma virtuella nätverk?
Nej, Azure genererar som standard olika nycklar för olika VPN-anslutningar. Du kan dock använda REST-API:n eller PowerShell-cmdleten Set VPN Gateway Key för att ange det nyckelvärde som du föredrar. Nyckeln MÅSTE vara en alfanumerisk sträng med 1–128 tecken.

### <a name="does-azure-charge-for-traffic-between-virtual-networks"></a>Debiterar Azure trafik mellan virtuella nätverk?
Vid trafik mellan olika virtuella Azure-nätverk debiterar Azure endast trafiken från en Azure-region till en annan. Prislistan finns på Azure-sidan [Prissättning för VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/).

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



<!--HONumber=Jan17_HO2-->



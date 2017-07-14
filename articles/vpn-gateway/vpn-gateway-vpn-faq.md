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
ms.date: 05/30/2017
ms.author: cherylmc,yushwang
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3627b3dfa5067883625b9a714645164ec99c8a51
ms.contentlocale: sv-se
ms.lasthandoff: 05/31/2017


---
# Vanliga frågor och svar om VPN Gateway
<a id="vpn-gateway-faq" class="xliff"></a>

## Ansluta till virtuella nätverk
<a id="connecting-to-virtual-networks" class="xliff"></a>

### Kan jag ansluta till virtuella nätverk i olika Azure-regioner?
<a id="can-i-connect-virtual-networks-in-different-azure-regions" class="xliff"></a>

Ja. Faktum är att det inte finns någon regionbegränsning. Ett virtuellt nätverk kan ansluta till ett annat virtuellt nätverk i samma region eller i en annan Azure-region. 

### Kan jag ansluta till virtuella nätverk i olika prenumerationer?
<a id="can-i-connect-virtual-networks-in-different-subscriptions" class="xliff"></a>

Ja.

### Kan jag ansluta till flera platser från en enda virtuellt nätverk?
<a id="can-i-connect-to-multiple-sites-from-a-single-virtual-network" class="xliff"></a>

Du kan ansluta till flera platser med hjälp av Windows PowerShell och Azure REST-API:er. Se [Multisite- och VNet-till-VNet-anslutning](#V2VMulti), avsnittet Vanliga frågor och svar.

### Vilka alternativ finns det för min anslutning till flera platser?
<a id="what-are-my-cross-premises-connection-options" class="xliff"></a>

Följande anslutningar mellan flera platser stöds:

* Plats-till-plats – VPN-anslutning via IPsec (IKE v1 och IKE v2). Den här typen av anslutning kräver en VPN-enhet eller RRAS. Mer information finns i [Plats-till-plats](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
* Punkt-till-plats – VPN-anslutning över SSTP (Secure Socket Tunneling Protocol). Den här anslutningen kräver inte någon VPN-enhet. Mer information finns i [Punkt-till-plats](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* VNet-till-VNet – Den här typen av anslutning är samma som en plats-till-plats-konfiguration. VNet-till-VNet är en VPN-anslutning via IPsec (IKE v1 och IKE v2). Den kräver inte någon VPN-enhet. Mer information finns i [VNet-till-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
* Flera platser – Det här är en variant av en plats-till-plats-konfiguration som gör att du kan ansluta flera lokala platser till ett virtuellt nätverk. Mer information finns i [Flera platser](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).
* ExpressRoute – ExpressRoute är en direkt anslutning till Azure från ditt WAN, inte en VPN-anslutning över internet. Mer information finns i [Teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md) och [Vanliga frågor och svar om ExpressRoute](../expressroute/expressroute-faqs.md).

Mer information om VPN-gatewayanslutningar finns i [Om VPN Gateway](vpn-gateway-about-vpngateways.md).

### Vad är skillnaden mellan en plats-till-plats-anslutning och en punkt-till-plats-anslutning?
<a id="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site" class="xliff"></a>

**Plats-till-plats**-konfigurationer (IPsec/IKE VPN-tunnel) sker mellan en lokal plats och Azure. Detta innebär att du kan ansluta mellan datorer i dina lokaler till valfri virtuell dator eller rollinstans i det virtuella nätverket, beroende på hur du väljer att konfigurera routning och behörigheter. Det är ett bra alternativ för att få en anslutning mellan flera platser som alltid är tillgänglig och den passar bra för hybridkonfigurationer. Den här typen av anslutning bygger på en IPsec VPN-installation (maskinvara eller programinstallation), som måste distribueras i utkanten av nätverket. När du ska skapa den här typen av anslutning måste du ha en extern IPv4-adress som inte ligger bakom en NAT.

Med **punkt-till-plats**-konfigurationer (VPN över SSTP) kan du ansluta från en enda dator varifrån som helst till något som finns i ditt virtuella nätverk. Den använder Windows som ingår i VPN-klienten. Som en del av punkt-till-plats-konfigurationen kan du installera ett certifikat och ett VPN-klientkonfigurationspaket, som innehåller inställningar för att datorn ska kunna ansluta till en virtuell dator eller rollinstans i det virtuella nätverket. Detta är användbart om du vill ansluta till ett virtuellt nätverk som inte finns lokalt. Det är också ett bra alternativ om du inte har tillgång till VPN-maskinvara eller en extern IPv4-adress. Båda krävs för en plats-till-plats-anslutning.

Du kan konfigurera ditt virtuella nätverk till att använda både plats-till-plats och punkt-till-plats samtidigt, förutsatt att du skapar din plats-till-plats-anslutning med en routningsbaserad VPN-typ för din gateway. Routningsbaserade VPN-typer kallas för dynamiska gateways i den klassiska distributionsmodellen.

## Virtuella nätverksgatewayer
<a id="virtual-network-gateways" class="xliff"></a>

### Är en VPN-gateway en virtuell nätverksgateway?
<a id="is-a-vpn-gateway-a-virtual-network-gateway" class="xliff"></a>

En VPN-gateway är en typ av virtuell nätverksgateway. En VPN-gateway skickar krypterad trafik mellan det virtuella nätverket och lokal plats via en offentlig anslutning. Du kan också använda en VPN-gateway för att skicka trafik mellan virtuella nätverk. När du skapar en VPN-gateway använder du värdet ”Vpn” för -GatewayType. Mer information finns i [Om konfigurationsinställningar för VPN-gateway](vpn-gateway-about-vpn-gateway-settings.md).

### Vad är en principbaserad gateway (statisk routning)?
<a id="what-is-a-policy-based-static-routing-gateway" class="xliff"></a>

Principbaserade gateways implementerar principbaserade VPN:er. Principbaserade VPN:er krypterar och dirigerar paket via IPsec-tunnlar, baserat på kombinationerna av adressprefix mellan ditt lokala nätverk och Azure VNet. Principen (eller trafikväljaren) definieras vanligtvis som en åtkomstlista i VPN-konfigurationen.

### Vad är en routningsbaserad gateway (dynamisk routning)?
<a id="what-is-a-route-based-dynamic-routing-gateway" class="xliff"></a>

Routningsbaserade gateways implementerar routningsbaserade VPN:er. Routningsbaserade VPN:er använder ”vägar” i IP-vidarebefordringen eller i routningstabellen för att dirigera paket till sina respektive tunnelgränssnitt. Tunnelgränssnitten krypterar eller dekrypterar sedan paketen in och ut från tunnlarna. Principen eller trafikväljaren för routningsbaserade VPN:er konfigureras som alla-till-alla (eller jokertecken).

### Behöver jag ett gatewayundernät?
<a id="do-i-need-a-gatewaysubnet" class="xliff"></a>

Ja. Gatewayundernätet innehåller de IP-adresser som gatewaytjänsterna för virtuella nätverk använder. Om du vill konfigurera en virtuell nätverksgateway måste du först skapa ett gatewayundernät för det virtuella nätverket. Alla gatewayundernät måste ha namnet ”GatewaySubnet” för att fungera korrekt. Ge inte något annat namn till gateway-undernätet. Och distribuera inte virtuella datorer eller något annat till gateway-undernätet.

När du skapar gatewayundernätet anger du det antal IP-adresser som undernätet innehåller. IP-adresserna i gatewayundernätet allokeras till gatewaytjänsten. Vissa konfigurationer kräver att fler IP-adresser allokeras till gatewaytjänsterna än andra. Du måste kontrollera att gatewayundernätet innehåller tillräckligt med IP-adresser för att hantera framtida tillväxt och eventuella ytterligare nya anslutningskonfigurationer. Även om du kan skapa ett gatewayundernät som är så litet som /29, rekommenderar vi att du skapar ett gatewayundernät på /27 eller större (/27, /26, /25 osv.). Granska kraven för den konfiguration som du vill skapa och kontrollera att gatewayundernätet som du har kommer att uppfylla dessa krav.

### Kan jag distribuera virtuella datorer eller rollinstanser till mitt gateway-undernät?
<a id="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet" class="xliff"></a>

Nej.

### Kan jag få min IP-adress för VPN-gatewayen innan jag skapar den?
<a id="can-i-get-my-vpn-gateway-ip-address-before-i-create-it" class="xliff"></a>

Nej. Du måste skapa din gateway innan du kan hämta IP-adressen. IP-adressen ändras om du tar bort och återskapar din VPN-gateway.

### Kan jag begära en statisk offentlig IP-adress för min VPN-gateway?
<a id="can-i-request-a-static-public-ip-address-for-my-vpn-gateway" class="xliff"></a>

Nej. Endast dynamisk IP-adresstilldelning stöds. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats din VPN-gateway. Den enda gången VPN-gatewayens IP-adress ändras är när gatewayen tas bort och återskapas. VPN-gatewayens offentliga IP-adress ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway. 

### Hur blir min VPN-tunnel autentiserad?
<a id="how-does-my-vpn-tunnel-get-authenticated" class="xliff"></a>

Azure VPN använder PSK-autentisering (I förväg delad nyckel). Vi kan generera en i förväg delad nyckel (PSK) när vi skapar VPN-tunneln. Du kan byta den automatiskt genererade PSK:n mot din egen med PowerShell-cmdleten Set Pre-Shared Key eller REST-API.

### Kan jag använda API:n Set Pre-Shared Key till att konfigurera min principbaserade gateway-VPN (statisk routning)?
<a id="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn" class="xliff"></a>

Ja, både API:n och PowerShell-cmdleten Set Pre-Shared Key kan användas för att konfigurera Azures principbaserade (statiska) VPN:er och routningsbaserade (dynamisk) routning-VPN:er.

### Kan jag använda andra autentiseringsalternativ?
<a id="can-i-use-other-authentication-options" class="xliff"></a>

Vi är begränsade till att använda PSK (I förväg delad nyckel) vid autentisering.

### Hur anger jag vilken trafik som ska passera VPN-gatewayen?
<a id="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway" class="xliff"></a>

#### Distributionsmodell med Resource Manager
<a id="resource-manager-deployment-model" class="xliff"></a>

* PowerShell: Använd ”AddressPrefix” för att ange trafik för den lokala nätverksgatewayen.
* Azure Portal: Gå till den lokala nätverksgatewayen > Konfiguration > Adressutrymme.

#### Klassisk distributionsmodell
<a id="classic-deployment-model" class="xliff"></a>

* Azure Portal: Gå till det klassiska virtuella nätverket > VPN-anslutningar > Plats-till-plats-VPN-anslutningar > Namn på lokal plats > Lokal plats > Adressutrymme för klient. 
* Klassisk portal: Lägg till varje intervall som du vill skicka via gatewayen för det virtuella nätverket på sidan Nätverk under Lokala nätverk. 

### Kan jag konfigurera tvingad tunneltrafik?
<a id="can-i-configure-forced-tunneling" class="xliff"></a>

Ja. Se [Konfigurera tvingad tunneltrafik](vpn-gateway-about-forced-tunneling.md).

### Kan jag installera min egen VPN-server i Azure och använda den för att ansluta till mitt lokala nätverk?
<a id="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network" class="xliff"></a>

Ja, du kan distribuera egna VPN-gatewayer eller servrar i Azure, antingen från Azure Marketplace eller genom att skapa egna VPN-routrar. Du måste konfigurera användardefinierade vägar i det virtuella nätverket så att trafiken dirigeras korrekt mellan de lokala nätverken och de virtuella undernätverken.

### Varför är vissa portar öppna på min VPN-gateway?
<a id="why-are-certain-ports-opened-on-my-vpn-gateway" class="xliff"></a>

De krävs för Azures infrastrukturkommunikation. De är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat kommer externa entiteter, inklusive kunderna till dessa gateways, inte kunna orsaka någon effekt på dessa slutpunkter.

En VPN-gateway är i grunden en multihomed-enhet med ett nätverkskort för kundens privata nätverk och ett nätverkskort som är riktat mot ett offentligt nätverk. Azure-infrastrukturens entiteter kan inte använda kundens privata nätverk av kompatibilitetsskäl, så de måste använda offentliga slutpunkter för infrastrukturkommunikationen. De offentliga slutpunkterna genomsöks regelbundet av Azures säkerhetsgranskning.

### Mer information om gateway-typer, krav och dataflöde
<a id="more-information-about-gateway-types-requirements-and-throughput" class="xliff"></a>

Mer information finns i [Om konfigurationsinställningar för VPN-gateway](vpn-gateway-about-vpn-gateway-settings.md).

## Plats-till-plats-anslutningar och VPN-enheter
<a id="site-to-site-connections-and-vpn-devices" class="xliff"></a>

### Vad bör jag tänka på när jag väljer en VPN-enhet?
<a id="what-should-i-consider-when-selecting-a-vpn-device" class="xliff"></a>

Vi har verifierat en uppsättning VPN-standardenheter för plats-till-plats tillsammans med våra enhetsleverantörer. En lista med kända kompatibla VPN-enheter, deras konfigurationsanvisningar eller exempel, samt specifikationer för enheten finns i artikeln [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Alla enheter i enhetsfamiljerna som är kompatibla bör fungera tillsammans med Virtual Network. Om du behöver hjälp med att konfigurera din VPN-enhet kan du se enhetens konfigurationsexempel eller den länk som leder till lämplig enhetsfamilj.

### Var hittar jag konfigurationsinställningar för VPN-enheter?
<a id="where-can-i-find-vpn-device-configuration-settings" class="xliff"></a>

[!INCLUDE [vpn devices](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### Hur redigerar jag konfigurationsexempel för VPN-enheter?
<a id="how-do-i-edit-vpn-device-configuration-samples" class="xliff"></a>

Mer information om att redigera enhetens konfigurationsexempel finns i [Redigera exempel](vpn-gateway-about-vpn-devices.md#editing).

### Var hittar jag IPsec- och IKE-parametrar?
<a id="where-do-i-find-ipsec-and-ike-parameters" class="xliff"></a>

Mer information om IPsec-/IKE-parametrar finns i [Parametrar](vpn-gateway-about-vpn-devices.md#ipsec).

### Varför stängs min principbaserade VPN-tunnel när trafiken är inaktiv?
<a id="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle" class="xliff"></a>

Detta är ett förväntat beteende för principbaserade VPN-gatewayer (även kallat statisk routning). När trafiken i tunneln varit inaktiv i mer än 5 minuter kommer tunneln att stängas. När trafik börjar flöda i båda riktningarna, upprättas tunneln på nytt omedelbart.

### Kan jag använda programvaru-VPN:er för att ansluta till Azure?
<a id="can-i-use-software-vpns-to-connect-to-azure" class="xliff"></a>

Vi har stöd för Routning och fjärråtkomst (RRAS) i Windows Server 2012 för plats-till-plats-konfiguration mellan flera platser.

Andra VPN-programlösningar bör fungera med vår gateway så länge de uppfyller branschens standardimplementeringar för IPsec. Kontakta leverantören av programvaran för konfigurations- och supportinstruktioner.

## <a name="P2S"></a>Punkt-till-plats-anslutningar

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="V2VMulti"></a>Anslutning mellan virtuella nätverk och flera platser

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

### Kan jag använda Azures VPN-gateway till att överföra trafik mellan mina lokala platser eller till ett annat virtuellt nätverk?
<a id="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network" class="xliff"></a>

**Distributionsmodell för Resource Manager**<br>
Ja. Mer information finns i avsnittet om [BGP](#bgp).

**Klassisk distributionsmodell**<br>
Överföringstrafik via Azures VPN-gateway är möjlig med den klassiska distributionsmodellen, men den förlitar sig på statiska definierade adressutrymmen i nätverkskonfigurationsfilen. BGP stöds inte ännu med virtuella Azure-nätverk och VPN-gatewayer som använder den klassiska distributionsmodellen. Utan BGP är manuellt definierade överföringsadressutrymmen mycket felbenägna och rekommenderas inte.

### Genererar Azure samma i förväg delade IPsec/IKE-nyckel för alla mina VPN-anslutningar för samma virtuella nätverk?
<a id="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network" class="xliff"></a>

Nej, Azure genererar som standard olika nycklar för olika VPN-anslutningar. Du kan dock använda REST-API:n eller PowerShell-cmdleten Set VPN Gateway Key för att ange det nyckelvärde som du föredrar. Nyckeln MÅSTE vara en alfanumerisk sträng med 1–128 tecken.

### Får jag mer bandbredd med fler plats-till-plats-VPN:er än med ett enda virtuellt nätverk?
<a id="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network" class="xliff"></a>

Nej, alla VPN-tunnlar, inklusive punkt-till-plats-VPN:er, delar samma Azure VPN-gateway och tillgänglig bandbredd.

### Kan jag konfigurera flera tunnlar mellan mitt virtuella nätverk och min lokala plats med hjälp av multisite-VPN?
<a id="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn" class="xliff"></a>

Ja, men du måste konfigurera BGP för båda tunnlarna till samma plats.

### Kan jag använda punkt-till-plats-VPN med mitt virtuella nätverk med flera VPN-tunnlar?
<a id="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels" class="xliff"></a>

Ja, VPN:er för punkt-till-plats (P2S) kan användas med VPN-gateways som ansluter till flera lokala platser och andra virtuella nätverk.

### Kan jag ansluta ett virtuellt nätverk med IPsec-VPN:er till min ExpressRoute-krets?
<a id="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit" class="xliff"></a>

Ja, det stöds. Mer information finns i [Konfigurera ExpressRoute och VPN-anslutningar för plats till plats som kan samexistera](../expressroute/expressroute-howto-coexist-classic.md)

## IPsec/IKE-princip
<a id="ipsecike-policy" class="xliff"></a>

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-ipsecikepolicy-faq-include.md)]


## <a name="bgp"></a>BGP

[!INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]

## Anslutning på flera platser och virtuella datorer 
<a id="cross-premises-connectivity-and-vms" class="xliff"></a>

### Om min virtuella dator finns i ett virtuellt nätverk och jag har en anslutning mellan flera platser, hur ansluter jag då till den virtuella datorn?
<a id="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm" class="xliff"></a>

Du har några alternativ att välja mellan. Om du har aktiverat RDP för din virtuella dator kan du ansluta till den genom att använda den privata IP-adressen. I detta fall anger du den privata IP-adressen och den port som du vill ansluta till (vanligtvis 3389). Du måste konfigurera porten på den virtuella datorn för trafiken.

Du kan också ansluta till den virtuella datorn med en privat IP-adress från en annan virtuell dator som finns i samma virtuella nätverk. Du kan inte använda RDP till den virtuella datorn med hjälp av den privata IP-adressen om du ansluter från en plats utanför ditt virtuella nätverk. Om du till exempel har konfigurerat ett virtuellt nätverk från punkt-till-plats och du inte har upprättat någon anslutning från datorn så kan du inte ansluta till den virtuella datorn med en privat IP-adress.

### Om min virtuella dator finns i ett virtuellt nätverk med flera platsanslutningar, går då all trafik från min virtuella dator via den anslutningen?
<a id="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection" class="xliff"></a>

Nej. Bara den trafik som har ett mål-IP som finns i det virtuella nätverkets lokala nätverks-IP-adressintervall som du har angett passerar den virtuella nätverksgatewayen. Trafik med ett mål-IP som finns i det virtuella nätverket stannar kvar i det virtuella nätverket. Annan trafik skickas via belastningsutjämnaren till offentliga nätverk. Om tvingad tunneltrafik används skickas den via Azures VPN-gateway.

### Hur felsöker jag en RDP-anslutning till en virtuell dator
<a id="how-do-i-troubleshoot-an-rdp-connection-to-a-vm" class="xliff"></a>

[!INCLUDE [Troubleshoot VM connection](../../includes/vpn-gateway-connect-vm-troubleshoot-include.md)]


## Vanliga frågor och svar om Virtual Network
<a id="virtual-network-faq" class="xliff"></a>

Du kan se mer information om virtuella nätverk i [Vanliga frågor och svar om Virtual Network](../virtual-network/virtual-networks-faq.md).

## Nästa steg
<a id="next-steps" class="xliff"></a>

* Mer information om VPN-gateway finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).
* Mer information om konfigurationsinställningar för VPN-gateway finns i [Om konfigurationsinställningar för VPN-gateway](vpn-gateway-about-vpn-gateway-settings.md).

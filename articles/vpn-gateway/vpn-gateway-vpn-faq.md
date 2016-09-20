<properties 
   pageTitle="Vanliga frågor och svar om virtuella nätverk i VPN Gateway | Microsoft Azure"
   description="Vanliga frågor och svar om VPN Gateway. Vanliga frågor och svar om Microsoft Azure Virtual Network-anslutningar på flera platser, konfiguration av hybridanslutningar och VPN-gatewayer"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/10/2016"
   ms.author="yushwang" />

# Vanliga frågor och svar om VPN Gateway

## Ansluta till virtuella nätverk

### Kan jag ansluta till virtuella nätverk i olika Azure-regioner?
Ja. Faktum är att det inte finns någon regionbegränsning. Ett virtuellt nätverk kan ansluta till ett annat virtuellt nätverk i samma region eller i en annan Azure-region.

### Kan jag ansluta till virtuella nätverk i olika prenumerationer?
Ja.

### Kan jag ansluta till flera platser från en enda virtuellt nätverk?

Du kan ansluta till flera platser med hjälp av Windows PowerShell och Azure REST-API:er. Se [Multisite- och VNet-till-VNet-anslutning](#multi-site-and-vnet-to-vnet-connectivity), avsnittet Vanliga frågor och svar.
## Vilka alternativ finns det för min anslutning till flera platser?

Följande anslutningar mellan flera platser stöds:

- [Plats-till-plats](vpn-gateway-site-to-site-create.md) – VPN-anslutning via IPsec (IKE v1 och IKE v2). Den här typen av anslutning kräver en VPN-enhet eller RRAS.

- [Punkt-till-plats](vpn-gateway-point-to-site-create.md) – VPN-anslutning över SSTP (Secure Socket Tunneling Protocol). Den här anslutningen kräver inte någon VPN-enhet.

- [VNet-till-VNet](virtual-networks-configure-vnet-to-vnet-connection.md) – Den här typen av anslutning är samma som en plats-till-plats-konfiguration. VNet-till-VNet är en VPN-anslutning via IPsec (IKE v1 och IKE v2). Den kräver inte någon VPN-enhet.

- [Multisite](vpn-gateway-multi-site.md) – Det här är en variant av en plats-till-plats-konfiguration som gör att du kan ansluta flera lokala platser till ett virtuellt nätverk.

- [ExpressRoute](../expressroute/expressroute-introduction.md) – ExpressRoute är en direkt anslutning till Azure från ditt WAN, i stället för över offentligt Internet. Mer information finns i [Teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md) och [Vanliga frågor och svar om ExpressRoute](../expressroute/expressroute-faqs.md).

Mer information om anslutningar finns i [Om VPN Gateway](vpn-gateway-about-vpngateways.md).

### Vad är skillnaden mellan en plats-till-plats-anslutning och en punkt-till-plats-anslutning?

Med **plats-till-plats**-anslutningar kan du ansluta mellan datorer i dina lokaler till valfri virtuell dator eller rollinstans i det virtuella nätverket, beroende på hur du väljer att konfigurera routning. Det är ett bra alternativ för att få en anslutning mellan flera platser som alltid är tillgänglig och den passar bra för hybridkonfigurationer. Den här typen av anslutning bygger på en IPsec VPN-installation (maskinvaru- eller programinstallation), som måste distribueras i utkanten av nätverket.  För att skapa den här typen av anslutning måste du ha den maskinvara som krävs för VPN och en extern IPv4-adress.

Med **punkt-till-plats**-anslutningar kan du ansluta från en enda dator varifrån som helst till något som finns i ditt virtuella nätverk. Den använder Windows som ingår i VPN-klienten. Som en del av punkt-till-plats-konfigurationen kan du installera ett certifikat och ett VPN-klientkonfigurationspaket, som innehåller inställningar för att datorn ska kunna ansluta till en virtuell dator eller rollinstans i det virtuella nätverket. Detta är användbart om du vill ansluta till ett virtuellt nätverk som inte finns lokalt. Det är också ett bra alternativ om du inte har tillgång till VPN-maskinvara eller en extern IPv4-adress. Båda krävs för en plats-till-plats-anslutning. 

Du kan konfigurera ditt virtuella nätverk till att använda både plats-till-plats och punkt-till-plats samtidigt, förutsatt att du skapar din plats-till-plats-anslutning med en routningsbaserad VPN-typ för din gateway. Routningsbaserade VPN-typer kallas för dynamiska gateways i den klassiska distributionsmodellen.

### Vad är ExpressRoute?

Med ExpressRoute kan du skapa privata anslutningar mellan Microsofts datacenter och infrastruktur som finns lokalt eller i en samplaceringsmiljö. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, t.ex. Microsoft Azure och Office 365 i en ExpressRoute-partners samplaceringsmiljö, eller ansluta direkt till Azure från ditt befintliga WAN-nätverk (t.ex ett MPLS VPN som tillhandahålls av en nätverkstjänstleverantör).

ExpressRoute-anslutningar har bättre säkerhet, är tillförlitligare, har större bandbredd och kortare svarstider än normala anslutningar över Internet. I vissa fall kan användningen av ExpressRoute-anslutningar för att överföra data mellan ditt lokala nätverk och Azure även ge betydande kostnadsfördelar. Om du redan har skapat en anslutning för flera platser från ditt lokala nätverk till Azure, kan du migrera till en ExpressRoute-anslutning samtidigt som det virtuella nätverket är oförändrat.

Se [Vanliga frågor och svar om ExpressRoute](../expressroute/expressroute-faqs.md) för mer information.

## Plats-till-plats-anslutningar och VPN-enheter

### Vad bör jag tänka på när jag väljer en VPN-enhet?

Vi har verifierat en uppsättning VPN-standardenheter för plats-till-plats tillsammans med våra enhetsleverantörer. En lista med kända kompatibla VPN-enheter, deras konfigurationsanvisningar eller exempel, samt specifikationer för enheten finns [här](vpn-gateway-about-vpn-devices.md). Alla enheter i enhetsfamiljerna som är kompatibla bör fungera tillsammans med Virtual Network. Om du behöver hjälp med att konfigurera din VPN-enhet kan du se enhetens konfigurationsexempel eller den länk som leder till lämplig enhetsfamilj.

### Vad gör jag om jag har en VPN-enhet som inte finns med i listan över kända kompatibla enheter?

Om inte din enhet anges som en känd kompatibel VPN-enhet och du vill använda den till VPN-anslutningen måste du kontrollera att den uppfyller IPsec/IKE-konfigurationsalternativen och parametrarna som visas [här](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). Enheter som uppfyller minimikraven bör fungera med VPN-gatewayer. Kontakta enhetstillverkaren för ytterligare information om support och konfiguration.

### Varför stängs min principbaserade VPN-tunnel när trafiken är inaktiv?

Detta är ett förväntat beteende för principbaserade VPN-gatewayer (även kallat statisk routning). När trafiken i tunneln varit inaktiv i mer än 5 minuter kommer tunneln att stängas. När trafik börjar flöda i båda riktningarna, upprättas tunneln på nytt omedelbart. Om du har en routningsbaserad VPN-gateway (även kallad dynamisk), kommer det här problemet inte att uppstå.

### Kan jag använda programvaru-VPN:er för att ansluta till Azure?

Vi har stöd för Routning och fjärråtkomst (RRAS) i Windows Server 2012 för plats-till-plats-konfiguration mellan flera platser.

Andra VPN-programlösningar bör fungera med vår gateway så länge de uppfyller branschens standardimplementeringar för IPsec. Kontakta leverantören av programvaran för konfigurations- och supportinstruktioner.

## Punkt-till-plats-anslutningar

### Vilka operativsystem kan jag använda med punkt-till-plats?

Följande operativsystem stöds:

- Windows 7 (32-bitars och 64-bitars)

- Windows Server 2008 R2 (endast 64-bitars)

- Windows 8 (32-bitars och 64-bitars)

- Windows 8.1 (32-bitars och 64-bitars)

- Windows Server 2012 (endast 64-bitars)

- Windows Server 2012 R2 (endast 64-bitars)

- Windows 10

### Kan jag använda alla VPN-programklienter för punkt-till-plats som har stöd för SSTP?

Nej. Stödet är enbart begränsat till de Windows-operativsystemversioner som anges ovan.

### Hur många slutpunkter på VPN-klienter kan jag ha i min punkt-till-plats-konfiguration?

Vi stöder att upp till 128 VPN-klienter kan ansluta till ett virtuellt nätverk på samma gång.

### Kan jag använda min egen interna PKI-rotcertifikatutfärdare för punkt-till-plats-anslutningen?

Ja. Tidigare kunde bara självsignerade rotcertifikat användas. Du kan fortfarande överföra 20 rotcertifikat.

### Kan jag korsa proxyservrar och brandväggar med punkt-till-plats-funktionen?

Ja. Vi använder SSTP (Secure Socket Tunneling Protocol) för tunneltrafik genom brandväggar. Den här tunneln visas som en HTTPs-anslutning.

### Om jag startar om en klientdator som konfigurerats för punkt-till-plats, kommer då VPN att återansluta automatiskt?

Som standard kommer klientdatorn inte återupprätta VPN-anslutningen automatiskt.

### Har punkt-till-plats stöd för automatisk återanslutning och DDNS på VPN-klienterna?

Automatisk återanslutning och DDNS stöds för närvarande inte i punkt-till-plats-VPN:er.

### Kan jag ha plats-till-plats- och punkt-till-plats-konfigurationer samtidigt på samma virtuella nätverk?

Ja. Båda dessa lösningar fungerar om du har en routningsbaserad VPN-typ för din gateway. I den klassiska distributionsmodellen måste du ha en dynamisk gateway. Vi har inte stöd för punkt-till-plats för VPN-gatewayer med statisk routning eller gatewayer som använder -VpnType PolicyBased.

### Kan jag konfigurera en punkt-till-plats-klient så att den ansluter till flera virtuella nätverk samtidigt?

Ja, det kan du. Men de virtuella nätverken får inte ha några överlappande IP-prefix, och adressutrymmen för punkt-till-plats får inte överlappa mellan de virtuella nätverken.

### Hur högt dataflöde kan jag förvänta mig vid anslutningar för plats-till-plats eller punkt-till-plats?

Det är svårt att bibehålla ett exakt dataflöde i VPN-tunnlarna. IPsec och SSTP är kryptografifrekventa VPN-protokoll. Dataflödet är också begränsat av svarstiden och bandbredden mellan din lokala plats och Internet.

## Gateways

### Vad är en principbaserad gateway (statisk routning)?

Principbaserade gateways implementerar principbaserade VPN:er. Principbaserade VPN:er krypterar och dirigerar paket via IPsec-tunnlar, baserat på kombinationerna av adressprefix mellan ditt lokala nätverk och Azure VNet. Principen (eller trafikväljaren) definieras vanligtvis som en åtkomstlista i VPN-konfigurationen.

### Vad är en routningsbaserad gateway (dynamisk routning)?

Routningsbaserade gateways implementerar routningsbaserade VPN:er. Routningsbaserade VPN:er använder ”vägar” i IP-vidarebefordringen eller i routningstabellen för att dirigera paket till sina respektive tunnelgränssnitt. Tunnelgränssnitten krypterar eller dekrypterar sedan paketen in och ut från tunnlarna. Principen eller trafikväljaren för routningsbaserade VPN:er konfigureras som alla-till-alla (eller jokertecken).

### Kan jag få min IP-adress för VPN-gatewayen innan jag skapar den?

Nej. Du måste skapa din gateway innan du kan hämta IP-adressen. IP-adressen ändras om du tar bort och återskapar din VPN-gateway.

### Hur blir min VPN-tunnel autentiserad?

Azure VPN använder PSK-autentisering (I förväg delad nyckel). Vi kan generera en i förväg delad nyckel (PSK) när vi skapar VPN-tunneln. Du kan byta den automatiskt genererade PSK:n mot din egen med PowerShell-cmdleten Set Pre-Shared Key eller REST-API.

### Kan jag använda API:n Set Pre-Shared Key till att konfigurera min principbaserade gateway-VPN (statisk routning)?

Ja, både API:n och PowerShell-cmdleten Set Pre-Shared Key kan användas för att konfigurera Azures principbaserade (statiska) VPN:er och routningsbaserade (dynamisk) routning-VPN:er.

### Kan jag använda andra autentiseringsalternativ?

Vi är begränsade till att använda PSK (I förväg delad nyckel) vid autentisering.

### Vad är ”gateway-undernätet” och varför behövs det?

Vi har en gateway-tjänst som vi kör för att aktivera anslutning mellan flera platser. 

Om du vill konfigurera en VPN-gateway, måste du först skapa ett gateway-undernät för ditt VNet. Alla gateway-undernät måste ha namnet GatewaySubnet för att fungera korrekt. Ge inte något annat namn till gateway-undernätet. Och distribuera inte virtuella datorer eller något annat till gateway-undernätet.

Den minsta storleken för gateway-undernätet beror helt på den konfiguration som du vill skapa. Även om det är möjligt att skapa ett gateway-undernät som är så litet som /29 för vissa konfigurationer, rekommenderar vi att du skapar ett gateway-undernät på /28 eller större (/28, /27, /26, etc.). 

### Kan jag distribuera virtuella datorer eller rollinstanser till mitt gateway-undernät?

Nej.

### Hur anger jag vilken trafik som ska passera VPN-gatewayen?

Om du använder den klassiska Azure-portalen lägger du till varje intervall som du vill skicka via gatewayen för ditt virtuella nätverk på sidan Nätverk under Lokala nätverk.

### Kan jag konfigurera tvingad tunneltrafik?

Ja. Se [Konfigurera tvingad tunneltrafik](vpn-gateway-about-forced-tunneling.md).

### Kan jag installera min egen VPN-server i Azure och använda den för att ansluta till mitt lokala nätverk?

Ja, du kan distribuera egna VPN-gatewayer eller servrar i Azure, antingen från Azure Marketplace eller genom att skapa egna VPN-routrar. Du måste konfigurera användardefinierade vägar i det virtuella nätverket, så att trafiken dirigeras korrekt mellan ditt lokala nätverk och ditt virtuella undernätverk.

### Varför är vissa portar öppna på min VPN-gateway?

De krävs för Azures infrastrukturkommunikation. De är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat kommer externa entiteter, inklusive kunderna till dessa gateways, inte kunna orsaka någon effekt på dessa slutpunkter.

En VPN-gateway är i grunden en multihomed-enhet med ett nätverkskort för kundens privata nätverk och ett nätverkskort som är riktat mot ett offentligt nätverk. Azure-infrastrukturens entiteter kan inte använda kundens privata nätverk av kompatibilitetsskäl, så de måste använda offentliga slutpunkter för infrastrukturkommunikationen. De offentliga slutpunkterna genomsöks regelbundet av Azures säkerhetsgranskning.


### Mer information om gateway-typer, krav och dataflöde

Mer information finns i [Om VPN Gateway-inställningar](vpn-gateway-about-vpn gateway-settings.md).

## Multisite- och VNet-till-VNet-anslutning

### Vilken typ av gateways har stöd för multisite- och VNet-till-VNet-anslutningar?

Endast routningsbaserade VPN:er (dynamisk routning).

### Kan jag ansluta ett VNet med en routningsbaserad VPN-typ till ett annat VNet med en policybaserad VPN-typ?

Nej, båda virtuella nätverken MÅSTE använda routningsbaserade VPN-anslutningar (dynamisk routning).

### Är VNet-till-VNet-trafiken säker?

Ja, den är skyddad med IPsec/IKE-kryptering.

### Transporteras VNet-till-VNet-trafiken via Azures stamnät?

Ja.

### Hur många lokala platser och virtuella nätverk kan ett virtuellt nätverk ansluta till?

Max. 10 kombinerat för dynamiska routnings-gateways Basic och Standard, samt 30 för VPN-gatewayer med hög kapacitet.

### Kan jag använda punkt-till-plats-VPN med mitt virtuella nätverk med flera VPN-tunnlar?

Ja, VPN:er för punkt-till-plats (P2S) kan användas med VPN-gateways som ansluter till flera lokala platser och andra virtuella nätverk.

### Kan jag konfigurera flera tunnlar mellan mitt virtuella nätverk och min lokala plats med hjälp av multisite-VPN?

Nej, redundanta tunnlar mellan ett virtuellt Azure-nätverk och en lokal plats stöds inte.

### Kan det finnas överlappande adressutrymmen i anslutna virtuella nätverk och lokala platser?

Nej. Överlappande adressutrymmen innebär att överföringen av nätverkskonfigurationsfilen eller ”Skapa virtuella nätverk” misslyckas.

### Får jag mer bandbredd med fler plats-till-plats-VPN:er än med ett enda virtuellt nätverk?

Nej, alla VPN-tunnlar, inklusive punkt-till-plats-VPN:er, delar samma Azure VPN-gateway och tillgänglig bandbredd.

### Kan jag använda Azures VPN-gateway till att överföra trafik mellan mina lokala platser eller till ett annat virtuellt nätverk?

**Klassisk distributionsmodell**<br>
Överföringstrafik via Azures VPN-gateway är möjlig med den klassiska distributionsmodellen, men den förlitar sig på statiska definierade adressutrymmen i nätverkskonfigurationsfilen. BGP stöds inte ännu med virtuella Azure-nätverk och VPN-gatewayer som använder den klassiska distributionsmodellen. Utan BGP är manuellt definierade överföringsadressutrymmen mycket felbenägna och rekommenderas inte.<br>
**Distributionsmodell med Resource Manager**<br>
Om du använder distributionsmodellen med Resource Manager finns det mer information i avsnittet [BGP](#bgp).

### Genererar Azure samma i förväg delade IPsec/IKE-nyckel för alla mina VPN-anslutningar för samma virtuella nätverk?

Nej, Azure genererar som standard olika nycklar för olika VPN-anslutningar. Du kan dock använda REST-API:n eller PowerShell-cmdleten Set VPN Gateway Key för att ange det nyckelvärde som du föredrar. Nyckeln MÅSTE vara en alfanumerisk sträng med 1–128 tecken.

### Debiterar Azure trafik mellan virtuella nätverk?

Vid trafik mellan olika virtuella Azure-nätverk debiterar Azure endast trafiken från en Azure-region till en annan. Prislistan finns på Azure-sidan [Prissättning för VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/).


### Kan jag ansluta ett virtuellt nätverk med IPsec-VPN:er till min ExpressRoute-krets?

Ja, det stöds. Mer information finns i [Konfigurera ExpressRoute och VPN-anslutningar för plats till plats som kan samexistera](../expressroute/expressroute-howto-coexist-classic.md)

## <a name="bgp"></a>BGP

[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 



## Anslutning på flera platser och virtuella datorer 

### Om min virtuella dator finns i ett virtuellt nätverk och jag har en anslutning mellan flera platser, hur ansluter jag då till den virtuella datorn?

Du har några alternativ att välja mellan. Om du har aktiverat RDP och har skapat en slutpunkt, kan du ansluta till den virtuella datorn genom att använda VIP. I detta fall anger du VIP:en och den port som du vill ansluta till. Du måste konfigurera porten på den virtuella datorn för trafiken. Vanligtvis går du till den klassiska Azure-portalen och sparar inställningarna för RDP-anslutningen till datorn. Inställningarna innehåller den anslutningsinformation som krävs.

Om du har ett virtuellt nätverk med flera platsanslutningar konfigurerade, kan du ansluta till den virtuella datorn genom att använda intern DIP eller en privat IP-adress. Du kan också ansluta till den virtuella datorn med intern DIP från en annan virtuell dator som finns i samma virtuella nätverk. Du kan inte använda RDP till den virtuella datorn med hjälp av DIP om du ansluter från en plats utanför ditt virtuella nätverk. Om du till exempel har konfigurerat ett virtuellt nätverk från punkt till plats och du inte har upprättat någon anslutning från datorn, kan du inte ansluta till den virtuella datorn med DIP.

### Om min virtuella dator finns i ett virtuellt nätverk med flera platsanslutningar, går då all trafik från min virtuella dator via den anslutningen?

Nej. Bara den trafik som har ett mål-IP som finns i det virtuella nätverkets lokala nätverks-IP-adressintervall som du har angett passerar den virtuella nätverksgatewayen. Trafik med ett mål-IP som finns i det virtuella nätverket stannar kvar i det virtuella nätverket. Annan trafik skickas via belastningsutjämnaren till offentliga nätverk. Om tvingad tunneltrafik används skickas den via Azures VPN-gateway. Om du felsöker är det viktigt att kontrollera att du har angett alla de intervall i det lokala nätverket som du vill skicka via gatewayen. Kontrollera att de lokala nätverksadressintervallen inte överlappar något av adressintervallen i det virtuella nätverket. Dessutom bör du kontrollera att DNS-servern som du använder matchar namnet på rätt IP-adress.


## Vanliga frågor och svar om Virtual Network

Du kan se mer information om virtuella nätverk i [Vanliga frågor och svar om Virtual Network](../virtual-network/virtual-networks-faq.md).
 



<!--HONumber=sep16_HO1-->



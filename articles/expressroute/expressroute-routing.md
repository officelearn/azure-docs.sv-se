---
title: Routningskrav för ExpressRoute | Microsoft Docs
description: Den här sidan innehåller detaljerade krav för att konfigurera och hantera routning för ExpressRoute-kretsar.
documentationcenter: na
services: expressroute
author: ganesr
manager: rossort
editor: ''

ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: ganesr

---
# ExpressRoute-routningskrav
För att kunna ansluta till Microsofts molntjänster med ExpressRoute måste du konfigurera och hantera routning. Vissa anslutningsleverantörer erbjuder konfigurering och hantering av routning som en hanterad tjänst. Fråga din anslutningsleverantör om de erbjuder denna tjänst. Om inte, måste du följa kraven som beskrivs nedan. 

I [Kretsar och routningsdomäner](expressroute-circuit-peerings.md) finns en beskrivning av de routningssessioner som måste konfigureras för att möjliggöra anslutningen.

**Obs:** Microsoft stöder inte några routerredundansprotokoll (t.ex. HSRP, VRRP) för konfigurationer med hög tillgänglighet. Vi förlitar oss på ett redundant par med BGP-sessioner per peering för hög tillgänglighet.

## IP-adresser för peerings
Du behöver reservera några IP-adressblock för att kunna konfigurera routning mellan ditt nätverk och Microsofts Enterprise-gränsroutrar (MSEE). Det här avsnittet innehåller en lista med krav och en beskrivning av reglerna för hur dessa IP-adresser måste hämtas och användas.

### IP-adresser för Azures privata peering
Du kan antingen använda privata IP-adresser eller offentliga IP-adresser för att konfigurera peerings. Adressintervallet som används för att konfigurera routning får inte överlappa de adressintervall som användes för att skapa virtuella nätverk i Azure. 

* Du måste reservera ett /29-undernät eller två /30-undernät för routningsgränssnitten.
* Undernät som används för routning kan vara antingen privata IP-adresser eller offentliga IP-adresser.
* Undernäten får inte stå i konflikt med det intervall som reserverats av kunden för användning i Microsoft-molnet.
* Om ett /29-undernät används delas det upp i två /30-undernät. 
  * Det första /30-undernätet används för den primära länken och det andra /30-undernätet används för den sekundära länken.
  * För båda /30-undernäten gäller att du måste använda den första IP-adressen för /30-undernätet på routern. Microsoft använder den andra IP-adressen för /30-undernätet till att konfigurera en BGP-session.
  * Du måste konfigurera båda BGP-sessionerna för att vårt [tillgänglighets-SLA](https://azure.microsoft.com/support/legal/sla/) ska vara giltigt.  

#### Exempel på privat peering
Om du väljer att använda a.b.c.d/29 för att konfigurera peeringen delas den upp i två /30-undernät. I exemplet nedan beskrivs hur a.b.c.d/29-undernätet används. 

a.b.c.d/29 kommer att delas upp till a.b.c.d/30 och a.b.c.d+4/30 samt skickas till Microsoft via etablerings-API: er. Du kommer att använda a.b.c.d+1 som VRF IP för din primära PE och Microsoft kommer att använda a.b.c.d+2 som VRF IP för den primära MSEE:n. Du kommer att använda a.b.c.d+5 som VRF IP för din sekundära PE och Microsoft kommer att använda a.b.c.d+6 som VRF IP för den sekundära MSEE:n.

Tänk dig ett fall där du väljer 192.168.100.128/29 för att konfigurera privat peering. 192.168.100.128/29 innehåller adresser från 192.168.100.128 till 192.168.100.135, däribland:

* 192.168.100.128/30 som tilldelas till link1, där leverantören använder 192.168.100.129 och Microsoft använder 192.168.100.130.
* 192.168.100.132/30 tilldelas till link2, där leverantören använder 192.168.100.133 och Microsoft använder 192.168.100.134.

### IP-adresser för Azures offentliga och Microsofts peering
Du måste använda offentliga IP-adresser som du äger när du konfigurerar BGP-sessionerna. Microsoft måste kunna verifiera ditt ägarskap till IP-adresserna via RIR (Routing Internet Registries) och IIR (Internet Routing Registries). 

* Du måste använda ett unikt /29-undernät eller två /30-undernät till att konfigurera BGP-peeringen för varje peering per ExpressRoute-krets (om du har fler än en). 
* Om ett /29-undernät används delas det upp i två /30-undernät. 
  * Det första /30-undernätet används för den primära länken och det andra /30-undernätet används för den sekundära länken.
  * För båda /30-undernäten gäller att du måste använda den första IP-adressen för /30-undernätet på routern. Microsoft använder den andra IP-adressen för /30-undernätet till att konfigurera en BGP-session.
  * Du måste konfigurera båda BGP-sessionerna för att vårt [tillgänglighets-SLA](https://azure.microsoft.com/support/legal/sla/) ska vara giltigt.

Kontrollera att din IP-adress och AS-nummer är registrerade till dig i något av registren nedan.

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](http://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](http://www.radb.net/)
* [ALTDB](http://altdb.net/)

## Dynamiskt routningsutbyte
Routningsutbytet kommer att ske via EBGP-protokollet. EBGP-sessioner upprättas mellan MSEE:erna och dina routrar. Autentisering av BGP-sessioner är inte något krav. Om det behövs kan en MD5-hash konfigureras. Se [Konfigurera routning](expressroute-howto-routing-classic.md) och [Kretsetablering av arbetsflöden och kretsstatus](expressroute-workflows.md) för information om att konfigurera BGP-sessioner.

## Autonoma systemnummer
Microsoft använder AS 12076 för Azures offentliga, Azures privata och Microsofts peering. Vi har reserverat ASN:er från 65515 till 65520 för intern användning. Både 16- och 32-bitars AS-nummer stöds. På peeringsidan (kund eller leverantör) kan AS vara en offentlig ASN om den kan verifieras till att ägas av dig, eller ett privat ASN-nummer.

Det finns inga krav på dataöverföringssymmetri på de primära och sekundära sökvägarna i alla angivna kretsar. Sökvägar vid vidarebefordran och retur kan passera olika routerpar. Identiska vägar måste annonseras från antingen den primära eller den sekundära sidan över alla angivna kretspar som du äger. Vägmåtten behöver inte vara identiska.

## Vägsammanställning och begränsningar för prefix
Vi stöder upp till 4 000 prefix som annonseras till oss via Azures privata peering. Detta kan utökas upp till 10 000 prefix om ExpressRoute-premiumtillägget är aktiverat. Vi kan acceptera upp till 200 prefix per BGP-session för Azures offentliga och Microsofts peering. 

BGP-sessionen kommer att tas bort om antalet prefix överskrider gränsen. Vi kommer endast att acceptera standardvägar på den privata peeringlänken. Leverantören eller kunden måste filtrera ut standardvägen och privata IP-adresser (RFC 1918) från BGP-annonserna till Azures offentliga och Microsofts peeringsökvägar. 

## Överföringsroutning och routning mellan regioner
ExpressRoute kan inte konfigureras som överföringsroutrar. Du måste förlita dig på din anslutningsleverantör vid överföring av routningstjänster.

## Annonsering av standardvägar
Standardvägar tillåts bara i Azures privata peeringsessioner. I dessa fall kommer vi dirigera all trafik från associerade virtuella nätverk till ditt nätverk. Annonsering av standardvägar till privat peering resulterar i att Internetvägen från Azure blockeras. Du måste använda på ditt företags gräns till att dirigera trafik från och till Internet för tjänster som finns i Azure. 

 Om du vill aktivera anslutningar till andra Azure-tjänster och infrastrukturtjänster, måste något av följande objekt finnas på plats:

* Azures offentliga peering ska vara aktiverad för att dirigera trafiken till offentliga slutpunkter
* Du använder användardefinierad routning för att tillåta anslutning till Internet för varje undernät som kräver Internetanslutning.

**Obs:** Annonsering av standardvägar avbryter Windows och annan licensaktivering av virtuella datorer. Följ anvisningarna [här](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) för kringgå detta.

## Stöd för BGP-communities (förhandsgranskning)
Det här avsnittet innehåller en översikt över hur BGP-communities kommer att användas med ExpressRoute. Microsoft kommer att annonsera vägar i offentliga och Microsofts peeringsökvägar med vägar som är taggade med lämpliga community-värden. Anledningen till att man gör detta samt information om community-värden beskrivs nedan. Microsoft använder dock inte några community-värden som är taggade på vägar som annonserats till Microsoft.

Om du ansluter till Microsoft via ExpressRoute på valfri peeringplats i en geopolitisk region, får du åtkomst till alla Microsoft-molntjänster i alla regioner inom den geopolitiska gränsen. 

Om du exempelvis har anslutit till Microsoft i Amsterdam via ExpressRoute, kommer du ha åtkomst till alla Microsoft-molntjänster som finns i norra Europa och västra Europa. 

Se sidan [ExpressRoute-partners och peeringplatser](expressroute-locations.md) för en detaljerad lista med geopolitiska regioner, associerade Azure-regioner och motsvarande ExpressRoute-peeringplatser.

Du kan köpa mer än en ExpressRoute-krets per geopolitisk region. Att ha flera anslutningar ger dig betydande fördelar med hög tillgänglighet tack vare den geografiska redundansen. I de fall där du har flera ExpressRoute-kretsar, får du samma uppsättning prefix annonserade från Microsoft för den offentliga peeringen och för Microsofts peeringsökvägar. Det innebär att du har flera sökvägar från ditt nätverk till Microsoft. Detta kan eventuellt medföra att icke-optimala beslut om routning tas i nätverket. Därför kan det uppstå en icke-optimal anslutning till olika tjänster. 

Microsoft taggar prefix som annonseras via offentlig peering och Microsofts peering med lämpliga värden för BGP-communityn som anger vilken region prefixen finns i. Du kan använda community-värden för att fatta rätt beslut om routning och erbjuda [optimal routning till kunderna](expressroute-optimize-routing.md).

| **Geopolitisk region** | **Microsoft Azure-region** | **Community-värde för BGP** |
| --- | --- | --- |
| **Nordamerika** | | |
| Östra USA |12076:51004 | |
| Östra USA 2 |12076:51005 | |
| Västra USA |12076:51006 | |
| Västra USA 2 |12076:51026 | |
| Västra centrala USA |12076:51027 | |
| Norra centrala USA |12076:51007 | |
| Södra centrala USA |12076:51008 | |
| Centrala USA |12076:51009 | |
| Centrala Kanada |12076:51020 | |
| Östra Kanada |12076:51021 | |
| **Sydamerika** | | |
| Södra Brasilien |12076:51014 | |
| **Europa** | | |
| Norra Europa |12076:51003 | |
| Västra Europa |12076:51002 | |
| **Asien och stillahavsområdet** | | |
| Östasien |12076:51010 | |
| Sydostasien |12076:51011 | |
| **Japan** | | |
| Östra Japan |12076:51012 | |
| Västra Japan |12076:51013 | |
| **Australien** | | |
| Östra Australien |12076:51015 | |
| Sydöstra Australien |12076:51016 | |
| **Indien** | | |
| Södra Indien |12076:51019 | |
| Västra Indien |12076:51018 | |
| Centrala Indien |12076:51017 | |

Alla vägar som annonseras från Microsoft taggas med lämpligt community-värde. 

> [!IMPORTANT]
> Globala prefix taggas med ett lämpligt community-värde och annonseras endast när ExpressRoute-premiumtillägget är aktiverat.
> 
> 

Förutom ovanstående taggar Microsoft också prefix baserat på vilken tjänst de tillhör. Detta gäller endast för Microsoft-peering. Tabellen nedan innehåller en mappning av tjänsten till community-värden för BGP.

| **Tjänst** | **Community-värde för BGP** |
| --- | --- |
| **Exchange** |12076:5010 |
| **SharePoint** |12076:5020 |
| **Skype för företag** |12076:5030 |
| **CRM Online** |12076:5040 |
| **Andra Office 365-tjänster** |12076:5100 |

> [!NOTE]
> Microsoft använder inte några community-värden för BGP som du har angett för vägar som annonseras till Microsoft.
> 
> 

## Nästa steg
* Konfigurera ExpressRoute-anslutningen.
  
  * [Skapa en ExpressRoute-krets för den klassiska distributionsmodellen](expressroute-howto-circuit-classic.md) eller [Skapa och ändra en ExpressRoute-krets med Azure Resource Manager](expressroute-howto-circuit-arm.md)
  * [Konfigurera routning för den klassiska distributionsmodellen](expressroute-howto-routing-classic.md) eller [Konfigurera routning för Resource Manager-distributionsmodellen](expressroute-howto-routing-arm.md)
  * [Länka ett klassiskt VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-classic.md) eller [Länka ett Resource Manager-VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)

<!--HONumber=Sep16_HO3-->



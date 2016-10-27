<properties 
   pageTitle="Introduktion till ExpressRoute | Microsoft Azure"
   description="Den här sidan innehåller en översikt över ExpressRoute-tjänsten, inklusive hur en ExpressRoute-anslutning fungerar."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="cherylmc"/>


# Teknisk översikt för ExpressRoute

Med Microsoft Azure ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet via en dedikerad privat anslutning från en anslutningsleverantör. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och CRM Online. Anslutningen kan vara från ett ”any-to-any”-nätverk (IP VPN), ett ”point-to-point”-nätverk med Ethernet eller en virtuell korsanslutning via en anslutningsleverantör på en samlokaliseringsanläggning. ExpressRoute-anslutningar går inte via offentligt Internet. Det innebär att ExpressRoute-anslutningar är tillförlitligare, snabbare, har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet.

![](./media/expressroute-introduction/expressroute-basic.png)

**Viktiga fördelar är:**

- Layer 3-anslutningen mellan ditt lokala nätverk och Microsoft Cloud via en anslutningsleverantör. Anslutningen kan vara från ett ”any-to-any”-nätverk (IPVPN), en ”point-to-point”-anslutning med Ethernet, eller med en virtuell korsanslutning via ett Ethernet-utbyte.
- Anslutning till Microsofts molntjänster i alla regioner i den geopolitiska regionen.
- Global anslutning till Microsofts tjänster i alla regioner med ExpressRoutes premiumtillägg.
- Dynamisk routning mellan ditt nätverk och Microsoft med branschens standardprotokoll (BGP).
- Inbyggd redundans i varje peeringplats för högre tillförlitlighet.
- Anslutningens drifttids-[SLA](https://azure.microsoft.com/support/legal/sla/).
- QoS och stöd för flera tjänstklasser för särskilda program, till exempel Skype för företag.

Se [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md) för mer information.

## <a name="howtoconnect"></a>Hur ansluter jag nätverket till Microsoft med ExpressRoute?

Du kan skapa en anslutning mellan ditt lokala nätverk och Microsoft-molnet på tre olika sätt:

### Samordnad i ett molnutbyte

Om du är samordnad i en anläggning med ett molnutbyte, kan du beställa virtuella korsanslutningar till Microsoft-molnet via samordningsleverantörens Ethernet-utbyte. Samordningsleverantörer kan erbjuda Layer-2-anslutningar eller hanterade Layer 3-korsanslutningar mellan infrastrukturen i samordningsanläggningen och Microsoft-molnet.

### ”Point-to-Point”-anslutningar med Ethernet 

Du kan ansluta dina lokala datacenter/kontor till Microsoft-molnet via ”point-to-point”-länkar med Ethernet. ”Point-to-Point”-leverantörer kan erbjuda Layer-2-anslutningar eller hanterade Layer 3-anslutningar mellan din webbplats och Microsoft-molnet.

### ”Any-to-any”-nätverk (IPVPN)

Du kan integrera ditt WAN med Microsoft-molnet. IPVPN-leverantörer (vanligtvis MPLS VPN) erbjuder ”any-to-any”-anslutningar mellan dina avdelningskontor och datacentra. Microsoft-molnet kan kopplas samman med ditt WAN-nätverk så att det ser ut precis som andra avdelningskontor. WAN-leverantörer erbjuder vanligtvis hanterad Layer 3-anslutning. Alla ExpressRoute-funktioner och egenskaper är identiska i ovanstående anslutningsmodeller. 

Anslutningsleverantörer kan erbjuda en eller flera modeller för anslutningen. Du kan rådfråga din anslutningsleverantör om vilken modell som passar dig bäst.

![](./media/expressroute-introduction/expressroute-connectivitymodels.png)



## ExpressRoute-funktioner

ExpressRoute har stöd för följande funktioner: 

### Layer 3-anslutning

Microsoft använder branschens standardprotokoll för dynamisk routning (BGP) för att utbyta routning mellan det lokala nätverket, dina instanser i Azure och Microsofts offentliga adresser.  Vi upprättar flera BGP-sessioner med ditt nätverk för olika trafikprofiler. Mer information finns i artikeln [ExpressRoute-krets och routningsdomäner](expressroute-circuit-peerings.md).

### Redundans

Varje ExpressRoute-krets består av två anslutningar till två Microsoft Enterprise-gränsroutrar (MSEE) från anslutningsleverantören/din nätverksgräns. Microsoft kräver en dubbel BGP-anslutning från anslutningsleverantören/din sida – en för varje MSEE. Du kan välja att inte distribuera redundanta enheter/Ethernet-kretsar. Dock använder anslutningsleverantörer redundanta enheter för att dina projekt ska lämnas över till Microsoft på ett redundant sätt. En redundant Layer 3-anslutningskonfiguration är ett krav för att vår [SLA](https://azure.microsoft.com/support/legal/sla/) ska vara giltig. 

### Anslutning till Microsofts molntjänster

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

ExpressRoute-anslutningar ger åtkomst till följande tjänster:

- Microsoft Azure-tjänster
- Microsoft Office 365-tjänster
- Microsoft CRM Online-tjänster 
 
Du kan gå till sidan [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md) för att se en detaljerad lista över de tjänster som stöds via ExpressRoute.

### Anslutning till alla regioner inom en geopolitisk region

Du kan ansluta till Microsoft på någon av våra [peeringplatser](expressroute-locations.md) och få åtkomst till alla regioner inom den geopolitiska regionen. 

Om du exempelvis har anslutit till Microsoft i Amsterdam via ExpressRoute, kommer du ha åtkomst till alla Microsoft-molntjänster som finns i norra Europa och västra Europa. Se artikeln [ExpressRoute-partners och peeringplatser](expressroute-locations.md) för en översikt över geopolitiska regioner, tillhörande Microsoft- molnområden och motsvarande ExpressRoute-peeringplatser.

### Global anslutning med ExpressRoutes premiumtillägg

Du kan aktivera ExpressRoutes premiumtillägg om du vill utöka anslutningen över geopolitiska gränser. Om du till exempel är ansluten till Microsoft i Amsterdam via ExpressRoute, får du åtkomst till alla Microsoft-molntjänster som finns i alla regioner över hela världen (med undantag för nationella moln). Du kan komma åt tjänster som distribueras i Sydamerika eller Australien på samma sätt som du har åtkomst till de norra och västra Europaregionerna.

### Utförligt ekosystem med anslutningspartner

ExpressRoute har ett ständigt växande ekosystem med anslutningsleverantörer och SI-partners. Läs artikeln [ExpressRoute-leverantörer och platser](expressroute-locations.md) för den senaste informationen.

### Anslutning till nationella moln

Microsoft använder isolerade molnmiljöer för särskilda geopolitiska regioner och kundsegment. Se artikeln [ExpressRoute-leverantörer och platser](expressroute-locations.md) för en lista med nationella moln och leverantörer.

### Bandbreddsalternativ som stöds

Du kan köpa ExpressRoute-kretsar för en mängd olika bandbredder. Listan med bandbredder som stöds finns nedan. Kontrollera med din anslutningsleverantör vilken bandbreddslista de erbjuder.

- 50 Mbit/s
- 100 Mbit/s
- 200 Mbit/s
- 500 Mbit/s
- 1 Gbit/s
- 2 Gbit/s
- 5 Gbit/s
- 10 Gbit/s

### Dynamisk skalning av bandbredd

Du kan öka ExpressRoute-kretsens bandbredd (baserat på bästa prestanda) utan att behöva avbryta dina anslutningar. 

### Flexibla faktureringsmodeller

Du kan välja den faktureringsmodell som passar dig bäst. Välj mellan faktureringsmodellerna nedan. Se sidan [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md) för mer information. 

- **Obegränsad data**. ExpressRoute-kretsen debiteras baserat på en månatlig avgift och all inkommande och utgående dataöverföring är kostnadsfri. 
- **Avgiftsbelagda data**. ExpressRoute-kretsens debitering baseras på en månatlig avgift. All inkommande dataöverföring är kostnadsfri. Utgående dataöverföring debiteras per GB data som överförs. Dataöverföringskostnader varierar beroende på region.
- **ExpressRoutes premiumtillägg**. ExpressRoutes premium är ett tillägg till ExpressRoute-kretsen. ExpressRoutes premiumtillägg innehåller följande funktioner: 
    - Ökade väggränser för Azures offentliga och privata peering från 4 000 vägar till 10 000 vägar.
    - Global anslutning för tjänster. En ExpressRoute-krets som skapats i en region (exklusive nationella moln) har åtkomst till resurser i alla andra regioner i världen. Till exempel kan ett virtuellt nätverk som skapats i västra Europa nås via en ExpressRoute-krets som etablerats i Silicon Valley.
    - Ökat antal VNet-länkar per ExpressRoute-krets från 10 till en högre gräns, beroende på kretsens bandbredd.

## Nästa steg

- Läs mer om ExpressRoute-anslutningar och routningsdomäner. Se [ExpressRoute-anslutningar och routningsdomäner](expressroute-circuit-peerings.md).
- Hitta en tjänstleverantör. Se [ExpressRoute-partners och peeringplatser](expressroute-locations.md).
- Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).
- Se kraven för [routning](expressroute-routing.md), [NAT](expressroute-nat.md) och [QoS](expressroute-qos.md).
- Konfigurera ExpressRoute-anslutningen.
    - [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md)
    - [Konfigurera routning](expressroute-howto-routing-classic.md)
    - [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-classic.md)



<!--HONumber=Oct16_HO3-->



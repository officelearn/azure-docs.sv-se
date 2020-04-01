---
title: Felsöka AZURE Virtual Network NAT-anslutning
titleSuffix: Azure Virtual Network
description: Felsöka problem med NAT för virtuellt nätverk.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: allensu
ms.openlocfilehash: c012a8d83761b88cc59b62d11fd3d5542ca7f7a1
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396095"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Felsöka AZURE Virtual Network NAT-anslutning

Den här artikeln hjälper administratörer att diagnostisera och lösa anslutningsproblem när de använder NAT för virtuellt nätverk.

## <a name="problems"></a>Problem

* [SNAT utmattning](#snat-exhaustion)
* [ICMP ping misslyckas](#icmp-ping-is-failing)
* [Anslutningsfel](#connectivity-failures)
* [IPv6 samexistens](#ipv6-coexistence)

LÃ¶s dessa problem genom att sÃ¥ lÃ¤5 nde i följande avsnitt.

## <a name="resolution"></a>Lösning

### <a name="snat-exhaustion"></a>SNAT utmattning

En enda [NAT-gatewayresurs](nat-gateway-resource.md) stöder från 64 000 upp till 1 miljon samtidiga flöden.  Varje IP-adress tillhandahåller 64 000 SNAT-portar till det tillgängliga lagret. Du kan använda upp till 16 IP-adresser per NAT-gatewayresurs.  SNAT-mekanismen beskrivs [här](nat-gateway-resource.md#source-network-address-translation) mer i detalj.

Ofta är grundorsaken till SNAT-utmattning ett anti-mönster för hur utgående anslutning upprättas, hanteras eller konfigurerbara timers ändras från deras standardvärden.  Granska det här avsnittet noggrant.

#### <a name="steps"></a>Steg

1. Kontrollera om du har ändrat standardtidsgränsen för inaktiv tid till ett värde som är högre än 4 minuter.
2. Undersök hur ditt program skapar utgående anslutning (till exempel kodgranskning eller paketinsamling). 
3. Ta reda på om den här aktiviteten är förväntat eller om programmet missköter sig.  Använd [mått](nat-metrics.md) i Azure Monitor för att underbygga dina resultat. Använd kategorin "Misslyckades" för SNAT-anslutningar.
4. Utvärdera om lämpliga mönster följs.
5. Utvärdera om SNAT-portutmattning bör minskas med ytterligare IP-adresser som tilldelats NAT gateway-resurs.

#### <a name="design-patterns"></a>Designmönster

Dra alltid nytta av återanvändning och anslutningspoolning när det är möjligt.  Dessa mönster kommer att undvika resursutmattningsproblem och resultera i förutsägbart beteende. Primitiver för dessa mönster finns i många utvecklingsbibliotek och ramverk.

_**Lösning:**_ Använd lämpliga mönster och metodtips

- NAT gateway-resurser har en standardtidsutbetalning för TCP-inaktiv på 4 minuter.  Om den här inställningen ändras till ett högre värde kommer NAT att hålla fast vid flödena längre och kan orsaka [onödigt tryck på SNAT-portlager](nat-gateway-resource.md#timers).
- Atomic förfrågningar (en begäran per anslutning) är ett dåligt designval. Sådana anti-mönster begränsar skala, minskar prestanda och minskar tillförlitligheten. Återanvänd i stället HTTP/S-anslutningar för att minska antalet anslutningar och associerade SNAT-portar. Programskalan ökar och prestanda förbättras på grund av minskade handslag, omkostnader och kryptografiska driftkostnader när du använder TLS.
- DNS kan introducera många enskilda flöden på volym när klienten inte cachelagrar DNS-resolvers-resultatet. Använd cachelagring.
- UDP-flöden (till exempel DNS-sökning) allokerar SNAT-portar under den inaktiva tidsgränsen. Ju längre inaktiv timeout, desto högre tryck på SNAT-portar. Använd kort tidsgränsen för inaktiv utskrift (till exempel 4 minuter).
- Använd anslutningspooler för att forma anslutningsvolymen.
- Överge aldrig tyst ett TCP-flöde och förlita dig på TCP-timers för att rensa upp flödet. Om du inte låter TCP uttryckligen stänga anslutningen förblir tillståndet allokerat vid mellanliggande system och slutpunkter och gör SNAT-portar otillgängliga för andra anslutningar. Detta kan utlösa programfel och SNAT-utmattning. 
- Ändra inte TCP-relaterade tidsbegränsade tidsbegränsade tidsbegränsade värden på OS-nivå utan expertkunskap om påverkan. TCP-stacken återställs, men programprestandan kan påverkas negativt när slutpunkterna för en anslutning har gett efter förväntningar. Viljan att byta timers är oftast ett tecken på ett underliggande designproblem. Granska följande rekommendationer.

Ofta kan SNAT-utmattning också förstärkas med andra anti-mönster i den underliggande applikationen. Granska dessa ytterligare mönster och metodtips för att förbättra tjänstens omfattning och tillförlitlighet.

- Utforska effekten av att minska [TCP-tidsgränsen](nat-gateway-resource.md#timers) för inaktiv till lägre värden, inklusive standardtidsgräns för inaktiv 4 minuter för att frigöra SNAT-portlager tidigare.
- Överväg [asynkrona avsökningsmönster](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) för tidskrävande åtgärder för att frigöra anslutningsresurser för andra åtgärder.
- Långlivade flöden (till exempel återanvända TCP-anslutningar) bör använda TCP keepalives eller applikationsskikt keepalives för att undvika mellanliggande system timing out. Att öka tidsgränsen för inaktiv tid är en sista utväg och kanske inte löser grundorsaken. En lång timeout kan orsaka låg hastighet fel när timeout löper ut och införa fördröjning och onödiga fel.
- Graciösa [återförsöksmönster](https://docs.microsoft.com/azure/architecture/patterns/retry) bör användas för att undvika aggressiva återförsök/bursts vid övergående fel eller felåterställning.
Att skapa en ny TCP-anslutning för varje HTTP-åtgärd (kallas även "atomanslutningar") är ett anti-mönster.  Atomanslutningar förhindrar att ditt program skalar bra och slösar resurser.  Rör alltid flera åtgärder till samma anslutning.  Ditt program kommer att gynnas i transaktionshastighet och resurskostnader.  När ditt program använder kryptering av transportlager (till exempel TLS) finns det en betydande kostnad i samband med bearbetning av nya anslutningar.  Granska [Azure Cloud Design Patterns](https://docs.microsoft.com/azure/architecture/patterns/) för ytterligare mönster för bästa praxis.

#### <a name="additional-possible-mitigations"></a>Ytterligare möjliga mildrande åtgärder

_**Lösning:**_ Skala utgående anslutning enligt följande:

| Scenario | Bevis |Åtgärd |
|---|---|---|
| Du upplever konkurrens om SNAT-portar och SNAT-portutmattning under perioder med hög användning. | Kategorin "Misslyckades" för SNAT-anslutningar [metric](nat-metrics.md) i Azure Monitor visar tillfälliga eller beständiga fel över tid och hög anslutningsvolym.  | Ta reda på om du kan lägga till ytterligare offentliga IP-adressresurser eller offentliga IP-prefixresurser. Det här tillägget tillåter upp till 16 IP-adresser totalt till din NAT-gateway. Det här tillägget ger mer lager för tillgängliga SNAT-portar (64 000 per IP-adress) och gör att du kan skala ditt scenario ytterligare.|
| Du har redan gett 16 IP-adresser och fortfarande upplever SNAT port utmattning. | Det gick inte att lägga till ytterligare IP-adress. Totalt antal IP-adresser från offentliga IP-adressresurser eller offentliga IP-prefixresurser överstiger totalt 16. | Distribuera programmiljön över flera undernät och tillhandahålla en NAT-gatewayresurs för varje undernät.  Omvärdera designmönstret för att optimera baserat på föregående [vägledning](#design-patterns). |

>[!NOTE]
>Det är viktigt att förstå varför SNAT utmattning uppstår. Se till att du använder rätt mönster för skalbara och tillförlitliga scenarier.  Lägga till fler SNAT-portar till ett scenario utan att förstå orsaken till efterfrågan bör vara en sista utväg. Om du inte förstår varför ditt scenario lägger till tryck på SNAT-portlager, kommer det bara att fördröja samma utmattningsfel om du lägger till fler SNAT-portar i lagret genom att lägga till fler IP-adresser.  Du kan maskera andra ineffektivitet och anti-mönster.

### <a name="icmp-ping-is-failing"></a>ICMP ping misslyckas

[Virtual Network NAT](nat-overview.md) stöder IPv4 UDP- och TCP-protokoll. ICMP stöds inte och förväntas misslyckas.  

_**Lösning:**_ Använd i stället TCP-anslutningstester (till exempel "TCP ping") och UDP-specifika programlagertester för att validera anslutning från slutpunkt till slutpunkt.

Följande tabell kan användas som utgångspunkt för vilka verktyg som ska användas för att starta tester.

| Operativsystem | Generiskt TCP-anslutningstest | Test av TCP-programlager | UDP |
|---|---|---|---|
| Linux | nc (generiskt anslutningstest) | curl (TCP-test av programlager) | tillämpningsspecifika |
| Windows | [PsPing (psping)](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Anropa-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | tillämpningsspecifika |

### <a name="connectivity-failures"></a>Anslutningsfel

Anslutningsproblem med [NAT för virtuellt nätverk](nat-overview.md) kan orsakas av flera olika problem:

* övergående eller ihållande [SNAT-utmattning](#snat-exhaustion) av NAT-gatewayen,
* tillfälliga fel i Azure-infrastrukturen, 
* tillfälliga fel i sökvägen mellan Azure och det offentliga Internet-målet, 
* tillfälliga eller beständiga fel på den offentliga Internet-destinationen.

Använd verktyg som följande för valideringsanslutning. [ICMP-ping stöds inte](#icmp-ping-is-failing).

| Operativsystem | Generiskt TCP-anslutningstest | Test av TCP-programlager | UDP |
|---|---|---|---|
| Linux | nc (generiskt anslutningstest) | curl (TCP-test av programlager) | tillämpningsspecifika |
| Windows | [PsPing (psping)](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Anropa-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | tillämpningsspecifika |

#### <a name="snat-exhaustion"></a>SNAT utmattning

Granska avsnittet om [SNAT-utmattning](#snat-exhaustion) i den här artikeln.

#### <a name="azure-infrastructure"></a>Azure-infrastruktur

Azure övervakar och driver sin infrastruktur med stor omsorg. Övergående fel kan uppstå, det finns ingen garanti för att sändningar är förlustfria.  Använd designmönster som möjliggör SYN-återsändningar för TCP-program. Använd tidsutgångar för anslutning som är tillräckligt stora för att TCP SYN ska kunna överföras för att minska tillfälliga effekter som orsakas av ett förlorat SYN-paket.

_**Lösning:**_

* Kontrollera om [det finns SNAT-utmattning.](#snat-exhaustion)
* Konfigurationsparametern i en TCP-stack som styr syn-återsändningsbeteendet kallas RTO ([Återsändande time-out](https://tools.ietf.org/html/rfc793)). RTO-värdet är justerbart men vanligtvis 1 sekund eller högre som standard med exponentiell back-off.  Om timeout för anslutning till programmet är för kort (till exempel 1 sekund) kan du se sporadiska anslutningstidsutgångar.  Öka time-time time-out för programanslutningen.
* Om du observerar längre, oväntade tidsutskrifter med standardprogramfunktioner öppnar du ett supportärende för ytterligare felsökning.

Vi rekommenderar inte att du på ett konstlat sätt minskar timeouten för TCP-anslutningen eller justerar RTO-parametern.

#### <a name="public-internet-transit"></a>Kollektivtrafik på Internet

Risken för tillfälliga fel ökar med en längre väg till destinationen och fler mellanliggande system. Det förväntas att tillfälliga fel kan öka i frekvens över [Azure-infrastruktur](#azure-infrastructure). 

Följ samma vägledning som föregående [Azure-infrastrukturavsnitt.](#azure-infrastructure)

#### <a name="internet-endpoint"></a>Internet-slutpunkt

Föregående avsnitt gäller, tillsammans med internetslutpunkten som kommunikationen upprättas med. Andra faktorer som kan påverka anslutningen framgång är:

* trafikledning på destinationssidan, inklusive
- API-hastighetsbegränsning som införts av målsidan
- Volymetriska DDoS-mildrande åtgärder eller transportskiktstrafikformning
* brandvägg eller andra komponenter på destinationen 

Vanligtvis paket fångar vid källan och målet (om tillgängligt) krävs för att avgöra vad som händer.

_**Lösning:**_

* Kontrollera om [det finns SNAT-utmattning.](#snat-exhaustion) 
* Verifiera anslutningen till en slutpunkt i samma region eller någon annanstans för jämförelse.  
* Om du skapar testning av hög volym eller transaktionshastighet kan du undersöka om en minskning av hastigheten minskar förekomsten av fel.
* Om ändrande hastighet påverkar felfrekvensen kontrollerar du om API-hastighetsbegränsningar eller andra begränsningar på målsidan kan ha uppnåtts.
* Om din undersökning är ofullständig öppnar du ett supportärende för ytterligare felsökning.

#### <a name="tcp-resets-received"></a>TCP Återställer mottaget

NAT-gatewayen genererar TCP-återställningar på källdatorn för trafik som inte känns igen som pågående.

En möjlig orsak är att TCP-anslutningen har tidssändelser som är inaktiv.  Du kan justera tidsgränsen för inaktiv tid från 4 minuter till upp till 120 minuter.

TCP-återställningar genereras inte på den offentliga sidan av NAT-gatewayresurser. TCP-återställningar på målsidan genereras av källdatorn, inte NAT-gatewayresursen.

_**Lösning:**_

* Granska rekommendationer för [designmönster.](#design-patterns)  
* Öppna ett supportärende för ytterligare felsökning om det behövs.

### <a name="ipv6-coexistence"></a>IPv6 samexistens

[Virtual Network NAT](nat-overview.md) stöder IPv4 UDP- och TCP-protokoll och distribution på ett [undernät med ett IPv6-prefix stöds inte](nat-overview.md#limitations).

_**Lösning:**_ Distribuera NAT-gateway på ett undernät utan IPv6-prefix.

Du kan ange intresse för ytterligare funktioner via [NAT UserVoice för virtuellt nätverk](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [NAT för virtuellt nätverk](nat-overview.md)
* Lär dig mer om [NAT-gatewayresurs](nat-gateway-resource.md)
* Lär dig mer om [mått och aviseringar för NAT-gatewayresurser](nat-metrics.md).
* [Berätta vad du ska bygga härnäst för VIRTUELLT NÄTVERK NAT i UserVoice](https://aka.ms/natuservoice).


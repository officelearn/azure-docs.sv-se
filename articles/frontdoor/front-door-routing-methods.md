---
title: Azure ytterdörren Service – trafikroutningsmetoder | Microsoft Docs
description: Den här artikeln hjälper dig att förstå de olika routningsmetoder som används av ytterdörren
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: bd1278db43ba31ed78f13a826a330e16c3bc8d57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736232"
---
# <a name="front-door-routing-methods"></a>Ytterdörren routningsmetoder

Azure ytterdörren Service stöder olika trafikroutningsmetoder för att avgöra hur du dirigerar HTTP/HTTPS-trafik till olika tjänstslutpunkter. Med var och en av dina klientbegäranden når ytterdörren tillämpas konfigurerade routningsmetod för att säkerställa att begäranden vidarebefordras till den bästa serverdel-instansen. 

Det finns fyra huvudsakliga begrepp som routning av nätverkstrafik i ytterdörren:

* **[Svarstid](#latency):** Svarstid-baserad routning säkerställer att begäranden skickas till den lägsta svarstid serverdelen godkända inom ett intervall för känslighet. I praktiken, skickas din användarförfrågningar till ”närmaste” uppsättning serverdelar med avseende på Nätverksfördröjningen.
* **[Prioritet](#priority):** Du kan tilldela prioriteringar till serverdelen olika när du vill använda en primär service-serverdelen för all trafik och ge säkerhetskopiering om primärt eller serverdelar för säkerhetskopiering är inte tillgänglig.
* **[Viktat](#weighted):** Du kan tilldela vikter till serverdelen olika när du vill distribuera trafik över en uppsättning serverdelar, jämnt eller enligt vikt koefficienter.
* **Sessionstillhörighet:** Du kan konfigurera sessionen tillhörighet för frontend-värdar eller domäner om du vill att efterföljande förfrågningar från en användare skickas till samma serverdel så länge som användarsessionen är fortfarande är aktiv och backend-instans fortfarande rapporterar felfritt baserat på hälsokontroller av slutpunkter. 

I alla Front Door-konfigurationer ingår övervakning av hälsotillståndet på serverdelen och automatiserad omedelbar global redundans. Mer information finns i [ytterdörren serverdel övervakning](front-door-health-probes.md). Ytterdörren kan konfigureras till antingen arbete som baseras på en enda routningsmetod och beroende på ditt program måste du kan använda flera eller alla dessa metoder för trafikroutning tillsammans för att skapa en optimal routning topologin.

## <a name = "latency"></a>Lägsta svarstider baserad routning av nätverkstrafik

Distribuera serverdelar i två eller flera platser i hela världen kan förbättra svarstiden för många program genom att dirigera trafiken till den plats som ligger närmast användarna. Routning av nätverkstrafik standardmetoden för din konfiguration för ytterdörren vidarebefordrar begäranden från dina slutanvändare till närmaste serverdelen från ytterdörren miljön som tog emot begäran. I kombination med Anycast-arkitekturen i Azure ytterdörren Service, garanterar den här metoden att var och en av dina slutanvändare få maximala prestanda anpassade baserat på deras plats.

”Närmaste' serverdelen är inte nödvändigtvis närmaste enligt geografisk avstånd. I stället anger ytterdörren närmaste serverdelar genom att mäta svarstiden i nätverket. Läs mer om [Front dörren routning arkitektur](front-door-routing-architecture.md). 

Nedan visas övergripande beslutsflödet:

| Tillgängliga serverdelar | Prioritet | Svarstid-signal (baserat på hälsoavsökning) | Vikter |
|-------------| ----------- | ----------- | ----------- |
| Det första väljer alla servrar som är aktiverade och returnerade felfritt (200 OK) för hälsoavsökningen. Betyder att det finns sex serverdelar A, B, C, D, E och F, och bland dem C är i feltillstånd och E är inaktiverat. Så listan över tillgängliga serverdelar är A, B och D F.  | Därefter har högsta prioritet serverdelar bland de tillgängliga markerats. Exempelvis serverdel A, B och D har prioritet 1 och serverdelen F har prioritet 2. Så valda serverdelar blir A, B och D.| Välj serverdelar med svarstidsintervallet (lägsta svarstid och svarstid känsligheten i ms som angetts). Exempelvis om A är 15 ms, B är 30 ms och D är 60 ms från ytterdörren miljön där begäran landat och svarstid känslighet är ms-30 och sedan lägsta svarstid pool består av serverdelen A och B, eftersom D är längre än 30 ms bort från den närmaste serverdel som är A. | Till sist kommer ytterdörren resursallokering trafik mellan sista valda poolen med serverdelar i förhållandet mellan vikterna anges. Anta att, om serverdelen A har en vikt på 5 och serverdelen B har en vikt på 8, så att trafiken ska distribueras i förhållandet mellan 5:8 bland serverdelar A och B. |

>[!NOTE]
> Som standard svarstid känslighet egenskapen är inställd på 0 ms, det vill säga, alltid vidarebefordra begäran till den snabbaste serverdelen.


## <a name = "priority"></a>Prioritetsbaserad-routning av nätverkstrafik

Ofta vill en organisation att tillhandahålla tillförlitlighet för sina tjänster genom att distribuera en eller flera säkerhetskopierade tjänster om deras primära tjänst slutar fungera. I branschen – är den här topologin även kallat aktivt/vänteläge eller aktiv/passiv-topologi för distribution. ”Prioritet” trafikdirigeringsmetoden kan Azure-kunder enkelt kan implementera det här mönstret för redundans.

Din standard ytterdörren innehåller samma prioritet över serverdelar. Som standard ytterdörren skickar trafik endast till vår högsta prioritet serverdelar (lägsta värde för prioritet) det vill säga den primära uppsättningen serverdelar. Om de primära serverdelar inte är tillgängliga dirigerar ytterdörren trafiken till den sekundära uppsättningen serverdelar (näst lägsta värde för prioritet). Om både de primära och sekundära servrar inte är tillgängliga går trafiken till den tredje och så vidare. Tillgängligheten för serverdelen baseras på den konfigurerade status (aktiverad eller inaktiverad) och pågående serverdel systemets hälsotillstånd avsökningar som kontrollerar hälsotillståndet.

### <a name="configuring-priority-for-backends"></a>Konfigurera prioritet för serverdelar

Varje serverdelar i backend-pool i ytterdörren-konfigurationen har en egenskap med namnet ”prioritet”, vilket kan vara ett tal mellan 1 och 5. Med Azure ytterdörren Service kan konfigurera du backend-prioritet uttryckligen med hjälp av den här egenskapen för varje serverdel. Den här egenskapen är ett värde mellan 1 och 5. Lägre värden motsvarar högre prioritet. Serverdelar kan dela prioritetsvärdet.

## <a name = "weighted"></a>Viktad trafikdirigeringsmetoden
Metoden 'Viktad' routning av nätverkstrafik kan du vill distribuera trafiken jämnt eller använda en fördefinierad värde.

I den viktad trafikdirigeringsmetoden tilldelar du en vikt att varje serverdel i ytterdörren konfigurationen av backend-pool. Vikten är ett heltal mellan 1 och 1 000. Den här parametern används standard ”50”.

Av listan över tillgängliga serverdelar inom godkända svarstid känslighet (enligt), hämtar trafiken distribueras i en resursallokering mekanism i förhållandet mellan vikterna anges. Om svarstiden känslighet anges till 0 millisekunder, börja inte den här egenskapen gälla om det inte finns två servrar med samma nätverksfördröjning. 

Metoden viktad aktiverar vissa användbara scenarier:

* **Gradvis Programuppgradering**: Tilldela en procentandel av trafik för dirigering till en ny serverdel och gradvis öka trafiken över tid för att göra den översättningsminnen med andra servrar.
* **Program-migreringen till Azure**: Skapa en serverdelspool med både Azure och externa serverdelar. Justera vikten för serverdelar att föredra nya serverdelar. Du kan gradvis konfigurera detta från och med med nya serverdelar inaktiverats kan sedan tilldela dem de lägsta vikterna långsamt öka den nivåer där de kan utföra de flesta trafik. Sedan slutligen inaktiverar mindre önskade serverdelar och ta bort dem från poolen.  
* **”Cloud bursting” för ytterligare kapacitet**: Snabbt utöka en lokal distribution till molnet genom att placera det bakom ytterdörren. När du behöver extra kapacitet i molnet kan du lägga till eller aktivera flera serverdelar och anger vilken del av trafiken går till varje serverdel.

## <a name = "affinity"></a>Sessionstillhörighet
Som standard utan sessionstillhörighet, ytterdörren vidarebefordrar förfrågningar som kommer från samma klient till olika serverdelar baserat på nätverksbelastning configuration särskilt när svarstider till olika serverdelar ändras eller om olika förfrågningar från samma användare oceanen på en annan ytterdörren-miljö. Men med vissa tillståndskänsliga program eller i vissa andra scenarier är det att föredra att efterföljande förfrågningar från samma användare hamnar på samma serverdel som bearbetade den första begäran. Cookie-baserad sessionstillhörighet är användbart om du vill behålla en användarsession på samma serverdel. Genom att använda ytterdörren hanteras cookies, kan Azure ytterdörren Service dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning av så länge serverdelen är felfri och användarsessionen inte har gått ut. 

Sessionstillhörighet kan aktiveras på nivån för klientdelsvärden, dvs. för var och en av dina konfigurerade domäner (eller underdomäner). När funktionen har aktiverats lägger Front Door till en cookie till användarens session. Med cookie-baserad sessionstillhörighet kan Front Door identifiera olika användare även bakom samma IP-adress, vilket i sin tur möjliggör en mer jämn fördelning av trafik mellan dina olika serverdelar.

Livslängden för cookien är samma som användarens session eftersom Front Door för närvarande endast stöder sessions-cookies. 

> [!NOTE]
> Offentliga proxyservrar kan störa sessionstillhörighet. Det beror på att upprätta en session kräver ytterdörren att lägga till en sessionscookie tillhörighet i svaret som inte kan utföras om svaret är komma eftersom det skulle störa cookies i andra klienter som begär samma resurs. Om du vill skydda dig mot det, kommer sessionstillhörighet **inte** upprättas om serverdelen skickar ett komma svar när det görs. Det spelar ingen roll om svaret från serverdelen är komma om sessionen redan har upprättats.
> Sessionstillhörighet upprättas under följande omständigheter **såvida inte** svaret har en 304 för HTTP-statuskod:
> - Svaret har specifika värden som angetts för den ```Cache-Control``` -huvud som förhindra cachelagring, till exempel ”privat” eller no-store ”.
> - Svaret innehåller en ```Authorization``` -huvud som inte har upphört att gälla.
> - Svaret har en HTTP 302-statuskod.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).

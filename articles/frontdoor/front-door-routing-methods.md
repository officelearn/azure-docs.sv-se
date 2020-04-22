---
title: Azure Ytterdörr - trafik routing metoder | Microsoft-dokument
description: Den här artikeln hjälper dig att förstå de olika trafikroutningsmetoder som används av Ytterdörren
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
ms.openlocfilehash: b7dd00d28ecfe844094677e0ae19f4fd359d97d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687812"
---
# <a name="front-door-routing-methods"></a>Routningsmetoder för ytterdörren

Azure Front Door stöder olika trafikroutningsmetoder för att avgöra hur du dirigerar DIN HTTP/HTTPS-trafik till de olika tjänstslutpunkterna. När var och en av klientbegärandena når Ytterdörren tillämpas den konfigurerade routningsmetoden för att säkerställa att begäranden vidarebefordras till den bästa serverdelsinstansen. 

Det finns fyra huvudbegrepp för trafikroutning som finns i Ytterdörren:

* ** [Latens:](#latency)** Den svarstidsbaserade routningen säkerställer att begäranden skickas till de lägsta svarstidshandlade serverdarna som är godtagbara inom ett känslighetsintervall. I grund och botten skickas dina användarförfrågningar till den "närmaste" uppsättningen med serverenheter med avseende på nätverksfördröjning.
* ** [Prioritet](#priority):** Du kan tilldela prioriteter till dina olika serverdelar när du vill använda en primär tjänst serverdel för all trafik och tillhandahålla säkerhetskopior om de primära eller säkerhetskopieringsdelarna inte är tillgängliga.
* ** [Viktad:](#weighted)** Du kan tilldela vikter till dina olika backends när du vill distribuera trafik över en uppsättning backends, antingen jämnt eller enligt viktkoefficienter.
* ** [Session Tillhörighet:](#affinity)** Du kan konfigurera sessionstillhörighet för frontend-värdar eller domäner när du vill att efterföljande begäranden från en användare ska skickas till samma serverdel så länge användarsessionen fortfarande är aktiv och serverdelsinstansen fortfarande rapporterar felfri baserat på hälsoavsökningar. 

I alla Front Door-konfigurationer ingår övervakning av hälsotillståndet på serverdelen och automatiserad omedelbar global redundans. Mer information finns i [Front Door Backend Monitoring](front-door-health-probes.md). Ytterdörren kan konfigureras för att antingen arbeta baserat på en enda routningsmetod och beroende på dina programbehov kan du använda flera eller alla dessa routningsmetoder i kombination för att skapa en optimal routningstopologi.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Lägsta svarstider baserade på trafikroutning

Om du distribuerar backends på två eller flera platser över hela världen kan du förbättra svarstiden för många program genom att dirigera trafik till den plats som är "närmast" till slutanvändarna. Standardinställningen för trafikroutning för front door-konfigurationen vidarebefordrar begäranden från slutanvändarna till närmaste serverdel från ytterdörren-miljön som tog emot begäran. Kombinerat med Anycast-arkitekturen i Azure Front Door säkerställer den här metoden att var och en av dina slutanvändare får maximal prestanda anpassad baserat på deras plats.

Den "närmaste" backend är inte nödvändigtvis närmast mätt med geografiskt avstånd. I stället bestämmer ytterdörren de närmaste backendsna genom att mäta nätverksfördröjning. Läs mer om [Ytterdörrens routningsarkitektur](front-door-routing-architecture.md). 

Nedan följer det övergripande beslutsflödet:

| Tillgängliga backends | Prioritet | Latenssignal (baserat på hälsosond) | Vikter |
|-------------| ----------- | ----------- | ----------- |
| För det första väljer du alla servernamn som är aktiverade och returnerade felfria (200 OK) för hälsoavsökningen. Säg, det finns sex serverdar A, B, C, D, E och F, och bland dem C är ohälsosamt och E är inaktiverat. Så, lista över tillgängliga backends är A, B, D och F.  | Därefter väljs de högsta prioritetsbackarna bland de tillgängliga. Säg, backend A, B och D har prioritet 1 och backend F har en prioritet på 2. Så, utvalda backends kommer att vara A, B och D.| Markera de med svarstider med svarstidsintervallet (minsta latens & latenskänslighet i ms angivet). Säg, om A är 15 ms, B är 30 ms och D är 60 ms bort från ytterdörren miljön där begäran landade, och latens känslighet är 30 ms, då lägsta latens pool består av backend A och B, eftersom D är bortom 30 ms bort från närmaste backend som är A. | Slutligen kommer Ytterdörren runda robin trafiken bland de slutliga valda pool av backends i förhållandet mellan vikter. Säg, om backend A har en vikt på 5 och backend B har en vikt på 8, då trafiken kommer att fördelas i förhållandet 5:8 bland backends A och B. |

>[!NOTE]
> Som standard är egenskapen latenskänslighet inställd på 0 ms, det villa alltid vidarebefordra begäran till den snabbaste tillgängliga backend.


## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Prioriterad trafikroutning

Ofta vill en organisation tillhandahålla tillförlitlighet för sina tjänster genom att distribuera en eller flera säkerhetskopieringstjänster om deras primära tjänst går ner. I branschen kallas den här topologin även aktiv/vänteläge eller aktiv/passiv distributionstopologi. Med trafikroutningsmetoden Prioritet kan Azure-kunder enkelt implementera det här redundansmönstret.

Standarddörren innehåller en lista med samma prioritet med bakåtsidor. Som standard skickar Ytterdörren endast trafik till de högsta prioritetsdelarna (lägsta värdet för prioritet) det vill ha den primära uppsättningen med backends. Om de primära backendsna inte är tillgängliga dirigerar Ytterdörren trafiken till den sekundära uppsättningen med backends (näst lägsta värdet för prioritet). Om både primära och sekundära backends inte är tillgängliga, går trafiken till den tredje och så vidare. Serveringstillgångstillgång baseras på den konfigurerade statusen (aktiverad eller inaktiverad) och den pågående servergångens hälsostatus som bestäms av hälsoavsökningarna.

### <a name="configuring-priority-for-backends"></a>Konfigurera prioritet för serverings-enheter

Var och en av serverdelen i din serverdelspool i frontdörrkonfigurationen har en egenskap som heter "Prioritet", som kan vara ett nummer mellan 1 och 5. Med Azure Front Door konfigurerar du serverdelsprioriteten explicit med den här egenskapen för varje serverdel. Den här egenskapen är ett värde mellan 1 och 5. Lägre värden representerar en högre prioritet. Backends kan dela prioritetsvärden.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Metod för viktad trafikdirigering
Med trafikroutningsmetoden "Viktad" kan du fördela trafiken jämnt eller använda en fördefinierad viktning.

I metoden Viktad trafik-routing tilldelar du en vikt till varje serverdel i frontdörrkonfigurationen för serverdelspoolen. Vikten är ett heltal mellan 1 och 1 000. Den här parametern använder en standardvikt på "50".

Bland listan över tillgängliga backends inom den accepterade latens känslighet (som anges), trafiken får distribueras i en round-robin mekanism i förhållandet mellan vikter som anges. Om svarstidskänsligheten är inställd på 0 millisekunder börjar den här egenskapen inte gälla om det inte finns två bakåtsträvningar med samma nätverksfördröjning. 

Den viktade metoden möjliggör några användbara scenarier:

* **Gradvis programuppgradering**: Tilldela en procentandel av trafiken för att dirigera till en ny backend och gradvis öka trafiken över tid för att få den i nivå med andra backends.
* **Programmigrering till Azure**: Skapa en backend-pool med både Azure och externa backends. Justera vikten på backends att föredra de nya backends. Du kan gradvis ställa in detta börjar med att ha de nya serverdelar inaktiveras, sedan tilldela dem de lägsta vikterna, långsamt öka den till nivåer där de tar mest trafik. Sedan slutligen inaktivera de mindre föredragna backends och ta bort dem från poolen.  
* **Cloud-bursting för ytterligare kapacitet:** Expandera snabbt en lokal distribution i molnet genom att lägga den bakom ytterdörren. När du behöver extra kapacitet i molnet kan du lägga till eller aktivera fler backends och ange vilken del av trafiken som går till varje backend.

## <a name="session-affinity"></a><a name = "affinity"></a>Session tillhörighet
Som standard, utan sessionstillhörighet, vidarebefordrar Front Door begäranden som kommer från samma klient till olika serverdelskonfiguration baserat på belastningsutjämningskonfiguration, särskilt som svarstider till olika serverdelar ändras eller om olika begäranden från samma användare landar på en annan frontdörrmiljö. Men med vissa tillståndskänsliga program eller i vissa andra scenarier är det att föredra att efterföljande förfrågningar från samma användare hamnar på samma serverdel som bearbetade den första begäran. Cookie-baserad sessionstillhörighet är användbart om du vill behålla en användarsession på samma serverdel. Genom att använda Front Door-hanterade cookies kan Azure Front Door dirigera efterföljande trafik från en användarsession till samma backend för bearbetning så länge backend är felfri och användarsessionen inte har upphört att gälla. 

Sessionstillhörighet kan aktiveras på nivån för klientdelsvärden, dvs. för var och en av dina konfigurerade domäner (eller underdomäner). När funktionen har aktiverats lägger Front Door till en cookie till användarens session. Med cookie-baserad sessionstillhörighet kan Front Door identifiera olika användare även bakom samma IP-adress, vilket i sin tur möjliggör en mer jämn fördelning av trafik mellan dina olika serverdelar.

Livslängden för cookien är samma som användarens session eftersom Front Door för närvarande endast stöder sessions-cookies. 

> [!NOTE]
> Offentliga proxyservrar kan störa sessionstillhörigheten. Detta beror på att upprätta en session kräver ytterdörren att lägga till en session tillhörighet cookie till svaret, vilket inte kan göras om svaret är cacheable eftersom det skulle störa cookies för andra klienter som begär samma resurs. För att skydda mot detta upprättas **inte** sessionstillhörighet om backend skickar ett cacheable svar när detta görs. Om sessionen redan har upprättats spelar det ingen roll om svaret från backend är cachelagbart.
> Sessionstillhörighet kommer att fastställas under följande omständigheter, **såvida inte** svaret har en HTTP 304-statuskod:
> - Svaret har specifika värden ```Cache-Control``` för huvudet som förhindrar cachelagring, till exempel "privat" eller ingen butik".
> - Svaret innehåller ett ```Authorization``` huvud som inte har upphört att gälla.
> - Svaret har en HTTP 302-statuskod.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).

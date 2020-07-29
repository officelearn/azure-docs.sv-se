---
title: Metoder för trafikroutning i Azures front dörr | Microsoft Docs
description: Den här artikeln hjälper dig att förstå de olika metoder för trafikroutning som används av front dörren
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81687812"
---
# <a name="front-door-routing-methods"></a>Metoder för routning av front dörr

Azures front dörr stöder olika metoder för trafik cirkulation för att fastställa hur din HTTP/HTTPS-trafik ska dirigeras till de olika tjänst slut punkterna. Med var och en av dina klient begär Anden når du den konfigurerade routningsmetod för att se till att begär Anden vidarebefordras till den bästa backend-instansen. 

Det finns fyra huvudsakliga begrepp för trafik dirigering som är tillgänglig i front dörren:

* ** [Svars tid](#latency):** Latens-baserad routning ser till att begär Anden skickas till de lägsta svars tiderna som accepteras inom ett känslighets intervall. I princip skickas dina användar förfrågningar till "närmsta" uppsättning Server delar med avseende på nätverks fördröjning.
* ** [Prioritet](#priority):** Du kan tilldela prioriteter till olika Server delar när du vill använda en primär tjänst Server del för all trafik, och ange säkerhets kopior om den primära servern eller säkerhets kopierings serverns reserv sidor inte är tillgängliga.
* ** [Viktat](#weighted):** Du kan tilldela vikter till olika Server delar när du vill distribuera trafik över en uppsättning av Server delar, antingen jämnt eller enligt viktning av koefficienter.
* ** [Tillhörighet mellan sessioner](#affinity):** Du kan konfigurera session tillhörighet för dina klient dels värdar eller domäner när du vill att efterföljande begär Anden från en användare skickas till samma server del förutsatt att användarsessionen fortfarande är aktiv och att Server dels instansen fortfarande rapporterar felfri baserat på hälso avsökningar. 

I alla Front Door-konfigurationer ingår övervakning av hälsotillståndet på serverdelen och automatiserad omedelbar global redundans. Mer information finns i [övervakning av front dörrs Server](front-door-health-probes.md)del. Din front dörr kan konfigureras för antingen arbete baserat på en enda routningsmetod och beroende på dina program behov kan du använda flera eller alla dessa routningsmetoder i en kombination för att bygga en optimal crawltopologi för routning.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Lägsta latens baserad trafik routning

Distribution av Server delar på två eller flera platser över hela världen kan förbättra svars tiden för många program genom att dirigera trafik till den plats som är närmast till dina slutanvändare. Standard metoden för trafikroutning för din frontend-konfiguration vidarebefordrar begär Anden från dina slutanvändare till närmaste Server del från den front dörr miljö som tog emot begäran. I kombination med anycast-arkitekturen i Azures frontend, ser den här metoden till att alla slutanvändare får bästa möjliga prestanda baserat på deras plats.

Server delen "närmast" är inte nödvändigt vis närmast som mäts efter geografiskt avstånd. I stället fastställer front dörren de närmaste server ändarna genom att mäta nätverks fördröjningen. Läs mer om [routnings arkitekturen för front dörren](front-door-routing-architecture.md). 

Nedan visas det övergripande besluts flödet:

| Tillgängliga Server delar | Prioritet | Svars tids signal (baserad på hälso avsökning) | Lekar |
|-------------| ----------- | ----------- | ----------- |
| Börja med att välja alla backend-ändar som är aktiverade och returnerade felfria (200 OK) för hälso avsökningen. Anta att det finns sex Server delar A, B, C, D, E och F, och att de båda är inaktiverade. Listan över tillgängliga Server delar är alltså A, B, D och F.  | Därefter väljs de högsta prioritets delarna bland de tillgängliga. Anta att Server del A, B och D har prioritet 1 och Server del F har prioritet 2. Därför blir de valda Server delarna A, B och D.| Välj server delar med latens intervall (minsta latens & fördröjning i MS angivet). Om t. ex. är 15 MS, B är 30 MS och D är 60 MS bort från den främre dörren där begäran landats, och fördröjnings känslighet är 30 MS, så består poolen med lägsta latens för Server del A och B, eftersom D är över 30 MS bort från den närmaste server delen som är en. | Till sist kommer front dörren att runda av trafiken mellan den slutliga valda poolen av Server delar i förhållandet mellan vikter som anges. Anta att om Server delen A har en vikt på 5 och Server del B har en vikt på 8, kommer trafiken att distribueras i förhållandet 5:8 mellan server delar A och B. |

>[!NOTE]
> Som standard är egenskapen svars känslighet inställd på 0 MS, det vill säga alltid vidarebefordra begäran till den snabbast tillgängliga Server delen.


## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Prioriterad trafik-routning

En organisation vill ofta tillhandahålla tillförlitlighet för sina tjänster genom att distribuera en eller flera säkerhets kopierings tjänster, om deras primära tjänst slutar fungera. I branschen kallas den här topologin även för aktiv/vänte läge eller aktiv/passiv distributions topologi. Med Traffic-metoden Traffic-routing kan Azure-kunder enkelt implementera det här mönstret för växling vid fel.

Din standard-front dörr innehåller en lista med samma prioritet som server delar. Som standard skickar front dörren enbart trafik till Server delens högsta prioritet (lägsta värdet för prioritet) som är den primära uppsättningen med Server delar. Om de primära server delarna inte är tillgängliga dirigerar front dörren trafiken till den sekundära uppsättningen Server delar (andra lägsta värdet för prioritet). Om både den primära och sekundära Server delen inte är tillgängliga, går trafiken till den tredje osv. Server delens tillgänglighet baseras på konfigurerad status (aktive rad eller inaktive rad) och den kontinuerliga hälso statusen för Server delen som bestäms av hälso avsökningarna.

### <a name="configuring-priority-for-backends"></a>Konfigurera prioritet för Server delar

Varje server del i din backend-pool i konfigurationen av front dörren har en egenskap som kallas "prioritet", som kan vara ett tal mellan 1 och 5. Med Azures front dörr konfigurerar du Server dels prioriteten uttryckligen med den här egenskapen för varje server del. Den här egenskapen är ett värde mellan 1 och 5. Lägre värden representerar en högre prioritet. Server delar kan dela prioritets värden.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Viktad metod för trafik dragning
Med metoden "viktad" för trafik dragning kan du distribuera trafik jämnt eller använda en fördefinierad viktning.

I den viktade metoden för trafik routning tilldelar du en vikt till varje server del i konfigurationen för den lokala server delen. Vikten är ett heltal mellan 1 och 1 000. Den här parametern använder en standard vikt på "50".

I listan över tillgängliga Server delar inom den accepterade svars tiden (enligt vad som anges) distribueras trafiken i en Round-Robin-mekanism i förhållandet mellan vikter. Om svars tiden är inställd på 0 millisekunder, börjar den här egenskapen inte gälla om det finns två server delar med samma nätverks fördröjning. 

Den viktade metoden möjliggör vissa användbara scenarier:

* **Gradvis program uppgradering**: allokera en procent andel av trafiken som ska vidarebefordras till en ny server del och öka trafiken gradvis med tiden för att ta den i parivärde med andra server delar.
* **Programmigrering till Azure**: skapa en backend-pool med både Azure och externa server delar. Justera vikten på Server delarna för att föredra de nya server delarna. Du kan gradvis konfigurera detta genom att låta de nya server delarna vara inaktiverade, och sedan tilldela dem de lägsta vikterna, så att det blir långsamt att öka det till nivåer där de tar störst trafik. Sedan inaktiverar du de mindre önskade Server delarna och tar bort dem från poolen.  
* **Cloud-burst för ytterligare kapacitet**: utöka snabbt en lokal distribution till molnet genom att placera den bakom en front dörr. När du behöver extra kapacitet i molnet kan du lägga till eller aktivera fler server delar och ange vilken del av trafiken som går till varje server del.

## <a name="session-affinity"></a><a name = "affinity"></a>Tillhörighet för session
Som standard, utan tillhörighet, vidarebefordrar front dörren begär Anden som kommer från samma klient till olika Server delar baserat på belastnings Utjämnings konfiguration, särskilt när fördröjningen för olika Server delar ändras eller om olika begär Anden från samma användare hamnar i en annan frontend-miljö. Men med vissa tillståndskänsliga program eller i vissa andra scenarier är det att föredra att efterföljande förfrågningar från samma användare hamnar på samma serverdel som bearbetade den första begäran. Cookie-baserad sessionstillhörighet är användbart om du vill behålla en användarsession på samma serverdel. Genom att använda en front dörrs hanterade cookies kan Azure-frontend dirigera efterföljande trafik från en användarsession till samma server del för bearbetning så länge Server delen är felfri och användarsessionen inte har upphört att gälla. 

Sessionstillhörighet kan aktiveras på nivån för klientdelsvärden, dvs. för var och en av dina konfigurerade domäner (eller underdomäner). När funktionen har aktiverats lägger Front Door till en cookie till användarens session. Med cookie-baserad sessionstillhörighet kan Front Door identifiera olika användare även bakom samma IP-adress, vilket i sin tur möjliggör en mer jämn fördelning av trafik mellan dina olika serverdelar.

Livslängden för cookien är samma som användarens session eftersom Front Door för närvarande endast stöder sessions-cookies. 

> [!NOTE]
> Offentliga proxyservrar kan störa tillhörighet mellan sessioner. Detta beror på att när du upprättar en session måste du lägga till en cookie för sessions-tillhörighet i svaret, som inte kan utföras om svaret är cacheable eftersom det skulle störa cookies från andra klienter som begär samma resurs. För att skydda mot detta, upprättas **ingen** mappning av sessionen om Server delen skickar ett cacheable-svar vid försöket. Om sessionen redan har etablerats spelar det ingen roll om svaret från Server delen är cacheable.
> Sessions-tillhörighet upprättas i följande fall, **om inte** svaret har en status kod för http 304:
> - Svaret har angivna värden som har angetts för ```Cache-Control``` rubriken som förhindrar cachelagring, t. ex. "privat" eller "ingen-butik".
> - Svaret innehåller ett ```Authorization``` huvud som inte har upphört att gälla.
> - Svaret har en status kod för HTTP 302.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).

---
title: Kontrollpunkts- och återställningskoncept för återställning i Azure Stream Analytics
description: I den här artikeln beskrivs begrepp för återställning av kontrollpunkter och uppspelning av jobb i Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f5bb2b97d7da770828c2f4f03167483ad2044c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426392"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Kontrollpunkts- och reprisbegrepp i Azure Stream Analytics-jobb
I den här artikeln beskrivs de interna kontrollpunkts- och reprisbegreppen i Azure Stream Analytics och hur de påverkar jobbåterställning. Varje gång ett Stream Analytics-jobb körs underhålls tillståndsinformation internt. Tillståndsinformationen sparas med jämna mellanrum i en kontrollpunkt. I vissa fall används kontrollpunktsinformationen för jobbåterställning om ett jobbfel eller en uppgradering inträffar. Under andra omständigheter kan kontrollpunkten inte användas för återställning och en repris är nödvändig.

## <a name="stateful-query-logicin-temporal-elements"></a>Tillståndskänslig frågelogik i temporala element
En av de unika funktionerna i Azure Stream Analytics-jobbet är att utföra tillståndskänslig bearbetning, till exempel fönsterade aggregat, temporala kopplingar och temporala analytiska funktioner. Var och en av dessa operatörer behåller tillståndsinformation när jobbet körs.Den maximala fönsterstorleken för dessa frågeelement är sju dagar. 

Temporal fönsterkonceptet visas i flera Stream Analytics-frågeelement:
1. Fönsterade aggregat (GROUP BY av Tumbling, Hopping och Sliding fönster)

2. Temporala kopplingar (JOIN med DATEDIFF)

3. Temporala analytiska funktioner (ISFIRST, LAST och LAG MED LIMIT DURATION)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Jobbåterställning från nodfel, inklusive os-uppgradering
Varje gång ett Stream Analytics-jobb körs skalas det internt ut för att arbeta över flera arbetsnoder. Varje arbetsnods tillstånd styrs med några minuters mellanrum, vilket hjälper systemet att återställas om ett fel inträffar.

Ibland kan en viss arbetsnod misslyckas eller så kan en uppgradering av operativsystemet ske för den arbetsnoden. Om du vill återställa automatiskt hämtar Stream Analytics en ny felfri nod och den tidigare arbetarnodens tillstånd återställs från den senaste tillgängliga kontrollpunkten. För att återuppta arbetet krävs en liten mängd repris för att återställa tillståndet från den tidpunkt då kontrollpunkten tas. Vanligtvis är återställningsgapet bara några minuter. När tillräckligt många strömningsenheter har valts för jobbet ska uppspelningen slutföras snabbt. 

I en helt parallell fråga är den tid det tar att komma ikapp efter ett arbetsnodfel proportionellt mot:

[indatahändelsehastigheten] x [mellanrumslängden] / [antal bearbetningspartitioner]

Om du någonsin observerar betydande bearbetningsfördröjning på grund av nodfel och OS-uppgradering kan du överväga att göra frågan helt parallell och skala jobbet så att den allokerar fler strömningsenheter. Mer information finns i [Skala ett Azure Stream Analytics-jobb för att öka dataflödet](stream-analytics-scale-jobs.md).

Aktuella Stream Analytics visar ingen rapport när den här typen av återställningsprocess pågår.

## <a name="job-recovery-from-a-service-upgrade"></a>Återställning av jobb från en tjänstuppgradering 
Microsoft uppgraderar ibland binärfilerna som kör Stream Analytics-jobben i Azure-tjänsten. Vid dessa tillfällen uppgraderas användarnas jobb som körs till nyare version och jobbet startas om automatiskt. 

För närvarande bevaras inte återställningskontrollpunktsformatet mellan uppgraderingar. Därför måste tillståndet för uppspelningsfrågan återställas helt med hjälp av repristeknik. För att Stream Analytics-jobb ska kunna spela upp exakt samma indata från tidigare är det viktigt att ange bevarandeprincipen för källdata till minst fönsterstorlekarna i frågan. Om du inte gör det kan det leda till felaktiga eller partiella resultat under uppgraderingen av tjänsten, eftersom källdata kanske inte sparas tillräckligt långt tillbaka för att inkludera hela fönsterstorleken.

I allmänhet är mängden repris som behövs proportionell mot fönstrets storlek multiplicerat med den genomsnittliga händelsefrekvensen. För ett jobb med en inmatningshastighet på 1 000 händelser per sekund anses till exempel en fönsterstorlek som är större än en timme ha en stor reprisstorlek. Upp till en timmes data kan behöva bearbetas på för att initiera tillståndet så att det kan ge fullständiga och korrekta resultat, vilket kan orsaka fördröjd utdata (ingen utdata) under en längre period. Frågor utan fönster eller andra temporala `JOIN` `LAG`operatorer, gilla eller , skulle ha noll repris.

## <a name="estimate-replay-catch-up-time"></a>Uppskatta återuppspelningstid
Om du vill uppskatta fördröjningens längd på grund av en tjänstuppgradering kan du följa den här tekniken:

1. Läs in indatahändelsehubben med tillräckligt med data för att täcka den största fönsterstorleken i frågan, med förväntad händelsefrekvens. Händelsernas tidsstämpel bör vara nära väggklockans tid under hela den tidsperioden, som om det är en direktinmatningsfeed. Om du till exempel har ett 3-dagarsfönster i frågan skickar du händelser till Event Hub i tre dagar och fortsätter att skicka händelser. 

2. Starta jobbet med **Nu** som starttid. 

3. Mät tiden mellan starttiden och när den första utdata genereras. Tiden är grov hur mycket fördröjning jobbet skulle medföra under en tjänst uppgradering.

4. Om fördröjningen är för lång försöker du partitionera jobbet och öka antalet SUs, så att belastningen sprids ut till fler noder. Du kan också överväga att minska fönsterstorlekarna i frågan och utföra ytterligare aggregering eller annan tillståndskänslig bearbetning på utdata som produceras av Stream Analytics-jobbet i nedströmsmottagaren (till exempel med hjälp av Azure SQL-databas).

Överväg att köra dubblettjobb i kopplade Azure-regioner för allmän servicestabilitet under uppgradering av verksamhetskritiska jobb. Mer information finns i [Garanti stream analytics-jobbtillförlitlighet under tjänstuppdateringar](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Jobbåterställning från en användare som initierats stoppa och starta
Om du vill redigera frågesyntaxen för ett direktuppspelningsjobb eller justera indata och utdata måste jobbet stoppas för att göra ändringarna och uppgradera jobbdesignen. I sådana fall, när en användare stoppar streamingjobbet och startar det igen, liknar återställningsscenariot tjänstuppgradering. 

Kontrollpunktsdata kan inte användas för en startad jobbstart för användare. Om du vill uppskatta fördröjningen av utdata under en sådan omstart använder du samma procedur som beskrivs i föregående avsnitt och tillämpar liknande begränsning om fördröjningen är för lång.

## <a name="next-steps"></a>Nästa steg
Mer information om tillförlitlighet och skalbarhet finns i följande artiklar:
- [Självstudiekurs: Konfigurera aviseringar för Azure Stream Analytics-jobb](stream-analytics-set-up-alerts.md)
- [Skala ett Azure Stream Analytics-jobb för att öka dataflödet](stream-analytics-scale-jobs.md)
- [Garantera Stream Analytics jobbtillförlitlighet under serviceuppdateringar](stream-analytics-job-reliability.md)

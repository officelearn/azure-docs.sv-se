---
title: Kontrollpunkt och återuppspelning jobbet recovery begrepp i Azure Stream Analytics
description: Den här artikeln beskriver kontrollpunkt och återuppspelning jobbet recovery begrepp i Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 9dcfbd4b5fcc8462c88b16f585424166ecd3d499
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088263"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Kontrollpunkt och återuppspelning begrepp i Azure Stream Analytics-jobb
Den här artikeln beskriver interna kontrollpunkt och återuppspelning begreppen i Azure Stream Analytics och inverkan de har på jobbet återställning. Varje gång bibehålls ett Stream Analytics-jobb körs, information om tillstånd internt. Den statusinformation sparas i en kontrollpunkt med jämna mellanrum. I vissa situationer används kontrollpunkt informationen för återställning av jobbet om ett jobb strömavbrott eller en uppgradering. I andra fall kontrollpunkten kan inte användas för återställning och spela krävs.

## <a name="stateful-query-logicin-temporal-elements"></a>Tillståndskänsliga frågans logik i temporala element
En av den unika funktionen för Azure Stream Analytics-jobb är att utföra tillståndskänsliga bearbetning, till exempel fönsteraggregeringar, temporala kopplingar och temporala analysfunktioner. Var och en av de här operatorerna behåller information om användartillstånd när jobbet körs. Den maximala fönsterstorleken för de här elementen i frågan är sju dagar. 

Begreppet temporalt fönster visas i flera element i Stream Analytics-fråga:
1. Fönsteraggregeringar (grupp av av rullande, hoppar och glidande windows)

2. Den temporala kopplingar (koppla ihop med DATEDIFF)

3. Den temporala analysfunktioner (ISFIRST-, LAST och FÖRDRÖJNING med LIMIT DURATION)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Jobbet återställning från nodfel, inklusive uppgradering av operativsystem
Varje gång ett Stream Analytics-jobb körs skalas internt ut till fungerar över flera worker-noder. Varje arbetsnod tillstånd är med kontrollpunkt några minuters mellanrum, vilket hjälper systemet återställa om ett fel inträffar.

Ibland en viss arbetsnod misslyckas, eller en uppgradering av operativsystemet kan inträffa om den arbetsnoden. Om du vill återställa automatiskt Stream Analytics skaffar en ny felfri nod och den tidigare arbetsnod fungerar igen från den senaste kontrollpunkten. Om du vill återuppta arbetet är en liten mängd repetitionsattacker nödvändigt att återställa från den tidpunkt när den kontrollpunkt. Återställ klyftan är vanligtvis bara några minuter. När tillräckligt med enheter för strömning väljs för jobbet, bör repetitionsattacker slutföras snabbt. 

I en helt parallell fråga är den tid det tar att komma ikapp efter ett nodfel worker proportion till:

[den inkommande takten] x [gap-längd] / [antalet bearbetar partitioner]

Om du någonsin se betydande bearbetningsfördröjning på grund av nodfel och OS uppgradera kan du överväga att göra frågan fullständigt parallell och skala jobb för att allokera fler Strömningsenheter. Mer information finns i [skala Azure Stream Analytics-jobb för att öka dataflödet](stream-analytics-scale-jobs.md).

Den aktuella Stream Analytics visar inte en rapport när den här typen av återställningen sker.

## <a name="job-recovery-from-a-service-upgrade"></a>Jobbet återställning från en uppgradering 
Microsoft uppgraderar ibland binärfiler som kör Stream Analytics-jobb i Azure-tjänsten. Vid följande tillfällen användarnas körningsjobb uppgraderas till nyare version och jobbet startas om automatiskt. 

För närvarande kan bevaras återställning kontrollpunkt formatet inte mellan uppgraderingar. Därför kan måste tillståndet för strömmande frågan återställas helt och hållet med repetitionsattacker tekniken. För att tillåta Stream Analytics-jobb att spela upp exakt samma indata från innan det är viktigt att ange bevarandeprincip för källdata till minst fönstret storlekar i frågan. Om du inte gör det kan resultera i felaktig eller partiella resultat under tjänsteuppgraderingen, eftersom datakällan inte kan behållas tillräckligt långt tillbaka för att inkludera den fullständiga fönsterstorleken.

I allmänhet är mängden repetitionsattacker behövs proportion till storleken på fönstret multiplicerat med genomsnittlig händelsefrekvens. Till exempel för ett jobb med en hastighet som inkommande 1000 händelser per sekund, anses en fönsterstorlek som är större än en timme ha en stor repetitionsattacker storlek. Upp till en timme av data kan behöva vara bearbetas på nytt att initiera tillståndet så att den kan ge fullständig och korrekt resultat, som kan orsaka fördröjd utdata (inga utdata) under en längre period. Frågor med inga windows- eller andra temporala operatorer som `JOIN` eller `LAG`, skulle ha noll repetitionsattacker.

## <a name="estimate-replay-catch-up-time"></a>Uppskattningen repetitionsattacker catch-up tid
Om du vill beräkna hur lång fördröjning på grund av en uppgradering måste följa du den här tekniken:

1. Läsa in indata Händelsehubben med tillräckligt med data för att täcka den största fönsterstorleken i din fråga med förväntad händelse hastighet. De händelser tidsstämpeln ska vara nära klocktid under hela den tidsperioden som om det är en live-indata feed. Till exempel om du har en 3-dagarsfönster i frågan, skicka händelser till Event Hub i tre dagar och fortsätter att skicka händelser. 

2. Starta jobbet med **nu** som starttid. 

3. Mät tiden mellan start- och när den första utdatan genereras. Tiden är grov hur stor fördröjning som jobbet skulle medföra vid en uppgradering av tjänsten.

4. Om fördröjningen är för lång, försök att partitionera arbetet och öka antalet SUs, så att belastningen sprids ut till fler noder. Du kan också du överväga att minska fönsterstorlek i din fråga och utföra ytterligare aggregering eller andra tillståndskänslig bearbetning på de utdata som genereras av Stream Analytics-jobb i den underordnade mottagaren (till exempel med hjälp av Azure SQL-databas).

Allmänna stabilitet problem under uppgradering av verksamhetskritiska kritiska jobb du överväga att duplicerade jobb som körs i parade Azure-regioner. Mer information finns i [garanti Stream Analytics-jobbet tillförlitlighet under uppdateringar av tjänsten](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Jobbet återställning från en användarinitierad stoppa och starta
Så här redigerar du frågesyntaxen på en strömningsuppgift eller för att justera indata och utdata, måste jobbet stoppas för att göra ändringar och uppgradera jobbet design. I sådana situationer när en användare stänger direktuppspelningsjobbet och startar den igen, liknar i ett scenario tjänsteuppgraderingen. 

Kontrollpunktsdata kan inte användas för användarinitierade jobb startas om. Om du vill beräkna fördröjning på utdata under sådana en omstart måste använda samma procedur som beskrivs i föregående avsnitt och tillämpa liknande lösning om fördröjningen är för lång.

## <a name="next-steps"></a>Nästa steg
Mer information om tillförlitlighet och skalbarhet, finns i följande artiklar:
- [Självstudie: Konfigurera aviseringar för Azure Stream Analytics-jobb](stream-analytics-set-up-alerts.md)
- [Skala Azure Stream Analytics-jobb för att öka dataflödet](stream-analytics-scale-jobs.md)
- [Garanterar tillförlitlighet för Stream Analytics-jobb under uppdateringar av tjänsten](stream-analytics-job-reliability.md)

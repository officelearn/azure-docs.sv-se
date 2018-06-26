---
title: Kontrollpunkts- och replay-jobbet recovery koncept i Azure Stream Analytics
description: Den här artikeln beskriver kontrollpunkts- och replay-jobbet recovery koncept i Azure Stream Analytics.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: 32970ff37d202cc73e7ab7aa1bf3d737dae895c1
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936725"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Kontrollpunkts- och replay koncept i Azure Stream Analytics-jobb
Den här artikeln beskriver interna kontrollpunkts- och replay begreppen i Azure Stream Analytics och inverkan de har på jobbet återställning. Varje gång upprätthålls ett Stream Analytics-jobb körs statusinformation internt. Den statusinformation sparas i en kontrollpunkt med jämna mellanrum. I vissa scenarier används kontrollpunkt informationen för återställning av jobbet om ett jobb strömavbrott eller en uppgradering. Kontrollpunkten kan inte användas för återställning under andra omständigheter och spela krävs.

## <a name="stateful-query-logic-in-temporal-elements"></a>Tillståndskänsliga frågan logiken i den temporala element
En unik möjligheterna för Azure Stream Analytics-jobbet är att genomföra tillståndskänslig bearbetning, till exempel fönsteraggregeringar, temporala kopplingar och temporal analysfunktioner. Var och en av de här operatorerna behåller statusinformation när jobbet körs. Den största fönsterstorleken för de här elementen i frågan är sju dagar. 

Begreppet temporalt fönster visas i flera Stream Analytics query element:
1. Fönsteraggregeringar (grupp av av rullande, Hopping och glidande windows)

2. Tidsbestämd kopplingar (koppla ihop med DATEDIFF)

3. Tidsbestämd analysfunktioner (ISFIRST-, LAST och FÖRDRÖJNING med LIMIT DURATION)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Jobbet återställning från nodfel, inklusive OS-uppgradering
Varje gång ett Stream Analytics-jobb körs skalas internt ut till fungerar över flera worker-noder. Varje arbetsnod tillstånd är kontrollpunkt några minuters mellanrum, vilket hjälper till att systemet återställa om ett fel inträffar.

Ibland kan en viss arbetsnod kan misslyckas eller en uppgradering av operativsystemet kan bero på att arbetsnoden. Om du vill återställa automatiskt Stream Analytics skaffar en ny felfri nod och tidigare worker nodens tillstånd återställs från den senaste kontrollpunkten. Om du vill återuppta arbetet, är en liten mängd replay nödvändigt att återställa tillståndet från den tidpunkt då kontrollpunkten tas. Återställ mellanrummet är vanligtvis bara några minuter. När tillräckligt med enheter för strömning väljs för jobbet utföras på replay snabbt. 

Den tid det tar att komma ikapp efter ett nodfel arbetare i en fullständigt parallella fråga är proportionell till:

[inkommande händelsetakten] x [lucka längd] / [antalet bearbetning partitioner]

Om du någonsin se betydande bearbetning fördröjning på grund av nodfel och OS uppgraderar du överväga att göra frågan fullständigt parallell och skala jobbet för att allokera fler enheter för strömning. Mer information finns i [skala Azure Stream Analytics-jobbet om du vill öka genomflödet](stream-analytics-scale-jobs.md).

Den aktuella Stream Analytics visar inte en rapport när den här typen av återställningen sker.

## <a name="job-recovery-from-a-service-upgrade"></a>Jobbet återställning från en tjänsteuppgradering pågår 
Microsoft uppgraderar ibland binärfilerna som kör Stream Analytics-jobb i Azure-tjänsten. Dessa tider användarnas jobb som körs har uppgraderats till en nyare version och jobbet startas om automatiskt. 

För närvarande kan bevaras återställning kontrollpunkt format inte mellan uppgraderingar. Tillståndet för strömmande frågan måste därför återställas helt med replay-teknik. För att tillåta Stream Analytics-jobb spela exakt samma indata från innan det är viktigt att ange bevarandeprincip för källdata till minst fönstret storlek i frågan. Om du inte gör det kan resultera i felaktig eller partiell resultat under tjänsteuppgraderingen eftersom datakällan inte bevaras kanske tillräckligt långt tillbaka om du vill inkludera fullständig fönsterstorleken.

I allmänhet är mängden replay behövs proportion till storleken på fönstret multiplicerat med genomsnittlig händelsefrekvens. Exempel för ett projekt med en inkommande frekvens av 1000 händelser per sekund, anses en fönsterstorlek som är större än en timme ha en stor replay-storlek. Upp till en timme av data kan behöva vara bearbetas på nytt initiera tillståndet så att den kan ge fullständig och korrekt resultat, vilket kan orsaka fördröjd utdata (inga utdata) under en längre tid. Frågor med ingen windows eller andra temporala operatorer som `JOIN` eller `LAG`, skulle ha noll repetitionsattacker.

## <a name="estimate-replay-catch-up-time"></a>Uppskattningen replay catch-up tid
Du kan följa den här tekniken för att beräkna hur lång fördröjningen på grund av en Serviceuppgradering av:

1. Läsa in inkommande Händelsehubben med tillräckliga data så att den täcker den största fönsterstorleken i frågan, med förväntad händelsehastighet. De händelser tidsstämpel ska vara nära klocktid under den tidsperioden som om det är en levande inmatning feed. Till exempel om du har ett 3-dagars fönster i frågan, skicka händelser till Händelsehubben tre dagar och fortsätter att skicka händelser. 

2. Starta jobb med hjälp av **nu** som starttid. 

3. Mät tiden mellan starttid och när den första utdatan skapas. Tiden är grov hur mycket fördröjning jobbet skulle innebära under en uppgradering av tjänsten.

4. Om fördröjningen är för långt, försök att partitionera jobbet och öka antalet SUs, så att belastningen sprids ut till fler noder. Du kan också Överväg att minska fönsterstorlek i frågan och utföra ytterligare aggregation eller andra stateful bearbetning på utdata som genereras av Stream Analytics-jobbet i underordnade sink (till exempel med hjälp av Azure SQL database).

Överväg att duplicerade jobb som körs i parad Azure-regioner för allmänna stabiliteten bekymmer under uppgradering av uppdrag kritiska jobb. Mer information finns i [garanti Stream Analytics-jobbet tillförlitlighet under tjänstuppdateringar](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Jobbet återställning från en användarinitierad stoppa och starta
Redigera frågesyntaxen på ett direktuppspelningsjobb eller justera indata och utdata måste jobbet stoppas för att göra ändringar och uppgradera jobbet design. I sådana situationer när en användare direktuppspelningsjobbet stoppar och startar den igen, liknar återställningsscenario tjänstuppgraderingen. 

Kontrollpunktsdata kan inte användas för användarinitierad jobbet startas om. Om du vill beräkna fördröjning av utdata under sådana omstarten, använder du samma procedur som beskrivs i föregående avsnitt och tillämpa liknande lösning om fördröjningen är för långt.

## <a name="next-steps"></a>Nästa steg
Mer information om tillförlitlighet och skalbarhet finns i följande artiklar:
- [Självstudier: Konfigurera aviseringar för Azure Stream Analytics-jobb](stream-analytics-set-up-alerts.md)
- [Skala Azure Stream Analytics-jobbet för att öka genomströmning](stream-analytics-scale-jobs.md)
- [Garantera Stream Analytics-jobbet tillförlitlighet under uppdateringar av tjänsten](stream-analytics-job-reliability.md)

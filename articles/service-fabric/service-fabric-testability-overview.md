---
title: "Översikt över fel Analysis Service | Microsoft Docs"
description: "Den här artikeln beskriver fel Analysis Service i Service Fabric för att fel och kör testscenarier mot dina tjänster."
services: service-fabric
documentationcenter: .net
author: anmolah
manager: timlt
editor: vturecek
ms.assetid: 1f064276-293a-4989-a513-e0d0b9fdf703
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: f275fa5d3d6d727b016e55c188321d7e68091a33
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="introduction-to-the-fault-analysis-service"></a>Introduktion till fel Analysis Service
Fel Analysis Service är utformad för att testa tjänster som bygger på Microsoft Azure Service Fabric. Med fel Analysis Service kan du framkalla meningsfulla fel och kör scenarier för testning mot dina program. Dessa fel och scenarier utöva och validera ett stort antal tillstånd och övergångar som en tjänst får under dess livslängd i en kontrollerad, säker och konsekvent sätt.

Åtgärder är enskilda fel inriktning på en tjänst för att testa den. En service-utvecklare kan använda dessa som byggblock för att skriva komplicerade scenarier. Exempel:

* Starta om en nod för att simulera valfritt antal situationer där en dator eller virtuell dator startas.
* Flytta en replik av tillståndskänslig tjänsten att simulera belastningsutjämning med växling vid fel eller uppgradering av programmet.
* Anropa förlorar kvorum för en tillståndskänslig tjänst för att skapa en situation där skrivåtgärder inte kan fortsätta eftersom det inte finns tillräckligt med ”säkerhetskopiering” eller ”sekundär” repliker att acceptera nya data.
* Anropa förlust av data på en tillståndskänslig tjänsten för att skapa en situation där alla InMemory-tillstånd rensas helt ut.

Scenarier är komplexa åtgärder som består av en eller flera åtgärder. Fel Analysis Services innehåller två inbyggda kompletta scenarier:

* Chaos Scenario
* Scenario för växling vid fel

## <a name="testing-as-a-service"></a>Testa som en tjänst
Fel Analysis Service är en tjänst för Service Fabric system som startas automatiskt med ett Service Fabric-kluster. Den här tjänsten fungerar som värd för fel injection och scenario testkörning hälsoanalys. 

![Fel Analysis Service][0]

När ett fel åtgärd eller test-scenario initieras, skickas ett kommando till fel Analysis Service att köra fel åtgärd eller testa scenariot. Fel Analysis Service är tillståndskänslig så att den kan på ett tillförlitligt sätt kör fel och scenarier och validera resultat. Exempelvis kan en tidskrävande Testscenario köras på ett tillförlitligt sätt av fel Analysis Services. Och eftersom tester körs i klustret, tjänsten kan undersöka statusen för klustret och dina tjänster för att ge mer information om misslyckade.

## <a name="testing-distributed-systems"></a>Testa distribuerade system
Service Fabric gör jobbet med skriva och hantera distribuerade skalbara program betydligt enklare. Fel Analysis Service gör att testningen ett distribuerat program på samma sätt enklare. Det finns tre huvudsakliga problem som behöver lösas vid testning:

1. Simulera/genererar fel som kan uppstå i verkliga scenarier: en för viktiga aspekter av Service Fabric är att den aktiverar distribuerade program efter olika fel. Om du vill testa att programmet är möjligt att återställa från dessa fel, måste dock en mekanism för att simulera/generera dessa verkliga fel i en kontrollerad testmiljö.
2. Möjligheten att generera korrelerade fel: grundläggande fel i systemet, till exempel nätverksfel och fel på datorn, är enkla att skapa individuellt. Genererar ett stort antal scenarier som kan inträffa i verkligheten på grund av interaktion mellan dessa enskilda fel är icke-trivial.
3. Enhetlig miljö på olika nivåer av utveckling och distribution: det finns många fel injection system som kan utföra olika typer av fel. Upplevelse i alla dessa är dålig när du flyttar från en utvecklare scenarier, till samma tester som körs i stor testmiljöer, till att använda dem för test i produktion.

Det finns många sätt att lösa dessa problem, ett system som har samma med nödvändiga garantier--ända från en miljö med en ruta utvecklare, testa i produktion kluster – saknas. Fel Analysis Service hjälper programutvecklare koncentrera dig på Testa sina affärslogik. Fel Analysis Services innehåller alla funktioner som behövs för att testa om samverkan mellan tjänsten med det underliggande distribuerade systemet.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Simulera/genererar verkliga scenarier
Vi behöver en mekanism för att generera fel om du vill testa stabilitet i ett distribuerat system mot fel. I teorin startar genererar ett fel som en nod nedåt verkar enkelt, träffa samma uppsättning konsekvenskontroll problem som du försöker lösa Service Fabric. Som exempel, om vi vill stänga av en nod, är krävs arbetsflödet följande:

1. Skicka en begäran om avstängning noden från klienten.
2. Skicka begäran till rätt nod.
   
    a. Om noden inte hittas, ska misslyckas.
   
    b. Om noden hittas ska den returnera endast om noden är avstängd.

För att verifiera fel från ett test-perspektiv, behöver testet veta att när det här felet framkallas felet faktiskt händer. Säkerheten som Service Fabric ger är att antingen noden ska gå nedåt eller var redan ned när kommandot uppnåtts noden. I båda fallen ska testet kunna korrekt skäl om tillstånd och lyckas eller misslyckas korrekt i dess validering. Ett system som genomförs utanför Service Fabric att göra samma uppsättning fel gick nådde många nätverk, maskinvara och programvaruproblem som skulle kunna hindra den från att tillhandahålla de föregående garantierna. Med de problem som anges innan Service Fabric kommer att omkonfigurera klustertillstånd att undvika problem och fel Analysis Service kommer därför fortfarande att kunna ge rätt uppsättning garantier.

### <a name="generating-required-events-and-scenarios"></a>Skapa nödvändiga händelser och scenarier
Simulera ett verkligt fel konsekvent är svåra att börja med, är kan du generera korrelerade fel även strängare. Till exempel sker en förlust av data i en tillståndskänslig beständiga tjänsten när händer följande:

1. Endast en skrivning kvorum repliker fångas replikering. De sekundära replikerna ligga efter den primära servern.
2. Skriv kvorum stängs av på grund av repliker gå (på grund av ett kodpaketet eller en nod gå).
3. Skriv kvorum kan inte gå tillbaka eftersom data för replikerna går förlorad (på grund av skadade disken eller återställning av avbildning datorn).

Dessa korrelerade fel inträffa i verkligheten men inte lika ofta som individuella fel. Möjlighet att testa för dessa scenarier innan de inträffar i produktion är viktigt. Även viktigare är möjligheten att simulera dessa scenarier med produktionsarbetsbelastningar under övervakning (mitt i dag med alla tekniker på däcket). Mycket bättre än att den inträffa för första gången i produktion klockan 02:00

### <a name="unified-experience-across-different-environments"></a>Enhetlig miljö mellan olika miljöer
Den här övningen har traditionellt att skapa tre olika uppsättningar av upplevelser, en för utvecklingsmiljön, en för tester och en för produktion. Modellen har:

1. Generera tillståndsövergångar som gör kontroller av enskilda metoder i utvecklingsmiljön.
2. Generera tillåtna felinloggningar slutpunkt till slutpunkt-tester som utövar olika scenarier i testmiljön.
3. Behåll produktionsmiljön enkla att hindra icke naturliga fel och så att det är mycket snabbt mänsklig svar på fel.

I Service Fabric via tjänsten fel analys avser vi att stänga av den här och använda samma metod från utvecklare miljö till produktionen. Det finns två sätt att åstadkomma detta:

1. Använd den fel Analysis Service API: er från en miljö med en ruta ända till driftskluster för att framkalla kontrollerade fel.
2. Om du vill ge klustret en skall som orsakar automatisk induktion fel, Använd fel Analysis Service för att generera automatisk fel. Styra frekvensen för fel med hjälp av konfiguration gör det möjligt för samma tjänst som ska testas på olika sätt i olika miljöer.

Med Service Fabric trots att skala fel skulle vara annorlunda i olika miljöer skulle faktiska mekanismer vara identiska. Detta ger en mycket snabbare kod till distribution pipeline och möjligheten att testa tjänster under verkliga belastning.

## <a name="using-the-fault-analysis-service"></a>Med hjälp av tjänsten fel analys
**C#**

Fel Analysis Services-funktioner finns i namnområdet System.Fabric i Microsoft.ServiceFabric NuGet-paketet. Om du vill använda fel Analysis Services-funktioner, med nuget-paketet som en referens i projektet.

**PowerShell**

Om du vill använda PowerShell, måste du installera Service Fabric-SDK. När du har installerat SDK är ServiceFabric PowerShell-modulen auto inlästa som du kan använda.

## <a name="next-steps"></a>Nästa steg
Om du vill skapa verkligen molnskala tjänster, är det viktigt att säkerställa, både före och efter distributionen services klarar verkligt fel. I världen services idag är möjlighet att förnya snabbt och snabbt flytta koden till produktionen mycket viktigt. Fel Analysis Service hjälper tjänstutvecklare exakt det.

Börja testa dina program och tjänster med hjälp av inbyggt [testa scenarier](service-fabric-testability-scenarios.md), eller skapa egna testscenarier med hjälp av den [fault åtgärder](service-fabric-testability-actions.md) tillhandahålls av fel Analysis Services.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png

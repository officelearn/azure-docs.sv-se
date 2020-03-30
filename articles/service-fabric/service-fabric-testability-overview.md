---
title: Översikt över tjänsten För felanalys
description: I den här artikeln beskrivs felanalystjänsten i Service Fabric för att inducera fel och köra testscenarier mot dina tjänster.
author: anmolah
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: d5c770a4d823ebe9b2700b081c407c54dd1d18a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465566"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Introduktion till felanalystjänsten
Felanalystjänsten är utformad för att testa tjänster som bygger på Microsoft Azure Service Fabric. Med felanalystjänsten kan du framkalla meningsfulla fel och köra fullständiga testscenarier mot dina program. Dessa fel och scenarier övar och validerar de många tillstånd och övergångar som en tjänst kommer att uppleva under hela sin livstid, allt på ett kontrollerat, säkert och konsekvent sätt.

Åtgärder är de enskilda fel som riktar sig till en tjänst för att testa den. En tjänstutvecklare kan använda dessa som byggstenar för att skriva komplicerade scenarier. Ett exempel:

* Starta om en nod för att simulera valfritt antal situationer där en dator eller virtuell dator startas om.
* Flytta en replik av din tillståndskänsliga tjänst för att simulera belastningsutjämning, redundans eller programuppgradering.
* Anropa kvorumförlust på en tillståndskänslig tjänst för att skapa en situation där skrivåtgärder inte kan fortsätta eftersom det inte finns tillräckligt med "back-up" eller "sekundära" repliker för att acceptera nya data.
* Anropa dataförlust på en tillståndskänslig tjänst för att skapa en situation där alla minnestillstånd är helt utraderade.

Scenarier är komplexa åtgärder som består av en eller flera åtgärder. Felanalystjänsten innehåller två inbyggda kompletta scenarier:

* Kaos Scenario
* Redundansscenario

## <a name="testing-as-a-service"></a>Testa som en tjänst
Felanalystjänsten är en servicetjänst för Service Fabric-system som startas automatiskt med ett Service Fabric-kluster. Den här tjänsten fungerar som värd för felinjektion, körning av testscenario och hälsoanalys. 

![Tjänsten för felanalys][0]

När en felåtgärd eller ett testscenario initieras skickas ett kommando till felanalystjänsten för att köra felåtgärden eller testscenariot. Felanalystjänsten är tillståndskänslig så att den på ett tillförlitligt sätt kan köra fel och scenarier och validera resultat. Ett tidskrävande testscenario kan till exempel utföras på ett tillförlitligt sätt av felanalystjänsten. Och eftersom tester körs i klustret kan tjänsten undersöka klustrets tillstånd och dina tjänster för att ge mer djupgående information om fel.

## <a name="testing-distributed-systems"></a>Testa distribuerade system
Service Fabric gör jobbet att skriva och hantera distribuerade skalbara program betydligt enklare. Felanalystjänsten gör det enklare att testa ett distribuerat program. Det finns tre huvudfrågor som måste lösas vid testning:

1. Simulera/generera fel som kan uppstå i verkliga scenarier: En av de viktiga aspekterna av Service Fabric är att det gör det möjligt för distribuerade program att återställa från olika fel. Men för att testa att programmet kan återställa från dessa fel behöver vi en mekanism för att simulera/generera dessa verkliga fel i en kontrollerad testmiljö.
1. Möjligheten att generera korrelerade fel: Grundläggande fel i systemet, till exempel nätverksfel och datorfel, är lätta att producera individuellt. Att generera ett stort antal scenarier som kan hända i den verkliga världen som ett resultat av interaktionerna mellan dessa enskilda fel är icke-trivialt.
1. Enhetlig erfarenhet över olika nivåer av utveckling och distribution: Det finns många felinsprutningssystem som kan göra olika typer av fel. Erfarenheten av alla dessa är dock dålig när du flyttar från en-box utvecklare scenarier, att köra samma tester i stora testmiljöer, att använda dem för tester i produktion.

Även om det finns många mekanismer för att lösa dessa problem, ett system som gör samma sak med nödvändiga garantier - hela vägen från en one-box utvecklare miljö, att testa i produktionskluster - saknas. Felanalystjänsten hjälper programutvecklarna att koncentrera sig på att testa sin affärslogik. Felanalystjänsten tillhandahåller alla funktioner som behövs för att testa interaktionen mellan tjänsten och det underliggande distribuerade systemet.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Simulera/generera verkliga felscenarier
För att testa robustheten i ett distribuerat system mot fel behöver vi en mekanism för att generera fel. Även i teorin, generera ett fel som en nod ner verkar lätt, det börjar slå samma uppsättning konsekvens problem som Service Fabric försöker lösa. Om vi till exempel vill stänga av en nod är det arbetsflöde som krävs följande:

1. Från klienten utfärdar du en begäran om avstängningsnod.
1. Skicka begäran till rätt nod.
   
    a. Om noden inte hittas bör den misslyckas.
   
    b. Om noden hittas bör den bara returneras om noden stängs av.

För att verifiera felet från ett testperspektiv måste testet veta att när felet orsakas inträffar felet faktiskt. Garantin som Service Fabric ger är att antingen noden kommer att gå ner eller var redan nere när kommandot nådde noden. I båda fallen testet bör kunna korrekt resonera om tillståndet och lyckas eller misslyckas korrekt i sin validering. Ett system som implementeras utanför Service Fabric för att göra samma uppsättning fel kan drabba många nätverk, hårdvara och programvara frågor, vilket skulle hindra den från att ge föregående garantier. I närvaro av de problem som angetts tidigare kommer Service Fabric att konfigurera om klustertillståndet för att komma runt problemen, och därför kommer felanalystjänsten fortfarande att kunna ge rätt uppsättning garantier.

### <a name="generating-required-events-and-scenarios"></a>Generera nödvändiga händelser och scenarier
Samtidigt simulera en verklig misslyckande konsekvent är svårt att börja med, förmågan att generera korrelerade misslyckanden är ännu tuffare. En dataförlust inträffar till exempel i en tillståndskänslig tjänst som sparats när följande saker inträffar:

1. Endast ett skrivkvorum för replikerna fångas upp på replikering. Alla sekundära repliker släpar efter den primära.
1. Skrivkvorumet går ner på grund av att replikerna går nedåt (på grund av att ett kodpaket eller nod går ner).
1. Skrivkvorumet kan inte komma upp eftersom data för replikerna går förlorade (på grund av diskskador eller omdimering av datorn).

Dessa korrelerade misslyckanden händer i den verkliga världen, men inte lika ofta som enskilda misslyckanden. Möjligheten att testa för dessa scenarier innan de inträffar i produktionen är avgörande. Ännu viktigare är möjligheten att simulera dessa scenarier med produktionsarbetsbelastningar under kontrollerade omständigheter (mitt på dagen med alla ingenjörer på däck). Det är mycket bättre än att ha det hända för första gången i produktion på 2:00

### <a name="unified-experience-across-different-environments"></a>Enhetlig upplevelse i olika miljöer
Praxis har traditionellt varit att skapa tre olika uppsättningar av erfarenheter, en för utvecklingsmiljön, en för tester, och en för produktion. Modellen var:

1. I utvecklingsmiljön, producera tillståndsövergångar som tillåter enhetstester av enskilda metoder.
1. I testmiljön, producera fel för att tillåta end-to-end-tester som utövar olika felscenarier.
1. Håll produktionsmiljön orörd för att förhindra icke-naturliga fel och för att säkerställa att det finns extremt snabb mänsklig reaktion på misslyckande.

I Service Fabric, genom Felanalystjänsten, föreslår vi att vi vänder på detta och använder samma metodik från utvecklarmiljö till produktion. Det finns två sätt att uppnå detta:

1. Om du vill inducera kontrollerade fel använder du API:erna för felanalystjänsten från en enläcksmiljö hela vägen till produktionskluster.
1. Om du vill ge klustret feber som orsakar automatisk induktion av fel använder du felanalystjänsten för att generera automatiska fel. Genom att kontrollera felfrekvensen genom konfigurationen kan samma tjänst testas på olika sätt i olika miljöer.

Med Service Fabric, även om omfattningen av fel skulle vara annorlunda i de olika miljöerna, skulle de faktiska mekanismerna vara identiska. Detta möjliggör en mycket snabbare pipeline för kod till distribution och möjligheten att testa tjänsterna under verkliga belastningar.

## <a name="using-the-fault-analysis-service"></a>Använda felanalystjänsten
**C#**

Felanalystjänstens funktioner finns i namnområdet System.Fabric i Microsoft.ServiceFabric NuGet-paketet. Om du vill använda funktionerna för felanalystjänsten inkluderar du nugetpaketet som referens i projektet.

**Powershell**

Om du vill använda PowerShell måste du installera Service Fabric SDK. När SDK har installerats läses Modulen ServiceFabric PowerShell automatiskt in så att du kan använda den.

## <a name="next-steps"></a>Nästa steg
För att skapa verkligt molnbaserade tjänster är det viktigt att se till att tjänster kan motstå verkliga fel, både före och efter distributionen. I tjänstevärlden idag är förmågan att förnya sig snabbt och snabbt flytta kod till produktion. Felanalystjänsten hjälper tjänstutvecklare att göra just detta.

Börja testa dina program och tjänster med hjälp av de inbyggda [testscenarierna](service-fabric-testability-scenarios.md)eller skapa egna testscenarier med hjälp av [felåtgärderna](service-fabric-testability-actions.md) som tillhandahålls av felanalystjänsten.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png

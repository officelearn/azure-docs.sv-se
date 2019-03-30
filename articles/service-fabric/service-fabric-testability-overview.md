---
title: Översikt över Fault Analysis Service | Microsoft Docs
description: Den här artikeln beskriver Fault Analysis Service i Service Fabric för att fel och körning av test-scenarier mot dina tjänster.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: chackdan
editor: vturecek
ms.assetid: 1f064276-293a-4989-a513-e0d0b9fdf703
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: 3581550779b2387515b4f300d211b4e0a894edc7
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662361"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Introduktion till Fault Analysis Service
Fault Analysis Service har utformats för att testa tjänster som bygger på Microsoft Azure Service Fabric. Med Fault Analysis Service du medföra meningsfulla fel och köra fullständig testscenarier mot dina program. Dessa fel och scenarier utöva och validera ett stort antal tillstånd och övergångar som en tjänst får under hela dess livslängd i en kontrollerad, säker och konsekvent sätt.

Åtgärder är enskilda fel som riktar in sig på en tjänst för att testa den. En tjänst-utvecklare kan använda dem som byggblock för att skriva komplicerade scenarier. Exempel:

* Starta om en nod för att simulera valfritt antal situationer där en dator eller virtuell dator startas om.
* Flytta en replik för tillståndskänsliga tjänsten att simulera Utjämning av nätverksbelastning, växling vid fel eller uppgradering av programmet.
* Anropa förlorar kvorum på en tillståndskänslig tjänst för att skapa en situation där skrivåtgärder inte kan fortsätta eftersom det inte finns tillräckligt med ”säkerhetskopiering” eller ”sekundär” repliker att acceptera nya data.
* Anropa dataförlust i en tillståndskänslig tjänst för att skapa en situation där alla InMemory-tillstånd helt rensar.

Scenarier är komplexa åtgärder som består av en eller flera åtgärder. Fault Analysis Service innehåller två inbyggda kompletta scenarier:

* Chaos Scenario
* Redundansscenario

## <a name="testing-as-a-service"></a>Testning som en tjänst
Fault Analysis Service är en Service Fabric-systemtjänst som startas automatiskt med Service Fabric-kluster. Den här tjänsten fungerar som värd för felinmatning, scenario testkörning och hälsoanalys. 

![Fault Analysis Service][0]

När ett fel åtgärd eller test-scenario initieras, skickas ett kommando till Fault Analysis Service att köra fel åtgärd eller testa scenariot. Fault Analysis Service är tillståndskänslig så att den kan tillförlitligt köra fel och scenarier och verifiera resultaten. Exempelvis kan en tidskrävande Testscenario utföras på ett tillförlitligt sätt av Fault Analysis Service. Och eftersom tester som körs i klustret, tjänsten kan granska status för klustret och dina tjänster att tillhandahålla mer detaljerad information om misslyckade.

## <a name="testing-distributed-systems"></a>Testa distribuerade system
Service Fabric gör jobbet med skriva och hantera distribuerade skalbara program som är betydligt enklare. Fault Analysis Service gör att du testar ett distribuerat program på samma sätt enklare. Det finns tre huvudsakliga problem som behöver lösas vid testning:

1. Simulera/genererar fel som kan uppstå i verkliga scenarier: En av de viktiga delarna av Service Fabric är att den kan distribuerade program efter olika fel. Om du vill testa att programmet ska kunna återställa från de här felen, måste vi dock en mekanism för att simulera/generera dessa verkliga fel i en kontrollerad testmiljö.
1. Du kan generera korrelerade fel: Grundläggande fel i systemet, till exempel nätverksfel och datorn fel är enkla att skapa individuellt. Genererar ett stort antal scenarier som kan ske i verkligheten till följd av interaktion mellan dessa enskilda fel är icke-trivialt.
1. Enhetlig upplevelse över olika nivåer av utveckling och distribution: Det finns många fault injection system som kan utföra olika typer av fel. Upplevelse i alla dessa är dålig när du flyttar från en utvecklare scenarier, till samma tester som körs i stora testmiljöer, kan användas för test i produktion.

Det finns många sätt att lösa dessa problem, ett system som gör samma sak med nödvändiga garantier--hela vägen från en miljö med en ruta developer, testa i produktionskluster – saknas. Fault Analysis Service hjälper programutvecklare koncentrera dig på Testa sina affärslogik. Fault Analysis Service innehåller alla funktioner som behövs för att testa interaktionen för tjänsten med det underliggande distribuerade systemet.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Simulera/genererar verkliga scenarier
Vi behöver en mekanism för att generera fel om du vill testa stabilitet för ett distribuerat system mot fel. I teorin startar genererar ett fel som en nod nedåt verkar enkelt, stöter på samma uppsättning konsekvens problem som du försöker lösa Service Fabric. Till exempel om vi vill stänga av en nod, krävs arbetsflödet är följande:

1. Skicka en begäran om avstängning noden från klienten.
1. Skicka begäran till rätt nod.
   
    a. Om det gick inte att hitta noden, bör det inte.
   
    b. Om noden hittas, bör den returnera endast om noden är avstängd.

Testet måste veta att när det här felet framkallas felet faktiskt händer för att kontrollera fel från ett test-perspektiv. Garanti för att Service Fabric tillhandahåller är att antingen noden kommer stängs av eller var redan ned när kommandot har nåtts noden. I båda fallen ska testet kunna korrekt anledning om tillståndet och lyckas eller misslyckas korrekt i dess validering. Ett system som implementerats utanför Service Fabric för att göra samma uppsättning fel gick når många nätverk, maskinvara och programvaruproblem som skulle kunna hindra den från att tillhandahålla de föregående garantierna. När det finns problem anges innan Service Fabric kommer att omkonfigurera klustertillstånd att kringgå problemen och Fault Analysis Service kommer därför fortfarande att kunna ge rätt uppsättning garantier.

### <a name="generating-required-events-and-scenarios"></a>Skapa nödvändiga händelser och scenarier
Simulera ett fel i verkliga konsekvent är svåra att börja med, är möjligheten att generera korrelerade fel även strängare. Till exempel händer en dataförlust i en tillståndskänslig beständiga tjänst när följande händer:

1. Endast ett skrivning kvorum repliker fångas replikering. De sekundära replikerna ligga efter primärt.
1. Skriv kvorum slutar fungera på grund av repliker gå (på grund av en kodpaketet eller en nod slutar fungera).
1. Skriv kvorum kan inte gå tillbaka eftersom data för replikerna förloras (på grund av diskskada eller avbildningen på datorn).

Sker dessa korrelerade fel i den verkliga världen men inte som ofta som enskilda fel. Möjlighet att testa dessa scenarier innan de inträffar i produktion är viktigt. Ännu viktigare är möjligheten att simulera dessa scenarier med produktionsarbetsbelastningar under övervakning (mitt i dag med alla tekniker på presentation). Det är mycket bättre än att det direkt för första gången i produktion för klockan 02:00

### <a name="unified-experience-across-different-environments"></a>Enhetlig upplevelse i olika miljöer
Den här övningen har traditionellt att skapa tre olika uppsättningar av upplevelser, en för utvecklingsmiljön, en för tester och en för produktion. Modellen har:

1. Generera tillståndsövergångar som tillåter enhetstester av enskilda metoder i utvecklingsmiljön.
1. Generera felinloggningar slutpunkt till slutpunkt-tester som utövar olika scenarier i testmiljön.
1. Behåll produktionsmiljön enkla att förhindra att icke-naturligt fel och kontrollera att det finns mycket snabbt mänskliga svar för fel.

I Service Fabric via Fault Analysis Service, vi föreslår att returtid detta och använda samma metod från utvecklarmiljön till produktion. Det finns två sätt att göra detta:

1. Använd Fault Analysis Service API: erna från en-miljö ända till produktionskluster för att framkalla kontrollerad fel.
1. Om du vill ge gruppen ett sina som orsakar automatisk induktion av fel, Använd Fault Analysis Service för att generera automatiska fel. Styra frekvensen för fel med hjälp av konfiguration kan samma tjänst som ska testas på olika sätt i olika miljöer.

Med Service Fabric, trots att skalan för fel skulle vara annorlunda i olika miljöer, skulle de faktiska mekanismerna vara identiska. Detta ger en mycket snabbare kod till distribution pipeline och möjlighet att testa tjänsterna under verkliga belastning.

## <a name="using-the-fault-analysis-service"></a>Med hjälp av Fault Analysis Service
**C#**

Fault Analysis Service-funktioner finns i namnområdet System.Fabric i Microsoft.ServiceFabric NuGet-paketet. Om du vill använda Fault Analysis Service-funktioner måste innehålla nuget-paketet som en referens i projektet.

**PowerShell**

Om du vill använda PowerShell måste du installera Service Fabric SDK. När du har installerat SDK är ServiceFabric PowerShell-modulen auto som lästs in som du kan använda.

## <a name="next-steps"></a>Nästa steg
För att skapa helt skalbar tjänster, är det viktigt att, både före och efter distributionen kan tjänster klarar verkliga fel. Tjänster över hela världen i dag, är möjlighet att snabbt utveckla och snabbt flytta kod till produktion mycket viktigt. Fault Analysis Service hjälper tjänstutvecklare att för exakt detta.

Börja testa dina program och tjänster som använder inbyggt [testscenarion](service-fabric-testability-scenarios.md), eller skapa egna test-scenarier med hjälp av den [felanalyser åtgärder](service-fabric-testability-actions.md) tillhandahålls av Fault Analysis Service.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png

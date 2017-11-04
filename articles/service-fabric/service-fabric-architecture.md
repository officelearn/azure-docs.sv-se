---
title: Arkitekturen i Azure Service Fabric | Microsoft Docs
description: "Service Fabric är en plattform för distribuerade system som används för att skapa skalbara, tillförlitliga och enkelt hanteras program för molnet. Den här artikeln visar arkitekturen för Service Fabric."
services: service-fabric
documentationcenter: .net
author: rishirsinha
manager: timlt
editor: rishirsinha
ms.assetid: 6b554243-70cb-4c22-9b28-1a8b4703f45e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/12/2017
ms.author: rsinha
ms.openlocfilehash: 3d1f9d6136622e0e9fc1e725d8230dbedd6af24a
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
# <a name="service-fabric-architecture"></a>Arkitektur för Service Fabric
Service Fabric bygger med överlappande undersystem. Dessa delsystem kan du skriva program som är:

* Hög tillgänglighet
* Skalbar
* Hanterbara
* Kan testas

Följande diagram visar viktiga undersystem för Service Fabric.

![Diagram över Service Fabric-arkitektur](media/service-fabric-architecture/service-fabric-architecture.png)

Möjligheten att säker kommunikation mellan noder i ett kluster är avgörande för ett distribuerat system. Basen för stacken är delsystemet transport, som tillhandahåller säker kommunikation mellan noder. Undersystemet ligger ovanför transporten undersystemet federation som kluster olika noder i en enda entitet (kallas kluster) så att Service Fabric kan identifiera fel, utföra ledande val och ger konsekvent routning. Undersystemet tillförlitlighet lager ovanpå undersystemet federation ansvarar för tillförlitligheten för Service Fabric-tjänster genom mekanismer som replikering, resurshantering och växling vid fel. Undersystemet federation för också delsystemet värd och aktivering, som hanterar livscykeln för ett program på en enda nod. Undersystem för nätverkshantering hanterar livscykeln för program och tjänster. Undersystemet datatillgång hjälper programutvecklare testa sina tjänster via simulerade fel före och efter distribution av program och tjänster till produktionsmiljöer. Service Fabric ger möjlighet att lösa tjänstplatserna via dess undersystemet för kommunikation. Programmet programmeringsmodeller exponeras för utvecklare läggs ovanpå dessa delsystem tillsammans med programmodell att aktivera verktygsuppsättning.

## <a name="transport-subsystem"></a>Transport-undersystemet
Transport-undersystemet implementerar en point-to-point datagram kommunikationskanal. Den här kanalen används för kommunikation inom service fabric-kluster och kommunikation mellan service fabric-kluster och klienter. Den stöder enkelriktade och request-reply-kommunikationsmönster som utgör grunden för att implementera broadcast och multicast i lagret för Federation. Transport-undersystemet skyddar kommunikation med hjälp av X509 certifikat eller Windows-säkerhet. Den här undersystemet används internt av Service Fabric och är inte direkt tillgänglig för utvecklare för programmering.

## <a name="federation-subsystem"></a>Undersystemet för Federation
Du måste ha en konsekvent vy av systemet för att kunna skäl om en uppsättning noder i ett distribuerat system. Undersystemet federation använder de kommunikation primitiver som tillhandahålls av undersystemet transport och häftar samman olika noder i ett enda enhetlig kluster som den kan skäl om. Det ger de distribuerade system primitiver som krävs av andra delsystem - felidentifiering ledande val och konsekvent routning. Undersystemet federation bygger på distribuerade hash-tabeller med en 128-bitars token utrymme. Undersystemet skapar en ringtopologi över noder, med varje nod i ringen som har tilldelats en delmängd av token utrymme för ägarskap. För att upptäcka fel används en leasingmekanism baserat på hjärta beating och skiljedom. Undersystemet federation garanterar även via invecklade koppling och avvikelse protokoll som en enda ägare av en token finns när som helst. Detta ger ledande val och konsekvent routning garantier.

## <a name="reliability-subsystem"></a>Tillförlitlighet undersystemet
Undersystemet tillförlitlighet tillhandahåller en mekanism för att kontrollera tillståndet för en tjänst för Service Fabric hög tillgänglighet med den *replikatorn*, *Failover Manager*, och *resurs Belastningsutjämnaren*.

* Replikatorn säkerställer att tillståndsändringar i replikeringen primära tjänsten automatiskt ska replikeras till sekundära repliker underhålla konsekvensen mellan primära och sekundära repliker i en replikuppsättning för tjänsten. Replikatorn är ansvarig för kvorumhantering av repliker i uppsättningen. Den samverkar med redundansenhet att hämta listan över åtgärder för att replikera och omkonfiguration agenten ger den konfigurationen av replikuppsättningen. Denna konfiguration anger vilka åtgärder som måste replikeras repliker. Service Fabric tillhandahåller en standard replikatorn kallas Fabric replikatorn som kan användas av Programmeringsgränssnitt för modellen för att kontrollera tjänstens tillstånd hög tillgänglighet och tillförlitlig.
* Failover Manager säkerställer att om noder läggs till i eller tas bort från klustret, omdistribueras belastningen automatiskt över tillgängliga noder. Om en nod i klustret inte klustret automatiskt att konfigurera tjänsten replikerna för att upprätthålla tillgänglighet.
* Resurshanteraren placerar service repliker över fel domän i klustret och ser till att alla redundansenheter är fungerar. Resurshanteraren balanserar också serviceresurser i den underliggande delade poolen med klusternoder för att uppnå optimal uniform belastningsdistribution.

## <a name="management-subsystem"></a>Undersystem för nätverkshantering
Undersystemet management ger slutpunkt till slutpunkt-tjänsten och Livscykelhantering för programmet. PowerShell-cmdlets och administrativa API: er kan du etablera, distribuera, uppdatera, uppgradera och avetablera program utan förlust av tillgänglighet. Undersystem för nätverkshantering utför via följande tjänster.

* **Klusterhanterare**: Detta är den primära tjänsten som interagerar med Failover Manager från tillförlitlighet att placera programmen på noder baserat på placeringen för tjänsten. Hanteraren för filserverresurser i redundans undersystemet säkerställer att begränsningarna aldrig är bruten. Klusterhanteraren hanterar livscykeln för programmen från etableras på avetablera. Den kan integreras med hälsoindikatorn så att programmets tillgänglighet inte är förlorade ur semantiska hälsa under uppgraderingar.
* **Hälsoindikatorn**: den här tjänsten kan hälsoövervakning av program, tjänster och klustret entiteter. Kluster-enheter (till exempel noder, partitioner för tjänsten och repliker) kan rapportera hälsoinformation sedan samman i arkivet för centraliserad hälsa. Den här hälsoinformation ger en övergripande i tidpunkt hälsa ögonblicksbild av tjänster och noder som distribueras över flera noder i klustret, så att du kan vidta eventuella nödvändiga åtgärder. Hälsotillstånd frågan API: er kan du fråga händelserna hälsotillstånd rapporteras till undersystemet hälsa. Hälsotillstånd frågan API: er returnera rådata hälsa data som lagras i health store eller aggregerad, tolkas hälsa data för ett specifikt kluster-enheten.
* **Image Store**: den här tjänsten tillhandahåller lagring och distribution av binärfilerna för programmet. Den här tjänsten tillhandahåller en enkel distribuerade filarkivet där programmen har överförts till och hämtas från.

## <a name="hosting-subsystem"></a>Värd för undersystemet
Klusterhanteraren informerar värd undersystemet (som körs på varje nod) vilka tjänster som behövs för att hantera för en viss nod. Undersystemet värd sedan hanterar livscykeln för programmet på noden. Den kommunicerar med tillförlitlighet komponenterna så att replikerna är på rätt plats och att de är felfria.

## <a name="communication-subsystem"></a>Undersystemet för kommunikation
Den här undersystemet erbjuder tillförlitlig meddelandehantering inom klustret och service discovery via Naming service. Tjänsten Naming matchar tjänstnamn till en plats i klustret och gör att användarna kan hantera tjänstnamn och egenskaper. Med tjänsten Naming kommunicera klienter på ett säkert sätt med en nod i klustret för att lösa ett tjänstnamn och hämta service metadata. Med en enkel Naming klient API kan utveckla användare av Service Fabric tjänster och klienter som kan lösa klientens aktuella nätverksplats trots nod dynamik eller nytt storlek i klustret.

## <a name="testability-subsystem"></a>Möjlighet att testa undersystemet
Möjlighet att testa är en uppsättning med verktyg som utformats för att testa tjänster som bygger på Service Fabric. Verktygen kan utvecklare enkelt medföra meningsfulla fel och kör testscenarier att utöva och validera ett stort antal tillstånd och övergångar som en tjänst får under dess livslängd i en kontrollerad och säkert sätt. Möjlighet att testa innehåller också en mekanism för att köra längre tester som kan iterera via olika möjliga fel utan att förlora tillgänglighet. Detta ger dig testa i produktionsmiljö.


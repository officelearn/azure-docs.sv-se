---
title: Arkitekturen i Azure Service Fabric | Microsoft Docs
description: Service Fabric är en distribuerad systemplattform som används för att skapa skalbara, tillförlitliga och enkelt hanterade program för molnet. Den här artikeln visar arkitekturen för Service Fabric.
services: service-fabric
documentationcenter: .net
author: rishirsinha
manager: chackdan
editor: rishirsinha
ms.assetid: 6b554243-70cb-4c22-9b28-1a8b4703f45e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/12/2017
ms.author: rsinha
ms.openlocfilehash: a1e68e2e39ea6f1c8cf8669e2e02d8dacaf0f284
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097852"
---
# <a name="service-fabric-architecture"></a>Arkitektur för Service Fabric
Service Fabric har byggts med överlappande undersystem. Dessa delsystem kan du skriva program som är:

* Med hög tillgänglighet
* Skalbar
* Hanterbara
* Kan testas

Följande diagram visar viktiga undersystem för Service Fabric.

![Diagram över Service Fabric-arkitektur](media/service-fabric-architecture/service-fabric-architecture.png)

I ett distribuerat system är möjlighet att kommunicera på ett säkert sätt mellan noder i ett kluster avgörande. Är delsystemet transport, som tillhandahåller säker kommunikation mellan noder till basberäkningspris i stacken. Ovan transport ligger undersystem delsystemet federation, som kluster på olika noder i en enda entitet (med namnet kluster) så att Service Fabric kan upptäcka fel, utföra val av ledare och ger konsekvent routning. Undersystemet tillförlitlighet lagrade ovanpå undersystemet federation ansvarar för tillförlitligheten för Service Fabric-tjänster via mekanismer som till exempel replikering, resurshantering och redundans. Undersystemet för federation också ligger till grund för delsystemet som är värd för och aktivering, som hanterar livscykeln för ett program på en enda nod. Undersystem för nätverkshantering hanterar livscykeln för program och tjänster. Undersystemet för testning hjälper programutvecklare att testa sina tjänster via simulerade fel före och efter distributionen av program och tjänster till produktionsmiljön. Service Fabric ger möjlighet att lösa serviceplatser via dess undersystemet för kommunikation. Programmeringsmodeller för program som är exponerade för utvecklare lagrade ovanpå dessa undersystem tillsammans med programmodell att aktivera verktyg.

## <a name="transport-subsystem"></a>Transport-undersystem
Transport-undersystemet implementerar en point-to-point datagram kommunikationskanal. Den här kanalen används för kommunikation inom service fabric-kluster och kommunikation mellan service fabric-kluster och klienter. Den stöder enkelriktad och begäran / svar-kommunikationsmönster som lägger grunden för att implementera broadcast- och multicast i Federation-lagret. Transport-undersystemet skyddar kommunikationen med hjälp av X509 certifikat eller Windows-säkerhet. Den här undersystem används internt av Service Fabric och är inte direkt tillgänglig för utvecklare för programmering.

## <a name="federation-subsystem"></a>Federation-undersystem
Du måste ha en enhetlig vy av systemet för att kunna anledning om en uppsättning noder i ett distribuerat system. Undersystemet för federation använder kommunikation-primitiver som tillhandahålls av undersystemet transport och häftar samman de olika noderna i en enda enhetlig kluster som den kan anledning om. Det ger de primitiver för distribuerade system som krävs av de andra delsystem - felidentifiering, val av ledare och konsekvent routning. Undersystemet för federation är byggt på distribuerade hash-tabeller med 128-bitars token kan. Undersystemet skapar en ringtopologi över noder, med varje nod i den ring som tilldelas en delmängd av området token för ägarskap. För felidentifiering använder lagret en leasingmekanism baserat på hjärtat beating och skiljedomar. Det garanterar även undersystemet federation via invecklade anslutnings- och avgång protokoll som en enda ägare av en token som finns när som helst. Detta ger val av ledare och konsekvent routning garantier.

## <a name="reliability-subsystem"></a>Tillförlitlighet undersystem
Undersystemet tillförlitlighet tillhandahåller möjligheten att kontrollera tillståndet för en Service Fabric-tjänst med hög tillgänglighet med den *Replikatorns*, *Redundanshanteraren*, och *resurs Belastningsutjämnaren*.

* Replikatorn säkerställer att tillståndsändringar i den primära tjänsterepliken automatiskt kommer att replikeras till sekundära repliker, upprätthålla konsekvens mellan primära och sekundära repliker i en replikuppsättning för tjänsten. Replikatorn ansvarar för kvorumhantering av repliker i uppsättningen. Det interagerar med redundansenheten att hämta listan över åtgärder för att replikera och omkonfiguration agenten tillhandahåller det med konfigurationen av replikuppsättningen. Konfigurationen anger vilka repliker åtgärderna måste replikeras. Service Fabric tillhandahåller en standard-replikator kallas i Fabric som kan användas av programming model-API för att kontrollera tjänstens tillstånd med hög tillgänglighet och tillförlitlighet.
* Hanteraren för redundanskluster säkerställer att när noder läggs till eller tas bort från klustret, omdistribueras belastningen automatiskt tillgängliga noder. Om det inte går att en nod i klustret, konfigurera klustret automatiskt om tjänsten replikerna för att upprätthålla tillgänglighet.
* Resource Manager placerar tjänsten repliker över fel domän i klustret och ser till att alla redundansenheter är operativa. Resource Manager balanserar också tjänstresurser i underliggande delad pool av noder att uppnå optimal uniform belastningsfördelning.

## <a name="management-subsystem"></a>Undersystem för nätverkshantering
Undersystem för nätverkshantering erbjuder slutpunkt till slutpunkt service och Livscykelhantering för program. PowerShell-cmdlets och administrativa API: er låter dig att etablera, distribuera, korrigera, uppgradera och avetablera program utan förlust av tillgängligheten. Undersystem för nätverkshantering utför via följande tjänster.

* **Klusterhanterare**: Det här är den primära tjänsten som interagerar med redundans Manager från tillförlitlighet att placera programmen på noderna efter placeringsbegränsningar för tjänsten. Hanteraren för filserverresurser i undersystemet för redundans garanterar att begränsningarna aldrig är bruten. Cluster manager i hanterar livscykeln för programmen från etablera avetablera. Det integreras med hälsoindikatorn så att programmets tillgänglighet inte är går förlorade från ett perspektiv för semantisk hälsa under uppgraderingar.
* **Hälsoindikatorn**: Den här tjänsten kan hälsoövervakning av program, tjänster och klustret entiteter. Kluster-entiteter (till exempel noder, tjänstpartitioner och repliker) kan rapportera hälsoinformation som sedan aggregeras till centraliserade health store. Den här hälsoinformation ger en övergripande point-in-time-hälsa ögonblicksbild av de tjänster och noder som är distribuerade över flera noder i klustret, så att du kan vidta alla nödvändiga åtgärder. Hälsotillstånd fråga API: er kan du fråga health-händelser som har rapporterats till undersystemet hälsotillstånd. Hälsotillstånd frågan API: erna returnerar rå health-data som lagras i health store eller den sammanställda tolkas hälsodata för ett specifikt kluster-entitet.
* **Bild Store**: Den här tjänsten tillhandahåller lagring och distribution av binärfilerna för programmet. Den här tjänsten tillhandahåller en enkel distribuerat filarkiv där programmen överförs till och hämtas från.

## <a name="hosting-subsystem"></a>Som är värd för undersystem
Cluster manager i informerar värdbaserade undersystemet (som körs på varje nod) vilka tjänster som behövs för att hantera för en viss nod. Undersystemet för värdtjänst hanterar sedan livscykeln för programmet på noden. Det interagerar med tillförlitlighet komponenter så att replikerna är på rätt plats och är felfria.

## <a name="communication-subsystem"></a>Undersystemet för kommunikation
Den här undersystem erbjuder tillförlitlig meddelandehantering i klustret och tjänsten identifiering via i namngivningstjänsten. I namngivningstjänsten matchar tjänstnamn till en plats i klustret och gör att användarna kan hantera tjänstnamn och egenskaper. Med tjänsten namngivning av kommunicera klienter på ett säkert sätt med alla noder i klustret för att lösa ett tjänstnamn och hämta tjänstens metadata. Med en enkel namngivning-klient API kan utveckla användare av Service Fabric tjänster och klienter som kan lösa klientens aktuella nätverksplats trots noden dynamik eller nytt storlek på klustret.

## <a name="testability-subsystem"></a>Möjligheten att testa undersystem
Testning är en uppsättning med verktyg som utformats för att testa tjänster som bygger på Service Fabric. Verktygen kan utvecklare enkelt medföra meningsfulla fel och kör test-scenarier för att använda och validera ett stort antal tillstånd och övergångar som en tjänst får under hela dess livslängd på ett kontrollerat och säkert sätt. Möjligheten att testa innehåller också en mekanism för att köra längre tester som kan iterera via olika möjliga fel utan att förlora tillgänglighet. Det ger dig testa i produktionsmiljö.


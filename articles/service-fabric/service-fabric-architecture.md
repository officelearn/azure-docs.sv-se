---
title: Arkitektur för Azure Service Fabric
description: I den här artikeln beskrivs arkitekturen i Service Fabric, en plattform för distribuerade system som används för att skapa skalbara, tillförlitliga och lätthanterade program för molnet.
services: service-fabric
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 972700dded1841994de9252b4aa4bbc8eaefeaf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76024705"
---
# <a name="service-fabric-architecture"></a>Arkitektur för Service Fabric

Service Fabric är byggt med lager undersystem. Med de här undersystemen kan du skriva program som är:

* Högt tillgänglighet
* Skalbar
* Hanterbara
* Testbara

Följande diagram visar de viktigaste delsystemen i Service Fabric.

![Diagram över arkitekturen Service Fabric](media/service-fabric-architecture/service-fabric-architecture.png)

I ett distribuerat system är möjligheten att på ett säkert sätt kommunicera mellan noder i ett kluster avgörande. Vid basen av stacken är transport delsystemet, som ger säker kommunikation mellan noder. Ovanför transportundersystemet ligger federationsundersystemet, som samlar de olika noderna till en enda entitet (namngivna kluster) så att Service Fabric kan identifiera fel, utföra ledareval och tillhandahålla konsekvent routning. Undersystemet tillförlitlighet, som är lager ovanpå federationsundersystemet, ansvarar för tillförlitligheten hos Service Fabric-tjänster genom mekanismer som replikering, resurshantering och redundans. Federationsundersystemet ligger också bakom undersystemet hosting och aktivering, som hanterar livscykeln för ett program på en enda nod. Undersystemet hantering hanterar livscykeln för program och tjänster. Testability delsystemet hjälper programutvecklare testa sina tjänster genom simulerade fel före och efter distribution av program och tjänster till produktionsmiljöer. Service Fabric ger möjlighet att lösa serviceplatser via sitt kommunikationsundersystem. Programprogrammeringsmodellerna som exponeras för utvecklare är skiktade ovanpå dessa delsystem tillsammans med programmodellen för att aktivera verktyg.

## <a name="transport-subsystem"></a>Delsystem för transport

Transportundersystemet implementerar en punkt-till-punkt-datagramkommunikationskanal. Den här kanalen används för kommunikation inom serviceinfrastrukturkluster och kommunikation mellan serviceinfrastrukturklustret och klienter. Den stöder enkelriktade kommunikationsmönster och kommunikationsmönster för begäran och svar, vilket utgör grunden för att implementera sändning och multicast i federationslagret. Transportundersystemet säkrar kommunikationen med hjälp av X509-certifikat eller Windows-säkerhet. Det här delsystemet används internt av Service Fabric och är inte direkt tillgängligt för utvecklare för programprogrammering.

## <a name="federation-subsystem"></a>Undersystem för federation

För att resonera om en uppsättning noder i ett distribuerat system måste du ha en konsekvent vy över systemet. Federationsundersystemet använder de kommunikationsprimitiver som tillhandahålls av transportundersystemet och syr de olika noderna i ett enda enhetligt kluster som det kan resonera om. Det ger de distribuerade systemprimeringar som behövs av de andra delsystemen - felidentifiering, ledarval och konsekvent routning. Federationsundersystemet bygger ovanpå distribuerade hash-tabeller med ett 128-bitars tokenutrymme. Delsystemet skapar en ringtopologi över noderna, där varje nod i ringen tilldelas en delmängd av tokenutrymmet för ägarskap. För feldetektering använder lagret en leasingmekanism baserad på hjärtslag och skiljedom. Federationsundersystemet garanterar också genom intrikata anslutnings- och avgångsprotokoll att endast en enskild ägare av en token finns när som helst. Detta ger ledare val och konsekvent routing garantier.

## <a name="reliability-subsystem"></a>Delsystem för tillförlitlighet

Delsystemet tillförlitlighet tillhandahåller mekanismen för att göra tillståndet för en servicelagringstjänst mycket tillgänglig med hjälp av *Replikator,* *Redundanhanteraren*och *Resource Balancer*.

* Replikatorn ser till att tillståndsändringar i primärtjänstrepliken automatiskt replikeras till sekundära repliker, vilket upprätthåller överensstämmelsen mellan de primära och sekundära replikerna i en tjänstreplikuppsättning. Replikatorn ansvarar för kvorumhantering bland replikerna i replikuppsättningen. Den interagerar med redundansenheten för att få listan över åtgärder att replikera, och omkonfigurationsagenten tillhandahåller den med konfigurationen av replikuppsättningen. Den konfigurationen anger vilka repliker som åtgärderna behöver replikeras. Service Fabric tillhandahåller en standardreplikator som kallas Fabric Replicator, som kan användas av programmeringsmodell-API:et för att göra tjänsttillståndet mycket tillgängligt och tillförlitligt.
* Redundanshanteraren säkerställer att när noder läggs till eller tas bort från klustret, omfördelas inläsningen automatiskt över de tillgängliga noderna. Om en nod i klustret misslyckas konfigurerar klustret automatiskt om tjänstrepliker för att upprätthålla tillgängligheten.
* Resource Manager placerar tjänstrepliker över feldomäner i klustret och ser till att alla redundansenheter är i drift. Resurshanteraren balanserar också tjänstresurser över den underliggande delade poolen med klusternoder för att uppnå optimal enhetlig belastningsfördelning.

## <a name="management-subsystem"></a>Delsystem för hantering

Hanteringsundersystemet tillhandahåller heltäckande tjänst- och programlivscykelhantering. PowerShell-cmdletar och administrativa API:er gör att du kan etablera, distribuera, korrigera, uppgradera och avetableringsprogram utan att du kan förlora tillgängligheten. Ledningens delsystem utför detta via följande tjänster.

* **Klusterhanteraren**: Det här är den primära tjänsten som interagerar med Redundanshanteraren från tillförlitlighet för att placera programmen på noderna baserat på begränsningarna för tjänstplacering. Resurshanteraren i undersystemet växlingsundersystem säkerställer att begränsningarna aldrig bryts. Klusterhanteraren hanterar livscykeln för programmen från etablering till avetablering. Den integreras med hälsohanteraren för att säkerställa att programtillgängligheten inte går förlorad ur ett semantiskt hälsoperspektiv under uppgraderingar.
* **Hälsohanteraren**: Den här tjänsten möjliggör hälsoövervakning av program, tjänster och klusterenheter. Klusterentiteter (till exempel noder, tjänstpartitioner och repliker) kan rapportera hälsoinformation, som sedan aggregeras till det centraliserade hälsoarkivet. Den här hälsoinformationen ger en övergripande ögonblicksbild av hälsoögonblicksbilder i tid av tjänster och noder som distribueras över flera noder i klustret, så att du kan vidta nödvändiga korrigerande åtgärder. Med API:er för hälsofråga kan du fråga hälsohändelser som rapporterats till hälsoundersystemet. Api:erna för hälsofrågor returnerar rådningsdata som lagras i hälsoarkivet eller de aggregerade, tolkade hälsodata för en viss klusterentitet.
* **Image Store**: Den här tjänsten tillhandahåller lagring och distribution av programbinärerna. Den här tjänsten tillhandahåller en enkel distribuerad filbutik där programmen laddas upp till och hämtas från.

## <a name="hosting-subsystem"></a>Hosting delsystem

Klusterhanteraren informerar värdundersystemet (som körs på varje nod) vilka tjänster som behövs för att hantera för en viss nod. Värdundersystemet hanterar sedan livscykeln för programmet på den noden. Den interagerar med tillförlitlighet och hälsokomponenter för att säkerställa att replikerna är korrekt placerade och är felfria.

## <a name="communication-subsystem"></a>Delsystem för kommunikation

Det här undersystemet tillhandahåller tillförlitliga meddelanden i kluster- och tjänstidentifieringen via namngivningstjänsten. Namngivningstjänsten matchar tjänstnamn till en plats i klustret och gör det möjligt för användare att hantera tjänstnamn och egenskaper. Med namngivningstjänsten kan klienter kommunicera säkert med valfri nod i klustret för att matcha ett tjänstnamn och hämta tjänstmetadata. Med hjälp av ett enkelt namngivningsklient-API kan användare av Service Fabric utveckla tjänster och klienter som kan lösa den aktuella nätverksplatsen trots noddynamik eller storleksändring av klustret.

## <a name="testability-subsystem"></a>Delsystem för testbarhet

Testability är en uppsättning verktyg som är särskilt utformade för att testa tjänster som bygger på Service Fabric. Verktygen låter en utvecklare enkelt inducera meningsfulla fel och köra testscenarier för att utöva och validera de många tillstånd och övergångar som en tjänst kommer att uppleva under hela sin livstid, allt på ett kontrollerat och säkert sätt. Testability ger också en mekanism för att köra längre tester som kan iterera genom olika möjliga fel utan att förlora tillgänglighet. Detta ger dig en test-i-produktion miljö.

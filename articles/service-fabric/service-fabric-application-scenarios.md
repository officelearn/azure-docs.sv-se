---
title: Programscenarier och design | Microsoft Docs
description: Översikt över kategorier av molnprogram i Service Fabric. Beskriver programmets design som använder tillståndskänsliga och tillståndslösa tjänster.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: f7ad295eb30d257cd195ae02d5a5b1d85de76bda
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228495"
---
# <a name="service-fabric-application-scenarios"></a>Scenarier för Service Fabric-program
Azure Service Fabric är en tillförlitlig och flexibel plattform som hjälper dig att skriva och köra många typer av program och tjänster. Dessa program och mikrotjänster kan vara tillståndslösa eller tillståndskänsliga och de är nätverksbelastning resursen för virtuella datorer för att maximera effektiviteten. Unikt arkitekturen i Service Fabric kan du utföra nära dataanalys i realtid, InMemory-beräkning, parallella transaktioner och händelsebearbetning i dina program. Du kan enkelt skala dina program upp eller ned (verkligen in eller ut), beroende på behoven föränderliga resurs.

Riktlinjer för designen på att bygga program läsa [arkitektur för mikrotjänster på Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) och [bästa praxis för programmets design med hjälp av Service Fabric](service-fabric-best-practices-applications.md)

Service Fabric-plattformen är idealisk för följande typer av program:

* **Datainsamling, bearbetnings- och IoT**: Eftersom Service Fabric hanterar stor skala och har låg latens via dess tillståndskänsliga tjänster, är det perfekt för bearbetning av miljontals enheter där data för enheten och beräkningen är samordnade.

    Kunder som har skapat IoT-tjänster som använder Service Fabric innehåller [hos Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL konstruktion](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), och [nät system](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Spel och sessionsbaserad interaktiva program**: Service Fabric är perfekt om programmet kräver låg latens vid läsning och skrivning, till exempel i onlinespel eller snabbmeddelanden. Service Fabric kan du skapa dessa interaktiva, tillståndskänsliga program utan att behöva skapa en separat lager eller cache. Besök [Azures spellösningar](https://azure.microsoft.com/solutions/gaming/) för designvägledning på [med Service Fabric i speltjänster](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    Kunder som har skapat speltjänster inkluderar [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) och [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Kunder som har skapat interaktiva sessioner inkluderar [hos Honeywell med Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **Dataanalys och arbetsflödesbearbetning**: Program som måste på ett tillförlitligt sätt bearbeta händelser eller dataströmmar också omfattas av optimerad läsningar och skrivningar i Service Fabric. Dessutom stöder Service Fabric pipelines för bearbetning av program, där resultaten måste vara pålitliga och skickade in nästa bearbetning steg utan att förlora. Dessa inkluderar transaktions- och finansiella system, där data konsekvens och alla beräkningar garantier är avgörande.

    Kunder som har skapat arbetsflödet företagstjänster inkluderar [Zeiss grupp](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) och [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)

* **Beräkning på data**: Service Fabric kan du skapa beräkning dataintensiva tillståndskänsliga applikationer. Service Fabric kan samplacering av bearbetning (beräkning) och data i program. När ditt program kräver åtkomst till data, begränsar vanligtvis svarstid för nätverk som är associerade med en extern cache eller storage datanivå tidsåtgången för beräkning. Med tillståndskänslig Service Fabric-tjänster som svarstid har eliminerats, aktivera mer optimerade läsningar och skrivningar. Tänk dig ett program som utför nästan i realtid rekommendation val för kunder med ett tidsfördröjningen för krav på mindre än 100 millisekunder. Svarstid och prestandaegenskaper för Service Fabric-tjänster (där beräkning av val av rekommendation är samordnat med data och regler) tillhandahåller en dynamisk för användaren som jämfört med modellen standardimplementeringen av behöva hämta nödvändiga data från Fjärrlagring.

    Kunder som har skapat beräkning tjänster inkluderar [Solidsoft svara](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) och [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)

* **Tjänster med hög tillgänglighet**: Service Fabric ger snabb redundans genom att skapa flera tjänsten secondary repliker. Om en nod, en process eller en enskild tjänst slutar att fungera på grund av maskinvara eller andra fel, befordras en av de sekundära replikerna till en primär replik med minimal förlust av tjänsten.

* **Skalbara tjänster**: Enskilda tjänster kan partitioneras, vilket ger tillstånd och kan inte skalas ut över klustret. Dessutom kan enskilda tjänster skapas och tas bort direkt. Tjänster kan snabbt och enkelt att skalas upp från några instanser på några få noder till tusentals instanser på många noder och sedan skalas i igen, beroende på dina resursbehov. Du kan använda Service Fabric för att skapa dessa tjänster och hantera sina fullständig supportlivscykel.

## <a name="application-design-case-studies"></a>Fallstudier för programmet design
Ett antal fallstudier som visar hur Service Fabric används för att utforma program publiceras på [kundberättelser](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) och [mikrotjänster lösningar plats](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Utforma program som består av tillståndslösa och tillståndskänsliga mikrotjänster
Att skapa program med arbetsroller i Azure Cloud Service är ett exempel på en tillståndslös tjänst. Tillståndskänsliga mikrotjänster har däremot sina auktoritativa tillstånd bortom begäran och svaret. Den här funktionen ger hög tillgänglighet och konsekvens av systemets tillstånd via enkla API: er som tillhandahåller transaktionella garantier som backas upp av replikering. Service Fabric tillståndskänsliga tjänster demokratisera hög tillgänglighet, få ut det på alla typer av program, inte bara databaser och datalager. Det här är en naturlig följd. Program har redan flyttat från att använda enbart relationsdatabaser för hög tillgänglighet till NoSQL-databaser. Själva programmen kan nu ha sina ”heta” tillstånd och data som hanteras i dem för ytterligare prestandavinster utan att offra tillförlitlighet, konsekvens och tillgänglighet.

När du utformar program som består av mikrotjänster kan du vanligtvis har en kombination av tillståndslösa web apps (ASP.NET, Node.js, o.s.v.) anropar till tillståndslösa och tillståndskänsliga mellannivå företagstjänster, alla distribueras i samma Service Fabric kluster med distribution av Service Fabric-kommandon. Var och en av dessa tjänster är oberoende avseende skalbarhet, tillförlitlighet och Resursanvändning, vilket avsevärt minskar tiden rörligheten och flexibiliteten i utvecklings- och livscykelhantering.

Tillståndskänsliga mikrotjänster förenkla programdesign eftersom de ta bort behovet av ytterligare köer och cacheminnen som traditionellt varit krävs att bemöta kraven på tillgänglighet och svarstid i helt och hållet tillståndslösa program. Eftersom tillståndskänsliga tjänster helt naturligt har hög tillgänglighet och låg latens, finns det färre rörliga delar som kan hantera i ditt program som helhet. Diagrammet nedan visar skillnaderna mellan att utforma ett program som är tillståndslösa och ett som är tillståndskänsliga. Genom att utnyttja den [Reliable Services](service-fabric-reliable-services-introduction.md) och [Reliable Actors](service-fabric-reliable-actors-introduction.md) programmeringsmodeller, tillståndskänsliga tjänster minska komplexiteten för program samtidigt som du uppnår högt dataflöde och låg fördröjning.

## <a name="an-application-built-using-stateless-services"></a>Ett program som skapats med hjälp av tillståndslösa tjänster
![Program med hjälp av tillståndslös tjänst][Image1]

## <a name="an-application-built-using-stateful-services"></a>Ett program som skapats med tillståndskänsliga tjänster
![Program med hjälp av tillståndslös tjänst][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg

* Läs mer om [kundfallstudier](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc)
* Läs mer om [mönster och scenarier](service-fabric-patterns-and-scenarios.md)

* Kom igång med att skapa tillståndslösa och tillståndskänsliga tjänster med Service Fabric [tillförlitliga tjänster](service-fabric-reliable-services-quick-start.md) och [tillförlitliga aktörer](service-fabric-reliable-actors-get-started.md) programmeringsmodeller.
* Besök Azure architecture center för vägledning på [skapa mikrotjänster på Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* Gå till [Metodtips för Azure Service Fabric-program och klustret](service-fabric-best-practices-overview.md) för designvägledning för programmet.

* Se också följande avsnitt:
  * [Berätta om mikrotjänster](service-fabric-overview-microservices.md)
  * [Definiera och hantera tjänstestatus](service-fabric-concepts-state.md)
  * [Tillgängligheten för Service Fabric-tjänster](service-fabric-availability-services.md)
  * [Skala Service Fabric-tjänster](service-fabric-concepts-scalability.md)
  * [Partitionera Service Fabric-tjänster](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg

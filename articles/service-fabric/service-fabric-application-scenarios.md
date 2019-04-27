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
ms.date: 7/02/2017
ms.author: atsenthi
ms.openlocfilehash: c9b2f9ac131e71b7c6b37ed85568adc0c3978dc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621232"
---
# <a name="service-fabric-application-scenarios"></a>Scenarier för Service Fabric-program
Azure Service Fabric är en tillförlitlig och flexibel plattform som hjälper dig att skriva och köra många typer av program och tjänster. Dessa program och mikrotjänster kan vara tillståndslösa eller tillståndskänsliga och de är nätverksbelastning resursen för virtuella datorer för att maximera effektiviteten. Unikt arkitekturen i Service Fabric kan du utföra nära dataanalys i realtid, InMemory-beräkning, parallella transaktioner och händelsebearbetning i dina program. Du kan enkelt skala dina program upp eller ned (verkligen in eller ut), beroende på behoven föränderliga resurs.

Service Fabric-plattformen i Azure är perfekt för följande typer av program:

* **Tjänster med hög tillgänglighet**: Service Fabric-tjänster ge snabb redundans genom att skapa flera tjänsten secondary repliker. Om en nod, en process eller en enskild tjänst slutar att fungera på grund av maskinvara eller andra fel, befordras en av de sekundära replikerna till en primär replik med minimal förlust av tjänsten.
* **Skalbara tjänster**: Enskilda tjänster kan partitioneras, vilket ger tillstånd och kan inte skalas ut över klustret. Dessutom kan enskilda tjänster skapas och tas bort direkt. Tjänster kan snabbt och enkelt skalade du ut från några instanser på några få noder till tusentals instanser på många noder och sedan skalas i igen, beroende på dina resursbehov. Du kan använda Service Fabric för att skapa dessa tjänster och hantera deras fullständiga livscykler.
* **Beräkning på icke-statiska data**: Service Fabric kan du skapa data, indata/utdata och beräkning beräkningsintensiva tillståndskänsliga program. Service Fabric kan samplacering av bearbetning (beräkning) och data i program. Normalt när ditt program kräver åtkomst till data, det finns svarstid för nätverk som är associerade med en extern cache eller storage datanivå. Med tillståndskänslig Service Fabric-tjänster elimineras som svarstid, aktiverar bättre läsningar och skrivningar. Anta exempelvis att det finns ett program som utför nästan i realtid rekommendation val för kunder med ett tidsfördröjningen för krav på mindre än 100 millisekunder. Svarstid och prestandaegenskaper för Service Fabric-tjänster (där beräkning av val av rekommendation är samordnad med data och regler) tillhandahåller en dynamisk för användaren som jämfört med modellen standardimplementeringen av behöva hämta nödvändiga data från Fjärrlagring.  
* **Sessionsbaserad interaktiva program**: Service Fabric är användbart om dina program, till exempel onlinespel eller snabbmeddelanden, kräver låg latens vid läsning och skrivning. Service Fabric kan du skapa dessa interaktiva, tillståndskänsliga program utan att behöva skapa något separat lager eller cache, som krävs för tillståndslösa appar. (Detta ökar svarstiden och introducerar potentiellt konsekvensproblem.).
* **Dataanalys och arbetsflöden**: Snabb läsning och skrivning av Service Fabric gör att appar som på ett tillförlitligt sätt måste bearbeta händelser eller dataströmmar. Service Fabric kan också program som beskriver pipelines för bearbetning, där resultaten måste vara pålitliga och skickade in nästa bearbetning steg utan dataförlust. Dessa inkluderar transaktions- och finansiella system, där data konsekvens och alla beräkningar garantier är avgörande.
* **Datainsamling, bearbetning och IoT**: Eftersom Service Fabric hanterar stor skala och har låg latens via dess tillståndskänsliga tjänster, är det perfekt för bearbetning av miljontals enheter där data för enheten och beräkningen finns på samma plats.
Vi har sett flera kunder som har skapat IoT-system med hjälp av Service Fabric, inklusive [BMW](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/24/service-fabric-customer-profile-bmw-technology-corporation/), [Schneider Electric](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/05/service-fabric-customer-profile-schneider-electric/) och [nät system](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/20/service-fabric-customer-profile-mesh-systems/).

## <a name="application-design-case-studies"></a>Fallstudier för programmet design
Ett antal fallstudier som visar hur Service Fabric används för att utforma program publiceras på den [Service Fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/) och [mikrotjänster lösningar plats](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Utforma program som består av tillståndslösa och tillståndskänsliga mikrotjänster
Att skapa program med arbetsroller i Azure Cloud Service är ett exempel på en tillståndslös tjänst. Tillståndskänsliga mikrotjänster har däremot sina auktoritativa tillstånd bortom begäran och svaret. Detta ger hög tillgänglighet och konsekvens i tillståndet via enkla API: er som tillhandahåller transaktionella garantier som backas upp av replikering. Service Fabric tillståndskänsliga tjänster demokratisera hög tillgänglighet, få ut det på alla typer av program, inte bara databaser och datalager. Det här är en naturlig följd. Program har redan flyttat från att använda enbart relationsdatabaser för hög tillgänglighet till NoSQL-databaser. Själva programmen kan nu ha sina ”heta” tillstånd och data som hanteras i dem för ytterligare prestandavinster utan att offra tillförlitlighet, konsekvens och tillgänglighet.

När du utformar program som består av mikrotjänster kan du vanligtvis har en kombination av tillståndslösa web apps (ASP.NET, Node.js, o.s.v.) anropar till tillståndslösa och tillståndskänsliga mellannivå företagstjänster, alla distribueras i samma Service Fabric kluster med distribution av Service Fabric-kommandon. Var och en av dessa tjänster är oberoende avseende skalbarhet, tillförlitlighet och resurs användning, vilket avsevärt minskar tiden flexibilitet i programutveckling och livscykelhantering.

Tillståndskänsliga mikrotjänster förenkla programdesign eftersom de ta bort behovet av ytterligare köer och cacheminnen som traditionellt varit krävs att bemöta kraven på tillgänglighet och svarstid i helt och hållet tillståndslösa program. Eftersom tillståndskänsliga tjänster är naturligt med hög tillgänglighet och låg latens, det innebär att det finns färre rörliga delar som kan hantera i ditt program som helhet. Diagrammet nedan visar skillnaderna mellan att utforma ett program som är tillståndslösa och ett som är tillståndskänsliga. Genom att utnyttja den [Reliable Services](service-fabric-reliable-services-introduction.md) och [Reliable Actors](service-fabric-reliable-actors-introduction.md) programmeringsmodeller, tillståndskänsliga tjänster minska komplexiteten för program samtidigt som du uppnår högt dataflöde och låg fördröjning.

## <a name="an-application-built-using-stateless-services"></a>Ett program som skapats med hjälp av tillståndslösa tjänster
![Program med hjälp av tillståndslös tjänst][Image1]

## <a name="an-application-built-using-stateful-services"></a>Ett program som skapats med tillståndskänsliga tjänster
![Program med hjälp av tillståndslös tjänst][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg

* Läs mer om [kundfallstudier](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/)
* Läs mer om [mönster och scenarier](service-fabric-patterns-and-scenarios.md)

* Kom igång med att skapa tillståndslösa och tillståndskänsliga tjänster med Service Fabric [tillförlitliga tjänster](service-fabric-reliable-services-quick-start.md) och [tillförlitliga aktörer](service-fabric-reliable-actors-get-started.md) programmeringsmodeller.
* Se också följande avsnitt:
  * [Berätta om mikrotjänster](service-fabric-overview-microservices.md)
  * [Definiera och hantera tjänstestatus](service-fabric-concepts-state.md)
  * [Tillgängligheten för Service Fabric-tjänster](service-fabric-availability-services.md)
  * [Skala Service Fabric-tjänster](service-fabric-concepts-scalability.md)
  * [Partitionera Service Fabric-tjänster](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg

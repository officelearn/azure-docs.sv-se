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
ms.openlocfilehash: 6563d6e7c454f44e1a70d725191e56d3f90315c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052596"
---
# <a name="service-fabric-application-scenarios"></a>Scenarier för Service Fabric-program
Azure Service Fabric är en tillförlitlig och flexibel plattform där du kan skriva och köra många typer av program och tjänster. Dessa program och mikrotjänster kan vara tillståndslösa eller tillståndskänsliga och de är resursen nätverksbelastning över virtuella datorer för att maximera effektiviteten. 

Unikt arkitekturen i Service Fabric kan du utföra nära dataanalys i realtid, InMemory-beräkning, parallella transaktioner och händelsebearbetning i dina program. Du kan enkelt skala dina program upp eller ned (verkligen in eller ut), beroende på behoven föränderliga resurs.

Riktlinjer för designen på att bygga program läsa [arkitektur för Mikrotjänster på Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) och [bästa praxis för programmets design med hjälp av Service Fabric](service-fabric-best-practices-applications.md).

Överväg att använda Service Fabric-plattformen för följande typer av program:

* **Datainsamling, bearbetnings- och IoT**: Service Fabric hanterar stor skala och har låg latens via dess tillståndskänsliga tjänster. Det kan bearbeta data i flera miljoner enheter där data för enheten och beräkningen är samordnade.

    Kunder som har skapat IoT-tjänster med hjälp av Service Fabric innehåller [hos Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL konstruktion](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), och [nät system](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Spel och sessionsbaserad interaktiva program**: Service Fabric är användbart om programmet kräver låg latens läsningar och skrivningar, till exempel i onlinespel eller snabbmeddelanden. Service Fabric kan du skapa dessa interaktiva, tillståndskänsliga program utan att behöva skapa en separat lager eller cache. Besök [Azures spellösningar](https://azure.microsoft.com/solutions/gaming/) för designvägledning på [med Service Fabric i speltjänster](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

    Kunder som har skapat speltjänster inkluderar [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) och [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Kunder som har skapat interaktiva sessioner inkluderar [hos Honeywell med Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Dataanalys och arbetsflödesbearbetning**: Program som på ett tillförlitligt sätt måste bearbeta händelser eller strömmar av data förmånen från optimerad läsningar och skrivningar i Service Fabric. Service Fabric stöder också pipelines för bearbetning av program, där resultaten måste vara pålitliga och skickade in nästa bearbetning steg utan att förlora. Dessa pipelines inkluderar transaktions- och finansiella system, där data konsekvens och alla beräkningar garantier är avgörande.

    Kunder som har skapat arbetsflödet företagstjänster inkluderar [Zeiss grupp](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [kvorum affärslösningar](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric), och [Société allmänna](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Beräkning på data**: Service Fabric kan du bygga tillståndskänsliga program som gör beräkningsintensiva data beräkning. Service Fabric kan samordning av bearbetning (beräkning) och data i program. 

   När ditt program kräver åtkomst till data, begränsar vanligtvis svarstid för nätverk som är associerade med en extern cache eller storage datanivå tidsåtgången för beräkning. Tillståndskänslig Service Fabric-tjänster eliminera den latens, aktivera mer optimerade läser och skriver. 
   
   Tänk dig ett program som utför nästan i realtid rekommendation val för kunder med ett tidsfördröjningen för krav på mindre än 100 millisekunder. Svarstid och prestandaegenskaper för Service Fabric-tjänster ger en dynamisk upplevelse för användaren, jämfört med vanliga implementeringen modellen för att hämta nödvändiga data från Fjärrlagring. Systemet är snabbare eftersom beräkning av val av rekommendation är samordnat med data och regler.

    Kunder som har skapat beräkning tjänster inkluderar [Solidsoft svara](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) och [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Tjänster med hög tillgänglighet**: Service Fabric ger snabb redundans genom att skapa flera tjänsten secondary repliker. Om en nod, en process eller en enskild tjänst slutar att fungera på grund av maskinvara eller andra fel, befordras en av de sekundära replikerna till en primär replik med minimal förlust av tjänsten.

* **Skalbara tjänster**: Enskilda tjänster kan partitioneras, vilket ger tillstånd och kan inte skalas ut över klustret. Enskilda tjänster kan även skapas och tas bort direkt. Du kan skala ut tjänster från några instanser på några få noder till tusentals instanser på många noder och sedan skala dem i igen efter behov. Du kan använda Service Fabric för att skapa dessa tjänster och hantera sina fullständig supportlivscykel.

## <a name="application-design-case-studies"></a>Fallstudier för programmet design
Fallstudier som visar hur Service Fabric används för att utforma program publiceras på den [kundberättelser](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) och [Mikrotjänster i Azure](https://azure.microsoft.com/solutions/microservice-applications/) platser.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Designa program består av tillståndslösa och tillståndskänsliga mikrotjänster
Att skapa program med arbetsroller i Azure Cloud Services är ett exempel på en tillståndslös tjänst. Tillståndskänsliga mikrotjänster har däremot sina auktoritativa tillstånd bortom begäran och svaret. Den här funktionen ger hög tillgänglighet och konsekvens av systemets tillstånd via enkla API: er som tillhandahåller transaktionella garantier som backas upp av replikering. 

Tillståndskänsliga tjänster i Service Fabric ger dig hög tillgänglighet till alla typer av program, inte bara databaser och datalager. Det här är en naturlig följd. Program har redan flyttat från att använda enbart relationsdatabaser för hög tillgänglighet till NoSQL-databaser. Själva programmen kan nu ha sina ”heta” tillstånd och data som hanteras i dem för ytterligare prestandavinster utan att offra tillförlitlighet, konsekvens och tillgänglighet.

När du skapar program som består av mikrotjänster, har du vanligtvis en kombination av tillståndslösa web apps (t.ex. ASP.NET och Node.js) anropande till tillståndslösa och tillståndskänsliga mellannivå företagstjänster. Appar och tjänster som distribueras i samma Service Fabric-klustret via kommandon för Service Fabric-distributionen. Var och en av dessa tjänster är oberoende vad gäller skala, tillförlitlighet och resursanvändningen. Detta oberoende förbättrar rörligheten och flexibiliteten i utvecklings- och livscykelhantering.

Tillståndskänsliga mikrotjänster förenkla programdesign eftersom de ta bort behovet av ytterligare köer och cacheminnen som traditionellt varit krävs att bemöta kraven på tillgänglighet och svarstid i helt och hållet tillståndslösa program. Eftersom tillståndskänsliga tjänster har hög tillgänglighet och låg latens, finns det färre information om du vill hantera i ditt program. 

Följande diagram visar skillnaderna mellan att utforma ett program som är tillståndslösa och ett som är tillståndskänsliga. Genom att utnyttja den [Reliable Services](service-fabric-reliable-services-introduction.md) och [Reliable Actors](service-fabric-reliable-actors-introduction.md) programmeringsmodeller, tillståndskänsliga tjänster minska komplexiteten för program samtidigt som du uppnår högt dataflöde och låg fördröjning.

Här är ett exempelprogram som använder tillståndslösa tjänster: ![Program som använder tillståndslösa tjänster][Image1]

Här är ett exempelprogram som använder tillståndskänsliga tjänster: ![Program som använder tillståndslösa tjänster][Image2]

## <a name="next-steps"></a>Nästa steg

* Läs mer om [mönster och scenarier](service-fabric-patterns-and-scenarios.md).

* Kom igång med att skapa tillståndslösa och tillståndskänsliga tjänster med Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) och [Reliable Actors](service-fabric-reliable-actors-get-started.md) programmeringsmodeller.
* Besök Azure Architecture Center anvisningar på [skapa mikrotjänster på Azure](https://docs.microsoft.com/azure/architecture/microservices/).
* Gå till [Metodtips för Azure Service Fabric-program och klustret](service-fabric-best-practices-overview.md) för designvägledning för programmet.

* Se också följande avsnitt:
  * [Berätta om mikrotjänster](service-fabric-overview-microservices.md)
  * [Definiera och hantera tjänstestatus](service-fabric-concepts-state.md)
  * [Tillgängligheten för Service Fabric-tjänster](service-fabric-availability-services.md)
  * [Skala Service Fabric-tjänster](service-fabric-concepts-scalability.md)
  * [Partitionera Service Fabric-tjänster](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg

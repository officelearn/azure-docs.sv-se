---
title: Programscenarier och design | Microsoft Docs
description: Översikt över kategorier av molnprogram i Service Fabric. Beskriver programdesign som använder tillståndskänsliga och tillståndslösa tjänster.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/02/2017
ms.author: mfussell
ms.openlocfilehash: c0a9b24704a91d6a6893937b4ee03765fb05f092
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-application-scenarios"></a>Service Fabric-Programscenarier
Azure Service Fabric är en tillförlitlig och flexibel plattform som gör att du kan skriva och köra många typer av program och tjänster. Dessa program och mikrotjänster kan vara tillståndslösa och tillståndskänsliga och de är Balanserat resurs för virtuella datorer för att maximera effektiviteten. Unik arkitekturen för Service Fabric kan du utföra nästan dataanalys i realtid, beräkning i minnet, parallella transaktioner och för händelsebearbetning i dina program. Du kan enkelt skala dina program uppåt eller nedåt (verkligen in eller ut), beroende på resurskraven ändras.

Service Fabric-plattformen i Azure är idealisk för följande typer av program:

* **Tjänster med hög tillgänglighet**: Service Fabric services ger snabb växling vid fel genom att skapa flera tjänsten secondary repliker. Om en nod, en process eller en enskild tjänst stängs av på grund av maskinvara eller andra fel, befordras en av de sekundära replikerna till en primär replik med minimal förlust av tjänsten.
* **Skalbara tjänster**: enskilda tjänster kan partitioneras för tillstånd och kan inte skalas ut i klustret. Dessutom kan individuella tjänster skapas och tas bort direkt. Tjänster kan snabbt och enkelt utskalad från några instanser på ett par noder till tusentals instanser på många noder och sedan skalas i igen, beroende på dina resursbehov. Du kan använda Service Fabric för att skapa dessa tjänster och hantera sina fullständiga livscykler.
* **Beräkningar på icke-statisk data**: Service Fabric kan du skapa data, i/o och beräkna beräkningsintensiva tillståndskänsliga program. Service Fabric kan samplacering av data och bearbetning (beräkning) i program. Vanligtvis när programmet kräver åtkomst till data, finns Nätverksfördröjningen som är associerade med en extern cache eller lagring datanivå. Med tillståndskänslig Service Fabric-tjänster elimineras den latens, aktivera flera performant läsning och skrivning. Anta till exempel att du har ett program som utför nära realtid rekommendation val för kunder med ett tidsfördröjningen krav för mindre än 100 millisekunder. Svarstid och prestandaegenskaper för Service Fabric-tjänster (där beräkning av rekommendation markeringen är samordnad med data och regler) tillhandahåller en dynamisk användaren jämfört med vanliga implementeringen modell med att hämta nödvändiga data från Fjärrlagring.  
* **Sessionsbaserade interaktiva program**: Service Fabric är användbart om krävs för ditt program, till exempel onlinespel eller snabbmeddelanden, låg latens läsningar och skrivningar. Service Fabric kan du skapa dessa interaktiv, tillståndskänsliga program utan att skapa ett separat Arkiv eller cache, vilket krävs för tillståndslösa appar. (Detta ökar svarstiden och kan vara introduceras problem med konsekvenskontroll.).
* **Dataanalys och arbetsflöden**: Snabb läsning och skrivning av Service Fabric kan program som måste på ett tillförlitligt sätt att bearbeta händelser eller dataströmmar. Service Fabric även gör det möjligt för program som beskriver bearbetning pipelines, där resultat måste vara tillförlitliga och skickas vidare bearbetning nästa fas utan dataförlust. Dessa inkluderar transaktionella och finansiella system, där data konsekvens och beräkning garantier är av yttersta vikt.
* **Att samla in data, bearbetning och IoT**: eftersom Service Fabric hanterar storskaliga och har låg latens via dess tillståndskänsliga tjänster, är det perfekt för databearbetning på miljontals enheter där data för enheten och beräkningen är samordnade.
Vi har sett flera kunder som har skapat IoT-system med hjälp av Service Fabric inklusive [BMW](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/24/service-fabric-customer-profile-bmw-technology-corporation/), [Schneider elektriska](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/05/service-fabric-customer-profile-schneider-electric/) och [nät system](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/20/service-fabric-customer-profile-mesh-systems/).

## <a name="application-design-case-studies"></a>Programmet design fallstudier
Ett antal fallstudier som visar hur Service Fabric används för att skapa program publiceras på den [Service Fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/) och [mikrotjänster lösningar plats](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Utforma program består av tillståndslösa och tillståndskänsliga mikrotjänster
Skapa program med Azure Cloud Service arbetsroller är ett exempel på en tillståndslös tjänst. Däremot upprätthålla tillståndskänslig mikrotjänster deras auktoritära tillstånd utöver begäran och sitt svar. Detta ger hög tillgänglighet och konsekvens i tillståndet via enkla API: er som ger transaktionell garantier backas upp av replikering. Service Fabric tillståndskänsliga tjänster demokratisera hög tillgänglighet, gör den till alla typer av program, inte bara databaser och andra dataarkiv. Det här är ett naturlig förlopp. Program har redan flyttat från att använda enbart relationsdatabaser för hög tillgänglighet till NoSQL-databaser. Programmen kan nu har sina ”heta” tillstånd och data som hanteras i dem för ytterligare prestandavinster utan att kompromissa tillförlitlighet, konsekvens och tillgänglighet.

När du skapar program som består av mikrotjänster vanligtvis har en kombination av tillståndslösa webbprogram (ASP.NET, Node.js, etc.) anrop till tillståndslösa och tillståndskänsliga mellannivå företagstjänster, alla som har distribuerats till samma Service Fabric kluster med kommandona Service Fabric-distribution. Var och en av dessa tjänster är oberoende avseende skalbarhet, tillförlitlighet och resursen användning avsevärt förbättra flexibilitet i utveckling och livscykelhantering.

Tillståndskänsliga mikrotjänster förenkla programmet Designer eftersom de tar bort behovet av ytterligare köer och cacheminnen traditionellt varit krävs att möta behovet av tillgänglighet och svarstid för rent tillståndslösa program. Eftersom tillståndskänsliga tjänster finns naturligt hög och låg latens, innebär detta att det finns färre rörliga delar som kan hantera i ditt program som helhet. Diagrammet nedan visar skillnaderna mellan skapar ett program som är tillståndslösa och ett som är tillståndskänslig. Genom att utnyttja den [Reliable Services](service-fabric-reliable-services-introduction.md) och [Reliable Actors](service-fabric-reliable-actors-introduction.md) programmeringsmodeller, tillståndskänsliga tjänster minska programmet komplexitet och upprätthålla högt genomflöde och låg latens.

## <a name="an-application-built-using-stateless-services"></a>Ett program som skapats med hjälp av tillståndslösa tjänster
![Program med hjälp av tillståndslösa tjänsten][Image1]

## <a name="an-application-built-using-stateful-services"></a>Ett program som skapats med tillståndskänsliga tjänster
![Program med hjälp av tillståndslösa tjänsten][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg

* Lyssna på [kundfallstudier](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=qDJnf86yC_5206218965
)
* Läs mer om [kundfallstudier](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/)
* Lär dig mer om [mönster och scenarier](service-fabric-patterns-and-scenarios.md)

* Börja bygga tillståndslösa och tillståndskänsliga tjänster med Service Fabric [reliable services](service-fabric-reliable-services-quick-start.md) och [tillförlitliga aktörer](service-fabric-reliable-actors-get-started.md) programmeringsmodeller.
* Se också följande avsnitt:
  * [Berätta om mikrotjänster](service-fabric-overview-microservices.md)
  * [Definiera och hantera tjänstens tillstånd](service-fabric-concepts-state.md)
  * [Tillgänglighet för Service Fabric-tjänster](service-fabric-availability-services.md)
  * [Skala Service Fabric-tjänster](service-fabric-concepts-scalability.md)
  * [Partitionen Service Fabric-tjänster](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg

---
title: Programscenarier och design
description: Översikt över kategorier av molnprogram i Service Fabric. I artikeln beskrivs programdesign som använder tillståndskänsliga och tillståndslösa tjänster.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: sfrev
ms.openlocfilehash: 0aeb8ab2923915befdd11f96025687be3b3c4ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76024736"
---
# <a name="service-fabric-application-scenarios"></a>Programscenarier för Service Fabric

Azure Service Fabric erbjuder en tillförlitlig och flexibel plattform där du kan skriva och köra många typer av affärsprogram och tjänster. Dessa program och mikrotjänster kan vara tillståndslösa eller tillståndskänsliga, och de är resursbalanserade över virtuella datorer för att maximera effektiviteten.

Med den unika arkitekturen i Service Fabric kan du utföra dataanalys i nära realtid, beräkning i minnet, parallella transaktioner och händelsebearbetning i dina program. Du kan enkelt skala dina program in eller ut beroende på dina förändrade resurskrav.

Om du vill ha designvägledning för att skapa program läser du [Arkitekturen Mikrotjänster på Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) och [metodtips för programdesign med Service Fabric](service-fabric-best-practices-applications.md).

Överväg att använda Service Fabric-plattformen för följande typer av program:

* **Datainsamling, bearbetning och IoT:** Service Fabric hanterar storskalig skala och har låg latens genom sina tillståndskänsliga tjänster. Det kan hjälpa till att bearbeta data på miljontals enheter där data för enheten och beräkningen är samlokaliseras.

    Kunder som har byggt IoT-tjänster med hjälp av Service Fabric inkluderar [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)och Mesh [Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Spel och sessionsbaserade interaktiva program:** Service Fabric är användbart om ditt program kräver läs- och skrivningar med låg latens, till exempel i onlinespel eller snabbmeddelanden. Med Service Fabric kan du skapa dessa interaktiva, tillståndskänsliga program utan att behöva skapa ett separat arkiv eller en separat cache. Besök [Azure-spellösningar](https://azure.microsoft.com/solutions/gaming/) för designvägledning om [hur du använder Service Fabric i speltjänster](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

    Kunder som har byggt speltjänster inkluderar [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) och [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Kunder som har byggt interaktiva sessioner inkluderar [Honeywell med Hololens.](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **Dataanalys och arbetsflödesbearbetning**: Program som på ett tillförlitligt sätt måste bearbeta händelser eller dataströmmar drar nytta av de optimerade läsningarna och skrivningarna i Service Fabric. Service Fabric stöder också programbearbetningspipelor, där resultaten måste vara tillförlitliga och föras vidare till nästa bearbetningsfas utan förlust. Dessa rörledningar omfattar transaktions- och finanssystem, där datakonsekvens och beräkningsgarantier är nödvändiga.

    Kunder som har byggt affärsarbetsflöden tjänster inkluderar [Zeiss Group,](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) [Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric), och [Société General](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Beräkning av data**: Med Service Fabric kan du skapa tillståndskänsliga program som utför intensiv databeräkning. Service Fabric möjliggör samlokalisering av bearbetning (beräkning) och data i applikationer. 

   Normalt, när ditt program kräver åtkomst till data, nätverksfördröjning som är associerad med en extern datacache eller lagringsnivå begränsar beräkningstiden. Tillståndskänsliga Service Fabric-tjänster eliminerar svarstiden, vilket möjliggör mer optimerade läsningar och skrivningar.

   Tänk dig till exempel ett program som utför nästan realtidsrekommendationsval för kunder, med ett tidskrav på mindre än 100 millisekunder. Svarstiden och prestandaegenskaperna hos Service Fabric-tjänster ger användaren en responsiv upplevelse jämfört med standardimplementeringsmodellen för att behöva hämta nödvändiga data från fjärrlagring. Systemet är mer responsivt eftersom beräkningen av rekommendationsval samlokaliseras med data och regler.

    Kunder som har byggt beräkningstjänster inkluderar [Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) och [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Mycket tillgängliga tjänster:** Service Fabric ger snabb redundans genom att skapa flera sekundära tjänstrepliker. Om en nod, process eller enskild tjänst går ner på grund av maskinvara eller annat fel, befordras en av de sekundära replikerna till en primär replik med minimal förlust av tjänst.

* **Skalbara tjänster**: Enskilda tjänster kan partitioneras, vilket gör att tillståndet kan skalas ut över klustret. Individuella tjänster kan också skapas och tas bort i farten. Du kan skala ut tjänster från några instanser på några få noder till tusentals instanser på många noder och sedan skala in dem igen efter behov. Du kan använda Service Fabric för att skapa dessa tjänster och hantera deras fullständiga livscykel.

## <a name="application-design-case-studies"></a>Ansökan design fallstudier

Fallstudier som visar hur Service Fabric används för att utforma program publiceras på [kundberättelser](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) och [mikrotjänster på](https://azure.microsoft.com/solutions/microservice-applications/) Azure-webbplatser.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Utforma program som består av tillståndslösa och tillståndskänsliga mikrotjänster

Att skapa program med Azure Cloud Services-arbetsroller är ett exempel på en tillståndslös tjänst. Tillståndskänsliga mikrotjänster behåller däremot sitt auktoritära tillstånd utöver begäran och dess svar. Den här funktionen ger hög tillgänglighet och konsekvens i tillståndet genom enkla API:er som tillhandahåller transaktionsgarantier som backas upp av replikering.

Tillståndskänsliga tjänster i Service Fabric ger hög tillgänglighet till alla typer av program, inte bara databaser och andra datalager. Detta är en naturlig utveckling. Program har redan flyttats från att använda rent relationsdatabaser för hög tillgänglighet till NoSQL-databaser. Nu kan programmen själva få sitt "heta" tillstånd och data hanterade inom dem för ytterligare prestandavinster utan att offra tillförlitlighet, konsekvens eller tillgänglighet.

När du skapar program som består av mikrotjänster har du vanligtvis en kombination av tillståndslösa webbappar (som ASP.NET och Node.js) som anropar tillståndslösa och tillståndskänsliga mellannivåtjänster på företag. Apparna och tjänsterna distribueras alla i samma Service Fabric-kluster via distributionskommandona för Service Fabric. Var och en av dessa tjänster är oberoende när det gäller skala, tillförlitlighet och resursanvändning. Detta oberoende förbättrar smidighet och flexibilitet i utveckling och livscykelhantering.

Tillståndskänsliga mikrotjänster förenklar programdesignefter att de tar bort behovet av ytterligare köer och cacheminnen som traditionellt har krävts för att åtgärda tillgänglighets- och svarstidskraven för rent tillståndslösa program. Eftersom tillståndskänsliga tjänster har hög tillgänglighet och låg latens finns det färre detaljer att hantera i ditt program.

Följande diagram illustrerar skillnaderna mellan att utforma ett program som är tillståndslöst och ett program som är tillståndskänsligt. Genom att dra nytta av [programmeringsmodellerna Reliable Services](service-fabric-reliable-services-introduction.md) och [Reliable Actors](service-fabric-reliable-actors-introduction.md) minskar tillståndskänsliga tjänster programkomplexiteten samtidigt som de uppnår hög genomströmning och låg latens.

Här är ett exempelprogram som använder ![tillståndslösa tjänster: Program som använder tillståndslösa tjänster][Image1]

Här är ett exempel program som ![använder tillståndskänsliga tjänster: Program som använder tillståndskänsliga tjänster][Image2]

## <a name="next-steps"></a>Nästa steg

* Kom igång med att bygga tillståndslösa och tillståndskänsliga tjänster med service fabric [reliable services](service-fabric-reliable-services-quick-start.md) och reliable actors-programmeringsmodeller. [Reliable Actors](service-fabric-reliable-actors-get-started.md)
* Besök Azure Architecture Center för vägledning om [hur du skapar mikrotjänster på Azure](https://docs.microsoft.com/azure/architecture/microservices/).
* Gå till [Azure Service Fabric-program och metodtips](service-fabric-best-practices-overview.md) för klusterdesign.

* Se även:
  * [Förstå mikrotjänster](service-fabric-overview-microservices.md)
  * [Definiera och hantera tjänsttillstånd](service-fabric-concepts-state.md)
  * [Tillgänglighet för tjänster](service-fabric-availability-services.md)
  * [Skala tjänster](service-fabric-concepts-scalability.md)
  * [Partition tjänster](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png

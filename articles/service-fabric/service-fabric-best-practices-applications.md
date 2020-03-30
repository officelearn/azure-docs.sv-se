---
title: Metodtips för azure service fabric-programdesign
description: Metodtips och designöverväganden för att utveckla program och tjänster med Hjälp av Azure Service Fabric.
author: markfussell
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 876980bd6a59bace9ab4e490358964d19fa52c7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586095"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Metodtips för azure service fabric-programdesign

Den här artikeln innehåller vägledning om bästa praxis för att skapa program och tjänster på Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Bekanta dig med Service Fabric
* Läs [så du vill veta om Service Fabric?](service-fabric-content-roadmap.md)
* Läs om [programscenarier för Service Fabric](service-fabric-application-scenarios.md).
* Förstå programmeringsmodellvalen genom att läsa [Service Fabric-programmeringsmodellöversikt](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Vägledning för programdesign
Bekanta dig med den [allmänna arkitekturen](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) i Service Fabric-applikationer och deras [designöverväganden](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Välj en API-gateway
Använd en API-gatewaytjänst som kommunicerar till backend-tjänster som sedan kan skalas ut. De vanligaste API-gatewaytjänsterna som används är:

- [Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), som är [integrerad med Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) eller [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/), med hjälp av [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) för att läsa från Event Hub-partitioner.
- [Træfik omvänd proxy](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/), med hjälp av [Azure Service Fabric-providern](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Azure Application Gateway är inte direkt integrerat med Service Fabric. Azure API Management är vanligtvis det självklara valet.
- Din egen specialbyggda ASP.NET Core-webbprogramgateway. [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore)

### <a name="stateless-services"></a>Statslösa tjänster
Vi rekommenderar att du alltid börjar med att skapa tillståndslösa tjänster med hjälp av [tillförlitliga tjänster](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) och lagringstillstånd i en Azure-databas, Azure Cosmos DB eller Azure Storage. Externaliserat tillstånd är den mer välbekanta metoden för de flesta utvecklare. Med den här metoden kan du också dra nytta av frågefunktionerna i butiken.  

### <a name="when-to-use-stateful-services"></a>När ska tillståndskänsliga tjänster användas
Överväg tillståndskänsliga tjänster när du har ett scenario för låg latens och måste hålla data nära beräkningen. Några exempel på scenarier är digitala tvillingenheter i inbyggda, speltillstånd, sessionstillstånd, cachelagring av data från en databas och tidskrävande arbetsflöden för att spåra samtal till andra tjänster.

Bestäm tidsramen för datalagring:

- **Cachelagrade data**. Använd cachelagring när svarstid till externa butiker är ett problem. Använd en tillståndskänslig tjänst som din egen datacache eller överväg att använda [den distribuerade cachen för socreate-tjänst](https://github.com/SoCreate/service-fabric-distributed-cache)för öppen källkod. I det här fallet behöver du inte vara orolig om du förlorar alla data i cacheminnet.
- **Tidsbundna data**. I det här fallet måste du hålla data nära att beräkna under en tidsperiod för svarstid, men du har råd att förlora data i en *katastrof*. I många IoT-lösningar måste data till exempel vara nära att beräknas, till exempel när medeltemperaturen under de senaste dagarna beräknas, men om dessa data går förlorade är de specifika datapunkter som registreras inte så viktiga. I det här scenariot bryr du dig också vanligtvis inte om att säkerhetskopiera de enskilda datapunkterna. Du säkerhetskopierar bara beräknade medelvärden som skrivs regelbundet till extern lagring.  
- **Långsiktiga data**. Tillförlitliga samlingar kan lagra dina data permanent. Men i det här fallet måste du [förbereda för haveriberedskap](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), inklusive [konfigurera periodiska principer](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) för säkerhetskopiering för dina kluster. I själva verket konfigurerar du vad som händer om klustret förstörs i en katastrof, där du skulle behöva skapa ett nytt kluster och hur du distribuerar nya programinstanser och återställer från den senaste säkerhetskopian.

Spara kostnader och förbättra tillgängligheten:
- Du kan minska kostnaderna genom att använda tillståndskänsliga tjänster eftersom du inte ådrar dig dataåtkomst och transaktionskostnader från fjärrarkivet och eftersom du inte behöver använda en annan tjänst, till exempel Azure Cache för Redis.
- Det är dyrt att använda tillståndskänsliga tjänster främst för lagring och inte för beräkning, och vi rekommenderar det inte. Tänk på tillståndskänsliga tjänster som beräkning med billig lokal lagring.
- Genom att ta bort beroenden för andra tjänster kan du förbättra tjänstens tillgänglighet. Hantera tillstånd med HA i klustret isolerar dig från andra driftstopp eller svarstidsproblem.

## <a name="how-to-work-with-reliable-services"></a>Så här arbetar du med reliable services
Med Service Fabric Reliable Services kan du enkelt skapa tillståndslösa och tillståndskänsliga tjänster. Mer information finns i [introduktionen till Tillförlitliga tjänster](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Hedra alltid [annulleringstoken](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) i `RunAsync()` metoden för `ChangeRole()` tillståndslösa och tillståndskänsliga tjänster och metoden för tillståndskänsliga tjänster. Om du inte gör det vet Service Fabric inte om din tjänst kan stängas. Om du till exempel inte respekterar annulleringstoken kan mycket längre uppgraderingstider för program inträffa.
-   Öppna och stäng [kommunikationslyssnare](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) i tid och hedra annulleringstokens.
-   Blanda aldrig synkroniseringskod med async-kod. Använd `.GetAwaiter().GetResult()` till exempel inte i dina asynkronanrop. Använd async *hela vägen* genom samtalsstacken.

## <a name="how-to-work-with-reliable-actors"></a>Hur man arbetar med pålitliga aktörer
Med Service Fabric Reliable Actors kan du enkelt skapa tillståndskänsliga, virtuella aktörer. Mer information finns i [introduktionen till Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Allvarligt överväga att använda pub / sub meddelanden mellan dina aktörer för att skala ditt program. Verktyg som tillhandahåller den här tjänsten inkluderar [socreate-servicen Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) och [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Gör aktörstillståndet så [detaljerat som möjligt](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Hantera [skådespelarens livscykel](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Ta bort skådespelare om du inte ska använda dem igen. Det är särskilt viktigt att ta bort onödiga aktörer när du använder [leverantören av flyktigt tillstånd](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)eftersom allt tillstånd lagras i minnet.
- På grund av deras [tur-baserade samtidighet](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency), aktörer används bäst som oberoende objekt. Skapa inte diagram över synkron metodanrop med flera aktörer (som var och en troligen blir ett separat nätverksanrop) eller skapa cirkelaktörsbegäranden. Dessa kommer att avsevärt påverka prestanda och skala.
- Blanda inte synkroniseringskod med async-kod. Använd async konsekvent för att förhindra prestandaproblem.
- Gör inte långvariga samtal i skådespelare. Långvariga samtal blockerar andra samtal till samma aktör på grund av den turbaserade samtidigheten.
- Om du kommunicerar med andra tjänster med hjälp av [Service Fabric-ommotning](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) och du skapar en `ServiceProxyFactory`skapar du fabriken på [aktörsservicenivå](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) och *inte* på aktörsnivå.


## <a name="application-diagnostics"></a>Programdiagnostik
Var noggrann med att lägga till [programloggning](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) i servicesamtal. Det hjälper dig att diagnostisera scenarier där tjänster ringer varandra. När A anropar B anropar C anropar till exempel D kan samtalet misslyckas var som helst. Om du inte har tillräckligt med loggning är det svårt att diagnostisera fel. Om tjänsterna loggar för mycket på grund av samtalsvolymer måste du åtminstone logga fel och varningar.

## <a name="iot-and-messaging-applications"></a>IoT- och meddelandeprogram
När du läser meddelanden från [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) eller [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)använder du [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). ServiceFabricProcessor integrerar med Service Fabric Reliable Services för att upprätthålla lästillståndet från händelsenavpartitionerna och skickar nya meddelanden till dina tjänster via `IEventProcessor::ProcessEventsAsync()` metoden.


## <a name="design-guidance-on-azure"></a>Designvägledning på Azure
* Besök [Azure-arkitekturcentret](https://docs.microsoft.com/azure/architecture/microservices/) för designvägledning om [hur du skapar mikrotjänster på Azure](https://docs.microsoft.com/azure/architecture/microservices/).

* Besök [Kom igång med Azure for Gaming](https://docs.microsoft.com/gaming/azure/) om du vill ha vägledning om hur du använder Service Fabric i [speltjänster.](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

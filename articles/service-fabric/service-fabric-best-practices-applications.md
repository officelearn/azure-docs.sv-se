---
title: Azure Service Fabric-program Metodtips för design | Microsoft Docs
description: Metodtips för utveckling av Service Fabric-program.
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/26/2019
ms.author: msfussell
ms.openlocfilehash: 55f043effc7cdb102acea856e89c58f660d0cde5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237754"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric-program Metodtips för design

Den här artikeln innehåller bästa praxis riktlinjer för att skapa program och tjänster på Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Bekanta dig med Service Fabric
* [Så att du vill lära dig om Service Fabric?](service-fabric-content-roadmap.md)
* Läs mer om [scenarier i Service Fabric-program](service-fabric-application-scenarios.md)
* Förstå programming model-val med [Service Fabric programming model-översikt](service-fabric-choose-framework.md)



## <a name="application-design-guidance"></a>Vägledning för design
Bekanta dig med den [allmän arkitektur](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) i ett Service Fabric-program och dess [designöverväganden](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Välj en API-gateway
Använd en API-gateway-tjänst som kommunicerar med backend-tjänster som sedan kan skalas upp. De vanligaste API gateway tjänster som används är:

- [Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), vilket är [integreras med Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)
- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) eller [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) med hjälp av den [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) att läsa från Event Hub partitioner
- [Træfik omvänd proxy](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/) med hjälp av den [Azure Service Fabric-providern](https://docs.traefik.io/configuration/backends/servicefabric/)
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) Obs!: det här inte är direkt integrerad med Service Fabric och Azure API Management är vanligtvis ett önskade val
- Skapa din egen [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) web application gateway

### <a name="choose-stateless-services"></a>Välj tillståndslösa tjänster
Vi rekommenderar att du alltid börjar med att skapa tillståndslösa tjänster med hjälp av [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) lagrar tillstånd i en Azure Database, Azure CosmosDB eller Azure storage. Med tillstånd externalized är mer bekant metoden för de flesta utvecklare och gör att du kan också dra nytta av frågefunktioner i store.  

### <a name="when-to-choose-stateful-services"></a>När du ska välja tillståndskänsliga tjänster
Överväg att tillståndskänsliga tjänster när du har ett scenario för låg latens och vill behålla data nära beräkningarna. Några exempel är digital tvilling IoT-enheter, spelstatus, sessionstillstånd, cachelagra data från en databas och tidskrävande arbetsflöden för att spåra anrop till andra tjänster.

Bestäm tidsperiod för kvarhållning av data:

- Cachelagrade data – du använder cachelagring där svarstid till externa datalager är ett problem. Använda en tillståndskänslig tjänst som dina egna data cachelagra eller Överväg att använda den [öppen källkod SoCreate service-fabric-distribuerad-cache](https://github.com/SoCreate/service-fabric-distributed-cache). I det här scenariot kan du förlorar alla data i cacheminnet och det spelar ingen roll.
- Tidsbundna data – du behöver data nära till hands om du vill beräkna för svarstid för en viss tidsperiod, men dessa data har råd att gå förlorad i en *haveriberedskap* scenario. Till exempel i många IoT-lösningar som data måste vara nära är beräkning, till exempel beräkna genomsnittet under de senaste dagarna, men om det här data går förlorade, sedan de specifika datapunkter registreras inte så viktiga. Även i det här scenariot bryr du vanligtvis sig inte om säkerhetskopiering för de enskilda datapunkterna, bara av de beräknade genomsnittliga värden som skrivs till extern lagring med jämna mellanrum.  
- Långsiktiga data - tillförlitliga samlingar kan lagra dina data permanent. I det här fallet du måste dock [förbereda för katastrofåterställning](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery) inklusive [konfigurera principer för regelbunden säkerhetskopiering](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) för dina kluster. Du konfigurerar i praktiken vad händer om klustret förstörs i ett scenario med katastrofåterställning, där du behöver skapa ett nytt kluster och hur du distribuerar nya instanser av programmet och återställa från den senaste säkerhetskopian.

Spara pengar och förbättra tillgängligheten:
- Du kan minska kostnaderna med tillståndskänsliga tjänster eftersom du inte någon data kostnader för åtkomst och transaktioner från arkivet för fjärråtkomst och behöver inte använda en annan tjänst, till exempel Azure Redis.
- Det är dyrt och rekommenderas inte att använda tillståndskänsliga tjänster främst för lagring och inte för beräkning. Överväg att tillståndskänsliga tjänster som beräkning med billiga lokal lagring.
- Du kan förbättra tillgängligheten till din tjänst genom att ta bort beroenden av andra tjänster. Med tillstånd som hanteras med hög tillgänglighet i klustret isolerar du från andra service stilleståndstider eller problem med nätverkssvarstiden.

## <a name="how-to-properly-work-with-reliable-services"></a>Så här fungerar korrekt med Reliable Services
Reliable Services kan du enkelt skapa tillståndslösa och tillståndskänsliga tjänster. Läs den [introduktion till Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)
- Respektera alltid den [annullering token](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) i den `RunAsync()` metod för tillståndslösa och tillståndskänsliga tjänster och `ChangeRole()` metod för tillståndskänsliga tjänster. Utan detta vet Service Fabric inte om din tjänst kan stängas. Inte respekterar annullering token kan exempelvis resultera i mycket längre tid programmet uppgradera gånger.
-   Öppna och stänga [Kommunikationslyssnarna](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) i tid och följer annulleringstoken.
-   Aldrig blanda Synkronisera kod med async-kod. Använd till exempel inte `.GetAwaiter().GetResult()` i dina asynkrona anrop; den måste vara async *ända* via anropsstacken.

## <a name="how-to-properly-work-with-reliable-actors"></a>Så här fungerar korrekt med Reliable Actors
Reliable Actors kan du enkelt skapa tillståndskänsliga, virtuell aktörer. Läs den [introduktion till Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)

- Starkt överväga att använda pub/sub-meddelandetjänst för kommunikation mellan dina aktörer för att skala programmet. Till exempel den [öppen källkod SoCreate pub/sub](https://service-fabric-pub-sub.socreate.it/) eller [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Kontrollera aktörstillstånd som [detaljerade som möjligt](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Hantera den [aktörs livscykeln](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Ta bort aktörer om du inte planerar att använda dem någonsin igen. Detta gäller särskilt när du använder den [VolatileState provider](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication) som alla tillstånd lagras i minnet.
- På grund av deras [aktivera baserat samtidighet](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency) aktörer är bäst att använda som oberoende objekt. Inte skapa diagram över flera aktör, synkron metodanrop (som mest sannolikt blir ett separat nätverk-anrop) eller har förfrågningar som cirkelformad actor; Dessa är av stor betydelse prestanda och skalning.
- Blanda inte synkronisera kod med async-kod. den måste vara async ända, för att förhindra prestandaproblem.
- Göra inte tidskrävande anrop i actors, så blockeras andra anrop till samma aktören, på grund av samtidighet tur-baserade.
- Om kommunikation med andra tjänster som använder [fjärrkommunikation för Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) och du skapar en `ServiceProxyFactory`, skapa fabriken på den [aktörstjänsten](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) nivå och *inte*på aktören nivå.


## <a name="application-diagnostics"></a>Programdiagnostik
- Vara noggrann lägga [programloggning](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) i tjänstanrop. Det hjälper dig i diagnostik av scenarier där tjänster anropar varandra. Till exempel när A -> B -> C -> D anropet kan misslyckas var som helst; Om det inte finns tillräckligt med loggning, är det svårt att diagnostisera. Om tjänsterna loggar för mycket på grund av samtalsvolymer, minst måste du logga fel och varningar.

## <a name="iot-and-messaging-applications"></a>IoT och meddelandeprogram
När du läser meddelanden från [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) eller [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) använder [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) som kan integreras med Service Fabric Reliable Services för att underhålla den tillståndet för läsning från Event Hub skapar partitioner och skickar nya meddelanden till dina tjänster via den `IEventProcessor::ProcessEventsAsync()` metoden.


## <a name="design-guidance-on-azure"></a>Designriktlinjer på Azure
* Gå till den [Azure arkitekturcenter](https://docs.microsoft.com/azure/architecture/microservices/) för designvägledning på [skapa mikrotjänster på Azure](https://docs.microsoft.com/azure/architecture/microservices/)

* Besök [Kom igång med Azure för spel](https://docs.microsoft.com/gaming/azure/) för designvägledning på [med Service Fabric i speltjänster](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

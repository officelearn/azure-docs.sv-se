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
ms.date: 06/18/2019
ms.author: msfussell
ms.openlocfilehash: 30d696337061ade6b79c7ec0e4c4de67651f0dad
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203446"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric-program Metodtips för design

Den här artikeln innehåller bästa praxis riktlinjer för att skapa program och tjänster på Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Bekanta dig med Service Fabric
* Läs den [så att du vill lära dig om Service Fabric?](service-fabric-content-roadmap.md) artikeln.
* Läs mer om [Service Fabric-Programscenarier](service-fabric-application-scenarios.md).
* Förstå programming model valen genom att läsa [Service Fabric programming model översikt](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Vägledning för design
Bekanta dig med den [allmän arkitektur](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) av Service Fabric-program och deras [designöverväganden](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Välj en API-gateway
Använd en API-gateway-tjänst som kommunicerar med backend-tjänster som sedan kan skalas upp. De vanligaste API gateway tjänster som används är:

- [Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), vilket är [integreras med Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) eller [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)med hjälp av den [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) att läsa från Event Hub-partitioner.
- [Træfik omvänd proxy](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)med hjälp av den [Azure Service Fabric-providern](https://docs.traefik.io/configuration/backends/servicefabric/).
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Azure Application Gateway inte är direkt integrerat med Service Fabric. Azure API Management är vanligtvis den önskade val.
- Din egen anpassade [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) web application gateway.

### <a name="stateless-services"></a>Tillståndslösa tjänster
Vi rekommenderar att du alltid börjar genom att skapa tillståndslösa tjänster med hjälp av [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) och lagrar tillstånd i en Azure database, Azure Cosmos DB eller Azure Storage. Externalized tillstånd är mer bekant metoden för de flesta utvecklare. Den här metoden kan du dra nytta av frågefunktioner i store.  

### <a name="when-to-use-stateful-services"></a>När du ska använda tillståndskänsliga tjänster
Överväg att tillståndskänsliga tjänster när du har ett scenario för låg latens och vill behålla data nära beräkningarna. Några exempelscenarier omfattar digital tvilling IoT-enheter, spelstatus, sessionstillstånd för cachelagring av data från en databas och tidskrävande arbetsflöden för att spåra anrop till andra tjänster.

Besluta om tidsperiod för kvarhållning av data:

- **Cachelagrade data**. Använd cachelagring när svarstiden till externa datalager är ett problem. Använda en tillståndskänslig tjänst som din egen Datacachen eller överväger att använda den [öppen källkod SoCreate Service Fabric distribuerad Cache](https://github.com/SoCreate/service-fabric-distributed-cache). I det här scenariot behöver du inte bry sig om du förlorar alla data i cacheminnet.
- **Tidsbundna data**. I det här scenariot som du behöver för att Stäng data för en viss tidsperiod för svarstid, men du har råd att förlora data i en *haveriberedskap*. Till exempel i många IoT-lösningar, data måste vara nära databearbetning, till exempel när medeltemperaturen under de senaste dagarna beräknas, men om dessa data förloras måste specifika datapunkter registreras är inte det viktigt. Även i det här scenariot du inte vanligtvis bryr dig om att säkerhetskopiera enskilda datapunkter. Du bara säkerhetskopiera beräknade genomsnittliga värden som skrivs med jämna mellanrum till extern lagring.  
- **Långsiktiga data**. Tillförlitliga samlingar kan lagra dina data permanent. Men i det här fallet måste du [förbereda för katastrofåterställning](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), inklusive [konfigurera principer för regelbunden säkerhetskopiering](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) för dina kluster. Du konfigurerar i praktiken vad händer om klustret förstörs i en katastrof, där du behöver skapa ett nytt kluster och hur du distribuerar nya instanser av programmet och återställa från den senaste säkerhetskopian.

Spara pengar och förbättra tillgängligheten:
- Du kan minska kostnaderna genom att använda tillståndskänsliga tjänster eftersom du inte drar på dig åtkomst till data och kostnader för transaktioner från arkivet för fjärråtkomst och eftersom du inte behöver använda en annan tjänst, som Azure Cache för Redis.
- Det är dyrt att använda tillståndskänsliga tjänster främst för lagring och inte för beräkning och det rekommenderas inte. Tänk på tillståndskänsliga tjänster som beräkning med billiga lokal lagring.
- Du kan förbättra tillgängligheten till din tjänst genom att ta bort beroenden av andra tjänster. Hantera tillstånd med hög tillgänglighet i klustret isolerar du från andra service stilleståndstider eller problem med nätverkssvarstiden.

## <a name="how-to-work-with-reliable-services"></a>Hur du arbetar med Reliable Services
Service Fabric Reliable Services kan du enkelt skapa tillståndslösa och tillståndskänsliga tjänster. Mer information finns i den [introduktion till Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Respektera alltid den [annullering token](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) i den `RunAsync()` metod för tillståndslösa och tillståndskänsliga tjänster och `ChangeRole()` metod för tillståndskänsliga tjänster. Om du inte vet inte Service Fabric om din tjänst kan stängas. Mycket längre tid programmet uppgradera gånger kan exempelvis uppstå om du inte följer annullering-token.
-   Öppna och stänga [kommunikationslyssnarna](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) i tid, och följer annulleringstoken.
-   Aldrig blanda Synkronisera kod med async-kod. Till exempel inte använda `.GetAwaiter().GetResult()` i dina asynkrona anrop. Använda async *ända* via anropsstacken.

## <a name="how-to-work-with-reliable-actors"></a>Hur du arbetar med Reliable Actors
Service Fabric Reliable Actors kan du enkelt skapa tillståndskänsliga, virtuell aktörer. Mer information finns i den [introduktion till tillförlitliga aktörer](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Allvarligt Överväg att använda pub/sub-meddelandetjänst för kommunikation mellan dina aktörer för att skala ditt program. Verktyg som ger den här tjänsten har den [öppen källkod SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) och [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Kontrollera aktörstillstånd som [detaljerade som möjligt](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Hantera den [aktörs livscykel](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Ta bort aktörer om du inte planerar att använda dem igen. Ta bort onödiga aktörer är särskilt viktigt när du använder den [föränderliga tillståndsprovider](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), eftersom alla tillstånd lagras i minnet.
- Grund av deras [tur-baserade samtidighet](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency), aktörer är bäst att använda som oberoende objekt. Inte skapa diagram över flera aktör, synkron metodanrop (som mest sannolikt blir ett separat nätverk-anrop) eller skapa cirkelformad actor begäranden. Dessa är av stor betydelse prestanda och skalning.
- Blanda inte synkronisera kod med async-kod. Använda async konsekvent för att förhindra prestandaproblem.
- Gör inte tidskrävande anrop i aktörer. Långvariga anrop blockerar andra anrop till samma aktören, på grund av samtidighet tur-baserade.
- Om du kommunicerar med andra tjänster med hjälp av [fjärrkommunikation för Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) och du skapar en `ServiceProxyFactory`, skapa fabriken på den [aktörstjänsten](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) nivå och *inte* på nivån aktör.


## <a name="application-diagnostics"></a>Programdiagnostik
Vara noggrann om att lägga till [programloggning](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) i tjänstanrop. Det hjälper dig att diagnostisera scenarier där-tjänster som anropar varandra. Till exempel när en anropar B anrop C anropar D, misslyckas anropet, var som helst. Om du inte har tillräckligt med loggning, är det svårt att diagnostisera fel. Om tjänsterna loggar för mycket på grund av samtalsvolymer, måste du minst logga fel och varningar.

## <a name="iot-and-messaging-applications"></a>IoT och meddelandeprogram
När du läser meddelanden från [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) eller [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/), använda [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). ServiceFabricProcessor kan integreras med Service Fabric Reliable Services för att underhålla tillståndet för läsning från event hub skapar partitioner och skickar nya meddelanden till dina tjänster via den `IEventProcessor::ProcessEventsAsync()` metoden.


## <a name="design-guidance-on-azure"></a>Designriktlinjer på Azure
* Gå till den [Azure arkitekturcenter](https://docs.microsoft.com/azure/architecture/microservices/) för designvägledning på [skapa mikrotjänster på Azure](https://docs.microsoft.com/azure/architecture/microservices/).

* Besök [Kom igång med Azure för spel](https://docs.microsoft.com/gaming/azure/) för designvägledning på [med Service Fabric i speltjänster](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

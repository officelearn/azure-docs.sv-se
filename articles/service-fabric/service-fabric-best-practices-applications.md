---
title: Metod tips för Azure Service Fabric program design | Microsoft Docs
description: Metod tips för att utveckla Service Fabric-program.
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
ms.author: mfussell
ms.openlocfilehash: eec5daf0100d527886a508f5adbdb2b0e3010b09
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262262"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Metod tips för Azure Service Fabric program design

Den här artikeln innehåller metod tips för att skapa program och tjänster på Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Bekanta dig med Service Fabric
* Läs [mer om Service Fabric?](service-fabric-content-roadmap.md) -artikeln.
* Läs om [Service Fabric program scenarier](service-fabric-application-scenarios.md).
* Förstå alternativen för programmerings modell genom att läsa [Översikt över Service Fabric programmerings modellen](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Vägledning för program design
Bekanta dig med den [allmänna arkitekturen](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) i Service Fabric program och deras [design överväganden](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Välj en API-Gateway
Använd en API Gateway-tjänst som kommunicerar med Server dels tjänster som sedan kan skalas ut. De vanligaste API Gateway-tjänsterna som används är:

- [Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), som är [integrerat med Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) eller [Azure-Event Hubs](https://docs.microsoft.com/azure/event-hubs/), med hjälp av [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) för att läsa från Event Hub-partitioner.
- [Træfik omvänd proxy](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)med [Azure Service Fabric](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/)-providern.
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Azure Application Gateway är inte direkt integrerad med Service Fabric. Azure API Management är vanligt vis det bästa valet.
- Din egen anpassade [ASP.net Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) Web Application Gateway.

### <a name="stateless-services"></a>Tillstånds lösa tjänster
Vi rekommenderar att du alltid börjar med att skapa tillstånds lösa tjänster genom att använda [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) och lagra tillstånd i en Azure-databas, Azure Cosmos DB eller Azure Storage. Externt läge är den mer välbekanta metoden för de flesta utvecklare. Med den här metoden kan du också dra nytta av fråge funktioner i butiken.  

### <a name="when-to-use-stateful-services"></a>När du ska använda tillstånds känsliga tjänster
Överväg tillstånds känsliga tjänster när du har ett scenario för låg latens och måste hålla data nära beräkningen. Några exempel scenarier är IoT Digital-enheter, spel status, sessionstillstånd, cachelagring av data från en databas och långvariga arbets flöden som spårar anrop till andra tjänster.

Bestäm tids perioden för datakvarhållning:

- **Cachelagrade data**. Använd cachelagring när svars tid till externa butiker är ett problem. Använd en tillstånds känslig tjänst som din egen datacache eller Överväg att använda [SoCreate för öppen källkod Service Fabric distribuerat cacheminne](https://github.com/SoCreate/service-fabric-distributed-cache). I det här scenariot behöver du inte oroa dig om du förlorar alla data i cacheminnet.
- **Tidsbegränsade data**. I det här scenariot måste du hålla data nära beräkning under en viss tids period, men du får råd att förlora data i en *katastrof*. I många IoT-lösningar måste till exempel data ligga nära beräkning, till exempel när genomsnitts temperaturen under de senaste dagarna beräknas, men om dessa data går förlorade är de data punkter som registreras inte viktiga. I det här scenariot är det också viktigt att du inte säkerhetskopierar de enskilda data punkterna. Du säkerhetskopierar bara beräknade medelvärden som regelbundet skrivs till extern lagring.  
- **Långsiktiga data**. Tillförlitliga samlingar kan lagra dina data permanent. Men i det här fallet måste du [förbereda för haveri beredskap](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), inklusive [Konfigurera principer för regelbunden säkerhets kopiering](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) för klustren. I praktiken konfigurerar du vad som händer om klustret förstörs i en katastrof, där du skulle behöva skapa ett nytt kluster och hur du distribuerar nya program instanser och återställer från den senaste säkerhets kopian.

Spara kostnader och förbättra tillgängligheten:
- Du kan minska kostnaderna genom att använda tillstånds känsliga tjänster eftersom du inte debiteras kostnader för data åtkomst och transaktioner från fjärrarkivet, och eftersom du inte behöver använda någon annan tjänst, t. ex. Azure cache för Redis.
- Att använda tillstånds känsliga tjänster främst för lagring och inte för beräkning är kostsamt, och vi rekommenderar inte det. Tänk på tillstånds känsliga tjänster som beräkning med billig lokal lagring.
- Genom att ta bort beroenden för andra tjänster kan du förbättra tjänstens tillgänglighet. Genom att hantera tillstånd med HA i klustret isoleras du från andra avbrott i tjänsten eller svars tider.

## <a name="how-to-work-with-reliable-services"></a>Så här arbetar du med Reliable Services
Med Service Fabric Reliable Services kan du enkelt skapa tillstånds lösa och tillstånds känsliga tjänster. Mer information finns i [Introduktion till Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Följ alltid token för `RunAsync()` avbrott i metoden för tillstånds lösa och tillstånds känsliga `ChangeRole()` tjänster och metoden för tillstånds känsliga tjänster. [](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) Om du inte gör det vet Service Fabric inte om tjänsten kan stängas. Om du till exempel inte följer den token för avbrytande token kan mycket längre program uppgraderings tider uppstå.
-   Öppna och Stäng [kommunikations lyssnare](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) i tid och följ de token för avbrutna.
-   Blanda aldrig synkronisera kod med asynkron kod. Använd `.GetAwaiter().GetResult()` till exempel inte i dina asynkrona anrop. Använd asynkront *hela vägen* via anrops stacken.

## <a name="how-to-work-with-reliable-actors"></a>Så här arbetar du med Reliable Actors
Med Service Fabric Reliable Actors kan du enkelt skapa tillstånds känsliga, virtuella aktörer. Mer information finns i [Introduktion till Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Vi rekommenderar att du använder pub/sub-meddelanden mellan dina aktörer för att skala ditt program. Verktyg som tillhandahåller den här tjänsten omfattar [SoCreate med öppen källkod Service Fabric pub/sub](https://service-fabric-pub-sub.socreate.it/) och [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Gör aktörens tillstånd så [detaljerat som möjligt](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Hantera [aktörens livs cykel](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Ta bort aktörer om du inte kommer att använda dem igen. Att ta bort onödiga aktörer är särskilt viktigt när du använder den [temporära tillstånds leverantören](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), eftersom all status är lagrad i minnet.
- På grund av [](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)deras egna samtidiga samtidighet används aktörerna bäst som oberoende objekt. Skapa inte diagram av multi-skådespelare, synkrona metod anrop (var och en av de mest sannolika nätverks anropen) eller skapa förfrågningar om cirkulär aktör. Dessa påverkar avsevärt prestanda och skalning.
- Blanda inte synkronisera kod med asynkron kod. Använd asynkront för att förhindra prestanda problem.
- Gör inte långvariga anrop i aktörer. Med tids krävande anrop blockeras andra anrop till samma aktör, på grund av den omkopplade samtidigheten.
- Om du kommunicerar med andra tjänster genom att använda [Service Fabric fjärr kommunikation](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) och du skapar `ServiceProxyFactory`en skapar du fabriken på [aktörs service](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) nivå och *inte* på aktörs nivån.


## <a name="application-diagnostics"></a>Programdiagnostik
Var noggrann med att lägga till [program loggning](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) i tjänst anrop. Det hjälper dig att diagnostisera scenarier där tjänster kallar varandra. Om t. ex. ett anrop B anropar C-anrop, kan anropet gå sönder var som helst. Om du inte har tillräckligt med loggning är fel svårt att diagnostisera. Om tjänsterna loggar för mycket på grund av anrops volymer, se till att minst Logga fel och varningar.

## <a name="iot-and-messaging-applications"></a>IoT-och meddelande program
När du läser meddelanden från [azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) eller [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)använder du [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). ServiceFabricProcessor integreras med Service Fabric Reliable Services för att upprätthålla läsnings tillstånd från händelsehubben och push-överför nya meddelanden till dina tjänster via `IEventProcessor::ProcessEventsAsync()` metoden.


## <a name="design-guidance-on-azure"></a>Design rikt linjer för Azure
* Besök [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/microservices/) för utformnings vägledning om hur du [skapar mikrotjänster på Azure](https://docs.microsoft.com/azure/architecture/microservices/).

* Besök [komma igång med Azure för att få](https://docs.microsoft.com/gaming/azure/) [hjälp med att använda Service Fabric i spel tjänster](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

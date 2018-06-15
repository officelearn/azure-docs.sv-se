---
title: Läs Azure Service Fabric-terminologi | Microsoft Docs
description: En terminologi översikt över Service Fabric. Beskriver viktiga termer begrepp och termer som används i resten av dokumentationen.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: ryanwi
ms.openlocfilehash: e3da081f9b327031d6d1e0afd2f2fb52383bf933
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212076"
---
# <a name="service-fabric-terminology-overview"></a>Översikt över Service Fabric-terminologi
Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Den här artikeln beskrivs de termer som används av Service Fabric att du förstår de termer som används i dokumentationen.

Koncept i det här avsnittet beskrivs också i följande videor för Microsoft Virtual Academy: <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">viktiga begrepp</a>, <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965">designläge begrepp</a>, och <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">Runtime begrepp</a>.

## <a name="infrastructure-concepts"></a>Begrepp för infrastrukturen
**Klustret**: en nätverksansluten uppsättning virtuella eller fysiska datorer som din mikrotjänster distribueras och hanteras.  Kluster kan skalas upp till tusentals datorer.

**Noden**: en dator eller virtuell dator som ingår i ett kluster kallas en *nod*. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper som till exempel placeringsegenskaper. Varje dator eller virtuell dator har en Windows-tjänsten för automatisk start, `FabricHost.exe`, som startar vid start och startar sedan två körbara filer: `Fabric.exe` och `FabricGateway.exe`. Dessa två körbara filer som utgör noden. För att testa scenarier, du kan vara värd för flera noder i en enskild dator eller virtuell dator genom att köra flera instanser av `Fabric.exe` och `FabricGateway.exe`.

## <a name="application-concepts"></a>Program-begrepp
**Programtyp**: namn/version som tilldelats till en samling av tjänsttyper. Det har definierats i en `ApplicationManifest.xml` fil- och inbäddat i ett paket för programkatalogen. Katalogen kopieras sedan till Service Fabric-klustret avbildningsarkivet. Du kan sedan skapa en namngiven programmet från den här tillämpningstypen i klustret.

Läs den [programmodell](service-fabric-application-model.md) artikel för mer information.

**Programpaketet**: en diskkatalog som innehåller programtypen `ApplicationManifest.xml` fil. Refererar till servicepaket för varje typ av tjänst som utgör programtypen. Filerna i katalogen program paketet kopieras till Service Fabric-klustret avbildningsarkivet. Ett programpaket för en typ av e-program kan exempelvis innehålla referenser till ett kötjänsten paket, ett frontend-tjänst-paket och ett databastjänsten paket.

**Programmet heter**: när du kopierar ett programpaket till image store kan du skapa en instans av program i klustret. Du kan skapa en instans när du anger det programpaketet programtyp, genom att använda dess namn eller versionsnummer. Varje typ av programinstans tilldelas ett uniform resource identifier (URI)-namn som ser ut som: `"fabric:/MyNamedApp"`. Du kan skapa flera namngivna program från en enda programtyp inom ett kluster. Du kan också skapa namngivna program från annan programtyper. Varje namngivet program är oberoende av varandra hanterade och en ny version.      

**Typen tjänst**: namn/version som tilldelats kod paket, data-paket och konfigurationspaket för en tjänst. Service-typen är definierad i den `ServiceManifest.xml` filen och inbäddat i ett paket katalog. Paketet katalog sedan refereras av ett programpaket `ApplicationManifest.xml` fil. I kluster kan när du har skapat ett namngivet program, du skapa en namngiven tjänst från en av de programtyp tjänsttyper. Service-typen `ServiceManifest.xml` filen beskriver tjänsten.

Läs den [programmodell](service-fabric-application-model.md) artikel för mer information.

Det finns två typer av tjänster:

* **Tillståndslösa**: Använd tillståndslösa tjänsten när tjänstens beständiga tillstånd lagras i en extern lagringsenhet tjänst, till exempel Azure Storage, Azure SQL Database och Azure Cosmos DB. Använd en tillståndslös tjänst när tjänsten har ingen beständig lagring. Till exempel för en tjänst på Kalkylatorn där värden skickas till tjänsten, utförs en beräkning som använder dessa värden och sedan resultatet returneras.
* **Stateful**: Använd en tillståndskänslig service när du vill Service Fabric att hantera din tjänstens tillstånd via dess tillförlitliga samlingar eller Reliable Actors programmeringsmodeller. När du skapar en namngiven tjänst anger du hur många partitioner som du vill att sprida ditt tillstånd i för skalbarhet. Ange hur många gånger för att replikera ditt tillstånd mellan noder för tillförlitlighet. Varje namngivet tjänst har en enda primär replik och flera sekundära repliker. Du kan ändra din tjänsten tillstånd vid skrivning till den primära repliken. Service Fabric replikeras sedan det här tillståndet till de sekundära replikerna synkronisera din tillstånd. Service Fabric identifierar automatiskt när en primär replik misslyckas och främjar en befintlig sekundär replik till en primär replik. Service Fabric skapar sedan en ny sekundär replik.  

**Repliker eller instanser** avser kod (och tillstånd för tillståndskänsliga tjänster) av en tjänst som har distribuerats och körs. Se [repliker och instanser](service-fabric-concepts-replica-lifecycle.md).

**Omkonfiguration** refererar till processen för ändringar i uppsättningen av en tjänst. Se [omkonfiguration](service-fabric-concepts-reconfiguration.md).

**Tjänstpaket**: en diskkatalog som innehåller service-typen `ServiceManifest.xml` fil. Den här filen refererar till kod, statiska data och konfigurationspaket för tjänsttypen. Filerna i katalogen service paketet refereras av typen programmet `ApplicationManifest.xml` fil. Inget tjänstepaket kan till exempel referera till kod, statiska data och konfigurationspaket som utgör en databastjänst.

**Med namnet service**: när du skapar en namngiven program, du kan skapa en instans av en av dess tjänsttyper i klustret. Du kan ange tjänsttypen genom att använda dess namn/version. Varje typ tjänstinstans tilldelas ett URI-namn omfång under dess namngivna program-URI. Till exempel om du skapar en ”mindatabas” med namnet tjänsten inom en ”MyNamedApp” med namnet på programmet, URI: N ser ut som: `"fabric:/MyNamedApp/MyDatabase"`. Du kan skapa flera namngivna tjänster i en namngiven programmet. Varje tjänsten kan ha sin egen partitionsschema och instans eller replik räknar.

**Kodpaketet**: en diskkatalog som innehåller service-typen körbara filer, vanligtvis EXE/DLL-filer. Service-typen refererar till filer i katalogen kod paketet `ServiceManifest.xml` fil. När du skapar en tjänsten kopieras kodpaketet till noden eller noder som har markerats för att köra tjänsten. Sedan startar koden. Det finns två typer av kod paketet körbara filer:

* **Gästen körbara filer**: körbara filer som kör som-på värdens operativsystem (Windows eller Linux). Dessa körbara filer inte länka till eller referera till Service Fabric runtime filer och därför inte använda några Service Fabric programmeringsmodeller. Dessa körbara filer kan inte använda vissa Service Fabric-funktioner, till exempel namngivningstjänst för identifiering av slutpunkten. Gästen körbara filer kan inte rapportera belastning mätvärden som är specifika för varje service-instans.
* **Tjänsten värd körbara filer**: körbara filer som använder Service Fabric programmeringsmodeller genom att länka till Service Fabric runtime-filer, aktivering av Service Fabric-funktioner. En namngiven tjänstinstans slutpunkter kan registrera med Service Fabric-namngivningstjänst och kan också rapportera belastning mått.      

**Datapaketet**: en diskkatalog som innehåller service-typen statiska, skrivskyddad datafiler, vanligtvis foto, ljud-och videofiler. Service-typen refererar till filer i katalogen data paketet `ServiceManifest.xml` fil. När du skapar en tjänsten kopieras datapaketet till noden eller noder som har markerats för att köra tjänsten. Koden körs och kan nu komma åt datafilerna.

**Konfigurationspaketet**: en diskkatalog som innehåller service-typen statiska, skrivskyddad konfigurationsfiler, vanligtvis textfiler. Service-typen refererar till filer i paketet konfigurationskatalogen `ServiceManifest.xml` fil. När du skapar en tjänsten filerna i konfigurationspaketet är kopieras en eller flera noder som kör tjänsten. Därefter startar koden körs och kan nu åtkomst konfigurationsfilerna.

**Behållare**: som standard Service Fabric distribuerar och aktiverar tjänster som processer. Service Fabric kan också distribuera tjänster i behållaren bilder. Behållare är en virtualiseringsteknik som virtualiserar det underliggande operativsystemet från program. Ett program och dess runtime, beroenden och bibliotek körs i en behållare. Behållaren har full, privat åtkomst till vyn behållarens egna isolerade för konstruktionerna för operativsystemet. Service Fabric stöder Docker behållare på Linux- och Windows Server-behållare. Mer information finns [Service Fabric och behållare](service-fabric-containers-overview.md).

**Partitionsschemat**: när du skapar en namngiven tjänst kan du ange ett partitionsschema. Tjänster med betydande mängder tillstånd dela data mellan partitioner som sprids tillståndet för den klusternoder. Genom att dela data mellan partitioner skala din tjänsten tillstånd. Inom en partition har tillståndslösa namngivna tjänster instanser, medan stateful som heter tjänster har repliker. Tillståndslösa namngivna tjänsterna har vanligtvis endast en partition, eftersom de inte har inget internt tillstånd. Partitionen instanser ger för tillgänglighet. Om en instans misslyckas andra instanser fortsätter att fungera normalt och Service Fabric skapar sedan en ny instans. Stateful som heter tjänster upprätthålla deras tillstånd i repliker och varje partition har sin egen replikuppsättningen så tillståndet hålls synkroniserade. Service Fabric skapar en replik misslyckas, en ny replik från de befintliga replikeringarna.

Läs den [Partition Service Fabric reliable services](service-fabric-concepts-partitioning.md) artikel för mer information.

## <a name="system-services"></a>Systemtjänster
Det finns systemtjänster som skapas i varje kluster som innehåller plattformsfunktioner för Service Fabric.

**Naming Service**: varje Service Fabric-klustret har en Naming Service som matchas tjänstnamn till en plats i klustret. Du hanterar tjänstnamn och egenskaper, precis som ett internet System DNS (Domain Name) för klustret. Klienter kommunicera på ett säkert sätt med en nod i klustret med hjälp av namngivning av tjänsten för att lösa ett tjänstnamn och dess plats. Att flytta program i klustret. Till exempel kan det bero på fel, resurs NLB eller storleksändring av klustret. Du kan utveckla tjänster och klienter att matcha klientens aktuella nätverksplats. Klienter kan hämta den faktiska datorn IP-adress och port där den körs.

Läs [kommunicera med tjänster](service-fabric-connect-and-communicate-with-services.md) mer information om kommunikationen klient- och API: er som fungerar med Naming Service.

**Image Store-tjänsten**: varje Service Fabric-klustret har en Image Store-tjänsten där distribuerade, skapas en ny version programpaket hålls. Kopiera ett programpaket till Image Store och sedan registrera programtyp som ingår i det programpaketet. Efter det att programtypen har etablerats kan du skapa en namngiven program från den. Du kan avregistrera programtyp från Image Store-tjänsten när alla namngivna program har tagits bort.

Läs [förstå inställningen ImageStoreConnectionString](service-fabric-image-store-connection-string.md) för mer information om Image Store-tjänsten.

Läs den [distribuera ett program](service-fabric-deploy-remove-applications.md) artikel för mer information om hur du distribuerar program till Image Store-tjänsten.

**Failover Manager service**: varje Service Fabric-klustret har en Failover Manager-tjänst som ansvarar för följande åtgärder:
   - Utför funktioner som är relaterade till hög tillgänglighet och konsekvenskontroll av tjänster.
   - Samordnar program och kluster uppgraderingar.
   - Samverkar med andra systemkomponenter.

**Reparera Manager-tjänsten**: Detta är ett valfritt systemtjänst som gör att repareringsåtgärder kan utföras på ett kluster på ett sätt som är felsäkert automatable och transparent. Reparera manager används på:
   - Utföra Azure Underhåll reparerar på [Silver- och guld hållbarhet](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Azure Service Fabric-kluster.
   - Utföra reparationsåtgärder för [korrigering Orchestration-program](service-fabric-patch-orchestration-application.md)

## <a name="built-in-programming-models"></a>Inbyggda programmeringsmodeller
.NET Framework och Java programmeringsmodeller är tillgängliga för dig att skapa Service Fabric-tjänster:

**Reliable Services**: en API för att skapa tillståndslösa och tillståndskänsliga tjänster. Tillståndskänsliga tjänster lagrar sina tillstånd i tillförlitliga samlingar, till exempel en ordlista eller en kö. Du kan också ansluta olika kommunikation stackar, till exempel webb-API och Windows Communication Foundation (WCF).

**Reliable Actors**: en API för att skapa tillståndslösa och tillståndskänsliga objekt genom virtuella aktören programmeringsmiljö. Den här modellen är användbart när du har många oberoende enheter av beräkning eller tillstånd. Den här modellen används en bygger trådade modell, så det är bäst att undvika kod som anropar till andra aktörer eller tjänster, eftersom en enskilda aktören inte kan behandla andra inkommande begäranden tills alla utgående förfrågningar är klar.

Du kan också köra befintliga program på Service Fabric:

**Behållare**: Service Fabric stöder distribution av Docker-behållare i Linux och Windows Server-behållare på Windows Server 2016 tillsammans med stöd för Hyper-V-isoleringsläge. I Service Fabric [programmodell](service-fabric-application-model.md), en behållare representerar en programvärd i vilken flera tjänst repliker placeras. Service Fabric kan köra behållare och scenariot liknar gäst körbara scenariot, där du paketerar ett befintligt program i en behållare. Dessutom kan du [köra Service Fabric-tjänster i behållare](service-fabric-services-inside-containers.md) samt.

**Gästen körbara filer**: du kan köra alla typer av kod, exempelvis Node.js, Java eller C++ i Azure Service Fabric som en tjänst. Service Fabric refererar till dessa typer av tjänster som gäst körbara filer som behandlas som tillståndslösa tjänster. Fördelarna med att köra gäst körbara i ett Service Fabric-kluster innehåller hög tillgänglighet, övervakning av hälsotillstånd, livscykelhantering för programmet, hög densitet och synlighet.

Läs den [väljer du en programmeringsmodell för din tjänst](service-fabric-choose-framework.md) artikel för mer information.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
Läs mer om Service Fabric:

* [Översikt över Service Fabric](service-fabric-overview.md)
* [Varför använda mikrotjänster för att bygga program?](service-fabric-overview-microservices.md)
* [Programscenarier](service-fabric-application-scenarios.md)



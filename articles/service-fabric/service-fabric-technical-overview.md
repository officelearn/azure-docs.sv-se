---
title: Lär dig Azure Service Fabric-terminologi | Microsoft Docs
description: En översikt över terminologi för Service Fabric. Beskriver viktiga termer begrepp och termer som används i resten av dokumentationen.
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
ms.date: 09/17/2018
ms.author: ryanwi
ms.openlocfilehash: fda6af0f253457aaf3aef1e8444850592255b318
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113681"
---
# <a name="service-fabric-terminology-overview"></a>Översikt över Service Fabric-terminologi
Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster.  Du kan [Service Fabric-värdkluster som helst](service-fabric-deploy-anywhere.md): Azure i ett lokalt datacenter eller på någon annan molnleverantör.  Service Fabric är orchestrator som driver [Azure Service Fabric nät](/azure/service-fabric-mesh). Du kan använda valfritt ramverk för att skriva dina tjänster och välj var du vill köra programmet från miljön välja mellan flera alternativ. Den här artikeln beskriver de termer som används av Service Fabric för att förstå de termer som används i dokumentationen.

## <a name="infrastructure-concepts"></a>Infrastruktur-begrepp
**Klustret**: En nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras.  Kluster kan skalas upp till tusentals datorer.

**Noden**: En dator eller virtuell dator som ingår i ett kluster kallas en *noden*. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel placeringsegenskaper. Varje dator eller virtuell dator har en Windows-tjänst för automatisk start, `FabricHost.exe`, som börjar köras vid start och startar sedan två körbara filer: `Fabric.exe` och `FabricGateway.exe`. Dessa två körbara filer som utgör noden. För att testa scenarier, du kan ha flera noder i en enskild dator eller virtuell dator genom att köra flera instanser av `Fabric.exe` och `FabricGateway.exe`.

## <a name="application-and-service-concepts"></a>Program- och koncept

**Service Fabric-program för nät**: Service Fabric-nät program beskrivs i resurs-modellen (resursfiler YAML och JSON) och kan distribueras till alla miljöer där Service Fabric kan köras.

**Service Fabric-interna program**: Service Fabric interna program beskrivs av interna program-modellen (XML-baserade program och tjänstmanifest).  Interna program för Service Fabric kan inte köras i Service Fabric-nät.

### <a name="service-fabric-mesh-application-concepts"></a>Service Fabric-nät program begrepp

**Programmet**: Ett program är Faktureringsenhet distribution, versionshantering och livslängden för en nät-programmet. Livscykeln för varje programinstans kan hanteras oberoende av varandra.  Program består av en eller flera kodpaket och inställningar. Ett program har definierats med hjälp av Azure Resource Model (RM)-schemat.  Services beskrivs som egenskaper för programresurs i en RM-mall.  Nätverk och volymer som används av programmet refereras av programmet.  När du skapar ett program, modelleras program, tjänster, nätverk och volymer med Service Fabric-Resursmodell.

**Tjänsten**: En tjänst i ett program representerar en mikrotjänst och utför en fullständig och fristående funktion. Varje tjänst består av en eller flera, kodpaket som beskriver allt som behövs för att köra den behållaravbildning som är associerade med kodpaketet.  Antal tjänster i ett program kan skalas upp och ned.

**Nätverk**: En nätverksresurs skapar ett privat nätverk för dina program och är oberoende av program eller tjänster som kan referera till den. Flera tjänster från olika program kan inte ingå i samma nätverk. Nätverk är distribuerbar resurser som refereras av program.

**Kodpaketet**: Kodpaket beskriver allt som behövs för att köra den behållaravbildning som är associerade med kodpaketet, inklusive följande:

* Behållarens namn, version och registret
* Processor- och resurser som krävs för varje behållare
* Nätverksslutpunkter
* Volymer ska monteras i behållaren som refererar till en separat volym-resurs.

Alla kodpaket som har definierats som en del av en resurs för en distribueras och aktiveras tillsammans som en grupp.

**Volymen**: Volymer är kataloger som monteras i dina behållarinstanser som du kan använda för att bevara tillstånd. Azure Files volym drivrutinen monterar en Azure Files-resurs till en behållare och ger tillgång till tillförlitlig datalagring via API: er som har stöd för fillagring. Volymer är distribuerbar resurser som refereras av program.

### <a name="service-fabric-native-application-concepts"></a>Internt program för Service Fabric-begrepp

**Programmet**: Ett program är en samling av dessa tjänster som utför en viss funktion eller funktioner. Livscykeln för varje programinstans kan hanteras oberoende av varandra.

**Tjänsten**: En tjänst kan utför en fullständig och fristående funktion och starta och köra oberoende av andra tjänster. En tjänst består av kod, konfiguration och data. För varje tjänst, kod består av körbara binärfilerna, konfigurationen består av service-inställningar som kan läsas in vid körning och data består av godtycklig statiska data som ska konsumeras av tjänsten.

**Programtyp**: Det namn/version tilldelad till en samling av tjänsttyper. Det har definierats i en `ApplicationManifest.xml` filen och inbäddad i ett paket för programkatalogen. Katalogen kopieras sedan till Service Fabric-klustrets avbildningsarkiv. Du kan sedan skapa en namngiven programmet från den här typen i klustret.

Läs den [programmodell](service-fabric-application-model.md) artikeln för mer information.

**Programpaket**: En diskkatalog som innehåller programtypen `ApplicationManifest.xml` fil. Refererar till servicepaket för varje typ av tjänst som utgör programtypen. Filerna i programkatalogen för paketet kopieras till Service Fabric-klustrets avbildningsarkiv. Programpaket för en e-program kan exempelvis innehålla referenser till ett paket för kö-tjänst, ett paket för frontend-tjänst och en-tjänst-paketet.

**Med namnet program**: När du kopierar ett programpaket till avbildningsarkivet, skapar du en instans av programmet i klustret. Du skapar en instans när du anger det programpaketet programtyp, med hjälp av dess namn eller versionsnummer. Varje programinstans typ tilldelas ett namn för uniform resource identifier (URI) som ser ut som: `"fabric:/MyNamedApp"`. Du kan skapa flera namngivna program från en enda programtyp i ett kluster. Du kan också skapa namngiven program från olika programtyperna. Varje namngiven program är hanterad och version oberoende av varandra.

**Typ av tjänst**: Det namn/version tilldelad till en tjänst kodpaket data paket och konfigurationspaket. Tjänsttypen har definierats i den `ServiceManifest.xml` filen och inbäddad i en tjänstkatalog för paketet. Tjänsten paketkatalogen sedan refererar till ett programpaket `ApplicationManifest.xml` fil. Inom klustret, kan när du har skapat en namngiven program, du skapa en namngiven tjänst från en av de programtyp tjänsttyper. Tjänsttypen `ServiceManifest.xml` filen beskriver tjänsten.

Läs den [programmodell](service-fabric-application-model.md) artikeln för mer information.

Det finns två typer av tjänster:

* **Tillståndslösa**: Använd en tillståndslös tjänst när tjänstens beständiga tillstånd lagras i en extern lagring-tjänst, till exempel Azure Storage, Azure SQL Database eller Azure Cosmos DB. Använd en tillståndslös tjänst när tjänsten har ingen beständig lagring. Till exempel för en kalkylator-tjänst där värden skickas till tjänsten, utförs en beräkning som använder dessa värden och sedan ett resultat returneras.
* **Tillståndskänslig**: Använd en tillståndskänslig tjänst när du vill att Service Fabric för att hantera tjänstestatus via dess tillförlitliga samlingar eller Reliable Actors programmeringsmodeller. När du skapar en namngiven tjänst kan du ange hur många partitioner som du vill att sprida din delstat i för skalbarhet. Ange hur många gånger för att replikera din delstat över noder för tillförlitlighet. Varje namngiven tjänst har en enda primär replik och flera sekundära repliker. Du kan ändra namngivna tjänstens tillstånd vid skrivning till den primära repliken. Service Fabric kan du sedan replikerar det här tillståndet till de sekundära replikerna att synkronisera din delstat. Service Fabric identifierar automatiskt när en primär replik misslyckas och främjar en befintlig sekundär replik till en primär replik. Service Fabric skapar sedan en ny sekundär replik.  

**Repliker eller instanser** avser kod (och tillstånd för tillståndskänsliga tjänster) av en tjänst som har distribuerats och körs. Se [repliker och instanser](service-fabric-concepts-replica-lifecycle.md).

**Omkonfiguration** menas en process eventuella ändringar i uppsättningen av en tjänst. Se [omkonfiguration](service-fabric-concepts-reconfiguration.md).

**Tjänstpaket**: En diskkatalog som innehåller tjänsttypen `ServiceManifest.xml` fil. Den här filen refererar till den kod och statiska data konfigurationspaket för tjänsttypen. Filerna i katalogen service paketet refererar till programtypen `ApplicationManifest.xml` fil. Exempelvis kan en tjänstpaket avser koden, statiska data och konfigurationspaket som utgör en databastjänst.

**Med namnet service**: När du skapar en namngiven program, kan du skapa en instans av en av dess tjänsttyper i klustret. Du kan ange tjänsttypen genom att använda dess namn/version. Varje typ av tjänstinstans tilldelas ett URI-namn som omfattar under dess namngivna program-URI. Till exempel om du skapar en ”MyDatabase” med namnet tjänsten i ett ”MyNamedApp” med namnet program, URI: N ser ut som: `"fabric:/MyNamedApp/MyDatabase"`. Du kan skapa flera namngivna tjänster i en namngiven program. Varje namngiven tjänst kan ha sin egen partitionsschema och instans eller repliken räknas.

**Kodpaketet**: En diskkatalog som innehåller den tjänsttypen körbara filer, vanligtvis EXE/DLL-filer. Filerna i katalogen kod paketet refererar till tjänsttypen `ServiceManifest.xml` fil. När du skapar en namngiven tjänst kopieras kodpaketet till noden eller noder som har markerats för att köra tjänsten. Sedan börjar koden köras. Det finns två typer av kod paketet körbara filer:

* **Körbara gäster**: Körbara filer som kör som-finns på värdens operativsystem (Windows eller Linux). Dessa körbara filer inte länka till eller referera till Service Fabric runtime filer och därför inte använda alla Service Fabrics programmeringsmodeller. Dessa körbara filer kan inte använda vissa Service Fabric-funktioner, till exempel namngivningstjänsten för slutpunktsidentifiering. Körbara gäster kan inte rapportera inläsningsmåtten som är specifika för varje tjänstinstans.
* **Tjänsten värd körbara filer**: Körbara filer som använder Service Fabrics programmeringsmodeller genom att länka till Service Fabric runtime-filer, aktivering Service Fabric-funktioner. En namngiven tjänst-instans kan registrera slutpunkter med Service Fabric-Namngivningstjänsten och även rapportera inläsningsmåtten.

**Datapaketet**: En diskkatalog som innehåller den tjänsttypen statisk, skrivskyddad datafiler, vanligtvis foto, ljud-och videofiler. Filerna i katalogen data paketet refererar till tjänsttypen `ServiceManifest.xml` fil. När du skapar en namngiven tjänst kopieras datapaketet till noden eller noder som har markerats för att köra tjänsten. Koden börjar köras och kan nu komma åt datafilerna.

**Konfigurationspaketet**: En diskkatalog som innehåller den tjänsttypen statisk, skrivskyddad konfigurationsfiler, vanligtvis textfiler. Filerna i konfigurationskatalogen för paketet refererar till tjänsttypen `ServiceManifest.xml` fil. När du skapar en namngiven tjänst kan filerna i konfigurationspaketet är kopieras en eller fler noder som valts för att köra tjänsten. Koden börjar sedan att köra och kan nu åtkomst till konfigurationsfilerna.

**Behållare**: Som standard, Service Fabric distribuerar och aktiverar tjänster som processer. Service Fabric kan också distribuera tjänster i behållaravbildningar. Behållare är en virtualiseringsteknik som virtualiserar det underliggande operativsystemet från program. Ett program och dess runtime, beroenden och -bibliotek körs i en behållare. Behållaren har fullständiga, privat åtkomst till behållarens egna isolerade vyn operativsystemet konstruktioner. Service Fabric har stöd för Docker-behållare på Linux och Windows Server-behållare. Mer information finns i [Service Fabric och behållare](service-fabric-containers-overview.md).

**Partitionsschemat**: När du skapar en namngiven tjänst kan ange du ett partitionsschema. Tjänster med betydande mängder tillstånd dela upp data över partitioner, som sprider tillståndet för de klusternoder. Namngivna tjänstens tillstånd kan skala genom att dela data över partitioner. Inom en partition har tillståndslösa tjänster för namngivna instanser, medan tillståndskänslig med namnet tjänster har repliker. Tillståndslösa namngivna tjänster har vanligtvis endast en partition, eftersom de har inga interna tillståndet. Partition-instanser ger för tillgänglighet. Om en instans inte andra instanser fortsätter att fungera normalt och Service Fabric skapar sedan en ny instans. Tillståndskänslig med namnet tjänster upprätthålla deras status i repliker och varje partition har en egen replikuppsättning så att tillståndet hålls synkroniserad. Service Fabric skapar får en replik misslyckas, en ny replik från de befintliga replikerna.

Läs den [Partition Service Fabric reliable services](service-fabric-concepts-partitioning.md) artikeln för mer information.

## <a name="system-services"></a>Systemtjänster
Det finns systemtjänster som skapas i varje kluster som tillhandahåller plattformsfunktioner för i Service Fabric.

**Namntjänst**: Varje Service Fabric-kluster har en namngivning av tjänsten, som matchar tjänstnamn till en plats i klustret. Du hanterar tjänstnamn och egenskaper, t.ex. en internet System DNS (Domain Name) för klustret. Klienter kommunicera på ett säkert sätt med en nod i klustret med hjälp av tjänsten Naming löser ett tjänstnamn och dess plats. Program flytta i klustret. Detta kan till exempel bero på fel, belastningsutjämning av resurs eller ändra storlek på klustret. Du kan utveckla tjänster och klienter som matcha klientens aktuella nätverksplats. Klienter kan hämta den faktiska datorn IP-adressen och porten där den körs.

Läs [kommunicera med tjänster](service-fabric-connect-and-communicate-with-services.md) mer information om kommunikationen klient- och API: er som arbetar med Namngivningstjänsten.

**Bild Store-tjänsten**: Varje Service Fabric-kluster har en avbildning Store-tjänst där distribueras, version programpaket hålls. Kopiera ett programpaket till avbildningen Store och sedan registrera vilken typ av program som ingår i det programpaketet. När programtypen har etablerats kan du skapa en namngiven program från den. Du kan avregistrera programtyp från avbildningen Store-tjänsten när alla namngivna program har tagits bort.

Läs [om inställningen ImageStoreConnectionString](service-fabric-image-store-connection-string.md) för mer information om avbildningen Store-tjänsten.

Läs den [distribuera ett program](service-fabric-deploy-remove-applications.md) artikeln för mer information om hur du distribuerar program till avbildningen Store-tjänsten.

**Redundanshanteraren service**: Varje Service Fabric-kluster är en tjänst för Redundanshanteraren som ansvarar för följande åtgärder:
   - Utför funktioner som är relaterade till hög tillgänglighet och konsekvens av tjänster.
   - Samordnar program- och uppgraderingar.
   - Samverkar med andra systemkomponenter.

**Reparera Manager service**: Det här är en valfri systemtjänst som gör att repareringsåtgärder kan utföras på ett kluster på ett sätt som är säkert, vägkarta och transparent. Reparera manager används på:
   - Utför Azure Underhåll reparerar på [Silver och Gold hållbarhet](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Azure Service Fabric-kluster.
   - Utföra reparationsåtgärder för [Patch Orchestration Application](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Modeller för distribution och program 

Om du vill distribuera dina tjänster behöver du att beskriva hur de ska köras. Service Fabric har stöd för tre olika distributionsmodeller:

### <a name="resource-model-preview"></a>Resursmodell (förhandsversion)
Service Fabric-resurser är något som kan distribueras separat till Service Fabric; inklusive program, tjänster, nätverk och volymer. Resurserna definieras med hjälp av en JSON-fil som kan distribueras till en slutpunkt för klustret.  Azure Resource Manager-schema används för Service Fabric-nät. Ett schema för YAML-fil kan också användas för att enkelt skapa definitionsfiler. Resurser kan distribueras som helst Service Fabric kan köras. Resurs-modellen är det enklaste sättet att beskriva dina Service Fabric-program. Dess huvudsakliga fokus ligger på enkel distribution och hantering av tjänster i behållare. Mer information, [introduktion till Service Fabric-Resursmodell](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Inbyggda datamodellen
Internt programmodellen innehåller dina program med fullständig lågnivå-åtkomst till Service Fabric. Program och tjänster definieras som registrerade typer i XML-manifest-filer.

Den inbyggda datamodellen stöder Reliable Services och Reliable Actors-ramverk, som ger åtkomst till Service Fabric-körningen API: er och hantering av API: er i C# och Java. Den inbyggda datamodellen stöder också godtyckliga behållare och körbara filer. Den inbyggda datamodellen stöds inte i den [Service Fabric-nät miljö](/azure/service-fabric-mesh/service-fabric-mesh-overview).

**Reliable Services**: En API för att skapa tillståndslösa och tillståndskänsliga tjänster. Tillståndskänsliga tjänster lagrar sina tillstånd i tillförlitliga samlingar, till exempel en ordlista eller en kö. Du kan också ansluta olika stackar för kommunikation, till exempel webb-API och Windows Communication Foundation (WCF).

**Reliable Actors**: En API för att skapa tillståndslösa och tillståndskänsliga objekt via den virtuella aktör programmeringsmodellen. Den här modellen är användbart när du har massor av oberoende enheter av beräkning eller tillstånd. Den här modellen använder en tur-baserade trådmodellen, så det är bäst att undvika kod som anropar till andra aktörer eller tjänster, eftersom en enskild aktör inte kan bearbeta andra förfrågningar tills alla utgående förfrågningar är klar.

Du kan också köra befintliga program i Service Fabric:

**Behållare**:  Service Fabric stöder distribution av Docker-behållare på Linux och Windows Server-behållare på Windows Server 2016, tillsammans med stöd för Hyper-V-isoleringsläget. I Service Fabric [programmodell](service-fabric-application-model.md), en behållare representerar en programvärd i vilken flera tjänst repliker placeras. Service Fabric kan köra alla behållare och scenariot som liknar det körbara scenariot Gäst, där du paketerar ett befintligt program i en behållare. Dessutom kan du [kör Service Fabric-tjänster i behållare](service-fabric-services-inside-containers.md) samt.

**Körbara gäster**: Du kan köra alla typer av kod som Node.js, Java eller C++ i Azure Service Fabric som en tjänst. Service Fabric refererar till dessa typer av tjänster som körbara gästfiler behandlas som tillståndslösa tjänster. Fördelarna med att köra gäst körbara i ett Service Fabric-kluster är hög tillgänglighet, övervakning av hälsotillstånd, livscykelhantering för program, hög densitet och synlighet.

Läs den [Välj en programmeringsmodell för din tjänst](service-fabric-choose-framework.md) artikeln för mer information.

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) är en del av Docker-projektet. Service Fabric har begränsat stöd för [distribution av program med hjälp av Docker Compose-modellen](service-fabric-docker-compose.md).

## <a name="environments"></a>Miljöer

Service Fabric är en teknik för öppen källkodsplattform som flera olika tjänster och produkter som är baserade på. Microsoft tillhandahåller följande alternativ:

 - **Azure Service Fabric Mesh**: En helt hanterad tjänst för att köra Service Fabric-program i Microsoft Azure.
 - **Azure Service Fabric**: Azure Service Fabric-kluster erbjudande som värd. Det ger integrering mellan Service Fabric och Azure-infrastrukturen, tillsammans med uppgradering och konfiguration hantering av Service Fabric-kluster.
 - **Fristående Service Fabric**: En uppsättning verktyg för installation och konfiguration att [distribuera Service Fabric-kluster var som helst](/azure/service-fabric/service-fabric-deploy-anywhere) (lokalt eller på någon annan molnleverantör). Inte hanteras av Azure.
 - **Service Fabric-kluster för utveckling**: Tillhandahåller en lokal utveckling på Windows, Linux eller Mac för utveckling av Service Fabric-program.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Supportmatris för miljön, ramverk och distributionsmodellen
Olika miljöer har olika stöd för ramverk och distributionsmodeller. I följande tabell beskrivs stöds framework och distribution av modellen kombinationer.

| Typ av program | Beskrivningen av | Azure Service Fabric Mesh | Azure Service Fabric-kluster (alla OS)| Lokalt kluster | Fristående kluster |
|---|---|---|---|---|---|
| Service Fabric-nät program | Resursmodell (YAML & JSON) | Stöds |Stöds inte | Windows - stöd för Linux och Mac-inte stöds | Windows-inte stöds |
|Interna Service Fabric-program | Internt programmodell (XML) | Stöds inte| Stöds|Stöds|Windows – stöds|

I följande tabell beskrivs de olika program-modellerna och verktyg som finns för dem mot Service Fabric.

| Typ av program | Beskrivningen av | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric-nät program | Resursmodell (YAML & JSON) | VS 2017 |Stöds inte |Stöds inte | Stöd för – nät-miljö | Stöds inte|
|Interna Service Fabric-program | Internt programmodell (XML) | VS 2017 och VS 2015| Stöds|Stöds|Stöds|Stöds|

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
Läs mer om Service Fabric:

* [Översikt över Service Fabric](service-fabric-overview.md)
* [Varför använda mikrotjänster för att bygga program?](service-fabric-overview-microservices.md)
* [Programscenarier](service-fabric-application-scenarios.md)

Läs mer om Service Fabric-nät:

* [Översikt över Service Fabric-nät](/azure/service-fabric-mesh/service-fabric-mesh-overview)

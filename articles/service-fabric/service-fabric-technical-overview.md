---
title: Lär dig Azure Service Fabric-terminologi
description: Lär dig viktiga terminologi och begrepp för Service Fabric som används i resten av dokumentationen.
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: a9266c2a8d2ad179cfdb12e367a14f37d1abc9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258244"
---
# <a name="service-fabric-terminology-overview"></a>Översikt över terminologi för Service Fabric

Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster.  Du kan [vara värd för Service Fabric-kluster var som helst:](service-fabric-deploy-anywhere.md)Azure, i ett lokalt datacenter eller på alla molnleverantörer.  Service Fabric är orchestrator som driver [Azure Service Fabric Mesh](/azure/service-fabric-mesh). Du kan använda alla ramverk för att skriva dina tjänster och välja var programmet ska köras från flera miljöval. I den här artikeln beskrivs den terminologi som används av Service Fabric för att förstå de termer som används i dokumentationen.

## <a name="infrastructure-concepts"></a>Infrastrukturkoncept

**Kluster**: En nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras till.  Kluster kan skalas upp till tusentals datorer.

**Nod:** En dator eller virtuell dator som ingår i ett kluster kallas *för en nod*. Varje nod tilldelas ett nodnamn (sträng). Noder har egenskaper, till exempel placeringsegenskaper. Varje dator eller virtuell dator har `FabricHost.exe`en windows-tjänst som startas automatiskt, `Fabric.exe` som `FabricGateway.exe`börjar köras vid start och sedan startar två körbara filer: och . Dessa två körbara filer utgör noden. För testscenarier kan du vara värd för flera noder `Fabric.exe` på `FabricGateway.exe`en enda dator eller virtuell dator genom att köra flera instanser av och .

## <a name="application-and-service-concepts"></a>Program- och tjänstbegrepp

**Service Fabric Mesh Application**: Service Fabric Mesh Applications beskrivs av resursmodellen (YAML- och JSON-resursfiler) och kan distribueras till alla miljöer där Service Fabric körs.

**Inbyggt program**för serviceinfrastruktur : Inbyggda program för serviceinfrastruktur beskrivs av den inbyggda programmodellen (XML-baserade program- och tjänstmanifest).  Inbyggda program för serviceinfrastruktur kan inte köras i Service Fabric Mesh.

### <a name="service-fabric-mesh-application-concepts"></a>Programkoncept för Service Fabric Mesh

**Program**: Ett program är enheten för distribution, versionshantering och livstid för ett Mesh-program. Livscykeln för varje programinstans kan hanteras oberoende av varandra.  Program består av ett eller flera servicekodpaket och inställningar. Ett program definieras med hjälp av Azure Resource Model (RM) schema.  Tjänster beskrivs som egenskaper för programresursen i en RM-mall.  Nätverk och volymer som används av programmet refereras av programmet.  När du skapar ett program modelleras programmet, tjänsterna, nätverket och volymen/volymerna med hjälp av resursmodellen Service Fabric.

**Tjänst:** En tjänst i ett program representerar en mikrotjänst och utför en komplett och fristående funktion. Varje tjänst består av ett eller flera kodpaket som beskriver allt som behövs för att köra behållaravbildningen som är associerad med kodpaketet.  Antalet tjänster i ett program kan skalas upp och ned.

**Nätverk**: En nätverksresurs skapar ett privat nätverk för dina program och är oberoende av de program eller tjänster som kan referera till det. Flera tjänster från olika program kan ingå i samma nätverk. Nätverk är distributionsbara resurser som refereras av program.

**Kodpaket:** Kodpaket beskriver allt som behövs för att köra behållaravbildningen som är associerad med kodpaketet, inklusive följande:

* Behållarnamn, version och register
* CPU- och minnesresurser som krävs för varje behållare
* Nätverksslutpunkter
* Volymer som ska monteras i behållaren, med hänvisning till en separat volymresurs.

Alla kodpaket som definierats som en del av en programresurs distribueras och aktiveras tillsammans som en grupp.

**Volym**: Volymer är kataloger som monteras i behållarinstanserna som du kan använda för att bevara tillståndet. Azure Files-volymdrivrutinen monterar en Azure-filresurs till en behållare och tillhandahåller tillförlitlig datalagring via alla API som stöder fillagring. Volymer är distributionsbara resurser som refereras av program.

### <a name="service-fabric-native-application-concepts"></a>Koncept för inbyggd program för serviceinfrastruktur

**Tillämpning**: Ett program är en samling ingående tjänster som utför en viss funktion eller funktioner. Livscykeln för varje programinstans kan hanteras oberoende av varandra.

**Service**: En tjänst utför en komplett och fristående funktion och kan starta och köra oberoende av andra tjänster. En tjänst består av kod, konfiguration och data. För varje tjänst består koden av de körbara binärfilerna, konfigurationen består av tjänstinställningar som kan läsas in vid körning och data består av godtyckliga statiska data som ska förbrukas av tjänsten.

**Programtyp**: Namnet/versionen som tilldelats en samling tjänsttyper. Den definieras i `ApplicationManifest.xml` en fil och bäddas in i en programpaketkatalog. Katalogen kopieras sedan till Service Fabric-klustrets bildarkiv. Du kan sedan skapa ett namngivet program från den här programtypen i klustret.

Läs artikel [om programmodell](service-fabric-application-model.md) för mer information.

**Programpaket**: En diskkatalog som `ApplicationManifest.xml` innehåller programtypens fil. Refererar till servicepaketen för varje tjänsttyp som utgör programtypen. Filerna i programpaketets katalog kopieras till Service Fabric-klustrets bildarkiv. Ett programpaket för en e-postprogramtyp kan till exempel innehålla referenser till ett kötjänstpaket, ett klientdelstjänstpaket och ett databastjänstpaket.

**Namngivet program**: När du har kopierat ett programpaket till bildarkivet skapar du en instans av programmet i klustret. Du skapar en instans när du anger programpaketets programtyp med hjälp av dess namn eller version. Varje programtypsinstans tilldelas ett URI-namn (Uniform Resource `"fabric:/MyNamedApp"`Identifier) som ser ut så här: . I ett kluster kan du skapa flera namngivna program från en enda programtyp. Du kan också skapa namngivna program från olika programtyper. Varje namngivet program hanteras och versions från varandra.

**Tjänsttyp**: Namnet/versionen som tilldelats en tjänsts kodpaket, datapaket och konfigurationspaket. Tjänsttypen `ServiceManifest.xml` definieras i filen och bäddas in i en tjänstpaketkatalog. Tjänstpaketkatalogen refereras sedan av en `ApplicationManifest.xml` programpaketfil. I klustret, efter att ha skapat ett namngivet program, kan du skapa en namngiven tjänst från en av programtypens tjänsttyper. Tjänsttypens `ServiceManifest.xml` fil beskriver tjänsten.

Läs artikel [om programmodell](service-fabric-application-model.md) för mer information.

Det finns två typer av tjänster:

* **Tillståndslös**: Använd en tillståndslös tjänst när tjänstens beständiga tillstånd lagras i en extern lagringstjänst, till exempel Azure Storage, Azure SQL Database eller Azure Cosmos DB. Använd en tillståndslös tjänst när tjänsten inte har någon beständig lagring. För en kalkylatortjänst där värden skickas till tjänsten utförs till exempel en beräkning som använder dessa värden och sedan returneras ett resultat.
* **Tillståndskänslig:** Använd en tillståndskänslig tjänst när du vill att Service Fabric ska hantera tjänstens tillstånd via dess reliable collections eller Reliable Actors-programmeringsmodeller. När du skapar en namngiven tjänst anger du hur många partitioner du vill sprida ditt tillstånd över för skalbarhet. Ange också hur många gånger du vill replikera ditt tillstånd över noder, för tillförlitlighet. Varje namngiven tjänst har en enda primär replik och flera sekundära repliker. Du ändrar den namngivna tjänstens tillstånd när du skriver till den primära repliken. Service Fabric replikerar sedan det här tillståndet till alla sekundära repliker för att hålla ditt tillstånd synkroniserat. Service Fabric identifierar automatiskt när en primär replik misslyckas och befordrar en befintlig sekundär replik till en primär replik. Service Fabric skapar sedan en ny sekundär replik.  

**Repliker eller instanser refererar** till kod (och tillstånd för tillståndskänsliga tjänster) för en tjänst som distribueras och körs. Se [Repliker och instanser](service-fabric-concepts-replica-lifecycle.md).

**Omkonfiguration** refererar till processen för alla ändringar i replikuppsättningen för en tjänst. Se [Omkonfiguration](service-fabric-concepts-reconfiguration.md).

**Servicepaket**: En diskkatalog som `ServiceManifest.xml` innehåller tjänsttypens fil. Den här filen refererar till kod-, statiska data- och konfigurationspaket för tjänsttypen. Filerna i tjänstpaketkatalogen refereras av programtypens `ApplicationManifest.xml` fil. Ett servicepaket kan till exempel referera till koden, statiska data och konfigurationspaket som utgör en databastjänst.

**Namngiven tjänst**: När du har skapat ett namngivet program kan du skapa en instans av en av dess tjänsttyper i klustret. Du anger tjänsttypen med hjälp av dess namn/version. Varje tjänsttypsinstans tilldelas ett URI-namn som omfattas av det namngivna programmets URI. Om du till exempel skapar en "MyDatabase"-namngiven tjänst i ett "MyNamedApp"-namngivet program ser URI ut som: `"fabric:/MyNamedApp/MyDatabase"`. I ett namngivet program kan du skapa flera namngivna tjänster. Varje namngiven tjänst kan ha ett eget partitionsschema och instans- eller replikantal.

**Kodpaket:** En diskkatalog som innehåller tjänsttypens körbara filer, vanligtvis EXE/DLL-filer. Filerna i kodpaketkatalogen refereras av tjänsttypens `ServiceManifest.xml` fil. När du skapar en namngiven tjänst kopieras kodpaketet till den nod eller noder som valts för att köra den namngivna tjänsten. Sedan börjar koden att köras. Det finns två typer av körbara kodpaket:

* **Gästkörbara filer**: Körbara filer som körs som de är på värdoperativsystemet (Windows eller Linux). Dessa körbara filer länkar inte till eller refererar till några Service Fabric-körningsfiler och använder därför inte några programmeringsmodeller för Service Fabric. Dessa körbara filer kan inte använda vissa Service Fabric-funktioner, till exempel namngivningstjänsten för identifiering av slutpunkter. Körbara gästar inga filer kan rapportera inläsningsmått som är specifika för varje tjänstinstans.
* **Körbara filer för tjänstvärd:** Körbara filer som använder programmeringsmodeller för Service Fabric genom att länka till Service Fabric-körningsfiler, vilket aktiverar Service Fabric-funktioner. En namngiven tjänstinstans kan till exempel registrera slutpunkter med Service Fabric:s namngivningstjänst och även rapportera inläsningsmått.

**Datapaket:** En diskkatalog som innehåller tjänsttypens statiska, skrivskyddade datafiler, vanligtvis foto-, ljud- och videofiler. Filerna i datapaketkatalogen refereras av tjänsttypens `ServiceManifest.xml` fil. När du skapar en namngiven tjänst kopieras datapaketet till den nod eller noder som valts för att köra den namngivna tjänsten. Koden börjar köras och kan nu komma åt datafilerna.

**Konfigurationspaket:** En diskkatalog som innehåller tjänsttypens statiska, skrivskyddade konfigurationsfiler, vanligtvis textfiler. Filerna i konfigurationspaketets katalog refereras av `ServiceManifest.xml` tjänsttypens fil. När du skapar en namngiven tjänst kopieras filerna i konfigurationspaketet till en eller flera noder som valts för att köra den namngivna tjänsten. Sedan börjar koden att köras och kan nu komma åt konfigurationsfilerna.

**Behållare**: Som standard distribuerar och aktiverar Service Fabric tjänster som processer. Service Fabric kan också distribuera tjänster i behållaravbildningar. Behållare är en virtualiseringsteknik som abstraherar det underliggande operativsystemet från program. Ett program och dess körning, beroenden och systembibliotek körs i en behållare. Behållaren har fullständig, privat åtkomst till behållarens egen isolerade vy över operativsystemets konstruktioner. Service Fabric stöder Windows Server-behållare och Docker-behållare på Linux. Mer information finns i [Service Fabric och behållare](service-fabric-containers-overview.md).

**Partitionsschema**: När du skapar en namngiven tjänst anger du ett partitionsschema. Tjänster med betydande mängder tillstånd dela upp data över partitioner, vilket sprider tillståndet över klustrets noder. Genom att dela upp data över partitioner kan den namngivna tjänstens tillstånd skalas. Inom en partition har tillståndslösa namngivna tjänster instanser, medan tillståndskänsliga namngivna tjänster har repliker. Vanligtvis har tillståndslösa namngivna tjänster bara en partition, eftersom de inte har något internt tillstånd. Partitionsinstanserna ger tillgänglighet. Om en instans misslyckas fortsätter andra instanser att fungera normalt och sedan skapar Service Fabric en ny instans. Tillståndskänsliga namngivna tjänster behåller sitt tillstånd i repliker och varje partition har en egen replikuppsättning så att tillståndet hålls synkroniserat. Om en replik misslyckas skapar Service Fabric en ny replik från de befintliga replikerna.

Läs artikeln om tillförlitliga tjänster för [Partition Service Fabric](service-fabric-concepts-partitioning.md) för mer information.

## <a name="system-services"></a>Systemtjänster

Det finns systemtjänster som skapas i varje kluster som tillhandahåller plattformsfunktionerna i Service Fabric.

**Namngivningstjänst:** Varje service fabric-kluster har en namngivningstjänst som matchar tjänstnamn till en plats i klustret. Du hanterar tjänstnamn och egenskaper, till exempel ett DNS-system (Internet Domain Name System) för klustret. Klienter kommunicerar säkert med valfri nod i klustret med hjälp av namngivningstjänsten för att matcha ett tjänstnamn och dess plats. Program flyttas inom klustret. Detta kan till exempel bero på fel, resursbalansering eller storleksändring av klustret. Du kan utveckla tjänster och klienter som löser den aktuella nätverksplatsen. Klienter får den faktiska datorns IP-adress och port där den körs för tillfället.

Läs [Kommunicera med tjänster](service-fabric-connect-and-communicate-with-services.md) för mer information om klient- och tjänstkommunikations-API:er som fungerar med namngivningstjänsten.

**Image Store-tjänsten**: Varje Service Fabric-kluster har en Image Store-tjänst där distribuerade, versionsversionsade programpaket behålls. Kopiera ett programpaket till Image Store och registrera sedan programtypen i programpaketet. När programtypen har etablerats skapar du ett namngivet program från den. Du kan avregistrera en programtyp från Tjänsten Image Store när alla namngivna program har tagits bort.

Läs [Förstå inställningen ImageStoreConnectionString](service-fabric-image-store-connection-string.md) för mer information om Image Store-tjänsten.

Läs artikeln [Distribuera ett program](service-fabric-deploy-remove-applications.md) för mer information om hur du distribuerar program till tjänsten Image Store.

**Redundanshanterarens tjänst**: Varje Service Fabric-kluster har en Redundanhanteraren-tjänst som ansvarar för följande åtgärder:

 - Utför funktioner relaterade till hög tillgänglighet och konsekvens av tjänster.
 - Dirigerar program- och klusteruppgraderingar.
 - Interagerar med andra systemkomponenter.

**Reparationshanterarens tjänst**: Detta är en valfri systemtjänst som gör att reparationsåtgärder kan utföras på ett kluster på ett sätt som är säkert, automatiserat och transparent. Reparationshanteraren används i:

   - Utföra Azure-underhållsreparationer på Azure Service Fabric-kluster [för silver och guld.](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)
   - Utföra reparationsåtgärder för [Patch Orchestration Application](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Distributions- och programmodeller

Om du vill distribuera dina tjänster måste du beskriva hur de ska köras. Service Fabric stöder tre olika distributionsmodeller:

### <a name="resource-model-preview"></a>Resursmodell (förhandsgranskning)

Service Fabric Resources är allt som kan distribueras individuellt till Service Fabric; inklusive applikationer, tjänster, nätverk och volymer. Resurser definieras med hjälp av en JSON-fil som kan distribueras till en klusterslutpunkt.  För Service Fabric Mesh används Azure Resource Model-schemat. Ett YAML-filschema kan också användas för att lättare skapa definitionsfiler. Resurser kan distribueras överallt där Service Fabric körs. Resursmodellen är det enklaste sättet att beskriva dina Service Fabric-program. Dess huvudsakliga fokus ligger på enkel distribution och hantering av containeriserade tjänster. Mer information finns [i Introduktion till resursmodellen För serviceinfrastruktur](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Inbyggd modell

Den inbyggda programmodellen ger dina program full åtkomst på låg nivå till Service Fabric. Program och tjänster definieras som registrerade typer i XML-manifestfiler.

Den inbyggda modellen stöder ramverken för reliable services och reliable actors, som ger åtkomst till API:er för service fabric-körning och klusterhanterings-API:er i C# och Java. Den inbyggda modellen stöder också godtyckliga behållare och körbara filer. Den inbyggda modellen stöds inte i [service fabric mesh-miljön](/azure/service-fabric-mesh/service-fabric-mesh-overview).

**Tillförlitliga tjänster:** Ett API för att skapa tillståndslösa och tillståndskänsliga tjänster. Tillståndskänsliga tjänster lagrar sitt tillstånd i tillförlitliga samlingar, till exempel en ordlista eller en kö. Du kan också koppla in olika kommunikationsstackar, till exempel Webb-API och Windows Communication Foundation (WCF).

**Reliable Actors**: Ett API för att skapa tillståndslösa och tillståndskänsliga objekt via den virtuella aktören programmeringsmodellen. Den här modellen är användbar när du har många oberoende beräkningsenheter eller tillstånd. Den här modellen använder en turbaserad trådmodell, så det är bäst att undvika kod som ropar till andra aktörer eller tjänster eftersom en enskild aktör inte kan bearbeta andra inkommande begäranden förrän alla dess utgående begäranden är klara.

Du kan också köra dina befintliga program på Service Fabric:

**Behållare**: Service Fabric stöder distribution av Docker-behållare på Linux- och Windows Server-behållare på Windows Server 2016, tillsammans med stöd för Hyper-V-isoleringsläge. I [programmodellen](service-fabric-application-model.md)Service Fabric representerar en behållare en programvärd där flera tjänstrepliker placeras. Service Fabric kan köra alla behållare och scenariot liknar det körbara gästscenariot, där du paketerar ett befintligt program i en behållare. Dessutom kan du [köra Service Fabric-tjänster inuti behållare](service-fabric-services-inside-containers.md) också.

**Körbara gästar**filer: Du kan köra alla typer av kod, till exempel Node.js, Python, Java eller C++ i Azure Service Fabric som en tjänst. Service Fabric avser dessa typer av tjänster som gäst körbara filer, som behandlas som tillståndslösa tjänster. Fördelarna med att köra en gäst körbar i ett Service Fabric-kluster inkluderar hög tillgänglighet, hälsoövervakning, programlivscykelhantering, hög densitet och upptäckbarhet.

Läs välj [en programmeringsmodell för din tjänstartikel](service-fabric-choose-framework.md) för mer information.

### <a name="docker-compose"></a>Docker Komponera 

[Docker Compose](https://docs.docker.com/compose/) är en del av Docker-projektet. Service Fabric ger begränsat stöd för [distribution av program med Docker Compose-modellen](service-fabric-docker-compose.md).

## <a name="environments"></a>Miljöer

Service Fabric är en plattformsteknik med öppen källkod som flera olika tjänster och produkter bygger på. Microsoft innehåller följande alternativ:

 - **Azure Service Fabric Mesh**: En fullständigt hanterad tjänst för att köra Service Fabric-program i Microsoft Azure.
 - **Azure Service Fabric**: Azure-värdtjänsten Fabric-klustererbjudande. Det ger integrering mellan Service Fabric och Azure-infrastrukturen, tillsammans med uppgradering och konfigurationshantering av Service Fabric-kluster.
 - **Service Fabric fristående**: En uppsättning installations- och konfigurationsverktyg för att [distribuera Service Fabric-kluster var som helst](/azure/service-fabric/service-fabric-deploy-anywhere) (lokalt eller på vilken molnleverantör som helst). Hanteras inte av Azure.
 - **Utvecklingskluster**för serviceinfrastruktur : Ger en lokal utvecklingsupplevelse på Windows, Linux eller Mac för utveckling av Service Fabric-program.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Stödmatris för miljö-, ram- och distributionsmodell

Olika miljöer har olika nivåer av stöd för ramverk och distributionsmodeller. I följande tabell beskrivs kombinationerna ramverk och distributionsmodell som stöds.

| Typ av program | Beskrivs av | Azure Service Fabric Mesh | Azure Service Fabric-kluster (alla operativsystem)| Lokalt kluster | Fristående kluster |
|---|---|---|---|---|---|
| Mesh-program för servicevävnad | Resursmodell (YAML & JSON) | Stöds |Stöds inte | Windows-stödda, Linux och Mac- stöds inte | Windows- stöds inte |
|Inbyggda program för serviceinfrastruktur | Inbyggd programmodell (XML) | Stöds inte| Stöds|Stöds|Windows- stöds|

I följande tabell beskrivs de olika programmodellerna och de verktyg som finns för dem mot Service Fabric.

| Typ av program | Beskrivs av | Visual Studio | Eclipse | SFCTL (SFCTL) | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Mesh-program för servicevävnad | Resursmodell (YAML & JSON) | VS 2017 |Stöds inte |Stöds inte | Stöds - Endast mesh-miljö | Stöds inte|
|Inbyggda program för serviceinfrastruktur | Inbyggd programmodell (XML) | VS 2017 och VS 2015| Stöds|Stöds|Stöds|Stöds|

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Service Fabric:

* [Översikt över Service Fabric](service-fabric-overview.md)
* [Varför använda mikrotjänster för att bygga program?](service-fabric-overview-microservices.md)
* [Programscenarier](service-fabric-application-scenarios.md)

Om du vill veta mer om Service Fabric Mesh:

* [Översikt över Service Fabric Mesh](/azure/service-fabric-mesh/service-fabric-mesh-overview)

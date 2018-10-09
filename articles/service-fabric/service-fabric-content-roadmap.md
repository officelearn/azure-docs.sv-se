---
title: Läs mer om Azure Service Fabric | Microsoft Docs
description: Läs mer om grundläggande begrepp och huvudområden i Azure Service Fabric. En utökad översikt över Service Fabric och hur du skapar mikrotjänster.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: ryanwi
ms.openlocfilehash: c55e67e24c8f5ff6e243c67b614592874b2cc459
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870663"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Så att du vill lära dig om Service Fabric?
Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster.  Service Fabric har ett stort utsatt område för dock och det är mycket mer.  Den här artikeln innehåller en sammanfattning av Service Fabric och beskriver grundläggande begrepp, programmeringsmodeller, programmets hela livscykel, testning, kluster och övervakning av hälsotillstånd. Läs den [översikt](service-fabric-overview.md) och [vad är mikrotjänster?](service-fabric-overview-microservices.md) för en introduktion och hur Service Fabric kan användas för att skapa mikrotjänster. Den här artikeln innehåller inte en omfattande innehållslistan, men länka till översikt och komma igång artiklar för alla områden i Service Fabric. 

## <a name="core-concepts"></a>Huvudkoncept
[Service Fabric-terminologi](service-fabric-technical-overview.md), [programmodell](service-fabric-application-model.md), och [programmeringsmodeller som stöds](service-fabric-choose-framework.md) ger mer begrepp och beskrivningar, men här är grunderna.

<table><tr><th>Huvudkoncept</th><th>Designtillfället</th><th>Körtid</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-application-type-service-type-application-package-and-manifest-service-package-and-manifest"></a>Utforma tid: programtyp, typ av tjänst, programpaket och manifest, servicepaket och manifest
Programtyp är namn/version som tilldelats till en samling av tjänsttyper. Det här är definierat i en *ApplicationManifest.xml* -fil som är inbäddad i ett paket för programkatalogen. Programpaketet kopieras sedan till Service Fabric-klustrets avbildningsarkiv. Du kan sedan skapa en namngiven programmet från den här programtypen som körs i klustret. 

En tjänst är namn/version som tilldelats till en tjänst kodpaket data paket och konfigurationspaket. Det här är definierat i servicemanifest.XML som är inbäddade i en tjänstkatalog för paketet. Tjänsten paketkatalogen sedan refererar till ett programpaket *ApplicationManifest.xml* fil. Inom klustret, kan när du har skapat en namngiven program, du skapa en namngiven tjänst från en av de programtyp tjänsttyper. En tjänsttyp beskrivs av dess *ServiceManifest.xml* fil. Tjänsttypen består av körbar kod service konfigurationsinställningar, som har lästs in vid körning och statiska data som används av tjänsten.

![Service Fabric programtyper och tjänsttyper][cluster-imagestore-apptypes]

Programpaketet är en diskkatalog som innehåller programtypen *ApplicationManifest.xml* -fil som refererar till servicepaket för varje typ av tjänst som utgör programtypen. Programpaket för en e-program kan exempelvis innehålla referenser till service-paketet en kö, ett paket för frontend-tjänsten och inget tjänstepaket för databasen. Filerna i programkatalogen för paketet kopieras till Service Fabric-klustrets avbildningsarkiv. 

Inget tjänstepaket är en diskkatalog som innehåller tjänsttypen *ServiceManifest.xml* -fil som refererar till den kod och statiska data konfigurationspaket för tjänsttypen. Filerna i katalogen service paketet refererar till programtypen *ApplicationManifest.xml* fil. Exempelvis kan en tjänstpaket avser koden, statiska data och konfigurationspaket som utgör en databastjänst.

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Körtid: kluster och noderna, program, tjänster, partitioner och repliker
Ett [Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras till och hanteras från. Kluster kan skalas upp till tusentals datorer.

En dator eller virtuell dator som ingår i ett kluster kallas för en nod. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel placeringsegenskaper. Varje dator eller virtuell dator har en Windows-tjänst för automatisk start, `FabricHost.exe`, som börjar köras vid start och startar sedan två körbara filer: `Fabric.exe` och `FabricGateway.exe`. Dessa två körbara filer som utgör noden. För utveckling eller testning av scenarier, du kan ha flera noder i en enskild dator eller virtuell dator genom att köra flera instanser av `Fabric.exe` och `FabricGateway.exe`.

En namngiven programmet är en samling namngivna tjänster som utför en viss funktion eller funktioner. En tjänst som utför en fullständig och fristående funktion (det kan starta och köra oberoende av andra tjänster) och består av kod, konfiguration och data. När ett programpaket kopieras till avbildningsarkivet, skapar du en instans av programmet i klustret genom att ange det programpaket programtyp (med dess namn/version). Varje programinstans typ tilldelas ett URI-namn som liknar *fabric: / MyNamedApp*. Du kan skapa flera namngivna program från en enda programtyp i ett kluster. Du kan också skapa namngiven program från olika programtyperna. Varje namngiven program är hanterad och version oberoende av varandra.

När du har skapat ett namngivna program kan du skapa en instans av en av dess tjänsttyper (en namngiven tjänst) i klustret genom att ange tjänsttypen (med dess namn/version). Varje typ av tjänstinstans tilldelas ett URI-namn som omfattar under dess namngivna program-URI. Till exempel om du skapar en ”MyDatabase” med namnet tjänsten i ett ”MyNamedApp” med namnet program, URI: N ser ut som: *fabric: / MyNamedApp/MyDatabase*. I en namngiven program kan skapa du en eller flera namngivna tjänster. Varje namngiven tjänst kan ha sin egen partitionsschema och instansreplik/räknas. 

Det finns två typer av tjänster: tillståndslösa och tillståndskänsliga. Tillståndslösa tjänster lagrar inte tillstånd i tjänsten. Tillståndslösa tjänster har inga beständig lagring alls eller lagra beständiga tillstånd i en extern storage-tjänst, till exempel Azure Storage, Azure SQL Database eller Azure Cosmos DB. En tillståndskänslig tjänst som lagrar tillstånd i tjänsten och använder tillförlitliga samlingar eller Reliable Actors programmeringsmodeller för att hantera tillstånd. 

När du skapar en namngiven tjänst kan ange du ett partitionsschema. Tjänster med stora mängder tillstånd dela upp data över partitioner. Varje partition är ansvarig för en del av fullständig tillståndet för tjänsten, som är fördelade på klustrets noder.  

Följande diagram visar relationen mellan program och instanser av tjänsten, partitioner och repliker.

![Partitioner och -repliker i en tjänst][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Partitionering, skalning och tillgänglighet
[Partitionering](service-fabric-concepts-partitioning.md) är inte unikt för Service Fabric. Ett välkänt formen av partitionering är Datapartitionering eller partitionering. Tillståndskänsliga tjänster med stora mängder tillstånd dela upp data över partitioner. Varje partition är ansvarig för en del av den fullständiga statusen för tjänsten. 

Repliker för varje partition är fördelade på klusternoder, vilket gör att din tjänsten tillstånd att [skala](service-fabric-concepts-scalability.md). När data behov ökar, partitioner växer och Service Fabric balanserar partitionerna mellan noder för att effektivt utnyttja maskinvaruresurser. Om du lägger till nya noder i klustret, kommer Service Fabric partitionsreplikerna över det större antalet noder. Övergripande förbättrar programmets prestanda och minskar konkurrensen för åtkomst till minnet. Om noderna i klustret inte som används effektivt, kan du minska antalet noder i klustret. Service Fabric balanserar igen partitionsrepliker i minskade antalet noder för att bättre utnyttja maskinvaran på varje nod.

Inom en partition har tillståndslösa tjänster för namngivna instanser och tillståndskänsliga namngivna tjänster har repliker. Vanligtvis har tillståndslösa namngivna tjänster bara en partition eftersom de har inga interna tillståndet. Partition-instanser ger för [tillgänglighet](service-fabric-availability-services.md). Om en instans inte andra instanser fortsätter att fungera normalt och Service Fabric skapar sedan en ny instans. Tillståndskänslig med namnet tjänster upprätthålla deras status i repliker och varje partition har en egen replikuppsättningen. Läsning och skrivning utförs på en replik (kallas primärt). Ändringar av tillstånd från skriva operations replikeras till flera repliker (kallas aktiva sekundära databaser). Service Fabric skapar får en replik misslyckas, en ny replik från de befintliga replikerna.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Tillståndslösa och tillståndskänsliga mikrotjänster för Service Fabric
Med Service Fabric kan du skapa program som består av mikrotjänster eller containrar. Tillståndslösa mikrotjänster (till exempel protokollgatewayar och webbproxyservrar) har inte ett föränderligt tillstånd utanför en begäran och svaret från tjänsten. Arbetsroller i Azure Cloud Services är ett exempel på en tillståndslös tjänst. Tillståndskänsliga mikrotjänster (till exempel användarkonton, databaser, enheter, kundvagnar och köer) har ett föränderligt och auktoritärt tillstånd bortom begäran och svaret. Dagens program med Internetskala består av en kombination av tillståndslösa och tillståndskänsliga mikrotjänster. 

En viktig skillnad med Service Fabric är dess stora fokus på att skapa tillståndskänsliga tjänster, antingen med den [inbyggda programmeringsmodeller ](service-fabric-choose-framework.md) eller med tillståndskänsliga tjänster i behållare. [Programscenarier](service-fabric-application-scenarios.md) beskriver scenarier där tillståndskänsliga tjänster används.

Varför har tillståndskänsliga mikrotjänster tillsammans med tillståndslösa som? De två viktigaste skälen är:

* Du kan skapa stora dataflöden, låg latens, feltoleranta online transaktionsbearbetning (OLTP) tjänster genom att hålla kod och data nära till hands på samma dator. Några exempel är interaktiva butiker, sökning, Internet of Things (IoT) system, handel system, kreditkort bearbetning och bedrägeri identifiering av system och personliga Posthantering.
* Du kan förenkla programdesignen. Tillståndskänsliga mikrotjänster ta bort behovet av ytterligare köer och cacheminnen som traditionellt krävs att bemöta kraven på tillgänglighet och svarstid i ett helt och hållet tillståndslösa program. Tillståndskänsliga tjänster är naturligt hög tillgänglighet och låg latens, vilket minskar antalet rörliga delar som kan hantera i ditt program som helhet.

## <a name="supported-programming-models"></a>Programmeringsmodeller som stöds
Service Fabric finns flera sätt att skriva och hantera dina tjänster. Tjänster kan använda Service Fabric-API: er för att dra full nytta av plattformens funktioner och ramverk för programmet. Tjänster kan också vara alla kompilerade körbart program skrivna på valfritt språk och finns i Service Fabric-kluster. Mer information finns i [programmeringsmodeller som stöds](service-fabric-choose-framework.md).

### <a name="containers"></a>Containrar
Som standard, Service Fabric distribuerar och aktiverar tjänster som processer. Service Fabric kan också distribuera tjänster i [behållare](service-fabric-containers-overview.md). Allt kan du blanda tjänster i processer och tjänster i behållare i samma program. Service Fabric stöder distribution av Linux-behållare och Windows-behållare på Windows Server 2016. Du kan distribuera befintliga program eller tillståndslösa tjänster tillståndskänsliga tjänster i behållare. 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) är ett enkelt ramverk för att skriva tjänster som integreras med Service Fabric-plattformen och dra nytta av den fullständiga uppsättningen funktioner. Reliable Services kan vara tillståndslösa (liknar de flesta service-plattformar, till exempel webbservrar eller arbetsroller i Azure Cloud Services), där tillståndet sparas i en extern lösning, till exempel Azure DB eller Azure Table Storage. Reliable Services kan också vara tillståndskänslig, där tillstånd sparat direkt i tjänsten själva med hjälp av tillförlitliga samlingar. Tillstånd görs [med hög tillgänglighet](service-fabric-availability-services.md) via replikering och distribuerade via [partitionering](service-fabric-concepts-partitioning.md), allt hanteras automatiskt av Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Bygger på Reliable Services den [tillförlitliga aktörer](service-fabric-reliable-actors-introduction.md) framework är ett programramverk som implementerar mönstret virtuella aktören baserat på designmönstret aktör. Reliable Actor-framework använder oberoende enheter av beräknings- och tillstånd med Enkeltrådig körning kallas aktörer. Tillförlitliga aktörer ramverket innehåller inbyggd kommunikation för aktörer och ange förväg tillstånd persistence och skala ut konfigurationer.

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric kan integreras med [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) som en första klassens programmeringsmodell för att skapa webbprogram och API: et.  ASP.NET Core kan användas på två olika sätt i Service Fabric:

- Värdbaserad som en körbar gäst. Det här används främst för att köra befintliga ASP.NET Core-program i Service Fabric utan kodändringar.
- Körs i en tillförlitlig tjänst. Detta möjliggör bättre integrering med Service Fabric-körningen och tillåter tillståndskänslig ASP.NET Core-tjänster.

### <a name="guest-executables"></a>Körbara gästfiler
En [körbar gäst](service-fabric-guest-executables-introduction.md) är en befintlig, skadlig körbar fil (skrivna på valfritt språk) på Service Fabric-kluster tillsammans med andra tjänster. Körbara gäster integrerar inte direkt med API: er för Service Fabric. Men de fortfarande dra nytta av funktioner som plattformen erbjuder, som anpassade hälsotillstånd och läsa in reporting och tjänsten för att göra genom att anropa REST API: er. De har också fullständig programmets livscykel för support. 

## <a name="application-lifecycle"></a>Programlivscykel
Som med andra plattformar, ett program i Service Fabric vanligtvis går igenom följande faser: design, utveckling, testning, distribution, uppgradering, underhåll och borttagning. Service Fabric erbjuder förstklassig support för hela programlivscykeln för molnprogram, från utveckling till distribution, daglig hantering och underhåll till eventuell inaktivering. Tjänstmodellen gör det möjligt för flera olika roller att delta oberoende av varandra i programmets hela livscykel. [Service Fabric-programlivscykeln](service-fabric-application-lifecycle.md) innehåller en översikt över API: er och hur de används av de olika rollerna i hela faserna i livscykeln för Service Fabric-program. 

Hela appens livstid kan hanteras med [PowerShell-cmdletar](/powershell/module/ServiceFabric/), [CLI-kommandon](service-fabric-sfctl.md), [C# API: er](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [Java API: er](/java/api/system.fabric._application_management_client), och [ REST API: er](/rest/api/servicefabric/). Du kan också ställa in pipelines för kontinuerlig integrering/kontinuerlig distribution med hjälp av verktyg som [Azure Pipelines](service-fabric-set-up-continuous-integration.md) eller [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).

Följande Microsoft Virtual Academy videoklipp beskriver hur du hanterar livscykeln för din: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-content-roadmap/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="test-applications-and-services"></a>Testa program och tjänster
Om du vill skapa verkligen molnskala tjänster, är det viktigt att kontrollera att dina program och tjänster klarar verkliga fel. Fault Analysis Service har utformats för att testa tjänster som bygger på Service Fabric. Med den [Fault Analysis Service](service-fabric-testability-overview.md), du kan medföra meningsfulla fel och köra fullständig testscenarier mot dina program. Dessa fel och scenarier utöva och validera ett stort antal tillstånd och övergångar som en tjänst får under hela dess livslängd i en kontrollerad, säker och konsekvent sätt.

[Åtgärder](service-fabric-testability-actions.md) rikta en tjänst för att testa med hjälp av enskilda fel. En tjänst-utvecklare kan använda dem som byggblock för att skriva komplicerade scenarier. Exempel på simulerade fel är:

* Starta om en nod för att simulera valfritt antal situationer där en dator eller virtuell dator startas om.
* Flytta en replik för tillståndskänsliga tjänsten att simulera Utjämning av nätverksbelastning, växling vid fel eller uppgradering av programmet.
* Anropa förlorar kvorum på en tillståndskänslig tjänst för att skapa en situation där skrivåtgärder inte kan fortsätta eftersom det inte finns tillräckligt med ”säkerhetskopiering” eller ”sekundär” repliker att acceptera nya data.
* Anropa dataförlust i en tillståndskänslig tjänst för att skapa en situation där alla InMemory-tillstånd helt rensar.

[Scenarier](service-fabric-testability-scenarios.md) komplex består av en eller flera åtgärder. Fault Analysis Service innehåller två inbyggda kompletta scenarier:

* [Chaos scenariot](service-fabric-controlled-chaos.md)-simulerar kontinuerlig, överlagrad fel (korrekt och okontrollerad) i hela klustret under en längre tid.
* [Redundansscenario](service-fabric-testability-scenarios.md#failover-test)– en version av chaos test-scenario som riktar sig mot en specifik tjänst-partition och lämna andra tjänster påverkas inte.

## <a name="clusters"></a>Kluster
Ett [Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras till och hanteras från. Kluster kan skalas upp till tusentals datorer. En dator eller virtuell dator som ingår i ett kluster kallas för en nod i klustret. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel placeringsegenskaper. Varje dator eller virtuell dator har en autostarttjänst `FabricHost.exe`, som börjar köras vid start och startar sedan två körbara filer: Fabric.exe och FabricGateway.exe. Dessa två körbara filer som utgör noden. För att testa scenarier, du kan ha flera noder i en enskild dator eller virtuell dator genom att köra flera instanser av `Fabric.exe` och `FabricGateway.exe`.

Service Fabric-kluster kan skapas på virtuella eller fysiska datorer som kör Windows Server eller Linux. Du kan distribuera och köra Service Fabric-program i alla miljöer där du har en uppsättning med Windows Server eller Linux-datorer som är sammankopplade: lokalt, på Microsoft Azure eller hos någon annan molnleverantör.

I följande Microsoft Virtual Academy-video beskrivs Service Fabric-kluster: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/ClusterOverview.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="clusters-on-azure"></a>Kluster i Azure
Service Fabric-kluster som körs på Azure tillhandahåller integrering med andra Azure-funktioner och tjänster, vilket gör åtgärder och hantering av klustret enklare och mer tillförlitlig. Ett kluster är en Azure Resource Manager-resurs, så att du kan utforma kluster som andra resurser i Azure. Resource Manager tillhandahåller också enkel hantering av alla resurser som används av klustret som en enda enhet. Kluster på Azure är integrerade med Azure-diagnostik och Log Analytics. Klustertyper som noden är [VM-skalningsuppsättningar](/azure/virtual-machine-scale-sets/index), så funktion för automatisk skalning är inbyggd i.

Du kan skapa ett kluster på Azure via den [Azure-portalen](service-fabric-cluster-creation-via-portal.md), från en [mall](service-fabric-cluster-creation-via-arm.md), eller från [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Service Fabric i Linux kan du bygga, distribuera och hantera högtillgängliga och skalbara program på Linux, precis som på Windows. Service Fabric-ramverk (Reliable Services och Reliable Actors) är tillgängliga i Java på Linux, förutom C# (.NET Core). Du kan också skapa [körbara gästtjänster](service-fabric-guest-executables-introduction.md) med valfritt språk eller ramverk. Samordna Docker-behållare stöds också. Docker-behållare kan köra körbara gäster eller interna Service Fabric-tjänster som använder Service Fabric-ramverk. Mer information finns i avsnittet om [Service Fabric i Linux](service-fabric-deploy-anywhere.md).

Det finns vissa funktioner som stöds på Windows, men inte i Linux. Mer information, [skillnader mellan Service Fabric i Linux och Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Fristående kluster
Service Fabric tillhandahåller ett installationspaket för dig att skapa fristående Service Fabric-kluster lokalt eller på någon annan molnleverantör. Fristående kluster ger dig friheten att vara värd för ett kluster var du vill. Om dina data är föremål för efterlevnad eller föreskrifter begränsningar, eller om du vill behålla dina data lokalt, kan du vara värd för ditt eget kluster och program. Service Fabric-program kan köras i flera värdmiljöer utan ändringar, så att dina kunskaper om att bygga program överförs från en värdmiljö till en annan. 

[Skapa ditt första fristående Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md)

Linux fristående kluster stöds inte ännu.

### <a name="cluster-security"></a>Klustersäkerhet
Kluster måste skyddas för att förhindra att obehöriga användare från att ansluta till ditt kluster, särskilt när den har produktionsarbetsbelastningar körs på den. Även om det är möjligt att skapa ett oskyddat kluster gör att det blir anonyma användare att ansluta till det om hanteringsslutpunkter exponeras för det offentliga internet. Det går inte att aktivera senare säkerheten på ett oskyddat kluster: Klustersäkerhet aktiveras när kluster skapas.

Säkerhetsscenarier för kluster är:
* Nod-till-nod-säkerhet
* Klient-till-nod-säkerhet
* Rollbaserad åtkomstkontroll (RBAC)

Mer information finns i [skydda ett kluster](service-fabric-cluster-security.md).

### <a name="scaling"></a>Skalning
Om du lägger till nya noder i klustret, balanserar Service Fabric partitionsrepliker och instanser i det större antalet noder. Övergripande förbättrar programmets prestanda och minskar konkurrensen för åtkomst till minnet. Om noderna i klustret inte som används effektivt, kan du minska antalet noder i klustret. Service Fabric balanserar igen partitionsrepliker och instanser över minskade antalet noder för att bättre utnyttja maskinvaran på varje nod. Du kan skala kluster på Azure antingen [manuellt](service-fabric-cluster-scale-up-down.md) eller [programmässigt](service-fabric-cluster-programmatic-scaling.md). Fristående kluster kan skalas [manuellt](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Klusteruppgradering
Med jämna mellanrum släpps nya versioner av Service Fabric-körningen. Utföra runtime eller infrastrukturresurserna, uppgraderingar i klustret så att du alltid använder en [version som stöds](service-fabric-support.md). Du kan också uppdatera klusterkonfiguration som certifikat och programportar förutom fabric-uppgraderingar.

Service Fabric-kluster är en resurs som du äger, men delvis hanteras av Microsoft. Microsoft ansvarar för uppdatering av det underliggande Operativsystemet och utföra infrastrukturuppgraderingar i klustret. Du kan ange att ta emot automatiska infrastrukturuppgraderingar när Microsoft publicerar en ny version eller välja en stöds fabric-version som du vill. Infrastruktur och konfiguration uppgraderingar kan ställas in via Azure portal eller via Resource Manager. Mer information finns i [uppgradera ett Service Fabric-kluster](service-fabric-cluster-upgrade.md). 

Ett fristående kluster är en resurs som du helt egna. Du är ansvarig för uppdatering av det underliggande Operativsystemet och initierar infrastrukturuppgraderingar. Om klustret kan ansluta till [ https://www.microsoft.com/download ](https://www.microsoft.com/download), du kan ställa in klustret för att automatiskt hämta och etablera det nya Service Fabric runtime-paketet. Sedan kan du initiera uppgraderingen. Om klustret inte kan komma åt [ https://www.microsoft.com/download ](https://www.microsoft.com/download), du kan hämta det nya runtime-paketet manuellt från en Internetansluten dator och sedan starta uppgraderingen. Mer information finns i [uppgradera en fristående Service Fabric-kluster](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Hälsoövervakning
Service Fabric introducerar en [hälsomodellen](service-fabric-health-introduction.md) utformats för att flaggan feltillstånd kluster och program villkor för specifika entiteter (som klusternoder och tjänsten repliker). Hälsomodell använder hälsotillstånd rapportörer (systemkomponenter och watchdogs). Målet är enkel och snabb diagnos och reparation. Skrivare för tjänsten måste du tänka igenom initiala om hälsotillstånd och hur du [utforma tillståndsrapportering](service-fabric-report-health.md#design-health-reporting). Alla villkor som kan påverka hälsotillståndet redovisas, särskilt om det kan hjälpa att flaggan problem nära roten. Hälsoinformation kan spara tid och arbete på felsökning och undersökning när tjänsten är igång i stor skala i produktion.

Service Fabric-rapportörer övervakaren identifierat villkor av intresse. De rapport om dessa villkor baserat på deras lokala vyn. Den [hälsoarkivet](service-fabric-health-introduction.md#health-store) aggregerar hälsodata som skickas av alla rapportörer att avgöra om entiteter är globalt felfritt. Modellen är avsedd att vara omfattande, flexibelt och enkelt att använda. Kvaliteten på hälsorapporterna anger precisionen i vyn hälsotillstånd för klustret. Falska positiva identifieringar som felaktigt visar felaktiga problem kan negativt påverka uppgraderingar eller andra tjänster som använder hälsodata. Exempel på sådana tjänster är reparation och mekanismer för aviseringar. Därför behövs upp för att tillhandahålla rapporter som samlar in villkor av intresse för bästa möjliga sätt.

Rapportering kan göras från:
* Övervakade Service Fabric service målrepliker eller instanser.
* Interna watchdogs distribueras som en Service Fabric-tjänst (till exempel en tillståndslös Service Fabric tjänst som övervakar villkor och skickar rapporter). Watchdogs kan distribueras på alla noder eller kan tillhöra den övervakade tjänsten.
* Interna watchdogs som körs på Service Fabric-noder, men inte har implementerats som Service Fabric-tjänster.
* Externa watchdogs som avsökning resursen från utanför Service Fabric-klustret (till exempel övervakningstjänst som Gomez).

Direkt rapport komponenterna för Service Fabric hälsotillstånd för alla entiteter i klustret. [Systemhälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) ger bättre inblick i klustret och programmet funktioner och flaggan problem med hjälp av hälsotillstånd. För program och tjänster Kontrollera systemhälsorapporter att entiteter implementeras och att den fungerar korrekt för Service Fabric-körningen. Rapporterna inte ger någon hälsoövervakning av affärslogiken i tjänsten eller identifiera låsta processer. Att lägga till hälsoinformation som är specifika för din tjänst logic [implementera anpassade tillståndsrapportering](service-fabric-report-health.md) i dina tjänster.

Service Fabric finns flera sätt att [visa hälsorapporter](service-fabric-view-entities-aggregated-health.md) aggregeras i health store:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) eller andra visualiseringsverktyg för.
* Hälsoförfrågningar (via [PowerShell](/powershell/module/ServiceFabric/), [CLI](service-fabric-sfctl.md), [C# FabricClient APIs](/dotnet/api/system.fabric.fabricclient.healthclient) och [Java FabricClient APIs](/java/api/system.fabric), eller [REST API: er](/rest/api/servicefabric)).
* Allmänna frågor som returnerar en lista över entiteter som har health som en av egenskaperna (via PowerShell, CLI, API: er eller REST).

Följande Microsoft Virtual Academy-video beskrivs hälsomodellen för Service Fabric och hur de används: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-content-roadmap/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik
[Övervakning och diagnostik](service-fabric-diagnostics-overview.md) är viktiga för att utveckla, testa och distribuera program och tjänster i alla miljöer. Service Fabric lösningar fungerar bäst när du planerar och implementerar övervakning och diagnostik som hjälper att se till att program och tjänster fungerar som förväntat i en lokal utvecklingsmiljö eller i produktion.

De huvudsakliga målen med övervakning och diagnostik är att:

- Identifiera och diagnostisera problem med maskinvara och infrastruktur
- Identifiera problem med programvara och app, minska driftstopp för tjänsten
- Förstå resource förbrukning och hjälper dig att fatta operations beslut
- Optimera prestanda för program, tjänst och infrastruktur
- Generera affärsinsikter och identifiera förbättringsområden
 
Det totala arbetsflödet för övervakning och diagnostik består av tre steg:

1. Generering av fönsterhändelse: Detta innefattar händelser (loggar, spårningar, anpassade händelser) på infrastruktur (kluster), plattform och program / nivå
2. Händelsen sammansättning: händelser som genereras behöver samlas in och sammanställs innan de kan visas
3. Analys: händelser måste vara visualiserade och är tillgänglig i vissa format, för analys och visa efter behov

Flera produkter är tillgängliga som täcker dessa tre områden och du kan välja olika tekniker för var och en. Mer information finns i [övervakning och diagnostik för Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du skapar ett [kluster i Azure](service-fabric-cluster-creation-via-portal.md) eller ett [fristående kluster i Windows](service-fabric-cluster-creation-for-windows-server.md).
* Prova att skapa en tjänst med hjälp av programmeringsmodellerna [Reliable Services](service-fabric-reliable-services-quick-start.md) eller [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Lär dig hur du [migrera från Cloud Services](service-fabric-cloud-services-migration-differences.md).
* Lär dig hur du [övervaka och diagnostisera tjänster](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Lär dig hur du [testa dina appar och tjänster](service-fabric-testability-overview.md).
* Lär dig hur du [hantera och dirigera klusterresurser](service-fabric-cluster-resource-manager-introduction.md).
* Titta igenom den [Service Fabric-exempel](http://aka.ms/servicefabricsamples).
* Lär dig mer om [Service Fabric-supportalternativ](service-fabric-support.md).
* Läs den [teambloggen](https://blogs.msdn.microsoft.com/azureservicefabric/) för artiklar och meddelanden.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png

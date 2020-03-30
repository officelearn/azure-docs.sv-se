---
title: Läs mer om Azure Service Fabric
description: Lär dig mer om de grundläggande begreppen och de viktigaste områdena i Azure Service Fabric. Ger en utökad översikt över Service Fabric och hur du skapar mikrotjänster.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 4e6e21f5f9ebfeddb5292e00dc8a929341e77372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458147"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Så du vill lära dig om Service Fabric?
Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster.  Service Fabric har dock en stor yta, och det finns mycket att lära.  Den här artikeln innehåller en sammanfattning av Service Fabric och beskriver kärnbegrepp, programmeringsmodeller, programlivscykel, testning, kluster och hälsoövervakning. Läs [översikten](service-fabric-overview.md) och [vad är mikrotjänster?](service-fabric-overview-microservices.md) för en introduktion och hur Service Fabric kan användas för att skapa mikrotjänster. Den här artikeln innehåller inte en omfattande innehållslista, men länkar till översikt och komma igång artiklar för varje område av Service Fabric. 

## <a name="core-concepts"></a>Huvudkoncept
[Service Fabric terminologi,](service-fabric-technical-overview.md) [Programmodell](service-fabric-application-model.md)och [Program som stöds programmeringsmodeller](service-fabric-choose-framework.md) ger fler begrepp och beskrivningar, men här är grunderna.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Designtid: tjänsttyp, servicepaket och manifest, programtyp, programpaket och manifest
En tjänsttyp är namnet/versionen som tilldelats en tjänsts kodpaket, datapaket och konfigurationspaket. Detta definieras i en ServiceManifest.xml-fil. Tjänsttypen består av körbara inställningar för kod och tjänstkonfiguration, som läses in vid körning och statiska data som förbrukas av tjänsten.

Ett servicepaket är en diskkatalog som innehåller tjänsttypens TjänstManifest.xml-fil, som refererar till koden, statiska data och konfigurationspaket för tjänsttypen. Ett servicepaket kan till exempel referera till koden, statiska data och konfigurationspaket som utgör en databastjänst.

En programtyp är namnet/versionen som tilldelats en samling tjänsttyper. Detta definieras i en ApplicationManifest.xml-fil.

![Programtyper och tjänsttyper för Service Fabric][cluster-imagestore-apptypes]

Programpaketet är en diskkatalog som innehåller programtypen ApplicationManifest.xml-fil, som refererar till tjänstpaketen för varje tjänsttyp som utgör programtypen. Ett programpaket för en e-postprogramtyp kan till exempel innehålla referenser till ett kötjänstpaket, ett klientdelstjänstpaket och ett databastjänstpaket.  

Filerna i programpaketkatalogen kopieras till Service Fabric-klustrets avbildningsarkiv. Du kan sedan skapa ett namngivet program från den här programtypen, som sedan körs i klustret. När du har skapat ett namngivet program kan du skapa en namngiven tjänst från en av programtypens tjänsttyper. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Körtid: kluster och noder, namngivna program, namngivna tjänster, partitioner och repliker
Ett [Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras till. Kluster kan skalas upp till tusentals datorer.

En dator eller virtuell dator som ingår i ett kluster kallas för en nod. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel placeringsegenskaper. Varje dator eller virtuell dator har `FabricHost.exe`en windows-tjänst som startas automatiskt, `Fabric.exe` som `FabricGateway.exe`börjar köras vid start och sedan startar två körbara filer: och . Dessa två körbara filer utgör noden. För utvecklings- eller testscenarier kan du vara värd för flera `Fabric.exe` noder på en enda dator eller virtuell dator genom att köra flera instanser av och `FabricGateway.exe`.

Ett namngivet program är en samling namngivna tjänster som utför en viss funktion eller funktioner. En tjänst utför en komplett och fristående funktion (den kan starta och köras oberoende av andra tjänster) och består av kod, konfiguration och data. När ett programpaket har kopierats till avbildningsarkivet skapar du en instans av programmet i klustret genom att ange programpaketets programtyp (med dess namn/version). Varje programtypsinstans tilldelas ett URI-namn som ser ut som *tyg:/MyNamedApp*. I ett kluster kan du skapa flera namngivna program från en enda programtyp. Du kan också skapa namngivna program från olika programtyper. Varje namngivet program hanteras och versions från varandra.

När du har skapat ett namngivet program kan du skapa en instans av en av dess tjänsttyper (en namngiven tjänst) i klustret genom att ange tjänsttypen (med dess namn/version). Varje tjänsttypsinstans tilldelas ett URI-namn som omfattas av det namngivna programmets URI. Om du till exempel skapar en "MyDatabase"-namngiven tjänst i ett "MyNamedApp"-namngivet program ser URI ut: *tyg:/MyNamedApp/MyDatabase*. I ett namngivet program kan du skapa en eller flera namngivna tjänster. Varje namngiven tjänst kan ha ett eget partitionsschema och instans-/replikantal. 

Det finns två typer av tjänster: statslösa och tillståndskänsliga. Tillståndslösa tjänster lagrar inte tillstånd inom tjänsten. Tillståndslösa tjänster har ingen beständig lagring alls eller lagrar beständigt tillstånd i en extern lagringstjänst som Azure Storage, Azure SQL Database eller Azure Cosmos DB. En tillståndskänslig tjänst lagrar tillstånd inom tjänsten och använder reliable collections eller Reliable Actors programmeringsmodeller för att hantera tillstånd. 

När du skapar en namngiven tjänst anger du ett partitionsschema. Tjänster med stora mängder tillstånd dela upp data mellan partitioner. Varje partition är ansvarig för en del av tjänstens fullständiga tillstånd, som är spridd över klustrets noder.  

I följande diagram visas förhållandet mellan program och tjänstinstanser, partitioner och repliker.

![Partitioner och repliker inom en tjänst][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Partitionering, skalning och tillgänglighet
[Partitionering](service-fabric-concepts-partitioning.md) är inte unikt för Service Fabric. En välkänd form av partitionering är datapartitionering eller fragmentering. Tillståndskänsliga tjänster med stora mängder tillstånd dela upp data mellan partitioner. Varje partition ansvarar för en del av tjänstens fullständiga tillstånd. 

Replikerna för varje partition är spridda över klustrets noder, vilket gör att den namngivna tjänstens tillstånd [kan skalas](service-fabric-concepts-scalability.md). När databehoven växer växer partitioner och Service Fabric balanserar partitioner över noder för att effektivt använda maskinvaruresurser. Om du lägger till nya noder i klustret balanserar Service Fabric om partitionsrepliker över det ökade antalet noder. Övergripande programprestanda förbättras och konkurrens om åtkomst till minne minskar. Om noderna i klustret inte används effektivt kan du minska antalet noder i klustret. Service Fabric balanserar återigen om partitionsrepliker över det minskade antalet noder för att bättre använda maskinvaran på varje nod.

I en partition har tillståndslösa namngivna tjänster instanser medan tillståndskänsliga namngivna tjänster har repliker. Vanligtvis har tillståndslösa namngivna tjänster bara en partition eftersom de inte har något internt tillstånd. Partitionsinstanserna ger [tillgänglighet](service-fabric-availability-services.md). Om en instans misslyckas fortsätter andra instanser att fungera normalt och sedan skapar Service Fabric en ny instans. Tillståndskänsliga namngivna tjänster behåller sitt tillstånd i repliker och varje partition har en egen replikuppsättning. Läs- och skrivåtgärder utförs på en replik (kallas primär). Ändringar av tillstånd från skrivåtgärder replikeras till flera andra repliker (kallas Aktiva sekundärer). Om en replik misslyckas skapar Service Fabric en ny replik från de befintliga replikerna.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Tillståndslösa och tillståndskänsliga mikrotjänster för Service Fabric
Med Service Fabric kan du skapa program som består av mikrotjänster eller containrar. Tillståndslösa mikrotjänster (till exempel protokollgatewayar och webbproxyservrar) har inte ett föränderligt tillstånd utanför en begäran och svaret från tjänsten. Arbetsroller i Azure Cloud Services är ett exempel på en tillståndslös tjänst. Tillståndskänsliga mikrotjänster (till exempel användarkonton, databaser, enheter, kundvagnar och köer) har ett föränderligt och auktoritärt tillstånd bortom begäran och svaret. Dagens program med Internetskala består av en kombination av tillståndslösa och tillståndskänsliga mikrotjänster. 

En viktig differentiering med Service Fabric är dess starka fokus på att bygga tillståndskänsliga tjänster, antingen med de [inbyggda programmeringsmodellerna](service-fabric-choose-framework.md) eller med containeriserade tillståndskänsliga tjänster. [Programscenarier](service-fabric-application-scenarios.md) beskriver scenarier där tillståndskänsliga tjänster används.

Varför har tillståndskänsliga mikrotjänster tillsammans med statslösa? De två främsta orsakerna är:

* Du kan skapa oltp-tjänster (high-throughput, low-lateency, failure-tolerant online transaction processing) genom att hålla kod och data nära på samma dator. Några exempel är interaktiva skyltfönster, sökning, Sakernas Internet (IoT) system, handelssystem, kreditkort bearbetning och bedrägeri upptäckt system, och personlig posthantering.
* Du kan förenkla programdesignen. Tillståndskänsliga mikrotjänster tar bort behovet av ytterligare köer och cacheminnen, som traditionellt krävs för att åtgärda tillgänglighets- och svarstidskraven för ett rent tillståndslöst program. Tillståndskänsliga tjänster är naturligtvis hög tillgänglighet och låg latens, vilket minskar antalet rörliga delar att hantera i ditt program som helhet.

## <a name="supported-programming-models"></a>Programmeringsmodeller som stöds
Service Fabric erbjuder flera sätt att skriva och hantera dina tjänster. Tjänster kan använda Api:erna för service fabric för att dra full nytta av plattformens funktioner och programramverk. Tjänster kan också vara alla kompilerade körbara program skrivna på valfritt språk och finns på ett Service Fabric-kluster. Mer information finns i [Programmeringsmodeller som stöds](service-fabric-choose-framework.md).

### <a name="containers"></a>Containrar
Som standard distribuerar och aktiverar Service Fabric tjänster som processer. Service Fabric kan också distribuera tjänster i [behållare](service-fabric-containers-overview.md). Viktigt är att du kan blanda tjänster i processer och tjänster i behållare i samma program. Service Fabric stöder distribution av Linux-behållare och Windows-behållare på Windows Server 2016. Du kan distribuera befintliga program, tillståndslösa tjänster eller tillståndskänsliga tjänster i behållare. 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) är ett lätt ramverk för att skriva tjänster som integreras med Service Fabric-plattformen och dra nytta av alla plattformsfunktioner. Reliable Services kan vara tillståndslösa (liknande de flesta tjänstplattformar, till exempel webbservrar eller arbetarroller i Azure Cloud Services), där tillståndet kvarstår i en extern lösning, till exempel Azure DB eller Azure Table Storage. Tillförlitliga tjänster kan också vara tillståndskänsliga, där tillståndet kvarstår direkt i själva tjänsten med hjälp av tillförlitliga samlingar. Tillstånd görs [mycket tillgängligt](service-fabric-availability-services.md) via replikering och distribueras genom [partitionering](service-fabric-concepts-partitioning.md), alla hanteras automatiskt av Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Reliable Actor-ramverket bygger [Reliable Actor](service-fabric-reliable-actors-introduction.md) på tillförlitliga tjänster och är ett programramverk som implementerar mönstret för den virtuella aktören, baserat på aktörsdesignmönstret. Reliable Actor-ramverket använder oberoende enheter för beräkning och tillstånd med entrådiga körning som kallas aktörer. Reliable Actor-ramverket tillhandahåller inbyggd kommunikation för aktörer och förinställda tillståndsbeständighet och utskalningskonfigurationer.

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric integrerar med [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) som en förstklassig programmeringsmodell för att skapa webb- och API-program.  ASP.NET Core kan användas på två olika sätt i Service Fabric:

- Värden är värd för en körbar gäst. Detta används främst för att köra befintliga ASP.NET Core-program på Service Fabric utan kodändringar.
- Kör inuti en tillförlitlig tjänst. Detta möjliggör bättre integrering med Service Fabric-körningen och möjliggör tillståndskänsliga ASP.NET Core-tjänster.

### <a name="guest-executables"></a>Körbara gästfiler
En [körbar gäst](service-fabric-guest-executables-introduction.md) är en befintlig, godtycklig körbar (skriven på alla språk) som finns på ett Service Fabric-kluster tillsammans med andra tjänster. Körbara gästar inte direkt med Api:er för service fabric. Men de drar fortfarande nytta av funktioner som plattformen erbjuder, till exempel anpassad hälso- och belastningsrapportering och tjänstupptäcktbarhet genom att anropa REST-API:er. De har också fullständigt stöd för programmets livscykel. 

## <a name="application-lifecycle"></a>Programlivscykel
Precis som med andra plattformar går ett program på Service Fabric vanligtvis igenom följande faser: design, utveckling, testning, distribution, uppgradering, underhåll och borttagning. Service Fabric ger förstklassigt stöd för hela programlivscykeln för molnprogram, från utveckling till distribution, daglig hantering och underhåll till eventuell avveckling. Tjänstmodellen gör det möjligt för flera olika roller att delta oberoende av dem i programmets livscykel. [Livscykeln för Tjänst fabric-program](service-fabric-application-lifecycle.md) ger en översikt över API:erna och hur de används av de olika rollerna under faserna i programlivscykeln för Service Fabric. 

Hela appens livscykel kan hanteras med [PowerShell-cmdlets,](/powershell/module/ServiceFabric/) [CLI-kommandon,](service-fabric-sfctl.md) [C#API:er,](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) [Java-API:er](/java/api/overview/azure/servicefabric)och [REST-API:er](/rest/api/servicefabric/). Du kan också ställa in pipelines för kontinuerlig integrering/kontinuerlig distribution med hjälp av verktyg som [Azure Pipelines](service-fabric-set-up-continuous-integration.md) eller [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).

## <a name="test-applications-and-services"></a>Testa program och tjänster
För att skapa verkligt molnbaserade tjänster är det viktigt att kontrollera att dina program och tjänster tål verkliga fel. Felanalystjänsten är utformad för testtjänster som är byggda på Service Fabric. Med [felanalystjänsten](service-fabric-testability-overview.md)kan du inducera meningsfulla fel och köra fullständiga testscenarier mot dina program. Dessa fel och scenarier övar och validerar de många tillstånd och övergångar som en tjänst kommer att uppleva under hela sin livstid, allt på ett kontrollerat, säkert och konsekvent sätt.

[Åtgärder](service-fabric-testability-actions.md) riktar sig till en tjänst för testning med hjälp av enskilda fel. En tjänstutvecklare kan använda dessa som byggstenar för att skriva komplicerade scenarier. Exempel på simulerade fel är:

* Starta om en nod för att simulera valfritt antal situationer där en dator eller virtuell dator startas om.
* Flytta en replik av din tillståndskänsliga tjänst för att simulera belastningsutjämning, redundans eller programuppgradering.
* Anropa kvorumförlust på en tillståndskänslig tjänst för att skapa en situation där skrivåtgärder inte kan fortsätta eftersom det inte finns tillräckligt med "back-up" eller "sekundära" repliker för att acceptera nya data.
* Anropa dataförlust på en tillståndskänslig tjänst för att skapa en situation där alla minnestillstånd är helt utraderade.

[Scenarier](service-fabric-testability-scenarios.md) är komplexa åtgärder som består av en eller flera åtgärder. Felanalystjänsten innehåller två inbyggda kompletta scenarier:

* [Kaos scenario](service-fabric-controlled-chaos.md)- simulerar kontinuerliga, interfolierade fel (både graciösa och ungraceful) i hela klustret under längre tidsperioder.
* [Redundansscenario](service-fabric-testability-scenarios.md#failover-test)- en version av kaostestscenariot som riktar sig till en viss tjänstpartition samtidigt som andra tjänster inte påverkas.

## <a name="clusters"></a>Kluster
Ett [Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras till. Kluster kan skalas upp till tusentals datorer. En dator eller virtuell dator som ingår i ett kluster kallas klusternod. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel placeringsegenskaper. Varje dator eller virtuell dator har `FabricHost.exe`en tjänst för automatisk start, som börjar köras vid start och sedan startar två körbara filer: Fabric.exe och FabricGateway.exe. Dessa två körbara filer utgör noden. För testscenarier kan du vara värd för flera noder `Fabric.exe` på `FabricGateway.exe`en enda dator eller virtuell dator genom att köra flera instanser av och .

Service Fabric-kluster kan skapas på virtuella eller fysiska datorer som kör Windows Server eller Linux. Du kan distribuera och köra Service Fabric-program i alla miljöer där du har en uppsättning Windows Server- eller Linux-datorer som är sammankopplade: lokalt, på Microsoft Azure eller på alla molnleverantörer.

### <a name="clusters-on-azure"></a>Kluster i Azure
Köra Service Fabric-kluster på Azure ger integrering med andra Azure-funktioner och tjänster, vilket gör drift och hantering av klustret enklare och mer tillförlitligt. Ett kluster är en Azure Resource Manager-resurs, så du kan modellera kluster som alla andra resurser i Azure. Resource Manager ger också enkel hantering av alla resurser som används av klustret som en enda enhet. Kluster på Azure är integrerade med Azure-diagnostik och Azure Monitor-loggar. Klusternodtyper är [skalningar för virtuella datorer,](/azure/virtual-machine-scale-sets/index)så funktionen för automatisk skalning är inbyggd.

Du kan skapa ett kluster på Azure via [Azure-portalen](service-fabric-cluster-creation-via-portal.md), från en [mall](service-fabric-cluster-creation-via-arm.md)eller från [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Service Fabric på Linux gör att du kan bygga, distribuera och hantera högtillgängliga, mycket skalbara program på Linux precis som du skulle göra i Windows. Service Fabric-ramverken (Reliable Services och Reliable Actors) finns i Java på Linux, förutom C# (.NET Core). Du kan också skapa [körbara gästtjänster](service-fabric-guest-executables-introduction.md) med valfritt språk eller ramverk. Orchestrating Docker-behållare stöds också. Docker-behållare kan köra gästkörbara filer eller inbyggda Service Fabric-tjänster, som använder ramverken för serviceinfrastruktur. Mer information finns i [Service Fabric på Linux](service-fabric-deploy-anywhere.md).

Det finns vissa funktioner som stöds på Windows, men inte på Linux. Mer information finns [i Skillnader mellan serviceinfrastruktur på Linux och Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Fristående kluster
Service Fabric tillhandahåller ett installationspaket där du kan skapa fristående Service Fabric-kluster lokalt eller på vilken molnleverantör som helst. Fristående kluster ger dig friheten att vara värd för ett kluster var du vill. Om dina data omfattas av efterlevnads- eller regelbegränsningar, eller om du vill behålla dina data lokalt, kan du vara värd för ditt eget kluster och program. Service Fabric-program kan köras i flera värdmiljöer utan ändringar, så din kunskap om att bygga program överförs från en värdmiljö till en annan. 

[Skapa ditt första fristående Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md)

Linux fristående kluster stöds ännu inte.

### <a name="cluster-security"></a>Klustersäkerhet
Kluster måste skyddas för att förhindra att obehöriga användare ansluter till klustret, särskilt när de har produktionsarbetsbelastningar som körs på det. Även om det är möjligt att skapa ett oskyddat kluster, gör det möjligt för anonyma användare att ansluta till det om hanteringsslutpunkter exponeras för det offentliga internet. Det går inte att senare aktivera säkerhet i ett oskyddat kluster: klustersäkerhet aktiveras vid skapande av kluster.

Klustersäkerhetsscenarierna är:
* Nod-till-nod säkerhet
* Säkerhet för klient-till-nod
* Rollbaserad åtkomstkontroll (RBAC)

Mer information finns i [Skydda ett kluster](service-fabric-cluster-security.md).

### <a name="scaling"></a>Skalning
Om du lägger till nya noder i klustret balanserar Service Fabric om partitionsrepliker och instanser över det ökade antalet noder. Övergripande programprestanda förbättras och konkurrens om åtkomst till minne minskar. Om noderna i klustret inte används effektivt kan du minska antalet noder i klustret. Service Fabric balanserar återigen om partitionsrepliker och instanser över det minskade antalet noder för att bättre använda maskinvaran på varje nod. Du kan skala kluster på Azure antingen [manuellt](service-fabric-cluster-scale-up-down.md) eller [programmässigt](service-fabric-cluster-programmatic-scaling.md). Fristående kluster kan skalas [manuellt](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Klusteruppgraderingar
Med jämna mellanrum släpps nya versioner av Service Fabric-körningen. Utför körnings- eller infrastrukturuppgraderingar i klustret så att du alltid kör en [version som stöds](service-fabric-support.md). Förutom infrastrukturuppgraderingar kan du även uppdatera klusterkonfiguration som certifikat eller programportar.

Ett Service Fabric-kluster är en resurs som du äger, men hanteras delvis av Microsoft. Microsoft ansvarar för att korrigera det underliggande operativsystemet och utföra infrastrukturuppgraderingar i klustret. Du kan ange att klustret ska ta emot automatiska infrastrukturuppgraderingar när Microsoft släpper en ny version eller välja en tygversion som stöds. Infrastruktur- och konfigurationsuppgraderingar kan ställas in via Azure-portalen eller via Resource Manager. Mer information finns i [Uppgradera ett service fabric-kluster](service-fabric-cluster-upgrade.md). 

Ett fristående kluster är en resurs som du äger helt och hållet. Du är ansvarig för att korrigera det underliggande operativsystemet och initiera tyguppgraderingar. Om klustret [https://www.microsoft.com/download](https://www.microsoft.com/download)kan ansluta till kan du ställa in klustret så att det automatiskt hämtar och etablerar det nya service fabric-körningspaketet. Du skulle då starta uppgraderingen. Om klustret inte [https://www.microsoft.com/download](https://www.microsoft.com/download)kan komma åt kan du manuellt hämta det nya körningspaketet från en internetansluten dator och sedan starta uppgraderingen. Mer information finns i [Uppgradera ett fristående Service Fabric-kluster](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Hälsoövervakning
Service Fabric introducerar en [hälsomodell](service-fabric-health-introduction.md) som utformats för att flagga felaktiga kluster- och programvillkor på specifika entiteter (till exempel klusternoder och tjänstrepliker). Hälsomodellen använder hälsoreportrar (systemkomponenter och vakthundar). Målet är enkel och snabb diagnos och reparation. Service författare måste tänka upfront om hälsa och hur man [utformar hälsorapportering](service-fabric-report-health.md#design-health-reporting). Alla tillstånd som kan påverka hälsan bör rapporteras, särskilt om det kan hjälpa flagga problem nära roten. Hälsoinformationen kan spara tid och ansträngning på felsökning och undersökning när tjänsten är igång i stor skala i produktion.

Service Fabric-reportrar övervakar identifierade villkor av intresse. De rapporterar om dessa villkor på grundval av deras lokala uppfattning. [Hälsoarkivet](service-fabric-health-introduction.md#health-store) sammanställer hälsodata som skickas av alla reportrar för att avgöra om entiteter är globalt felfria. Modellen är avsedd att vara rik, flexibel och enkel att använda. Kvaliteten på hälsorapporterna avgör riktigheten i hälsovyn i klustret. Falska positiva identifieringar som felaktigt visar felaktiga problem kan påverka uppgraderingar eller andra tjänster som använder hälsodata negativt. Exempel på sådana tjänster är reparationstjänster och varningsmekanismer. Därför behövs en del tankar för att tillhandahålla rapporter som fångar upp förhållanden av intresse på bästa möjliga sätt.

Rapportering kan göras från:
* Den övervakade servicepläckningsrepliken eller instansen för Tjänsten Fabric.
* Interna watchdogs som distribueras som en Service Fabric-tjänst (till exempel en tjänst fabric-tjänst som övervakar villkor och ärenderapporter). Watchdogs kan distribueras på alla noder eller kan affiniteras till den övervakade tjänsten.
* Interna watchdogs som körs på noderna Service Fabric men som inte implementeras som Service Fabric-tjänster.
* Externa watchdogs som avsöker resursen utanför Service Fabric-klustret (till exempel övervakningstjänsten som Gomez).

I samma läge rapporterar Service Fabric-komponenter hälsotillståndet för alla entiteter i klustret. [Systemhälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) ger insyn i kluster- och programfunktioner och flaggar problem via hälsotillstånd. För program och tjänster verifierar systemhälsorapporter att entiteter implementeras och beter sig korrekt ur service fabric-körningen. Rapporterna ger ingen hälsoövervakning av tjänstens affärslogik eller identifierar processer som har slutat svara. Om du vill lägga till hälsoinformation som är specifik för tjänstens logik [implementerar du anpassad hälsorapportering](service-fabric-report-health.md) i dina tjänster.

Service Fabric erbjuder flera sätt att [visa hälsorapporter](service-fabric-view-entities-aggregated-health.md) som aggregerats i hälsoarkivet:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) eller andra visualiseringsverktyg.
* Hälsofrågor (via [PowerShell,](/powershell/module/ServiceFabric/) [CLI](service-fabric-sfctl.md), [C# FabricClient API:er](/dotnet/api/system.fabric.fabricclient.healthclient) och [Java FabricClient API:er](/java/api/system.fabric)eller [REST API:er](/rest/api/servicefabric)).
* Allmänna frågor som returnerar en lista över entiteter som har hälsotillstånd som en av egenskaperna (via PowerShell, CLI, API:erna eller REST).

## <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik
[Övervakning och diagnostik](service-fabric-diagnostics-overview.md) är avgörande för att utveckla, testa och distribuera program och tjänster i alla miljöer. Service Fabric-lösningar fungerar bäst när du planerar och implementerar övervakning och diagnostik som säkerställer att program och tjänster fungerar som förväntat i en lokal utvecklingsmiljö eller i produktion.

De huvudsakliga målen för övervakning och diagnostik är att:

- Identifiera och diagnostisera maskinvaru- och infrastrukturproblem
- Identifiera problem med programvara och appar, minska driftstoppet för tjänsten
- Förstå resursförbrukning och hjälpa till att driva verksamhetsbeslut
- Optimera program-, tjänst- och infrastrukturprestanda
- Generera affärsinsikter och identifiera förbättringsområden
 
Det övergripande arbetsflödet för övervakning och diagnostik består av tre steg:

1. Händelsegenerering: detta inkluderar händelser (loggar, spårningar, anpassade händelser) på infrastrukturen (kluster), plattform och program-/tjänstnivå
2. Händelseaggregering: genererade händelser måste samlas in och aggregeras innan de kan visas
3. Analys: händelser måste visualiseras och vara tillgängliga i vissa format, för att möjliggöra analys och visning efter behov

Flera produkter finns tillgängliga som täcker dessa tre områden, och du är fri att välja olika tekniker för varje. Mer information finns i [Övervakning och diagnostik för Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du skapar ett [kluster i Azure](service-fabric-cluster-creation-via-portal.md) eller ett [fristående kluster i Windows](service-fabric-cluster-creation-for-windows-server.md).
* Prova att skapa en tjänst med hjälp av programmeringsmodellerna [Reliable Services](service-fabric-reliable-services-quick-start.md) eller [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Läs om hur du [migrerar från Molntjänster](service-fabric-cloud-services-migration-differences.md).
* Lär dig att [övervaka och diagnostisera tjänster](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Lär dig att [testa dina appar och tjänster](service-fabric-testability-overview.md).
* Lär dig [att hantera och orkestrera klusterresurser](service-fabric-cluster-resource-manager-introduction.md).
* Titta igenom [Service Fabric-exemplen](https://aka.ms/servicefabricsamples).
* Läs mer om [supportalternativ för Service Fabric.](service-fabric-support.md)
* Läs [teamet blogg](https://blogs.msdn.microsoft.com/azureservicefabric/) för artiklar och meddelanden.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png

---
title: Lär dig mer om Azure Service Fabric | Microsoft Docs
description: Lär dig mer om grundläggande begrepp och huvudområden i Azure Service Fabric. En utökad översikt över Service Fabric och hur du skapar mikrotjänster.
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
ms.openlocfilehash: 1c3ea5b041cf2a961ef57bc168ae86b83412e044
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Därför vill du lär dig mer om Service Fabric?
Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster.  Service Fabric har en stor ytan emellertid och det är mycket mer.  Den här artikeln innehåller en sammanfattning av Service Fabric och beskriver grundläggande begrepp programmeringsmodeller, program livscykel, testa, kluster och övervakning av hälsotillstånd. Läs den [översikt](service-fabric-overview.md) och [vad är mikrotjänster?](service-fabric-overview-microservices.md) för en introduktion och hur Service Fabric kan användas för att skapa mikrotjänster. Den här artikeln innehåller inte en omfattande lista för innehåll, men länkar till översikt och komma igång artiklar för varje del av Service Fabric. 

## <a name="core-concepts"></a>Huvudkoncept
[Service Fabric-terminologi](service-fabric-technical-overview.md), [programmodell](service-fabric-application-model.md), och [stöds programmeringsmodeller](service-fabric-choose-framework.md) får du större begrepp och beskrivningar, men här finns grunderna.

<table><tr><th>Huvudkoncept</th><th>Designtillfället</th><th>Körtid</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-application-type-service-type-application-package-and-manifest-service-package-and-manifest"></a>Designtid: programtyp, tjänsttyp, programpaket och manifestet, servicepaket och manifestet
En typ av program är namn/version som tilldelats till en samling av tjänsttyper. Detta är definierad i en *ApplicationManifest.xml* fil som är inbäddad i ett paket för programkatalogen. Programpaketet kopieras sedan till Service Fabric-klustret avbildningsarkivet. Du kan sedan skapa en namngiven programmet från den här programtypen som körs i klustret. 

En tjänst är namn/version som tilldelats kod paket, data-paket och konfigurationspaket för en tjänst. Detta har definierats i en ServiceManifest.xml-fil som är inbäddad i ett paket katalog. Paketet katalog sedan refereras av ett programpaket *ApplicationManifest.xml* fil. I kluster kan när du har skapat ett namngivet program, du skapa en namngiven tjänst från en av de programtyp tjänsttyper. En typ av beskrivs av dess *ServiceManifest.xml* fil. Tjänsttypen består av körbar kod service konfigurationsinställningar, som läses in vid körning, och statiska data som används av tjänsten.

![Service Fabric programtyper och typer av tjänster][cluster-imagestore-apptypes]

Programpaketet är en diskkatalog som innehåller programtypen *ApplicationManifest.xml* filen som refererar till servicepaket för varje typ av tjänst som utgör programtypen. Ett programpaket för en typ av e-program kan exempelvis innehålla referenser till en kö servicepaket, en klientdel servicepaket och inget tjänstepaket för databasen. Filerna i katalogen program paketet kopieras till Service Fabric-klustret avbildningsarkivet. 

Inget tjänstepaket är en diskkatalog som innehåller service-typen *ServiceManifest.xml* filen som refererar till kod, statiska data och konfigurationspaket för tjänsttypen. Filerna i katalogen service paketet refereras av typen programmet *ApplicationManifest.xml* fil. Inget tjänstepaket kan till exempel referera till kod, statiska data och konfigurationspaket som utgör en databastjänst.

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Körtid: kluster och program, tjänster, partitioner och repliker noderna
Ett [Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras till och hanteras från. Kluster kan skalas upp till tusentals datorer.

En dator eller virtuell dator som ingår i ett kluster kallas för en nod. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel placeringsegenskaper. Varje dator eller virtuell dator har en Windows-tjänsten för automatisk start, `FabricHost.exe`, som startar vid start och startar sedan två körbara filer: `Fabric.exe` och `FabricGateway.exe`. Dessa två körbara filer som utgör noden. För utveckling eller tester scenarier, du kan vara värd för flera noder i en enskild dator eller virtuell dator genom att köra flera instanser av `Fabric.exe` och `FabricGateway.exe`.

Ett namngivet program är en samling namngivna tjänster som utför en viss funktion eller funktioner. En tjänst som utför en fullständig och fristående funktion (det kan starta och köra oberoende av andra tjänster) och består av koden, konfiguration och data. När ett programpaket kopieras till image store, kan du skapa en instans av program i klustret genom att ange det programpaketet programtyp (med dess namn/version). Varje typ av programinstans tilldelas ett URI-namn som ser ut som *fabric: / MyNamedApp*. Du kan skapa flera namngivna program från en enda programtyp inom ett kluster. Du kan också skapa namngivna program från annan programtyper. Varje namngivet program är oberoende av varandra hanterade och en ny version.

Efter att ett namngivet program kan skapa du en instans av en av dess tjänsttyper (tjänsten) i klustret genom att ange tjänsttypen (med dess namn/version). Varje typ tjänstinstans tilldelas ett URI-namn omfång under dess namngivna program-URI. Till exempel om du skapar en ”mindatabas” med namnet tjänsten inom en ”MyNamedApp” med namnet på programmet, URI: N ser ut så: *fabric: / MyNamedApp/mindatabas*. Du kan skapa en eller flera namngivna tjänster i en namngiven programmet. Varje tjänsten kan ha sin egen partitionsschema och instansreplik/räknar. 

Det finns två typer av tjänster: tillståndslösa och tillståndskänsliga. Tillståndslösa tjänster kan lagra beständiga tillstånd i en extern storage-tjänst som Azure Storage, Azure SQL Database eller Azure Cosmos DB. Använda en tillståndslös tjänst när tjänsten har ingen beständig lagring alls. En tillståndskänslig tjänst använder Service Fabric för att hantera din tjänstens tillstånd via dess tillförlitliga samlingar eller Reliable Actors programmeringsmodeller. 

När du skapar en namngiven tjänst kan ange du ett partitionsschema. Tjänster med stora mängder tillstånd dela data mellan partitioner. Varje partition är ansvarig för en del av slutförd status för tjänsten, som är fördelade på de klusternoder.  

Följande diagram visar relationen mellan program och instanser av tjänsten, partitioner och repliker.

![Partitioner och repliker i en tjänst][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Partitionering, skalning och tillgänglighet
[Partitionering](service-fabric-concepts-partitioning.md) inte är unik för Service Fabric. Ett välkänt formulär partitionering är Datapartitionering eller horisontell partitionering. Tillståndskänsliga tjänster med stora mängder tillstånd dela data mellan partitioner. Varje partition är ansvarig för en del av den fullständiga statusen för tjänsten. 

Repliker för varje partition är fördelade på klusternoder, vilket gör att din tjänsten tillstånd att [skala](service-fabric-concepts-scalability.md). När data behöver växa, partitioner växer och Service Fabric balanserar partitioner mellan noder för att effektivt utnyttja maskinvaruresurser. Om du lägger till nya noder i klustret kommer Service Fabric balansera partition replikerna för ökad antalet noder. Övergripande förbättrar programmets prestanda och minskar konkurrens om åtkomst till minnet. Om noderna i klustret inte används effektivt, kan du minska antalet noder i klustret. Service Fabric balanserar igen partition replikerna över minskade antalet noder för att bättre utnyttja maskinvara på varje nod.

Inom en partition har tillståndslösa tjänster för namngivna instanser medan tillståndskänslig namngivna tjänster ha repliker. Vanligtvis har tillståndslösa namngivna tjänster bara en partition eftersom de har inget internt tillstånd. Partitionen instanser ange för [tillgänglighet](service-fabric-availability-services.md). Om en instans misslyckas, andra instanser fortsätter att fungera normalt och Service Fabric skapar sedan en ny instans. Stateful som heter tjänster upprätthålla deras tillstånd i repliker och varje partition har sin egen replikuppsättningen. Läsa och skriva åtgärder utförs i en replik (kallas den primära servern). Ändringar av tillstånd från skriva operations replikeras till flera repliker (kallas Active sekundärservrar). Service Fabric skapar en replik misslyckas, en ny replik från de befintliga replikeringarna.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Tillståndslösa och tillståndskänsliga mikrotjänster för Service Fabric
Med Service Fabric kan du skapa program som består av mikrotjänster eller behållare. Tillståndslösa mikrotjänster (till exempel protokollgatewayar och webbproxyservrar) har inte ett föränderligt tillstånd utanför en begäran och svaret från tjänsten. Arbetsroller i Azure Cloud Services är ett exempel på en tillståndslös tjänst. Tillståndskänsliga mikrotjänster (till exempel användarkonton, databaser, enheter, kundvagnar och köer) har ett föränderligt och auktoritärt tillstånd bortom begäran och svaret. Dagens program med Internetskala består av en kombination av tillståndslösa och tillståndskänsliga mikrotjänster. 

En nyckel differentation med Service Fabric är starkt fokus på bygga tillståndskänsliga tjänster, antingen med den [inbyggda programmeringsmodeller ](service-fabric-choose-framework.md) eller med container tillståndskänsliga tjänster. [Programscenarier](service-fabric-application-scenarios.md) beskriver scenarier där tillståndskänsliga tjänster används.

Varför har tillståndskänsliga mikrotjänster tillsammans med tillståndslös dem? De två viktigaste skälen är:

* Du kan skapa hög genomströmning, låg latens, feltoleranta online transaktionsbearbetning (OLTP) services genom att hålla kod och Stäng data på samma dator. Några exempel är interaktiva skyltfönster, sökning, Sakernas Internet (IoT)-system, handelssystem, kreditkort bearbetning och bedrägeri identifiering system och personliga Posthantering.
* Du kan förenkla programdesign. Tillståndskänsliga mikrotjänster ta bort behovet av ytterligare köer och cacheminnen som traditionellt krävs för att uppfylla krav för tillgänglighet och svarstid i ett rent tillståndslösa program. Tillståndskänsliga tjänster är naturligt hög tillgänglighet och låg latens, vilket minskar antalet rörliga delar som kan hantera i ditt program som helhet.

## <a name="supported-programming-models"></a>Programmeringsmodeller som stöds
Service Fabric finns flera sätt att skriva och hantera dina tjänster. Tjänster kan använda Service Fabric-API: er för att dra full nytta av funktioner och ramverk för programmet för plattformen. Tjänster kan också vara alla kompilerade körbara program på alla språk och finns på ett Service Fabric-kluster. Mer information finns i [stöds programmeringsmodeller](service-fabric-choose-framework.md).

### <a name="containers"></a>Behållare
Som standard Service Fabric distribuerar och aktiverar tjänster som processer. Service Fabric kan också distribuera tjänster i [behållare](service-fabric-containers-overview.md). Allt kan du blanda tjänster i processer och tjänster i behållare i samma program. Service Fabric stöder distribution av Linux-behållare Windows behållare på Windows Server 2016. Du kan distribuera befintliga program, tillståndslösa tjänster eller tillståndskänsliga tjänster i behållare. 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) är ett enkelt ramverk för att skriva tjänster som integreras med Service Fabric-plattformen och dra nytta av en fullständig uppsättning funktioner. Reliable Services kan vara tillståndslös (liknar de flesta service-plattformar, till exempel webbservrar eller arbetsroller i Azure Cloud Services), där tillståndet sparas i en extern lösning, till exempel Azure DB eller Azure Table Storage. Reliable Services kan också vara stateful, där tillstånd sparat direkt i tjänsten sig själv med hjälp av tillförlitliga samlingar. Tillståndet gjort [högtillgänglig](service-fabric-availability-services.md) via replikering och distribueras via [partitionering](service-fabric-concepts-partitioning.md), allt hanteras automatiskt av Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Bygger på Reliable Services den [tillförlitliga aktören](service-fabric-reliable-actors-introduction.md) framework är ett programramverk som implementerar virtuella aktören mönster, baserat på aktören designmönstret. Tillförlitliga aktören framework används oberoende enheter beräkning och tillstånd med Enkeltrådig körning kallas aktörer. Tillförlitliga aktören ramverket innehåller inbyggd kommunikation för aktörer och ange före tillstånd beständighet och skalbar konfigurationer.

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric kan integreras med [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) som en förstklassigt programmeringsmodell för att skapa webb- och API-program.  ASP.NET Core kan användas på två olika sätt i Service Fabric:

- Värdbaserad som en gäst körbar fil. Främst används för att köra befintliga ASP.NET Core program på Service Fabric utan ändringar i koden.
- Kör i en tillförlitlig tjänst. Detta kan bättre integration med Service Fabric-runtime och tillståndskänsliga ASP.NET Core services.

### <a name="guest-executables"></a>Körbara gästfiler
En [gäst körbara](service-fabric-guest-executables-introduction.md) är en befintlig, valfri körbar fil (skrivs på olika språk) finns på ett Service Fabric-kluster tillsammans med andra tjänster. Gästen körbara filer integrerar inte direkt med Service Fabric API: er. Men de fortfarande dra nytta av funktioner som plattformen erbjuder, till exempel anpassade hälsa och läsa in reporting och tjänsten synlighet genom att anropa REST API: er. De kan också ha fullständig programmets livscykeln för support. 

## <a name="application-lifecycle"></a>Programlivscykel
Som med andra plattformar, ett program på Service Fabric vanligtvis går igenom följande faser: design, utveckling, testning, distribution, uppgradering, underhåll och borttagning. Service Fabric har förstklassigt stöd för fullständiga programmet livscykeln för molnprogram, från utveckling till distribution, dagliga hantering och underhåll för inaktivering av eventuell. Tjänstmodellen gör det möjligt för flera olika roller separat delta i programmet livscykel. [Service Fabric application livscykel](service-fabric-application-lifecycle.md) ger en översikt över de API: er och hur de används av olika roller i faserna i livscykeln för Service Fabric-programmet. 

Hela applivscykeln kan hanteras med [PowerShell-cmdlets](/powershell/module/ServiceFabric/), [CLI-kommandona](service-fabric-sfctl.md), [C#-API: er](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [Java API: er](/java/api/system.fabric._application_management_client), och [ REST API: er](/rest/api/servicefabric/). Du kan också ställa in kontinuerlig integration/kontinuerlig distribution pipelines med hjälp av verktyg som [Visual Studio Team Services](service-fabric-set-up-continuous-integration.md) eller [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).

Följande Microsoft Virtual Academy videon beskriver hur du hanterar livscykeln för programmet: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-content-roadmap/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="test-applications-and-services"></a>Testa program och tjänster
Om du vill skapa verkligen molnskala tjänster, är det viktigt att kontrollera att dina program och tjänster kan klara verkliga fel. Fel Analysis Service är utformad för att testa tjänster som bygger på Service Fabric. Med den [fel Analysis Service](service-fabric-testability-overview.md), du kan medföra meningsfulla fel och köra scenarier för testning mot dina program. Dessa fel och scenarier utöva och validera ett stort antal tillstånd och övergångar som en tjänst får under dess livslängd i en kontrollerad, säker och konsekvent sätt.

[Åtgärder](service-fabric-testability-actions.md) riktade till en tjänst för att testa med hjälp av enskilda fel. En service-utvecklare kan använda dessa som byggblock för att skriva komplicerade scenarier. Exempel på simulerade fel är:

* Starta om en nod för att simulera valfritt antal situationer där en dator eller virtuell dator startas.
* Flytta en replik av tillståndskänslig tjänsten att simulera belastningsutjämning med växling vid fel eller uppgradering av programmet.
* Anropa förlorar kvorum för en tillståndskänslig tjänst för att skapa en situation där skrivåtgärder inte kan fortsätta eftersom det inte finns tillräckligt med ”säkerhetskopiering” eller ”sekundär” repliker att acceptera nya data.
* Anropa förlust av data på en tillståndskänslig tjänsten för att skapa en situation där alla InMemory-tillstånd rensas helt ut.

[Scenarier](service-fabric-testability-scenarios.md) komplex består av en eller flera åtgärder. Fel Analysis Services innehåller två inbyggda kompletta scenarier:

* [Chaos scenariot](service-fabric-controlled-chaos.md)-simulerar kontinuerlig, överlagrad fel (korrekt och städat) i hela klustret under en längre tid.
* [Redundans scenariot](service-fabric-testability-scenarios.md#failover-test)-en version av chaos test-scenario som riktar sig till en specifik tjänst partition medan andra tjänster påverkas inte.

## <a name="clusters"></a>Kluster
Ett [Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras till och hanteras från. Kluster kan skalas upp till tusentals datorer. En dator eller virtuell dator som ingår i ett kluster kallas för en nod i klustret. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel placeringsegenskaper. Varje dator eller virtuell dator har en autostarttjänst `FabricHost.exe`, som startar vid start och startar sedan två körbara filer: Fabric.exe och FabricGateway.exe. Dessa två körbara filer som utgör noden. För att testa scenarier, du kan vara värd för flera noder i en enskild dator eller virtuell dator genom att köra flera instanser av `Fabric.exe` och `FabricGateway.exe`.

Service Fabric-kluster kan skapas på virtuella eller fysiska datorer som kör Windows Server- eller Linux. Du kan distribuera och köra Service Fabric-program i en miljö där du har en uppsättning Windows Server- eller Linux-datorer som är sammankopplade: lokalt, i Microsoft Azure eller på alla moln-providern.

I följande Microsoft Virtual Academy videon beskrivs Service Fabric-kluster: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/ClusterOverview.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="clusters-on-azure"></a>Kluster i Azure
Service Fabric-kluster som körs på Azure möjliggör integrering med andra Azure-funktioner och tjänster, vilket gör åtgärder och hantering av klustret enklare och mer tillförlitlig. Ett kluster är en Azure Resource Manager-resurs så att du kan ange modellen kluster som andra resurser i Azure. Resource Manager innehåller också enklare att hantera alla resurser som används av klustret som en enda enhet. Kluster på Azure är integrerade med Azure-diagnostik och logganalys. Klustret nodtyper är [skalningsuppsättningar i virtuella](/azure/virtual-machine-scale-sets/index), så autoskalning funktionen är inbyggd i.

Du kan skapa ett kluster i Azure via den [Azure-portalen](service-fabric-cluster-creation-via-portal.md), från en [mallen](service-fabric-cluster-creation-via-arm.md), eller från [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Service Fabric på Linux kan du skapa, distribuera och hantera hög tillgänglighet och skalbara program på Linux precis som i Windows. Service Fabric-ramverk (Reliable Services och Reliable Actors) är tillgängliga i Java på Linux, förutom C# (.NET Core). Du kan också skapa [körbara gästtjänster](service-fabric-guest-executables-introduction.md) med valfritt språk eller ramverk. Samordna Docker behållare stöds också. Docker-behållare kan köra gäst körbara filer eller interna Service Fabric-tjänster som använder Service Fabric-ramverk. Mer information finns i avsnittet om [Service Fabric på Linux](service-fabric-deploy-anywhere.md).

Det finns vissa funktioner som stöds i Windows, men inte på Linux. Mer information, [skillnader mellan Service Fabric på Linux och Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Fristående kluster
Service Fabric ger ett installationspaket att skapa fristående Service Fabric-kluster lokalt eller på alla moln-providern. Fristående kluster ger friheten att vara värd för ett kluster var du vill. Om dina data regleras av kompatibilitet eller andra begränsningar, eller du vill behålla dina data lokalt, du kan vara värd för ditt eget kluster och program. Service Fabric-program kan köras i flera värdbaserade miljöer utan ändringar, så att dina kunskaper för att skapa program följer med från en värdmiljö till en annan. 

[Skapa din första fristående Service Fabric-kluster](service-fabric-get-started-standalone-cluster.md)

Linux fristående kluster stöds inte ännu.

### <a name="cluster-security"></a>Klustersäkerhet
Kluster måste skyddas för att förhindra att obehöriga användare från att ansluta till klustret, särskilt när den har produktionsarbetsbelastningar som körs på den. Även om det är möjligt att skapa ett oskyddat kluster, gör att det blir anonyma användare att ansluta till den om av hanteringsslutpunkter som exponeras av det offentliga internet. Det går inte att senare aktiverar säkerheten på ett oskyddat kluster: Klustersäkerhet är aktiverat när klustret skapas.

Säkerhetsscenarier för klustret är:
* Säkerhet för nod till nod
* Klient-till-nod-säkerhet
* Rollbaserad åtkomstkontroll (RBAC)

Mer information finns [skydda ett kluster](service-fabric-cluster-security.md).

### <a name="scaling"></a>Skalning
Om du lägger till nya noder i klustret, balanserar Service Fabric partition repliker och instanser för det ökade antalet noder. Övergripande förbättrar programmets prestanda och minskar konkurrens om åtkomst till minnet. Om noderna i klustret inte används effektivt, kan du minska antalet noder i klustret. Service Fabric balanserar igen partition repliker och instanser för det minska antalet noder för att bättre utnyttja maskinvara på varje nod. Du kan skala kluster på Azure antingen [manuellt](service-fabric-cluster-scale-up-down.md) eller [programmässigt](service-fabric-cluster-programmatic-scaling.md). Fristående kluster kan skalas [manuellt](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Klusteruppgradering
Med jämna mellanrum släpps nya versioner av Service Fabric-körningsmiljön. Utför runtime eller fabric, uppgraderingar på klustret så att du alltid använder en [version som stöds](service-fabric-support.md). Du kan också uppdatera klusterkonfigurationen, till exempel certifikat eller programmet portar förutom fabric uppgraderingar.

Ett Service Fabric-kluster är en resurs som du äger, men delvis hanteras av Microsoft. Microsoft ansvarar för korrigerar det underliggande Operativsystemet och utföra fabric uppgraderingar på ditt kluster. Du kan ange ditt kluster ska ta emot automatiska fabric uppgraderingar när Microsoft publicerar en ny version eller välj en stöds fabric-versionen som du vill. Uppgradering av infrastrukturen och konfigurationen kan ställas in via Azure-portalen eller via Resource Manager. Mer information finns [uppgradera ett Service Fabric-kluster](service-fabric-cluster-upgrade.md). 

En fristående klustret är en resurs som du äger helt. Du är ansvarig för uppdatering av det underliggande Operativsystemet och initierar fabric uppgraderingar. Om klustret kan ansluta till [ https://www.microsoft.com/download ](https://www.microsoft.com/download), du kan ange ditt kluster ska hämta automatiskt och etablera nya Service Fabric runtime-paketet. Du kan sedan initiera uppgraderingen. Om klustret inte kan komma åt [ https://www.microsoft.com/download ](https://www.microsoft.com/download), du kan hämta det nya runtime-paketet manuellt från en internet-ansluten dator och sedan initiera uppgraderingen. Mer information finns [uppgradera en fristående Service Fabric-kluster](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Hälsoövervakning
Service Fabric introducerar en [hälsomodell](service-fabric-health-introduction.md) utformats för att flaggan ohälsosamt klustret och programmet villkoren på specifika enheter (till exempel klusternoder och tjänsten repliker). Hälsomodell använder hälsa rapportörer (systemkomponenter och watchdogs). Målet är enkelt och snabbt diagnos och reparation. Service-skrivare måste du tänka igenom summa om hälsotillstånd och hur du [utforma hälsa reporting](service-fabric-report-health.md#design-health-reporting). Eventuella villkor som kan påverka hälsa redovisas, särskilt om det kan hjälpa flaggan problem nära roten. Information om hälsa kan spara tid och arbete på felsökning och undersökning när tjänsten är igång och körs i skala i produktion.

Service Fabric-rapportörer övervakaren identifieras villkor av intresse. De rapport om dessa villkor baserat på deras lokala vyn. Den [hälsoarkivet](service-fabric-health-introduction.md#health-store) samlar in hälsa data skickas av alla rapportörer för att avgöra om enheterna är globalt felfritt. Modellen är avsedd att vara omfattande, flexibla och enkla att använda. Kvaliteten på hälsorapporter anger korrektheten i vyn hälsa i klustret. Falska positiva identifieringar som visar felaktigt ohälsosamt problem kan påverka uppgraderingar och andra tjänster som använder hälsa data. Exempel på sådana tjänster är reparation och mekanismer för aviseringar. Därför behövs tänka för att tillhandahålla rapporter som samlar in villkor för intresse för bästa möjliga sätt.

Rapportering kan göras från:
* Övervakade Service Fabric-tjänsten målrepliker eller instanser.
* Internt watchdogs distribueras som en Service Fabric-tjänsten till exempel ett Service Fabric tillståndslös som övervakar villkor och skickar rapporter. Watchdogs kan distribueras på alla noder eller kan tillhöra den övervakade tjänsten.
* Internt watchdogs som körs på Service Fabric-noder men har inte implementerats som Service Fabric-tjänster.
* Externa watchdogs som avsökning resurs från utanför Service Fabric-klustret (till exempel övervakningstjänsten som Gomez).

Out of box rapport Service Fabric-komponenter hälsotillstånd för alla entiteter i klustret. [System hälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) ger inblick i klustret och programmet funktioner och flaggan problem med hjälp av hälsotillstånd. System hälsorapporter Kontrollera att entiteter implementeras och fungerar korrekt ur ett Service Fabric runtime för program och tjänster. Rapporterar inte ange några hälsoövervakning av affärslogiken för tjänsten eller identifiera låsta processer. Att lägga till specifika hälsoinformation i din tjänst logik [implementera anpassade hälsa reporting](service-fabric-report-health.md) i dina tjänster.

Service Fabric finns flera sätt att [visa hälsorapporter](service-fabric-view-entities-aggregated-health.md) samman i health store:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) eller andra visualiseringsverktyg för.
* Hälsoförfrågningar (via [PowerShell](/powershell/module/ServiceFabric/), [CLI](service-fabric-sfctl.md), [C# FabricClient APIs](/dotnet/api/system.fabric.fabricclient.healthclient) och [Java FabricClient APIs](/java/api/system.fabric._health_client), eller [REST API: er](/rest/api/servicefabric)).
* Allmänna frågor som returnerar en lista över enheter som har hälsa som någon av egenskaperna (via PowerShell, CLI, API: er eller REST).

Följande Microsoft Virtual Academy videon beskrivs Service Fabric-hälsomodell och hur de används: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-content-roadmap/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik
[Övervaknings- och diagnostikfunktionerna](service-fabric-diagnostics-overview.md) är viktigt att utveckla, testa och distribuera program och tjänster i en miljö. Service Fabric-lösningar som fungerar bäst när du planerar och implementerar övervakning och diagnostik för att kontrollera program och tjänster fungerar som förväntat i en miljö för lokal utveckling eller i produktion.

De huvudsakliga målen med övervakning och diagnostik är att:

- Identifiera och diagnostisera problem med maskinvara och infrastruktur
- Identifiera problem med programvara och app, minska avbrottstiden för tjänsten
- Förstå resource förbrukning och hjälper dig att enheten operations beslut
- Optimera prestanda för program, tjänster och infrastruktur
- Generera affärsinsikter och identifiera förbättringsområden
 
Det totala arbetsflödet för övervakning och diagnostik består av tre steg:

1. Händelsegenerering: Detta innefattar händelser (loggar, spårningar, anpassade händelser) i infrastrukturen (kluster), plattform och program / service nivå
2. Händelsen aggregering: genererade händelser behöver samlas in och sammanställs innan de kan visas
3. Analys: händelser måste vara visualiserade och tillgänglig i vissa format för analys och visa efter behov

Flera produkter är tillgängliga som omfattar följande tre områden och du kan välja olika tekniker för varje. Mer information finns [övervaknings- och diagnostikfunktionerna för Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du skapar ett [kluster i Azure](service-fabric-cluster-creation-via-portal.md) eller ett [fristående kluster i Windows](service-fabric-cluster-creation-for-windows-server.md).
* Prova att skapa en tjänst med hjälp av programmeringsmodellerna [Reliable Services](service-fabric-reliable-services-quick-start.md) eller [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Lär dig hur du [migrera från molntjänster](service-fabric-cloud-services-migration-differences.md).
* Lär dig hur du [övervaka och diagnostisera services](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Lär dig hur du [testa dina appar och tjänster](service-fabric-testability-overview.md).
* Lär dig hur du [hantera och samordna klusterresurser](service-fabric-cluster-resource-manager-introduction.md).
* Titta igenom den [Service Fabric-exempel](http://aka.ms/servicefabricsamples).
* Lär dig mer om [Service Fabric supportalternativ](service-fabric-support.md).
* Läs den [teambloggen](https://blogs.msdn.microsoft.com/azureservicefabric/) för artiklar och meddelanden.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png

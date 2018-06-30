---
title: Övervakning av säkerhetshälsa i Service Fabric | Microsoft Docs
description: En introduktion till Azure Service Fabric-modell, som ger övervakning av klustret och dess program och tjänster för hälsoövervakning.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: 1d979210-b1eb-4022-be24-799fd9d8e003
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: fc0bb56e85c2a9cf7a458b0f6d97887d392ee65f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114324"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Introduktion till Service Fabric-hälsoövervakning
Azure Service Fabric introducerar en hälsomodell som ger omfattande, flexibla och utökningsbara hälsoutvärderingen och rapportering. Modellen tillåter nära realtid övervakning av tillståndet för klustret och de tjänster som körs i den. Du kan lätt få information om hälsa och rätta potentiella problem innan de sprids och orsaka massiv avbrott. I vanliga modellen services skicka rapporter baserat på deras lokala vyer och att informationen sammanställs för att ge en övergripande-klusternivå vyn.

Service Fabric-komponenter använder den här omfattande hälsomodell för att rapportera det aktuella tillståndet. Du kan använda samma metod för rapporten hälsa från ditt program. Om du investerar i hög kvalitet hälsa reporting som samlar in din anpassade villkor kan du identifiera och åtgärda problem för tillämpningsprogrammet körs mycket enklare.

Följande Microsoft Virtual Academy videon beskriver också Service Fabric-hälsomodell och hur de används: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-health-introduction/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

> [!NOTE]
> Vi har startats undersystemet hälsotillstånd för att tillgodose ett behov för övervakade uppgraderingar. Service Fabric ger övervakade program och kluster uppgraderingar som garantera fullständig, inget driftstopp och minimala att ingen åtgärd från användaren. Uppgraderingen kontrollerar hälsotillstånd baserat på konfigurerad uppgradera principer för att uppnå dessa mål. En uppgradering kan fortsätta endast när hälsa respekterar önskade trösklar. Annars är uppgraderingen antingen automatiskt återställs eller pausas för att ge administratörer möjlighet att åtgärda problemen. Läs mer om programuppgraderingar i [i den här artikeln](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Hälsoarkivet
Health store behåller hälsorelaterade information om entiteter i klustret för enkel hämtning och utvärdering. Det implementeras som en Service Fabric beständiga tillståndskänslig service för att säkerställa hög tillgänglighet och skalbarhet. Health store är en del av den **fabric: / System** program och är tillgänglig när klustret är igång och körs.

## <a name="health-entities-and-hierarchy"></a>Hälsotillstånd entiteter och hierarki
Hälsotillstånd entiteter är uppdelade i en logisk hierarki som samlar in interaktioner och beroenden mellan olika enheter. Health store skapar automatiskt hälsa entiteter och hierarki baserat på rapporter som tagits emot från Service Fabric-komponenter.

Hälsotillstånd entiteter spegla Service Fabric-enheter. (Till exempel **hälsa programmet entiteten** matchar en programinstans distribuerats i klustret, medan **hälsa nod entiteten** matchar en klusternod för Service Fabric.) Hälsotillstånd hierarkin avbildas interaktioner systementiteter och det är grunden för avancerade hälsoutvärderingen. Du kan lära dig om nyckelbegrepp i Service Fabric [teknisk översikt för Service Fabric](service-fabric-technical-overview.md). Mer information om programmet finns [Service Fabric programmodell](service-fabric-application-model.md).

Hälsotillstånd entiteter och hierarki kan klustret och program till att effektivt rapporterade felsökas och övervakas. Hälsomodell ger en rättvisande *detaljerade* representation av hälsotillståndet för många flytta delar i klustret.

![Enheter som hälsa.][1]
Entiteterna hälsa ordnas i en hierarki baserat på överordnade och underordnade relationer.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Hälsotillstånd entiteter är:

* **Klustret**. Representerar hälsan för Service Fabric-klustret. Klustret hälsorapporter beskrivs villkor som påverkar hela klustret. Dessa villkor påverkar flera entiteter i klustret eller själva klustret. Baserat på villkoret kan inte personen begränsa problemet till en eller flera ohälsosamt underordnade. Exempel: hjärna klustrets dela på grund av nätverksproblem partitioneringen och kommunikation.
* **Noden**. Representerar hälsan för en Service Fabric-nod. Noden hälsorapporter beskrivs villkor som påverkar funktionen nod. De vanligtvis påverkar alla distribuerade entiteter som körs på den. Exempel är noden slut på diskutrymme (eller andra datoromfattande egenskaper, till exempel minne, anslutningar) och när en nod är nere. Noden entiteten identifieras av nodnamnet (sträng).
* **Programmet**. Representerar hälsan för en programinstans som körs i klustret. Programmet hälsorapporter beskrivs villkor som påverkar den övergripande hälsan för programmet. De kan inte begränsas till enskilda underordnade (tjänster eller distribuerade program). Exempel inkluderar slutpunkt till slutpunkt interaktionen mellan olika tjänster i programmet. Entiteten programmet identifieras med namnet på programmet (URI).
* **Tjänsten**. Representerar hälsan för en tjänst som körs i klustret. Tjänsten hälsorapporter beskrivs villkor som påverkar den övergripande hälsan för tjänsten. Personen kan inte begränsa problemet till ett ohälsosamt partition eller replik. Exempel är en tjänstkonfiguration (till exempel port eller externa filresurs) som orsakar problem för alla partitioner. Tjänst-enhet identifieras av tjänstnamn (URI).
* **Partitionen**. Representerar hälsan för en partition med tjänsten. Partitionen hälsorapporter beskrivs villkor som påverkar hela replikuppsättningen. Exempel: när antalet repliker är under målantalet och när en partition är förlorar kvorum. Entiteten partition identifieras med partitions-ID (GUID).
* **Replik**. Representerar hälsan för en tillståndskänslig service replik eller en tillståndslös tjänstinstans. Repliken är den minsta enheten watchdogs och dess komponenter kan rapportera om för ett program. Exempel är en primär replik som inte kan replikera operations till sekundärservrar och långsam replikering för tillståndskänsliga tjänster. Dessutom kan en tillståndslös instans rapportera när den har snart slut på resurser eller har problem med nätverksanslutningen. Entiteten replik identifieras med partitions-ID (GUID) och repliken eller instans-ID (long).
* **DeployedApplication**. Representerar hälsan för en *program som körs på en nod*. Distribuerade program hälsorapporter beskriver villkor som är specifika för programmet på den nod som kan begränsas tjänsten paket distribueras på samma nod. Exempel: fel när programpaket inte kan hämtas på noden och problem med att ställa in programmet säkerhetsobjekt på noden. Det distribuerade programmet identifieras av namn (URI) och nodnamnet (sträng).
* **DeployedServicePackage**. Representerar hälsan för en tjänstepaket som körs på en nod i klustret. Det beskriver villkor som är specifika för service-paket som inte påverkar andra servicepaket på samma nod för samma program. Exempel är en kodpaketet i tjänstepaketet som inte kan startas och ett konfigurationspaket som inte kan läsas. Distribuerat tjänstpaket identifieras av programnamn (URI), nodnamnet (sträng), manifestet tjänstnamn (sträng) och activation service paket-ID (sträng).

Granulariteten för hälsomodell gör det enkelt att identifiera och åtgärda problem. Till exempel är det möjligt att rapportera att programinstansen är i feltillstånd om en tjänst inte svarar. Rapportering på att nivå inte är idealiskt, men eftersom problemet inte kanske påverkar alla tjänster i programmet. Rapporten ska användas till tjänsten feltillstånd eller till en viss underordnad partition om mer information som pekar på den aktuella partitionen. Data automatiskt hämtar via en hierarki och en ohälsosamt partition görs synliga på tjänster och program. Den här aggregeringen hjälper till att identifiera och lösa orsaken till problemet snabbare.

Hälsotillstånd hierarki består av överordnade och underordnade relationer. Ett kluster består av noder och program. Program tjänster och distribuerade program. Distribuerade program har distribuerat tjänstpaket. Tjänster som har partitioner och varje partition har en eller flera repliker. Det finns en särskild relation mellan noder och distribuerade entiteter. Ett feltillstånd nod påverkar som rapporteras av dess utfärdare systemkomponent Failover Manager-tjänsten distribuerade program, servicepaket och repliker som har distribuerats på den.

Hälsotillstånd hierarkin representerar det aktuella tillståndet i systemet baserat på senaste hälsorapporter som är nästan realtidsdata.
Interna och externa watchdogs kan rapportera på samma enheter baserat på programspecifika logik eller anpassade övervakade villkor. Användarrapporter samexistera med systemrapporter.

Planera att investera i hur du rapportera och svara på hälsa vid utformningen av en stor molntjänst. Den här direkta investement gör det enklare att felsöka, övervaka och hantera tjänsten.

## <a name="health-states"></a>Hälsotillstånd
Service Fabric använder tre hälsotillstånd för att beskriva om en entitet är felfri: OK, varning och fel. Alla rapporter som skickas till arkivet hälsa måste ange ett av dessa lägen. Utvärderingsresultat hälsa är ett av dessa lägen.

Möjliga [hälsotillstånd](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) är:

* **OK**. Entiteten är felfri. Det finns inga kända problem rapporteras på den eller dess underordnade objekt (i förekommande fall).
* **Varning**. Entiteten har några problem, men det kan fortfarande fungera korrekt. Till exempel fördröjning, men de gör inte problemen funktionella ännu. I vissa fall kan åtgärda varningsvillkor själva utan externa åtgärder. I dessa fall hälsorapporter öka medvetenhet och ger inblick i vad som händer. I annat fall kan varningsvillkor försämras i ett allvarligt problem utan åtgärder från användaren.
* **Fel**. Entiteten är felfri. Bör åtgärdas för att åtgärda tillståndet för entiteten eftersom den inte fungera korrekt.
* **Okänd**. Entiteten finns inte i health store. Det här resultatet kan hämtas från de distribuerade frågor som sammanfoga resultat från flera komponenter. Till exempel get-noden listfrågan går till **FailoverManager**, **ClusterManager**, och **HealthManager**; hämta programmet listfrågan går till  **ClusterManager** och **HealthManager**. De här frågorna sammanfoga resultat från flera komponenter. Om en annan komponent returnerar en entitet som inte finns i health store resultatet är okänd hälsotillstånd. En entitet är inte i arkivet eftersom hälsorapporter som ännu inte har bearbetats eller entiteten har rensats efter borttagningen.

## <a name="health-policies"></a>Hälsoprinciper
Health store gäller hälsoprinciper för att avgöra om en entitet är felfri baserat på dess rapporter och dess underordnade.

> [!NOTE]
> Hälsoprinciper kan anges i klustermanifestet (för kluster- och nodnamn hälsoutvärderingen) eller i programmanifestet (för utvärdering av programmet och alla dess underordnade). Hälsotillstånd utvärdering begäranden kan även skicka in anpassade utvärdering hälsoprinciper som endast används för att utvärdering.
> 
> 

Som standard gäller Service Fabric strikta regler (allt måste vara felfria) för den hierarkiska relationen överordnad-underordnad. Om ett enda av underordnade har en ohälsosam händelse anses överordnad feltillstånd.

### <a name="cluster-health-policy"></a>Klustret hälsoprincip
Den [kluster hälsoprincip](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) används för att utvärdera hälsotillståndet för klustret och noden hälsotillstånd. Principen kan definieras i klustermanifestet. Om det inte finns, används standardprincipen (noll tillåten fel).
Klustret hälsoprincipen innehåller:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Anger om att behandla varningen hälsa rapporterar som fel under hälsoutvärderingen. Standard: false.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Anger maximalt tillåten procentandel av program som kan vara felaktiga innan klustret anses vara fel.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Anger maximalt tillåten procentandel av noder som kan vara felaktiga innan klustret anses vara fel. I stora kluster är vissa noder alltid ned eller ut för reparationer, så den här procentandelen ska konfigureras för att tolerera som.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Programmet hälsa princip typkarta kan användas under hälsoutvärderingen för klustret för att beskriva specialprogram typer. Som standard är alla program sätta i en pool och med MaxPercentUnhealthyApplications. Om vissa programtyper ska behandlas annorlunda, kan de tas bort från den globala poolen. I stället utvärderas de mot procenttal som är associerade med deras programmets typnamn i kartan. Till exempel i ett kluster finns tusentals program av olika typer och några programinstanser för kontroll av en särskild programtyp. Styra program ska aldrig vara fel. Du kan ange globala MaxPercentUnhealthyApplications till 20% till tolerera några fel, men för programtyp ”ControlApplicationType” ange MaxPercentUnhealthyApplications till 0. På så sätt kan om några av de många program är felfria, men under den globala ohälsosamma procentsatsen klustret skulle utvärderas till varning. Ett varningshälsotillstånd påverkar inte klustrets uppgraderingen eller andra övervakning utlöses av hälsotillståndet för fel. Men även en kontrollprogrammet i fel skulle göra kluster feltillstånd, vilket utlöser återställningsfasen eller pausar klusteruppgradering, beroende på konfigurationen av uppgraderingen.
  För de programtyper som definieras i kartan, tas alla programinstanser utanför den globala poolen av program. De utvärderas baserat på det totala antalet program av programtypen med specifika MaxPercentUnhealthyApplications från mappningen. Alla övriga program finns kvar i den globala poolen och utvärderas med MaxPercentUnhealthyApplications.

I följande exempel är ett utdrag ur ett klustermanifest. Om du vill definiera poster i typen programavbildningen prefix parameternamnet med ”ApplicationTypeMaxPercentUnhealthyApplications-”, följt av namnet på programmet.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Programmets hälsoprincip
Den [programmets hälsoprincip](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) beskriver hur utvärderingen av händelser och underordnade tillstånd aggregeringen är klar för program och deras underordnade. Det kan definieras i programmanifestet, **ApplicationManifest.xml**, i programpaketet. Om inga principer anges, förutsätter Service Fabric att entiteten feltillstånd om den har en hälsorapport eller en underordnad på hälsotillståndet varning eller fel.
De konfigurerbara principerna är:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Anger om att behandla varningen hälsa rapporterar som fel under hälsoutvärderingen. Standard: false.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Anger maximalt tillåten procentandel av distribuerade program som kan vara felaktiga innan programmet anses vara fel. Den här procent beräknas genom att dividera antalet felaktiga distribuerade program över antalet noder som program som för närvarande har distribuerats på i klustret. Beräkningen Avrundar uppåt till tolererar att ett fel på litet antal noder. Standard procentandel: noll.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Anger standard service typen hälsoprincipen, som ersätter standardprincipen för hälsotillstånd för alla tjänsttyper av i programmet.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Innehåller en karta över service hälsoprinciper per typ av tjänst. Dessa principer ersätter tjänsten typen hälsa standardprinciperna för varje typ av angivna tjänsten. Du kan till exempel konfigurera hälsoprinciper för utvärderingen annorlunda om ett program har en typ av tillståndslösa gateway och en tillståndskänslig engine service type. När du anger princip per typ av tjänst kan du få mer detaljerad kontroll över hälsotillståndet för tjänsten.

### <a name="service-type-health-policy"></a>Hälsoprincip för Service-typen
Den [service type hälsoprincip](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) anger hur att utvärdera och sammanställa tjänsterna och underordnade tjänster. Principen innehåller:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Anger maximalt tillåten procentandel av feltillstånd partitioner innan en tjänst anses vara felaktig. Standard procentandel: noll.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Anger maximalt tillåten procentandel av feltillstånd repliker innan en partition anses vara felaktig. Standard procentandel: noll.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Anger maximalt tillåten procentandel av feltillstånd tjänster innan programmet anses vara felaktig. Standard procentandel: noll.

Följande exempel är ett utdrag ur ett programmanifest:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Utvärdering av hälsotillstånd
Användare och tjänster som automatiskt kan utvärdera hälsan för en enhet när som helst. Om du vill utvärdera en entitets hälsotillstånd health store mängder hälsotillstånd rapporter på entiteten och utvärderar alla dess underordnade (i förekommande fall). Hälsotillstånd aggregering algoritmen använder hälsoprinciper som anger hur du utvärdera hälsorapporter och sammanställa underordnade hälsotillstånd (i förekommande fall).

### <a name="health-report-aggregation"></a>Hälsotillstånd rapporten aggregering
En entitet kan ha flera hälsorapporter som skickas av olika rapportörer (systemkomponenter eller watchdogs) på olika egenskaper. Aggregeringen använder associerade hälsoprinciper, särskilt ConsiderWarningAsError tillhör hälsoprincip för programmet eller ett kluster. ConsiderWarningAsError anger hur att utvärdera varningar.

Sammanställda hälsotillståndet utlöses av den *sämsta* hälsorapporter på enheten. Om det finns minst ett fel hälsorapport, är aggregerade hälsotillståndet ett fel.

![Hälsotillstånd rapporten aggregering med felrapport.][2]

En hälsa entitet som har en eller flera fel hälsorapporter utvärderas som fel. Detsamma gäller för ett utgånget hälsorapport, oavsett dess hälsotillstånd.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Om det finns inga felrapporter och en eller flera varningar, är det totala hälsotillståndet varning eller fel, beroende på flaggan för princip för ConsiderWarningAsError.

![Hälsotillstånd rapporten aggregering med varning rapporten och ConsiderWarningAsError false.][3]

Hälsotillstånd rapporten aggregering med varning rapporten och ConsiderWarningAsError FALSKT (standard).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Underordnade hälsa aggregering
Sammanställda hälsotillståndet för en entitet visar hälsotillstånd underordnade (i förekommande fall). Algoritmen för sammanställningen underordnade hälsotillstånd använder hälsoprinciper tillämpliga utifrån enhetstyp.

![Underordnade entiteter hälsa aggregering.][4]

Underordnade aggregering baserat på hälsoprinciper.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

När health store har utvärderas alla underordnade aggregerar deras hälsotillstånd baserat på den konfigurerade maximala procentandelen ohälsosamt underordnade. Procenttalet hämtas från principen som baseras på typen entiteten och underordnade.

* Om alla underordnade har OK tillstånd, är hälsotillstånd underordnade som aggregeras OK.
* Om underordnade har både OK och varning tillstånd, hälsotillstånd underordnade som aggregeras är en varning.
* Om det finns underordnade med fel tillstånd som inte tar hänsyn till det högsta tillåtna procentandelen ohälsosamt underordnade, är aggregerade hälsotillståndet ett fel.
* Om underordnade med fel tillstånd respektera maximala procentandelen ohälsosamt underordnade, aggregerade hälsotillståndet är en varning.

## <a name="health-reporting"></a>Rapportering av hälsotillstånd
Systemkomponenter, System Fabric program och intern/extern watchdogs kan rapportera mot Service Fabric-enheter. Rapportörer Se *lokala* bestämning av hälsotillståndet för övervakade enheter, baserat på de villkor som de övervakar. De behöver inte titta på alla globalt tillstånd eller samla in data. Önskat beteende är att enkelt rapportörer och inte komplexa organismer som behöver titta på många saker att härleda vilken information som ska skickas.

Om du vill skicka hälsotillståndsdata till health store, måste en rapport att identifiera berörda entiteten och skapa en hälsorapport. Om du vill skicka rapporten använder den [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API, rapporten hälsa API: er som är exponerad på den `Partition` eller `CodePackageActivationContext` objekt, PowerShell-cmdletar och REST.

### <a name="health-reports"></a>Hälsorapporter
Den [hälsorapporter](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) innehålla följande information för varje entiteter i klustret:

* **SourceId**. En sträng som unikt identifierar rapport i händelsen hälsa.
* **Identifiering**. Identifierar den enhet där rapporten används. Den skiljer sig beroende på den [entitetstypen](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Kluster. Ingen.
  * Noden. Nodnamnet (sträng).
  * Programmet. Programnamn (URI). Representerar namnet på programinstansen distribueras i klustret.
  * Tjänsten. Tjänstens namn (URI). Representerar namnet på instansen för tjänsten som distribuerats i klustret.
  * Partition. Partitions-ID (GUID). Representerar den unika identifieraren för partitionen.
  * Repliken. Tillståndskänslig service-replik-ID eller tillståndslösa tjänstinstans-ID (INT64).
  * DeployedApplication. Programnamnet (URI) och nodnamnet (sträng).
  * DeployedServicePackage. Programnamn (URI), nodnamnet (sträng) och service manifest namn (sträng).
* **Egenskapen**. En *sträng* (inte en fast uppräkning) som gör att personen att kategorisera händelsen hälsa för en specifik egenskap för entiteten. Till exempel rapport A kan rapportera om hälsan hos Node01 ”lagringsegenskap” och rapport B kan rapportera om hälsan hos Node01 ”anslutning”-egenskap. Rapporterna behandlas som separata hälsa händelser för entiteten Node01 i health store.
* **Beskrivning av**. En sträng som gör att en rapport att tillhandahålla detaljerad information om händelsen hälsa. **SourceId**, **egenskapen**, och **HealthState** fullständigt beskriva rapporten. Beskrivningen lägger till läsbart information om rapporten. Texten som gör det enklare för administratörer och användare att förstå hälsorapporten.
* **HealthState**. En [uppräkningen](service-fabric-health-introduction.md#health-states) som beskriver hälsotillståndet för rapporten. De godkända värdena är OK, varning och fel.
* **TimeToLive**. Timespan som anger hur länge hälsorapporten är giltig. Tillsammans med **RemoveWhenExpired**, gör det hälsoarkivet vet hur du ska utvärdera händelser som har upphört att gälla. Värdet är oändlig och rapporten är giltig alltid som standard.
* **RemoveWhenExpired**. Ett booleskt värde. Om värdet är true har upphört att gälla hälsorapport automatiskt bort från health store och rapporten påverkar inte entiteten hälsoutvärderingen. Används när rapporten är giltig för en angiven tidsperiod endast och personen behöver inte uttryckligen rensa den. Det används också för att ta bort rapporter från health store (till exempel en watchdog ändras och slutar att skicka rapporter med föregående käll- och egenskapen). Den kan skicka en rapport med en kort TimeToLive tillsammans med RemoveWhenExpired för att rensa alla tidigare tillstånd från health store. Om värdet anges till false, behandlas rapporten har upphört att gälla som ett fel på utvärderingen hälsa. False-värde som signalerar till health store att källan bör regelbundet rapportera om den här egenskapen. Om det inte måste sedan det vara något fel med watchdog. Den watchdog hälsa fångas av överväger händelsen som ett fel.
* **SequenceNumber**. Ett positivt heltal som måste vara allt större representerar ordningen på rapporterna. Den används av health store för att identifiera inaktuella rapporter som tas emot sent på grund av fördröjningar i nätverket eller andra problem. En rapport avvisas om sekvensnumret är mindre än eller lika med de nyligen tillämpas för samma entitet, källa och egenskapen. Om den inte är angiven ska sekvensnumret genereras automatiskt. Det är nödvändigt att placera i sekvensnumret endast när rapportering för övergångslägen. I den här situationen måste källan Kom ihåg vilken den skicka rapporterna och informationen för återställning på växling vid fel.

Dessa fyra delar av information – SourceId, identifiering, egenskap och HealthState--krävs för varje hälsorapport. SourceId strängen får inte börja med prefixet ”**System.**”, som är reserverad för systemrapporter. Det finns endast en rapport för samma käll- och egenskapen för samma entitet. Flera rapporter för samma käll- och egenskapen åsidosätter varandra, antingen på klientsidan hälsa (om de grupperas) eller hälsa lagra sida. Ersätter baseras på sekvensnummer; nyare rapporter (med högre sekvensnummer) ersätter äldre rapporter.

### <a name="health-events"></a>Hälsotillstånd händelser
Internt health store håller [hälsa händelser](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), som innehåller all information från rapporter och ytterligare metadata. Metadata innehåller den tid som rapporten har angetts till klienten hälsotillstånd och den tid som den har ändrats på serversidan. Händelserna hälsa returneras av [hälsoförfrågningar](service-fabric-view-entities-aggregated-health.md#health-queries).

Tillagda metadata innehåller:

* **SourceUtcTimestamp**. Tiden i rapporten har fått hälsa klienten (Coordinated Universal Time).
* **LastModifiedUtcTimestamp**. Den tid som rapporten senast ändrades på serversidan (Coordinated Universal Time).
* **IsExpired**. En flagga som indikerar om rapporten har upphört att gälla när frågan kördes av health store. En händelse kan ha gått endast om RemoveWhenExpired är false. Annars händelsen returneras inte av frågan och tas bort från store.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. Senaste tid för fel-OK/varning övergångar. De här fälten kan historik över hälsotillståndet tillståndsövergångar för händelsen.

Fälten tillstånd övergång kan användas för smartare aviseringar eller ”historiska” hälsoinformation för händelsen. De aktiverar som scenarier:

* Meddela när en egenskap har varit varning/fel för mer än X minuter. Kontrollerar villkor för en viss tidsperiod undviker aviseringar på tillfälliga förhållanden. Till exempel en avisering om hälsotillståndet har varning under mer än fem minuter kan översättas till (HealthState == varning och nu - LastWarningTransitionTime > 5 minuter).
* Aviseringen endast på de villkor som har ändrats under senaste X minuter. Om en rapport var redan vid fel innan den angivna tiden, kan den ignoreras eftersom den redan har signalerat tidigare.
* Om en egenskap växla mellan varnings- och bestämma hur länge den har varit ohälsosam (det vill säga inte OK). Till exempel en avisering om egenskapen inte har varit vid god hälsa under mer än fem minuter kan översättas till (HealthState! = Ok och nu - LastOkTransitionTime > 5 minuter).

## <a name="example-report-and-evaluate-application-health"></a>Exempel: Rapport och utvärdera programs hälsa
I följande exempel skickas en hälsorapport via PowerShell på programmet **fabric: / WordCount** från källan **MyWatchdog**. Hälsorapporten innehåller information om hälsa egenskapen ”tillgänglighet” i ett felaktigt hälsotillstånd med oändlig TimeToLive. Sedan frågar programmets hälsotillstånd, vilken returnerar samman hälsa tillstånd fel och rapporterade hälsa händelser i listan över hälsotillstånd händelser.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Hälsotillstånd Modellanvändning
Hälsomodell kan molntjänster och den underliggande Service Fabric-plattformen för att skala, eftersom övervakning och hälsotillstånd fördelas mellan de olika övervakarna i klustret.
Andra system har en centraliserad tjänst på klusternivå som Parsar alla de *potentiellt* användbar information som sänds av tjänster. Den här metoden hämmar sina skalbarhet. Den också tillåter inte att samla in viss information för att identifiera problem och eventuella problem som nära rotorsaken som möjligt.

Hälsomodell används kraftigt för övervakning och diagnos av, för att bedöma hälsa för klustret och program och för övervakade uppgraderingar. Andra tjänster använder hälsa data för att utföra automatiska reparationer, skapa klustret hälsohistorik och utfärda aviseringar på vissa villkor.

## <a name="next-steps"></a>Nästa steg
[Visa Service Fabric-hälsorapporter](service-fabric-view-entities-aggregated-health.md)

[Använda systemet hälsorapporter för felsökning](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Hur du rapporten och kontrollera tjänstens hälsa](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Lägg till anpassade hälsorapporter för Service Fabric](service-fabric-report-health.md)

[Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md)


---
title: Hälsoövervakning i Service Fabric | Microsoft Docs
description: En introduktion till Azure Service Fabric-modell som tillhandahåller övervakning för klustret och dess program och tjänster för hälsoövervakning.
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
ms.openlocfilehash: 6cba4e1fd9c9fe5fdaa7ff4513218a606a4eace9
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215238"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Introduktion till Service Fabric-hälsoövervakning
Azure Service Fabric introducerar en hälsomodellen som tillhandahåller omfattande, flexibel och utökningsbar hälsotillstånd utvärdering och rapportering. Modellen tillåter nära realtid övervakning av statusen för klustret och de tjänster som körs i den. Du kan enkelt hämta hälsoinformation och korrigera potentiella problem innan de spridas vidare och orsaka avbrott i stora. I den vanliga modellen tjänster skicka rapporter baserat på deras lokala vyer och att informationen sammanställs för att ge en övergripande-klusternivå vy.

Service Fabric-komponenter använder den här omfattande hälsomodellen för att rapportera enheternas aktuella status. Du kan använda samma metod för rapporten hälsa från dina program. Om du investerar i hög kvalitet tillståndsrapportering som samlar in dina anpassade villkor kan du identifiera och åtgärda problem för programmet som körs på ett enkelt sätt.

Följande Microsoft Virtual Academy-video beskrivs också hälsomodellen Service Fabric och hur de används: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-health-introduction/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

> [!NOTE]
> Vi igång undersystemet hälsotillstånd för att åtgärda ett behov av övervakade uppgraderingar. Service Fabric tillhandahåller övervakade program- och uppgraderingar som ser till att full tillgänglighet, utan avbrott och minimal att ingen åtgärd från användaren. Uppgraderingen kontrollerar hälsotillstånd baserat på konfigurerade uppgradera principer för att uppnå dessa mål. En uppgradering Fortsätt bara om hälsotillstånd respekterar önskade trösklar. I annat fall är uppgraderingen antingen automatiskt återtagits eller pausas för att ge administratörer en chans att åtgärda problemen. Läs mer om programuppgraderingar i [i den här artikeln](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Hälsoarkivet
Health store behåller hälsorelaterade information om entiteter i klustret för enkel hämtning och utvärdering. Det implementeras som ett Service Fabric beständiga tillståndskänslig tjänst för att säkerställa hög tillgänglighet och skalbarhet. Health store är en del av den **fabric: / System** program, och det är tillgängligt när klustret är igång och körs.

## <a name="health-entities-and-hierarchy"></a>Hälsotillstånd entiteter och hierarki
Entiteterna hälsotillstånd är ordnade i en logisk hierarki som samlar in interaktioner och beroenden mellan olika entiteter. Health store skapar automatiskt hälsotillstånd entiteter och hierarki baserat på rapporter som togs emot från Service Fabric-komponenter.

Entiteterna hälsotillstånd spegla Service Fabric-entiteter. (Till exempel **hälsotillstånd programmet entitet** matchar en programinstans distribuerat i klustret, medan **hälsotillstånd noden entitet** matchar en Service Fabric-klusternod.) Hierarkin hälsotillstånd samlar in interaktioner systementiteter och den utgör grunden för avancerade hälsotillstånd utvärdering. Du kan lära dig om viktiga begrepp för Service Fabric i [teknisk översikt för Service Fabric](service-fabric-technical-overview.md). Mer information om programmet Se [Service Fabric-programmodellen](service-fabric-application-model.md).

Tillåt kluster och program effektivt rapporteras, felsöks, och övervakas hälsotillstånd entiteter och hierarkin. Hälsomodell ger en rättvisande *detaljerade* representation av hälsotillståndet för många rörliga delar i klustret.

![Hälsotillstånd entiteter.][1]
Health-entiteter, ordnade i en hierarki som baseras på överordnade och underordnade relationer.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Hälsotillstånd entiteterna är:

* **Klustret**. Representerar hälsan för Service Fabric-kluster. Klustret hälsorapporter beskrivs villkor som påverkar hela klustret. Dessa villkor påverkar flera entiteter i klustret eller själva klustret. Baserat på villkoret kan inte personen begränsa problemet till en eller flera defekta underordnade. Exempel innefattar hjärna klustret dela på grund av nätverksproblem partitioneringen och kommunikation.
* **Noden**. Representerar hälsan för en Service Fabric-nod. Noden hälsorapporter beskrivs villkor som påverkar nod-funktioner. De vanligtvis påverkar alla distribuerade entiteter som körs på den. Exempel är noden slut på diskutrymme (eller andra datoromfattande egenskaper, till exempel minne, anslutningar) och när en nod är nere. Noden entiteten identifieras med namnet på noden (sträng).
* **Programmet**. Representerar hälsan för en programinstans som körs i klustret. Rapporter om hälsotillstånd beskrivs villkor som påverkar den övergripande hälsan för programmet. De kan inte begränsas till enskilda underordnade (tjänster eller distribuerade program). Exempel innefattar slutpunkt till slutpunkt interaktionen mellan olika tjänster i programmet. Entiteten programmet identifieras med programmets namn (URI).
* **Tjänsten**. Representerar hälsan för en tjänst som körs i klustret. Rapporter om hälsotillstånd beskrivs villkor som påverkar den övergripande hälsan för tjänsten. Personen kan inte begränsa problemet till en felaktig partition eller repliken. Exempel är en tjänstkonfiguration (till exempel port eller externa filresurs) som orsakar problem för alla partitioner. Service-entitet har identifierats av tjänstens namn (URI).
* **Partitionen**. Representerar hälsan för en tjänstpartition. Partition hälsorapporter beskrivs villkor som påverkar hela uppsättningen. Exempel är när antalet repliker är under målet antal och när en partition är förlorar kvorum. Partition entiteten identifieras med partitions-ID (GUID).
* **Repliken**. Representerar hälsan för en replik för tillståndskänsliga tjänster eller en tillståndslös tjänst-instans. Repliken är den minsta enheten watchdogs och dess komponenter kan rapportera om för ett program. Exempel är en primär replik som det går inte att replikera operations sekundärservrar och långsam replikering för tillståndskänsliga tjänster. En tillståndslös instans kan också rapportera när den är slut på resurser eller har problem med nätverksanslutningen. Entiteten replik identifieras med partitions-ID (GUID) och målreplik eller ID (long).
* **DeployedApplication**. Representerar hälsan för en *program som körs på en nod*. Distribuerat program hälsorapporter beskriver villkor som är specifika för programmet på den nod som inte kan begränsas att paket distribueras på samma nod. Exempel: fel när programpaket inte kan hämtas på noden och problem med att konfigurera programmet säkerhetsobjekt på noden. Det distribuerade programmet identifieras av namn (URI) och nodnamnet (sträng).
* **DeployedServicePackage**. Representerar hälsan för en tjänstepaket som körs på en nod i klustret. Det beskriver villkor som är specifika för ett tjänstpaket som inte påverkar andra servicepaket på samma nod för samma program. Exempel innefattar ett kodpaket i tjänstpaketet som inte kan startas och ett konfigurationspaket som inte kan läsas. Distribuerade tjänstpaket identifieras av programnamn (URI), nodnamnet (sträng), manifest tjänstnamnet (sträng) och tjänsten paket-ID för (sträng).

Granulariteten för hälsomodellen gör det enkelt att identifiera och åtgärda problem. Till exempel om en tjänst inte svarar, är det möjligt att rapportera att programinstansen är skadad. Rapportering på att nivån inte är perfekt, men eftersom problemet inte kanske påverkar alla tjänster inom programmet. Rapporten ska användas till felaktig tjänst eller till en viss underordnad partition om mer information som pekar på partitionen. Data automatiskt Surface-enheter via en hierarki och en felaktig partition syns på tjänsten och program. Den här aggregeringen hjälper dig att identifiera och lösa grundorsaken till problemet snabbare.

Hierarkin health består av överordnade och underordnade relationer. Ett kluster består av noder och program. Program har tjänster och distribuerade program. Distribuerade program har distribuerat tjänstpaket. Tjänster har partitioner och varje partition har en eller flera repliker. Det finns en särskild relation mellan noder och distribuerade entiteter. En felaktig nod påverkar som rapporterats av dess utfärdare systemkomponenten Redundanshanteraren-tjänsten distribuerade program, paket och kopior som distribueras på den.

Hälsotillstånd hierarkin representerar det senaste tillståndet för systemet baserat på senaste hälsorapporter som är information nästan i realtid.
Interna och externa watchdogs kan rapportera om samma entiteter utifrån programspecifika logik eller anpassade övervakade villkor. Användarrapporter samexistera med systemrapporter.

Planera att investera i hur du rapporterar och svara på hälsa under utformningen av en stor molntjänst. Den här direkta investement gör det enklare att felsöka, övervaka och hantera tjänsten.

## <a name="health-states"></a>Hälsotillstånd
Service Fabric använder tre hälsotillstånd för att beskriva om en entitet är felfria eller inte: OK, varning och fel. Alla rapporter som skickas till hälsoarkivet måste ange ett av dessa lägen. Utvärderingsresultat hälsotillstånd är ett av dessa lägen.

Möjliga [hälsotillstånd](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) är:

* **OK**. Entiteten är felfri. Det finns inga kända problem som rapporteras på den eller dess underordnade (om tillämpligt).
* **Varning**. Entiteten har några problem, men den kan fungera korrekt. Till exempel fördröjning, men de medför inte några funktionella problem ännu. I vissa fall kan åtgärda varningsvillkor själva utan inblandning av externa. I dessa fall hälsorapporter öka medvetenheten och ger bättre inblick i vad som händer. Varning-villkoret kan försämras i ett allvarligt problem utan inblandning av användaren i andra fall.
* **Fel**. Entiteten är felfri. Åtgärden måste åtgärdas för att åtgärda tillståndet för entiteten, eftersom den inte fungera korrekt.
* **Okänd**. Entiteten finns inte i health store. Det här resultatet kan hämtas från de distribuerade frågor som slå samman resultat från flera komponenter. Till exempel get-noden listfrågan går till **FailoverManager**, **ClusterManager**, och **HealthManager**; program listfrågan går till  **ClusterManager** och **HealthManager**. De här frågorna sammanfoga resultaten från flera systemkomponenter. Om en annan systemkomponent returnerar en entitet som inte finns i health store, sammanfogade resultatet har den okända hälsotillstånd. En entitet är inte i store eftersom hälsorapporter som ännu inte har behandlats eller entiteten har rensats efter borttagningen.

## <a name="health-policies"></a>Hälsoprinciper
Health store gäller hälsoprinciper för att avgöra om en entitet är felfri baserat på dess rapporter och dess underordnade.

> [!NOTE]
> Hälsoprinciper kan anges i klustermanifestet (för klustret och noden hälsotillstånd utvärdering) eller i applikationsmanifestet (för programutvärderingar och alla dess underordnade). Hälsotillstånd utvärdering begäranden kan även skicka i anpassad utvärdering hälsopolicys, vilket används enbart för den utvärderingen.
> 
> 

Som standard tillämpar strikta regler (allt måste vara felfri) för den hierarkiska relationen överordnad-underordnad i Service Fabric. Om även en av de underordnade objekten har en ohälsosam händelse, betraktas överordnat defekt.

### <a name="cluster-health-policy"></a>Klustret hälsoprincip
Den [kluster hälsoprincip](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) används för att utvärdera hälsotillstånd för kluster och hälsotillstånd för noden. Principen kan definieras i klustermanifestet. Om det inte finns, används standardprincipen (noll tolererade fel).
Hälsoprincip klustret innehåller:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Anger om att behandla varningen hälsotillstånd rapporterar som fel under utvärdering av hälsotillstånd. Standard: FALSKT.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Anger högsta tolererat procentandelen av program som kan vara felaktig innan klustret anses av misstag.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Anger högsta tillåtna tolererat procentandel noder som kan vara felaktig innan klustret anses av misstag. I stora kluster är vissa noder alltid ned eller ut för reparationer, så den här procentandelen ska konfigureras för att kunna hanteras som.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Typ hälsotillstånd princip programkartan kan användas under klustret hälsotillstånd utvärdering för att beskriva särskilda programtyper. Som standard är alla program placera i en pool och utvärderas med MaxPercentUnhealthyApplications. Om vissa programtyper ska behandlas annorlunda, kan de tas bort från den globala poolen. I stället utvärderas de mot procenttal som är associerad med deras programtypnamn på kartan. Till exempel i ett kluster finns tusentals program av olika typer och några programinstanser för kontroll av en särskild programtyp. Styra program ska aldrig vara fel. Du kan ange globala MaxPercentUnhealthyApplications till 20% ska kunna hanteras några fel, men för vilken typ av program ”ControlApplicationType” ange MaxPercentUnhealthyApplications till 0. Det här sättet, om några av de många program är skadade, men under den globala feltillstånd procentsatsen klustret skulle utvärderas till varning. Ett varningshälsotillstånd påverkar inte uppgradering av klustret eller andra övervaknings utlöses av felaktigt hälsotillstånd. Men även en kontrollprogrammet i fel skulle göra kluster som är skadade, vilket utlöser återställningsfasen eller pausar klusteruppgraderingen, beroende på konfigurationen av uppgraderingen.
  För programtyper som definieras i kartan, är alla instanser av programmet tas bort från den globala poolen av program. De utvärderas baserat på det totala antalet program av programtypen med hjälp av specifika MaxPercentUnhealthyApplications från mappningen. Resten av program som finns kvar i den globala poolen och utvärderas med MaxPercentUnhealthyApplications.

I följande exempel är ett utdrag från en klustermanifestet. För att definiera poster i typen programavbildning prefix du parameternamnet på med ”ApplicationTypeMaxPercentUnhealthyApplications-”, följt av namnet på programmet.

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
Den [programmets hälsoprincip](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) beskriver hur görs utvärderingen av händelser och underordnade tillstånd aggregering för program och deras underordnade objekt. Det kan definieras i applikationsmanifestet, **ApplicationManifest.xml**, i programpaketet. Om inga principer har angetts förutsätter Service Fabric att entiteten är i feltillstånd om den har en hälsorapport eller en underordnad på hälsotillståndet varning eller fel.
De konfigurerbara principerna är:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Anger om att behandla varningen hälsotillstånd rapporterar som fel under utvärdering av hälsotillstånd. Standard: FALSKT.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Anger högsta tolererat procentandelen av distribuerade program som kan vara felaktig innan programmet ska anses vara fel. Den här procent beräknas genom att dividera antalet felaktiga distribuerade program på antalet noder som program som för närvarande har distribuerats på i klustret. Beräkningen Avrundar uppåt till tolerera fel på ett fel på små antal noder. Standard procentandel: noll.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Anger service typ hälsotillstånd standardprincipen, som ersätter hälsotillstånd standardprincipen för alla typer av tjänster i programmet.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Innehåller en karta över tjänsten hälsoprinciper per typ av tjänst. Dessa principer ersätter hälsoprinciper standard service typ för varje typ av angivna tjänsten. Du kan till exempel konfigurera hälsoprinciper för utvärderingen på olika sätt om ett program har en typ av tillståndslösa gateway och en tillståndskänslig engine service type. När du anger princip per typ av tjänst, kan du få mer detaljerad kontroll över hälsotillståndet för tjänsten.

### <a name="service-type-health-policy"></a>Tjänsten typ hälsoprincip
Den [tjänsten typ hälsoprincip](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) anger hur att utvärdera och aggregera tjänsterna och dess underordnade tjänster. Principen innehåller:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Anger maximalt tillåten procentandel partitioner som är felaktiga innan en tjänst betraktas som defekt. Standard procentandel: noll.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Anger maximalt tillåten procentandel skadade repliker innan en partition betraktas som defekt. Standard procentandel: noll.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Anger maximalt tillåten procentandel tjänster som är felaktiga innan programmet betraktas som defekt. Standard procentandel: noll.

I följande exempel är ett utdrag från ett programmanifest:

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
Användare och automatiserade tjänster kan utvärdera hälsan för en entitet när som helst. Om du vill utvärdera en entitets hälsotillstånd, health store aggregeringar hälsotillstånd rapporter på entiteten och utvärderar alla dess underordnade (om tillämpligt). Hälsotillstånd aggregering algoritmen använder hälsoprinciper som anger hur att utvärdera hälsorapporter och hur du aggregera underordnade hälsotillstånd (om tillämpligt).

### <a name="health-report-aggregation"></a>Hälsotillstånd rapporten aggregering
En entitet kan ha flera hälsorapporter som skickas av olika rapportörer (systemkomponenter eller watchdogs) på olika egenskaper. Aggregeringen använder associerade hälsoprinciper, särskilt program eller ett kluster hälsoprincip ConsiderWarningAsError medlem. ConsiderWarningAsError anger hur att utvärdera varningar.

Sammanställda hälsotillståndet utlöses av den *sämsta* systemhälsorapporter om entiteten. Om det finns minst ett fel hälsorapport, är aggregerade hälsotillståndet ett fel.

![Hälsotillstånd rapporten aggregering med felrapport.][2]

En health-entitet som har en eller flera fel hälsorapporter utvärderas som fel. Detsamma gäller för en har upphört att gälla hälsorapport, oavsett dess hälsotillstånd.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Om det finns inga felrapporter och en eller flera varningar, är aggregerade hälsotillståndet varning eller fel, beroende på flaggan ConsiderWarningAsError princip.

![Hälsotillstånd rapporten aggregering varning rapport och ConsiderWarningAsError false.][3]

Hälsotillstånd rapporten aggregering varning rapport och ConsiderWarningAsError inställt på Falskt (standard).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Underordnade health-aggregering
Sammanställda hälsotillståndet för en entitet visar hälsotillstånd underordnade (om tillämpligt). Algoritmen för sammanställning av underordnade hälsotillstånd använder hälsoprinciper tillämpliga beroende på vilken entitet.

![Underordnade entiteter hälsotillstånd aggregering.][4]

Underordnade aggregering baserat på hälsoprinciper.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

När hälsoarkivet har utvärderat alla underordnade, aggregerar deras hälsotillstånd baserat på konfigurerade högsta procentandelen av defekta underordnade. Den här procentandelen hämtas från principen som baseras på vilken entitets- och underordnade.

* Om alla underordnade ha OK tillstånd, är hälsotillstånd underordnade som aggregeras OK.
* Om underordnade har både OK och varning tillstånd, hälsotillstånd underordnade som aggregeras är en varning.
* Om det finns underordnade med feltillstånd som överskrider det högsta tillåtna procentandel av defekta barn, finns hälsotillståndet aggregerade överordnade ett fel.
* Om respekterar de underordnade objekten med feltillstånd högsta tillåtna procentandelen av defekta barn, hälsotillståndet aggregerade överordnade är en varning.

## <a name="health-reporting"></a>Tillståndsrapportering
Systemkomponenter System Fabric-program och intern/extern watchdogs kan rapportera mot Service Fabric-entiteter. Rapportörer gör *lokala* bestämning av hälsotillståndet för övervakade enheter, baserat på de villkor som de övervakar. Behöver de inte att titta på alla globala tillstånd eller aggregerade data. Önskat beteende är att ha enkel rapportörer och inte komplexa organismer som behöver titta på många saker som härleder vilken information som ska skickas.

Om du vill skicka hälsodata till arkivet hälsotillstånd, måste en rapport att identifiera den berörda enheten och skapa en hälsorapport. Använda för att skicka rapporten, den [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API, report-health API: er tillgängliga på den `Partition` eller `CodePackageActivationContext` objekt, PowerShell-cmdletar eller REST.

### <a name="health-reports"></a>Hälsorapporter
Den [hälsorapporter](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) för var och en av entiteterna i klustret innehåller följande information:

* **SourceId**. En sträng som unikt identifierar rapport av hälsohändelsen.
* **Entitets-ID**. Identifierar den entitet som där rapporten används. Den skiljer sig beroende på den [entitetstypen](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Kluster. Ingen.
  * Noden. Nodnamnet (sträng).
  * programmet. Programnamn (URI). Representerar namnet på programinstansen distribueras i klustret.
  * Tjänsten. Tjänstnamn (URI). Representerar namnet på den tjänstinstans distribuerat i klustret.
  * Partition. Partitions-ID (GUID). Representerar den unika identifieraren för partitionen.
  * Repliken. Tillståndskänslig tjänst-replik-ID eller tillståndslösa tjänstinstans-ID (INT64).
  * DeployedApplication. Programnamnet (URI) och nodnamnet (sträng).
  * DeployedServicePackage. Programnamn (URI), nodnamnet (sträng) och service manifestnamn (sträng).
* **Egenskapen**. En *sträng* (inte en fast uppräkning) som gör att personen att kategorisera hälsohändelsen för en viss egenskap för entiteten. Rapport A kan till exempel rapportera om hälsan hos Node01 ”Storage” egenskap och rapport B kan rapportera om hälsan hos Node01 ”anslutning”-egenskap. I health store behandlas de här rapporterna som separata health-händelser för entiteten Node01.
* **Beskrivning av**. En sträng som gör att en rapport som ger detaljerad information om hälsohändelsen. **SourceId**, **egenskapen**, och **HealthState** fullständigt bör beskriva rapporten. Beskrivningen lägger till läsbart information om rapporten. Texten gör det enklare för administratörer och användare att förstå hälsorapporten.
* **HealthState**. En [uppräkning](service-fabric-health-introduction.md#health-states) som beskriver hälsotillståndet för rapporten. Godkända värden är OK, varning och fel.
* **TimeToLive**. Timespan som anger hur lång tid hälsorapporten är giltig. Tillsammans med **RemoveWhenExpired**, kan hälsoarkivet vet hur att utvärdera händelser som har upphört att gälla. Värdet är oändlig som standard och rapporten är giltig alltid.
* **RemoveWhenExpired**. Ett booleskt värde. Om värdet är true, har upphört att gälla hälsorapporten tas automatiskt bort från health store och rapporten påverkar inte entiteten hälsotillstånd utvärdering. Används när rapporten är giltig för en angiven tidsperiod endast och personen behöver inte uttryckligen tar den bort. Den används också för att ta bort rapporter från health store (till exempel en watchdog ändras och slutar att skicka rapporter med föregående källa och egenskap). Den kan skicka en rapport med en kort TimeToLive tillsammans med RemoveWhenExpired för att rensa alla tidigare tillstånd från health store. Om värdet är inställt på FALSKT, behandlas har upphört att gälla rapporten som ett fel på hälsotillståndet utvärderingen. False-värde som signalerar till health store att källan bör regelbundet rapportera om den här egenskapen. Om så inte är måste sedan det vara något fel med watchdog. Den watchdog hälsa samlas in av överväger händelsen som ett fel.
* **SequenceNumber**. Ett positivt heltal som måste vara begripliga, representerar ordningen på rapporterna. Den används av health store för att identifiera inaktuella rapporter som tas emot sent på grund av fördröjningar i nätverket eller andra problem. En rapport avvisas om sekvensnumret är mindre än eller lika med de nyligen tillämpade nummer för samma entitet, källa och egenskap. Om det inte anges genereras sekvensnumret automatiskt. Det är nödvändigt att placera i sekvensnumret endast om du rapporterar för övergångslägen. I så fall måste källan kommer ihåg vilka rapporter som har det skickats och hålla informationen för återställning vid redundans.

Dessa fyra delar av information – SourceId, entitets-ID, egenskap och HealthState – krävs för varje hälsorapport. SourceId strängen får inte inledas med prefixet ”**System.**”, som är reserverade för systemrapporter. Det finns endast en rapport för samma källa och egenskap för samma entitet. Flera rapporter för samma käll- och egenskapen åsidosätter varandra, antingen på klientsidan hälsotillstånd (om de grupperas) eller på hälsotillståndet lagra sida. Ersättningen är baserad på sekvensnummer; nyare rapporter (med högre sekvensnummer) ersätter äldre rapporter.

### <a name="health-events"></a>Hälsohändelser
Internt i hälsoarkivet håller [hälsohändelser](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), som innehåller all information från rapporter och ytterligare metadata. Metadata innehåller den tid som rapporten har angetts till klienten hälsotillstånd och den tid som den har ändrats på serversidan. Health-händelser som returneras av [hälsoförfrågningar](service-fabric-view-entities-aggregated-health.md#health-queries).

Har lagts till metadata innehåller:

* **SourceUtcTimestamp**. Tiden i rapporten har angetts till hälsotillstånd klienten (Coordinated Universal Time).
* **LastModifiedUtcTimestamp**. Den tid som rapporten senast ändrades på serversidan (Coordinated Universal Time).
* **IsExpired**. En flagga som indikerar om rapporten har upphört att gälla när frågan kördes av health store. En händelse kan ha upphört att gälla endast om RemoveWhenExpired är FALSKT. I annat fall händelsen returneras inte av frågan och tas bort från store.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. Senaste tid för OK/varningar/fel övergångar. De här fälten kan historiken över hälsotillståndet tillståndsövergångar för händelsen.

Tillstånd övergången fält kan användas för smartare aviseringar eller ”historiska” hälsoinformation för händelsen. De möjliggör scenarier som:

* Avisera när en egenskap har varit varningar/fel för mer än X minuter. Kontrollerar villkor för en viss tidsperiod undviker aviseringar på tillfälliga förhållanden. Exempelvis kan en avisering om hälsotillståndet har varning under mer än fem minuter kan översättas till (HealthState == varnings- och nu - LastWarningTransitionTime > 5 minuter).
* Aviseringar endast på villkor som har ändrats under senaste X minuter. Om en rapport har redan vid fel före den angivna tiden, kan den ignoreras eftersom den redan har signalerat tidigare.
* Om en egenskap växla mellan varnings- och avgöra hur länge den har varit ohälsosam (det vill säga inte OK). Exempelvis kan en avisering om egenskapen inte har varit vid god hälsa under mer än fem minuter kan översättas till (HealthState! = Ok och nu - LastOkTransitionTime > 5 minuter).

## <a name="example-report-and-evaluate-application-health"></a>Exempel: Rapportera och utvärdera programmets hälsotillstånd
I följande exempel skickar en hälsorapport via PowerShell på programmet **fabric: / WordCount** från källan **MyWatchdog**. Hälsorapporten innehåller information om hälsotillstånd egenskapen ”tillgänglighet” i ett felaktigt hälsotillstånd med oändlig TimeToLive. Därefter beordrar den programhälsa, vilken returnerar aggregeras problem med hälsotillståndet tillstånd och rapporterade health-händelser i listan över health-händelser.

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
Hälsomodell kan cloud services och den underliggande Service Fabric-plattformen för att skala, eftersom övervakning och hälsotillstånd fördelas mellan de olika övervakarna i klustret.
Andra system har en enkel och centraliserad tjänst på klusternivå som Parsar alla de *potentiellt* användbar information som genereras av tjänster. Den här metoden hämmar sina skalbarhet. Den också tillåter inte att samla in viss information för att identifiera problem och potentiella problem som nära den grundläggande orsaken som möjligt.

Hälsomodell används kraftigt för övervakning och diagnostik för utvärdering av kluster- och programhantering hälsotillstånd och för övervakade uppgraderingar. Andra tjänster använder hälsodata för att utföra automatiska reparationer, skapa klustret hälsohistorik och utfärda aviseringar på vissa villkor.

## <a name="next-steps"></a>Nästa steg
[Visa hälsorapporter i Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Hjälp för felsökning](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Rapportera och kontrollera hälsan hos tjänster](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Lägg till anpassade hälsorapporter i Service Fabric](service-fabric-report-health.md)

[Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-Programuppgradering](service-fabric-application-upgrade.md)


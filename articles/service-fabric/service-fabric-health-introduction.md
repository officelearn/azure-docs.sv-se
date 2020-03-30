---
title: Hälsoövervakning i Service Fabric
description: En introduktion till hälsoövervakningsmodellen för Azure Service Fabric, som tillhandahåller övervakning av klustret och dess program och tjänster.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 473aa2b9a74193a857390cd3e29b2b559b6084d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282424"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Introduktion till Service Fabric-hälsoövervakning
Azure Service Fabric introducerar en hälsomodell som ger omfattande, flexibel och utökningsbar hälsoutvärdering och rapportering. Modellen möjliggör nästan realtidsövervakning av klustrets tillstånd och de tjänster som körs i det. Du kan enkelt få hälsoinformation och korrigera potentiella problem innan de kaskad och orsaka massiva avbrott. I den typiska modellen skickar tjänster rapporter baserat på deras lokala vyer och den informationen sammanställs för att ge en övergripande vy på klusternivå.

Service Fabric-komponenter använder den här avancerade hälsomodellen för att rapportera deras aktuella tillstånd. Du kan använda samma mekanism för att rapportera hälsotillstånd från dina program. Om du investerar i hälsorapportering av hög kvalitet som fångar dina anpassade villkor kan du identifiera och åtgärda problem för ditt program som körs mycket enklare.

> [!NOTE]
> Vi startade hälsoundersystemet för att åtgärda ett behov av övervakade uppgraderingar. Service Fabric tillhandahåller övervakade program- och klusteruppgraderingar som säkerställer full tillgänglighet, inga driftstopp och minimala till inga användaråtgärder. För att uppnå dessa mål kontrollerar uppgraderingen hälsotillstånd baserat på konfigurerade uppgraderingsprinciper. En uppgradering kan endast fortsätta när hälsoskydd respekterar önskade tröskelvärden. Annars återställs uppgraderingen antingen automatiskt eller pausas för att ge administratörerna en chans att åtgärda problemen. Mer information om programuppgraderingar finns i [den här artikeln](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Hälsobutik
Hälsoarkivet behåller hälsorelaterad information om entiteter i klustret för enkel hämtning och utvärdering. Den implementeras som en service fabric-beständig tillståndskänslig tjänst för att säkerställa hög tillgänglighet och skalbarhet. Hälsoarkivet är en del av **programmet fabric:/system** och är tillgängligt när klustret är igång.

## <a name="health-entities-and-hierarchy"></a>Hälsoentiteter och hierarki
Hälsoentiteterna är ordnade i en logisk hierarki som fångar interaktioner och beroenden mellan olika entiteter. Hälsoarkivet skapar automatiskt hälsoentiteter och hierarki baserat på rapporter som tagits emot från Service Fabric-komponenter.

Hälsoentiteterna speglar service fabric-entiteterna. **(Hälsoprogramentiteten** matchar till exempel en programinstans som distribueras i klustret, medan **hälsonodentiteten** matchar en klusternod för tjänstinfrastruktur.) Hälsohierarkin fångar interaktionerna mellan systementiteterna och den ligger till grund för avancerad hälsoutvärdering. Du kan läsa mer om viktiga Service Fabric-koncept i [den tekniska översikten serviceinfrastruktur](service-fabric-technical-overview.md). Mer information om programmet finns i [Programmodell för Service Fabric](service-fabric-application-model.md).

Hälsoentiteterna och hierarkin gör att klustret och programmen kan rapporteras, avbuggas och övervakas på ett effektivt sätt. Hälsomodellen ger en korrekt, *detaljerad* representation av hälsan hos de många rörliga delarna i klustret.

![Hälsoentiteter.][1]
Hälsoentiteterna, organiserade i en hierarki baserat på överordnade och underordnade relationer.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Hälsoenheterna är:

* **Kluster**. Representerar hälsotillståndet för ett service fabric-kluster. Klusterhälsorapporter beskriver villkor som påverkar hela klustret. Dessa villkor påverkar flera entiteter i klustret eller klustret självt. Baserat på tillståndet kan reportern inte begränsa problemet till ett eller flera ohälsosamma barn. Exempel är hjärnan i klusterdelning på grund av nätverkspartitionering eller kommunikationsproblem.
* **Nod**. Representerar hälsotillståndet för en service fabric-nod. Hälsorapporter för nod beskriver villkor som påverkar nodfunktionen. De påverkar vanligtvis alla distribuerade entiteter som körs på den. Exempel på detta är noden utanför diskutrymmet (eller andra maskinomfattande egenskaper, till exempel minne, anslutningar) och när en nod är nere. Nodeniteten identifieras med nodnamnet (strängen).
* **Ansökan**. Representerar hälsotillståndet för en programinstans som körs i klustret. Programhälsorapporter beskriver villkor som påverkar programmets allmänna hälsotillstånd. De kan inte begränsas till enskilda underordnade (tjänster eller distribuerade program). Exempel är den heltäckande interaktionen mellan olika tjänster i programmet. Programentiteten identifieras med programnamnet (URI).
* **Service**. Representerar hälsotillståndet för en tjänst som körs i klustret. Hälsorapporter för tjänster beskriver förhållanden som påverkar tjänstens allmänna hälsa. Reportern kan inte begränsa problemet till en felaktig partition eller replik. Exempel på detta är en tjänstkonfiguration (till exempel port eller extern filresurs) som orsakar problem för alla partitioner. Serviceentiteten identifieras med tjänstnamnet (URI).
* **Partition**. Representerar hälsotillståndet för en tjänstpartition. Hälsorapporter för partition beskriver villkor som påverkar hela replikuppsättningen. Exempel är när antalet repliker är lägre än antalet mål och när en partition är i kvorumförlust. Partitionentiteten identifieras av partitions-ID (GUID).
* **Replik**. Representerar hälsotillståndet för en tillståndskänslig tjänstreplik eller en tillståndslös tjänstinstans. Repliken är den minsta enhet som watchdogs och systemkomponenter kan rapportera om för ett program. För tillståndskänsliga tjänster innehåller exempel en primär replik som inte kan replikera åtgärder till sekundärer och långsam replikering. Dessutom kan en tillståndslös instans rapportera när resurserna har eller har anslutningsproblem. Replikentiteten identifieras av partitions-ID (GUID) och replik- eller instans-ID (long).
* **DistribueradTillämning**. Representerar hälsotillståndet för ett *program som körs på en nod*. Distribuerade programhälsorapporter beskriver villkor som är specifika för programmet på noden som inte kan begränsas till tjänstpaket som distribueras på samma nod. Exempel är fel när programpaketet inte kan hämtas på den noden och utfärdar princip för programsäkerhet på noden. Det distribuerade programmet identifieras med programnamnet (URI) och nodnamnet (strängen).
* **DistribueradServicePackage**. Representerar hälsotillståndet för ett tjänstpaket som körs på en nod i klustret. Den beskriver villkor som är specifika för ett servicepaket som inte påverkar de andra servicepaketen på samma nod för samma program. Exempel är ett kodpaket i tjänstpaketet som inte kan startas och ett konfigurationspaket som inte kan läsas. Det distribuerade tjänstpaketet identifieras med programnamnet (URI), nodnamn (sträng), tjänstmanifestnamn (sträng) och aktiverings-ID för tjänstpaket (sträng).

Granulariteten i hälsomodellen gör det enkelt att upptäcka och korrigera problem. Om en tjänst till exempel inte svarar är det möjligt att rapportera att programinstansen är felaktig. Rapportering på den nivån är dock inte idealiskt, eftersom problemet kanske inte påverkar alla tjänster i det programmet. Rapporten bör tillämpas på den felaktiga tjänsten eller på en viss underordnad partition, om mer information pekar på den partitionen. Data ytors automatiskt genom hierarkin och en felaktig partition görs synlig på tjänst- och programnivå. Den här aggregeringen hjälper till att identifiera och lösa orsaken till problemet snabbare.

Hälsohierarkin består av överordnade och underordnade relationer. Ett kluster består av noder och program. Program har tjänster och distribuerade program. Distribuerade program har distribuerade tjänstpaket. Tjänster har partitioner och varje partition har en eller flera repliker. Det finns en särskild relation mellan noder och distribuerade entiteter. En felaktig nod som rapporterats av dess myndighetssystemkomponent, Redundanshanteraren, påverkar de distribuerade programmen, tjänstpaketen och replikerna som distribueras på den.

Hälsohierarkin representerar det senaste tillståndet i systemet baserat på de senaste hälsorapporterna, vilket är nästan realtidsinformation.
Interna och externa watchdogs kan rapportera om samma entiteter baserat på programspecifik logik eller anpassade övervakade villkor. Användarrapporter samexisterar med systemrapporterna.

Planera att investera i hur du rapporterar och svarar på hälsa under utformningen av en stor molntjänst. Den här investeringen gör tjänsten enklare att felsöka, övervaka och använda.

## <a name="health-states"></a>Hälsotillstånd
Service Fabric använder tre hälsotillstånd för att beskriva om en entitet är felfri eller inte: OK, varning och fel. Alla rapporter som skickas till hälsoarkivet måste ange ett av dessa tillstånd. Hälsoutvärderingsresultatet är ett av dessa tillstånd.

De möjliga [hälsotillstånden](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) är:

* **OK.** Entiteten är felfri. Det finns inga kända problem som rapporterats om den eller dess barn (i förekommande fall).
* **Varning**. Entiteten har vissa problem, men den kan fortfarande fungera korrekt. Det finns till exempel förseningar, men de orsakar inga funktionella problem ännu. I vissa fall kan varningsvillkoret åtgärda sig själv utan extern inblandning. I dessa fall ökar hälsorapporterna medvetenheten och synliggör vad som händer. I andra fall kan varningstillståndet försämras till ett allvarligt problem utan att användaren behöver ingripa.
* **Fel**. Entiteten är felfritt. Åtgärder bör vidtas för att åtgärda tillståndet för entiteten, eftersom den inte kan fungera korrekt.
* **Okänd**. Entiteten finns inte i hälsoarkivet. Det här resultatet kan erhållas från de distribuerade frågor som sammanfogar resultat från flera komponenter. Frågan hämta nodlista går till exempel till **FailoverManager**, **ClusterManager**och **HealthManager**; hämta programlistefrågan går till **ClusterManager** och **HealthManager**. Dessa frågor sammanfogar resultat från flera systemkomponenter. Om en annan systemkomponent returnerar en entitet som inte finns i hälsoarkivet har det kopplade resultatet okänt hälsotillstånd. Entitet finns inte i lager eftersom hälsorapporter ännu inte har bearbetats eller om entiteten har rensats efter borttagningen.

## <a name="health-policies"></a>Hälsopolitik
Hälsoarkivet tillämpar hälsoprinciper för att avgöra om en entitet är felfri baserat på dess rapporter och dess underordnade.

> [!NOTE]
> Hälsoprinciper kan anges i klustermanifestet (för hälsoutvärdering av kluster- och nod) eller i programmanifestet (för programutvärdering och någon av dess underordnade). Hälsoutvärderingsbegäranden kan också passera i anpassade hälsoutvärderingsprinciper, som endast används för den utvärderingen.
> 
> 

Som standard tillämpar Service Fabric strikta regler (allt måste vara felfritt) för den överordnade och underordnade hierarkiska relationen. Om även ett av barnen har en ohälsosam händelse anses föräldern vara ohälsosam.

### <a name="cluster-health-policy"></a>Hälsoprincip för kluster
[Klusterhälsoprincipen](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) används för att utvärdera hälsotillståndet för klustret och hälsotillståndet för nod. Principen kan definieras i klustermanifestet. Om den inte finns används standardprincipen (noll tolererade fel).
Klusterhälsoprincipen innehåller:

* [ÖvervägWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Anger om varningshälsorapporter ska behandlas som fel under hälsoutvärderingen. Standard: falskt.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Anger den högsta tillåtna procentandelen av program som kan vara felaktiga innan klustret betraktas som ett fel.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Anger den maximala tolererade procentandelen noder som kan vara felaktiga innan klustret betraktas som ett fel. I stora kluster är vissa noder alltid nere eller ute för reparationer, så den här procentsatsen bör konfigureras för att tolerera det.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Hälsoprincipmappningen för programtyp kan användas vid utvärdering av klusterhälsa för att beskriva särskilda programtyper. Som standard läggs alla program i en pool och utvärderas med MaxPercentUnhealthyApplications. Om vissa programtyper ska behandlas olika kan de tas ut ur den globala poolen. I stället utvärderas de mot de procentsatser som är associerade med deras programtypsnamn i kartan. I ett kluster finns det till exempel tusentals program av olika typer och några kontrollprograminstanser av en särskild programtyp. Kontrollprogrammen får aldrig vara felaktiga. Du kan ange globala MaxPercentUnhealthyApplications till 20% för att tolerera vissa fel, men för programtypen "ControlApplicationType" ange MaxPercentUnhealthyApplications till 0. På så sätt, om några av de många programmen är felaktiga, men under den globala felaktiga procentsatsen, skulle klustret utvärderas till Varning. Ett varningshälsotillstånd påverkar inte klusteruppgraderingen eller annan övervakning som utlöses av felhälsotillstånd. Men även ett kontrollprogram av misstag skulle göra klustret felfritt, vilket utlöser återställa eller pausar klusteruppgraderingen, beroende på uppgraderingskonfigurationen.
  För de programtyper som definierats i kartan tas alla programinstanser bort från den globala programpoolen. De utvärderas baserat på det totala antalet program av programtypen, med hjälp av specifika MaxPercentUnhealthyApplications från kartan. Alla resten av programmen finns kvar i den globala poolen och utvärderas med MaxPercentUnhealthyApplications.

Följande exempel är ett utdrag från ett klustermanifest. Om du vill definiera poster i programtypskartan prefixar parameternamnet med "ApplicationTypeMaxPercentUnhealthyApplications-", följt av programtypsnamnet.

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

### <a name="application-health-policy"></a>Hälsoprincip för program
[Programmets hälsoprincip](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) beskriver hur utvärderingen av händelser och aggregering av underordnade tillstånd görs för program och deras underordnade. Det kan definieras i **programmanifest.xml,** i programpaketet. Om inga principer anges förutsätter Service Fabric att entiteten är felfritt om den har en hälsorapport eller ett underordnat vid varnings- eller felhälsotillståndet.
De konfigurerbara principerna är:

* [ÖvervägWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Anger om varningshälsorapporter ska behandlas som fel under hälsoutvärderingen. Standard: falskt.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Anger den maximala tolererade procentandelen distribuerade program som kan vara felaktiga innan programmet betraktas som ett fel. Den här procentsatsen beräknas genom att dividera antalet felaktiga distribuerade program över antalet noder som programmen för närvarande distribueras i klustret. Beräkningen avrundar upp för att tolerera ett fel på ett litet antal noder. Standardprocent: noll.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Anger hälsoprincipen för standardtjänsttyp, som ersätter standardhälsoprincipen för alla tjänsttyper i programmet.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Innehåller en karta över tjänsthälsoprinciper per tjänsttyp. Dessa principer ersätter standardhälsoprinciperna för tjänsttyp för varje angiven tjänsttyp. Om ett program till exempel har en tillståndslös gateway-tjänsttyp och en tillståndskänslig motortjänsttyp kan du konfigurera hälsoprinciperna för utvärderingen på ett annat sätt. När du anger princip per tjänsttyp kan du få mer detaljerad kontroll över tjänstens hälsotillstånd.

### <a name="service-type-health-policy"></a>Hälsoprincip för tjänsttyp
[Hälsoprincipen för tjänsttyp](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) anger hur du utvärderar och aggregerar tjänster och underordnade tjänster. Principen innehåller:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Anger den maximala tolererade procentandelen felaktiga partitioner innan en tjänst anses vara fel. Standardprocent: noll.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Anger den maximala tolererade procentandelen felaktiga repliker innan en partition anses vara fel. Standardprocent: noll.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Anger den maximala tolererade procentandelen felaktiga tjänster innan programmet anses vara felfritt. Standardprocent: noll.

Följande exempel är ett utdrag från ett programmanifest:

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

## <a name="health-evaluation"></a>Utvärdering av hälsa
Användare och automatiserade tjänster kan utvärdera hälsotillståndet för alla enheter när som helst. För att utvärdera en entitets hälsotillstånd sammanställer hälsoarkivet alla hälsorapporter på entiteten och utvärderar alla dess underordnade (i förekommande fall). Hälsoaggregeringsalgoritmen använder hälsoprinciper som anger hur hälsorapporter ska utvärderas och hur du aggregerar underordnade hälsotillstånd (i förekommande fall).

### <a name="health-report-aggregation"></a>Aggregering av hälsorapport
En entitet kan ha flera hälsorapporter som skickas av olika reportrar (systemkomponenter eller watchdogs) på olika egenskaper. Aggregeringen använder de associerade hälsoprinciperna, särskilt ConsiderWarningAsError-medlemmen i programmets eller klustrets hälsoprincip. ConsiderWarningAsError anger hur varningar ska utvärderas.

Det aggregerade hälsotillståndet utlöses av de *sämsta* hälsorapporterna på entiteten. Om det finns minst en felhälsorapport är det aggregerade hälsotillståndet ett fel.

![Aggregering av hälsorapporter med felrapport.][2]

En hälsoentitet som har en eller flera felhälsorapporter utvärderas som fel. Detsamma gäller för en hälsorapport som har upphört att gälla, oavsett hälsotillstånd.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Om det inte finns några felrapporter och en eller flera varningar är det aggregerade hälsotillståndet antingen varning eller fel, beroende på policyflaggan considerWarningAsError.

![Hälsorapport aggregering med varningsrapport och ConsiderWarningAsError falskt.][3]

Hälsorapportaggregering med varningsrapport och ConsiderWarningAsError inställd på false (standard).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Aggregering av barnhälsa
Det aggregerade hälsotillståndet för en enhet återspeglar barnhälsotillstånden (i förekommande fall). Algoritmen för att aggregera underordnade hälsotillstånd använder de hälsoprinciper som gäller baserat på entitetstypen.

![Hälsoaggregering av underordnade enheter.][4]

Underordnad aggregering baserat på hälsoprinciper.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

När hälsoarkivet har utvärderat alla underordnade, sammanställer det deras hälsotillstånd baserat på den konfigurerade maximala procentandelen ohälsosamma underordnade. Den här procentsatsen hämtas från principen baserat på entitets- och underordnade typ.

* Om alla underordnade har OK-tillstånd är det samlade hälsotillståndet OK.
* Om barn har både OK och varningstillstånd varnar det samlade hälsotillståndet för barn.
* Om det finns underordnade med feltillstånd som inte respekterar den högsta tillåtna procentandelen ohälsosamma underordnade, är det aggregerade överordnade hälsotillståndet ett fel.
* Om underordnade med feltillstånd respekterar den högsta tillåtna procentandelen ohälsosamma underordnade, varnar det aggregerade överordnade hälsotillståndet.

## <a name="health-reporting"></a>Hälsorapportering
Systemkomponenter, System Fabric-program och interna/externa watchdogs kan rapportera mot Service Fabric-entiteter. Reportrarna gör *lokala* bestämningar av de övervakade enheternas hälsa, baserat på de förhållanden som de övervakar. De behöver inte titta på någon global stat eller aggregerade data. Det önskade beteendet är att ha enkla reportrar, och inte komplexa organismer som behöver titta på många saker att dra slutsatsen vilken information att skicka.

Om du vill skicka hälsodata till hälsoarkivet måste en reporter identifiera den berörda entiteten och skapa en hälsorapport. Om du vill skicka rapporten använder du API:et [FabricClient.HealthClient.ReportHealth,](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) rapportera hälso-API:er som exponeras på objekten eller `Partition` `CodePackageActivationContext` objekten PowerShell eller REST.

### <a name="health-reports"></a>Hälsorapporter
[Hälsorapporterna](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) för var och en av entiteterna i klustret innehåller följande information:

* **SourceId**. En sträng som unikt identifierar hälsohändelsens reporter.
* **Entitetsidentifierare**. Identifierar entiteten där rapporten tillämpas. Den skiljer sig beroende på [enhetstypen:](service-fabric-health-introduction.md#health-entities-and-hierarchy)
  
  * Kluster. Inga.
  * Nod. Nodnamn (sträng).
  * Program. Programnamn (URI). Representerar namnet på programinstansen som distribueras i klustret.
  * Tjänst. Tjänstnamn (URI). Representerar namnet på den tjänstinstans som distribueras i klustret.
  * Partition. Partition ID (GUID). Representerar partitionens unika identifierare.
  * Replika. Det tillståndskänsliga tjänstreplik-ID:t eller det tillståndslösa tjänstinstans-ID :t (INT64).
  * DistribueradTillämning. Programnamn (URI) och nodnamn (sträng).
  * DistribueradServicePaket. Programnamn (URI), nodnamn (sträng) och tjänstmanifestnamn (sträng).
* **Egenskap**. En *sträng* (inte en fast uppräkning) som gör att reportern kan kategorisera hälsohändelsen för en viss egenskap för entiteten. Reporter A kan till exempel rapportera hälsotillståndet för egenskapen "Lagring" nod01 och reporter B kan rapportera hälsotillståndet för egenskapen Node01 "Anslutning". I hälsoarkivet behandlas dessa rapporter som separata hälsohändelser för entiteten Node01.
* **Beskrivning**. En sträng som gör att en reporter kan ge detaljerad information om hälsohändelsen. **SourceId,** **egenskap**och **HealthState** bör beskriva rapporten fullständigt. Beskrivningen lägger till läsbar information om rapporten. Texten gör det enklare för administratörer och användare att förstå hälsorapporten.
* **HealthState**. En [uppräkning](service-fabric-health-introduction.md#health-states) som beskriver hälsotillståndet för rapporten. De godkända värdena är OK, Varning och Fel.
* **TimeToLive**. En tidsspann som anger hur länge hälsorapporten är giltig. Tillsammans med **RemoveWhenExpired**låter det hälsoarkivet veta hur du utvärderar utgångna händelser. Som standard är värdet oändligt och rapporten är giltig för alltid.
* **RemoveWhenExpired**. En boolesk. Om den är inställd på true tas den utgångna hälsorapporten automatiskt bort från hälsoarkivet och rapporten påverkar inte entitetshälsoutvärderingen. Används när rapporten bara är giltig under en viss tidsperiod och reportern behöver inte uttryckligen rensa den. Det används också för att ta bort rapporter från hälsoarkivet (till exempel ändras en watchdog och slutar skicka rapporter med tidigare källa och egendom). Det kan skicka en rapport med en kort TimeToLive tillsammans med RemoveWhenExpired att rensa upp något tidigare tillstånd från hälsobutiken. Om värdet är inställt på false behandlas den utgångna rapporten som ett fel i hälsoutvärderingen. Det falska värdet signalerar till hälsoarkivet att källan ska rapportera regelbundet på den här egenskapen. Om det inte gör det, då måste det vara något fel med vakthund. Den watchdog hälsa fångas genom att betrakta händelsen som ett fel.
* **SequenceNumber**. Ett positivt heltal som ständigt måste öka, det representerar ordningen på rapporterna. Den används av hälsoarkivet för att identifiera inaktuella rapporter som tas emot sent på grund av nätverksförseningar eller andra problem. En rapport avvisas om sekvensnumret är mindre än eller lika med det senast tillämpade numret för samma entitet, källa och egenskap. Om det inte anges genereras sekvensnumret automatiskt. Det är nödvändigt att placera i sekvensnumret endast när du rapporterar om tillståndsövergångar. I det här fallet måste källan komma ihåg vilka rapporter den skickade och behålla informationen för återställning vid redundans.

Dessa fyra delar av information - SourceId, entitet identifierare, egenskap och HealthState - krävs för varje hälsorapport. SourceId-strängen får inte börja med prefixet "**System.**", som är reserverat för systemrapporter. För samma entitet finns det bara en rapport för samma källa och egenskap. Flera rapporter för samma källa och egenskap åsidosätter varandra, antingen på hälsoklientsidan (om de är batchade) eller på hälsobutikssidan. Ersättningen baseras på sekvensnummer. nyare rapporter (med högre sekvensnummer) ersätter äldre rapporter.

### <a name="health-events"></a>Hälsoevenemang
Internt behåller hälsoarkivet [hälsohändelser](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), som innehåller all information från rapporterna, och ytterligare metadata. Metadata innehåller den tid rapporten gavs till hälsoklienten och den tidpunkt då den ändrades på serversidan. Hälsohändelserna returneras av [hälsofrågor](service-fabric-view-entities-aggregated-health.md#health-queries).

De tillagda metadata innehåller:

* **SourceUtcTimestamp**. Den tid rapporten gavs till hälsoklienten (Coordinated Universal Time).
* **LastModifiedUtcTimestamp**. Den tidpunkt då rapporten senast ändrades på serversidan (Coordinated Universal Time).
* **IsExpired**. En flagga som anger om rapporten har upphört att gälla när frågan kördes av hälsoarkivet. En händelse kan bara förfalla om RemoveWhenExpired är falskt. Annars returneras inte händelsen av frågan och tas bort från arkivet.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. Sista gången för OK/varning/felövergångar. Dessa fält ger historiken över hälsotillståndsövergångarna för händelsen.

Tillståndsövergångsfälten kan användas för smartare aviseringar eller "historisk" hälsohändelseinformation. De möjliggör scenarier som:

* Avisera när en egenskap har varit på varning/fel i mer än X minuter. Genom att kontrollera villkoret under en viss tid undviks varningar om tillfälliga förhållanden. En avisering om hälsotillståndet har varnats i mer än fem minuter kan till exempel översättas till (HealthState == Varning och nu - LastWarningTransitionTime > 5 minuter).
* Avisering endast om villkor som har ändrats under de senaste X minuterna. Om en rapport redan var fel före den angivna tiden kan den ignoreras eftersom den redan har signalerats tidigare.
* Om en egenskap växlar mellan varning och fel, avgöra hur länge det har varit felfritt (det vill vara, inte OK). En avisering om egenskapen inte har varit felfri i mer än fem minuter kan till exempel översättas till (HealthState != Ok och Now - LastOkTransitionTime > 5 minuter).

## <a name="example-report-and-evaluate-application-health"></a>Exempel: Rapportera och utvärdera programmets hälsotillstånd
I följande exempel skickas en hälsorapport via PowerShell på **programstrukturen:/WordCount** från källan **MyWatchdog**. Hälsorapporten innehåller information om hälsoegenskapen "tillgänglighet" i ett felhälsotillstånd, med oändlig TimeToLive. Sedan frågar programmets hälsotillstånd, som returnerar aggregerade hälsotillståndsfel och rapporterade hälsohändelser i listan över hälsohändelser.

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

## <a name="health-model-usage"></a>Användning av hälsomodeller
Hälsomodellen gör att molntjänster och den underliggande Service Fabric-plattformen kan skalas, eftersom övervaknings- och hälsobestämningar fördelas mellan de olika övervakarna i klustret.
Andra system har en enda centraliserad tjänst på klusternivå som tolkar all *potentiellt* användbar information som skickas ut av tjänster. Detta tillvägagångssätt hindrar deras skalbarhet. Det tillåter dem inte heller att samla in specifik information för att identifiera problem och potentiella problem så nära grundorsaken som möjligt.

Hälsomodellen används mycket för övervakning och diagnos, för utvärdering av kluster- och programhälsa och för övervakade uppgraderingar. Andra tjänster använder hälsodata för att utföra automatiska reparationer, skapa klusterhälsohistorik och utfärda aviseringar om vissa villkor.

## <a name="next-steps"></a>Nästa steg
[Visa hälsorapporter för Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Använda systemhälsorapporter för felsökning](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Så här rapporterar och kontrollerar du tjänstens hälsa](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Lägga till anpassade hälsorapporter för Service Fabric](service-fabric-report-health.md)

[Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md)


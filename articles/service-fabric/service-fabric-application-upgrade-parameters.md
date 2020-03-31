---
title: 'Uppgradering av program: uppgraderingsparametrar'
description: Beskriver parametrar relaterade till uppgradering av ett Service Fabric-program, inklusive hälsokontroller att utföra och principer för att automatiskt ångra uppgraderingen.
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 42b5c52181cfb006ae57e43c183b96a059a9c63a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377981"
---
# <a name="application-upgrade-parameters"></a>Programuppgraderingsparametrar
I den här artikeln beskrivs de olika parametrar som gäller under uppgraderingen av ett Azure Service Fabric-program. Programuppgraderingsparametrar styr de time-outs och hälsokontroller som tillämpas under uppgraderingen, och de anger de principer som måste tillämpas när en uppgradering misslyckas. Programparametrar gäller för uppgraderingar med hjälp av:
- PowerShell
- Visual Studio
- SFCTL (SFCTL)
- [Resten](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Programuppgraderingar initieras via ett av tre uppgraderingslägen som kan väljas av användaren. Varje läge har sin egen uppsättning programparametrar:
- Övervakas
- Oövervakad Auto
- Oövervakad manuell

De tillämpliga obligatoriska och valfria parametrarna beskrivs i varje avsnitt enligt följande.

## <a name="visual-studio-and-powershell-parameters"></a>Parametern Visual Studio och PowerShell

Service Fabric-programuppgraderingar med PowerShell använder kommandot [Start-ServiceFabricApplicationUpgrade.](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) Uppgraderingsläget väljs genom att antingen den **övervakade,** **oövervakadeAuto**eller **oövervakadmanual** parametern till [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

Uppgraderingsparametrar för Visual Studio Service Fabric-program ställs in via dialogrutan Inställningar för Uppgradering av Visual Studio. Uppgraderingsläget för Visual Studio väljs med hjälp av listrutan **Uppgraderingsläge** för att antingen **övervakas,** **oövervakad Auto**eller **UnmonitoredManual**. Mer information finns i [Konfigurera uppgraderingen av ett Service Fabric-program i Visual Studio](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Obligatoriska parametrar
(PS=PowerShell, VS=Visual Studio)

| Parameter | Gäller för | Beskrivning |
| --- | --- | --- |
ApplicationName |PS| Namn på programmet som uppgraderas. Exempel: tyg:/VisualObjects, tyg:/ClusterMonitor. |
ApplicationTypeVersion (ApplicationTypeVersion)|PS|Den version av programtypen som uppgraderingen riktar sig till. |
Felåtgärd |PS, VS|Tillåtna värden är **Återställning,** **Manuell**och **Ogiltig**. Den kompenserande åtgärden som ska utföras när en *övervakad* uppgradering stöter på övervakningsprincip eller hälsoprincipöverträdelser. <br>**Återställning** anger att uppgraderingen automatiskt återställs till förhandsuppgraderingsversionen. <br>**Manualen** anger att uppgraderingen växlar till *unmonitoredManual uppgraderingsläge.* <br>**Ogiltig** anger att felåtgärden är ogiltig.|
Övervakas |PS|Anger att uppgraderingsläget övervakas. När cmdleten har slutfört en uppgradering för en uppgraderingsdomän, om uppgraderingsdomänens och klustrets hälsotillstånd uppfyller de hälsoprinciper som du definierar, uppgraderar Service Fabric nästa uppgraderingsdomän. Om uppgraderingsdomänen eller klustret inte uppfyller hälsoprinciper misslyckas uppgraderingen och Service Fabric återställer uppgraderingen för uppgraderingsdomänen eller återgår till manuellt läge enligt den angivna principen. Detta är det rekommenderade läget för programuppgraderingar i en produktionsmiljö. |
UpgradeMode (UpgradeMode) | Vs | Tillåtna värden **övervakas** (standard), **UnmonitoredAuto**eller **UnmonitoredManual**. Mer information finns i PowerShell-parametrar för varje läge i den här artikeln. |
OövervakadeAuto | PS | Anger att uppgraderingsläget är oövervakat automatiskt. När Service Fabric har uppgraderat en uppgraderingsdomän uppgraderar Service Fabric nästa uppgraderingsdomän oavsett programmets hälsotillstånd. Det här läget rekommenderas inte för produktion och är endast användbart under utvecklingen av ett program. |
OövervakadeManual | PS | Anger att uppgraderingsläget är oövervakad manuell. När Service Fabric har uppgraderat en uppgraderingsdomän väntar den på att du ska uppgradera nästa uppgraderingsdomän med hjälp av *cmdleten Resume-ServiceFabricApplicationUpgrade.* |

### <a name="optional-parameters"></a>Valfria parametrar

Parametrarna för hälsoutvärdering är valfria. Om hälsoutvärderingskriterierna inte anges när en uppgradering startar använder Service Fabric de programhälsoprinciper som anges i ApplicationManifest.xml för programinstansen.

> [!div class="mx-tdBreakAll"]
> | Parameter | Gäller för | Beskrivning |
> | --- | --- | --- |
> | ApplicationParameter |PS, VS| Anger åsidosättningar för programparametrar.<br>PowerShell-programparametrar anges som hashtable namn/värdepar. Till exempel @{ "VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1" }.<br>Visual Studio-programparametrar kan anges i dialogrutan Publicera tjänst fabric application i fältet **Programparametrar fil.**
> | Bekräfta |PS| Tillåtna värden är **Sant** och **Falskt**. Frågar efter bekräftelse innan cmdleten körs. |
> | ÖvervägWarningAsError |PS, VS |Tillåtna värden är **Sant** och **Falskt**. Standardvärdet är **Falskt**. Behandla varningshälsohändelser för programmet som fel när du utvärderar programmets hälsotillstånd under uppgraderingen. Som standard utvärderar Service Fabric inte varningshälsohändelser som fel (fel), så uppgraderingen kan fortsätta även om det finns varningshändelser. |
> | DefaultServiceTypeHealthPolicy | PS, VS |Anger hälsoprincipen för den standardtjänsttyp som ska användas för den övervakade uppgraderingen i formatet MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. 5,10,15 anger till exempel följande värden: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
> | Force | PS, VS | Tillåtna värden är **Sant** och **Falskt**. Anger att uppgraderingsprocessen hoppar över varningsmeddelandet och tvingar uppgraderingen även när versionsnumret inte har ändrats. Detta är användbart för lokala tester men rekommenderas inte för användning i en produktionsmiljö eftersom det kräver att du tar bort den befintliga distributionen som orsakar driftstopp och potentiell dataförlust. |
> | ForceRestart |PS, VS |Om du uppdaterar en konfiguration eller ett datapaket utan att uppdatera tjänstkoden startas tjänsten bara om egenskapen ForceRestart är inställd på **True**. När uppdateringen är klar meddelar Service Fabric tjänsten att ett nytt konfigurationspaket eller datapaket är tillgängligt. Tjänsten ansvarar för att tillämpa ändringarna. Om det behövs kan tjänsten starta om sig själv. |
> | HälsaCheckRetryTimeoutSec |PS, VS |Varaktigheten (i sekunder) som Service Fabric fortsätter att utföra hälsoutvärdering innan uppgraderingen deklareras som misslyckad. Standardvärdet är 600 sekunder. Den här varaktigheten börjar efter *att HealthCheckWaitDurationSec* har uppnåtts. I den här *HealthCheckRetryTimeout*kan Service Fabric utföra flera hälsokontroller av programmets hälsotillstånd. Standardvärdet är 10 minuter och bör anpassas på lämpligt sätt för ditt program. |
> | HälsaKontrolleraUnderationSec |PS, VS |Varaktigheten (i sekunder) för att kontrollera att programmet är stabilt innan du flyttar till nästa uppgraderingsdomän eller slutför uppgraderingen. Den här väntetiden används för att förhindra oupptäckta hälsoändringar direkt efter att hälsokontrollen har utförts. Standardvärdet är 120 sekunder och bör anpassas på lämpligt sätt för ditt program. |
> | HälsaCheckWaitDurationSec |PS, VS | Tiden att vänta (i sekunder) efter att uppgraderingen har slutförts på uppgraderingsdomänen innan Service Fabric utvärderar programmets hälsotillstånd. Den här varaktigheten kan också betraktas som den tid då ett program ska köras innan det kan betraktas som felfri. Om hälsokontrollen godkänns fortsätter uppgraderingsprocessen till nästa uppgraderingsdomän.  Om hälsokontrollen misslyckas väntar Service Fabric på [UpgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) innan hälsokontrollen försöker igen tills *HealthCheckRetryTimeoutSec* har nåtts. Standardvärdet och det rekommenderade värdet är 0 sekunder. |
> | MaxPercentUnhealthyDeployedApplications|PS, VS |Standard- och rekommenderat värde är 0. Ange det maximala antalet distribuerade program (se [avsnittet Hälso)](service-fabric-health-introduction.md)som kan vara felfritt innan programmet anses vara felfritt och misslyckas med uppgraderingen. Den här parametern definierar programmets hälsotillstånd på noden och hjälper till att identifiera problem under uppgraderingen. Vanligtvis blir replikerna av programmet belastningsbalanserade till den andra noden, vilket gör att programmet kan verka felfritt, vilket gör att uppgraderingen kan fortsätta. Genom att ange en strikt *MaxPercentUnhealthyDeployedApplications* hälsa, service fabric kan upptäcka ett problem med programpaketet snabbt och bidra till att producera en misslyckad snabb uppgradering. |
> | MaxPercentUnhealthyServices |PS, VS |En parameter till *DefaultServiceTypeHealthPolicy* och *ServiceTypeHealthPolicyMap*. Standard- och rekommenderat värde är 0. Ange det maximala antalet tjänster i programinstansen som kan vara felaktiga innan programmet anses vara felfritt och misslyckas uppgraderingen. |
> | MaxPercentUnhealthyPartitionsPerService|PS, VS |En parameter till *DefaultServiceTypeHealthPolicy* och *ServiceTypeHealthPolicyMap*. Standard- och rekommenderat värde är 0. Ange det maximala antalet partitioner i en tjänst som kan vara felaktiga innan tjänsten anses vara fel. |
> | MaxPercentUnhealthyReplicasPerPartition|PS, VS |En parameter till *DefaultServiceTypeHealthPolicy* och *ServiceTypeHealthPolicyMap*. Standard- och rekommenderat värde är 0. Ange det maximala antalet repliker i partitionen som kan vara felaktiga innan partitionen anses vara fel. |
> | ServiceTypeHealthPolicyMap | PS, VS | Representerar hälsoprincipen som används för att utvärdera hälsotillståndet för tjänster som tillhör en tjänsttyp. Tar en hash-tabellinmatning i följande format: @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} Till exempel: @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } |
> | TidsgränsenSec | PS, VS | Anger time-out-perioden i sekunder för operationen. |
> | Uppgradera DomäntimeoutSec |PS, VS |Maximal tid (i sekunder) för uppgradering av en enda uppgraderingsdomän. Om den här time-outen nås stoppas och fortsätter uppgraderingen baserat på inställningen för *FailureAction*. Standardvärdet är aldrig (Oändligt) och bör anpassas på lämpligt sätt för ditt program. |
> | UppgraderaReplicaSetCheckTimeoutSec |PS, VS |Mätt i sekunder.<br>**Tillståndslös tjänst**– Inom en enda uppgraderingsdomän försöker Service Fabric se till att ytterligare instanser av tjänsten är tillgängliga. Om antalet målinstanser är fler än en väntar Service Fabric på att mer än en instans ska vara tillgänglig, upp till ett maximalt time out-värde. Den här timeouten anges med egenskapen *UpgradeReplicaSetCheckTimeoutSec.* Om time-out upphör att gälla fortsätter Service Fabric med uppgraderingen, oavsett antalet tjänstinstanser. Om antalet målinstanser är ett väntar inte Service Fabric och fortsätter omedelbart med uppgraderingen.<br><br>**Tillståndskänslig tjänst**– Inom en enda uppgraderingsdomän försöker Service Fabric se till att replikuppsättningen har kvorum. Service Fabric väntar på att ett kvorum ska vara tillgängligt, upp till ett maximalt timeout-värde (angivet av egenskapen *UpgradeReplicaSetCheckTimeoutSec).* Om time-out upphör att gälla fortsätter Service Fabric med uppgraderingen, oavsett kvorum. Den här inställningen ställs in som aldrig (oändlig) när du rullar framåt och 1200 sekunder när du rullar tillbaka. |
> | UpgradeTimeoutSec |PS, VS |En time-out (i sekunder) som gäller för hela uppgraderingen. Om den här time-outen nås stoppas uppgraderingen och *FailureAction* utlöses. Standardvärdet är aldrig (Oändligt) och bör anpassas på lämpligt sätt för ditt program. |
> | WhatIf | PS | Tillåtna värden är **Sant** och **Falskt**. Visar vad som skulle hända om cmdleten kördes. Cmdleten körs inte. |

Kriterierna *MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService*och *MaxPercentUnhealthyReplicasPerPartition* kan anges per tjänsttyp för en programinstans. Om du ställer in dessa parametrar per tjänst kan ett program innehålla olika tjänsttyper med olika utvärderingsprinciper. En tillståndslös gateway-tjänsttyp kan till exempel ha en *MaxPercentUnhealthyPartitionsPerService* som skiljer sig från en tillståndskänslig motortjänsttyp för en viss programinstans.

## <a name="sfctl-parameters"></a>SFCTL-parametrar

Service Fabric-programuppgraderingar med hjälp av Service Fabric CLI använder [uppgraderingskommandot sfctl-program](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) tillsammans med följande obligatoriska och valfria parametrar.

### <a name="required-parameters"></a>Obligatoriska parametrar

| Parameter | Beskrivning |
| --- | --- |
| ansökan-id  |ID för programmet som uppgraderas. <br> Detta är vanligtvis det fullständiga namnet på programmet utan URI-schemat "fabric:". Från och med version 6.0 avgränsas hierarkiska\~namn med tecknet ' ' . Om programnamnet till exempel är "fabric:/myapp/app1" skulle programidentiteten\~vara "myapp app1" i 6.0+ och 'myapp/app1' i tidigare versioner.|
program-version |Den version av programtypen som uppgraderingen riktar sig till.|
parameters  |En JSON-kodad lista över programparameter åsidosättningar som ska tillämpas vid uppgradering av programmet.|

### <a name="optional-parameters"></a>Valfria parametrar

| Parameter | Beskrivning |
| --- | --- |
standard-service-hälsoprincip | [JSON](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) kodade specifikationen av hälsoprincipen som används som standard för att utvärdera hälsotillståndet för en tjänsttyp. Kartan är tom som standard. |
felåtgärd | Tillåtna värden är **Återställning,** **Manuell**och **Ogiltig**. Den kompenserande åtgärden som ska utföras när en *övervakad* uppgradering stöter på övervakningsprincip eller hälsoprincipöverträdelser. <br>**Återställning** anger att uppgraderingen automatiskt återställs till förhandsuppgraderingsversionen. <br>**Manualen** anger att uppgraderingen växlar till *unmonitoredManual uppgraderingsläge.* <br>**Ogiltig** anger att felåtgärden är ogiltig.|
kraft-omstart | Om du uppdaterar en konfiguration eller ett datapaket utan att uppdatera tjänstkoden startas tjänsten bara om egenskapen ForceRestart är inställd på **True**. När uppdateringen är klar meddelar Service Fabric tjänsten att ett nytt konfigurationspaket eller datapaket är tillgängligt. Tjänsten ansvarar för att tillämpa ändringarna. Om det behövs kan tjänsten starta om sig själv. |
tidsgränsen för hälsokontroll igen | Hur lång tid det tar att försöka om hälsoutvärderingen när programmet eller klustret är felfritt innan *FailureAction* körs. Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. Standard: PT0H10M0S. |
hälsokontroll-stabil varaktighet | Den tid som programmet eller klustret måste förbli felfritt innan uppgraderingen fortsätter till nästa uppgraderingsdomän. Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. Standard: PT0H2M0S. |
hälsokontroll-vänta-varaktighet | Hur lång tid det ska ta innan du har slutfört en uppgraderingsdomän innan hälsoprinciper tillämpas. Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. Standard: 0.|
max-ohälsosamma appar | Standard- och rekommenderat värde är 0. Ange det maximala antalet distribuerade program (se [avsnittet Hälso)](service-fabric-health-introduction.md)som kan vara felfritt innan programmet anses vara felfritt och misslyckas med uppgraderingen. Den här parametern definierar programmets hälsotillstånd på noden och hjälper till att identifiera problem under uppgraderingen. Vanligtvis blir replikerna av programmet belastningsbalanserade till den andra noden, vilket gör att programmet kan verka felfritt, vilket gör att uppgraderingen kan fortsätta. Genom att ange en strikt *hälsotillstånd för max-fel-appar* kan Service Fabric upptäcka ett problem med programpaketet snabbt och hjälpa till att skapa en misslyckad snabb uppgradering. Representerad som ett tal mellan 0 och 100. |
mode | Tillåtna värden **övervakas,** **UpgradeMode**, **UnmonitoredAuto**, **UnmonitoredManual**. Standard är **UnmonitoredAuto**. I avsnittet Parametrar för obligatoriska *i* Visual Studio och PowerShell finns beskrivningar av dessa värden.|
tidsgränsen för replik-set-utcheckning |Mätt i sekunder. <br>**Tillståndslös tjänst**– Inom en enda uppgraderingsdomän försöker Service Fabric se till att ytterligare instanser av tjänsten är tillgängliga. Om antalet målinstanser är fler än en väntar Service Fabric på att mer än en instans ska vara tillgänglig, upp till ett maximalt time out-värde. Den här timeouten anges med hjälp av egenskapen *replik-set-check-timeout.* Om time-out upphör att gälla fortsätter Service Fabric med uppgraderingen, oavsett antalet tjänstinstanser. Om antalet målinstanser är ett väntar inte Service Fabric och fortsätter omedelbart med uppgraderingen.<br><br>**Tillståndskänslig tjänst**– Inom en enda uppgraderingsdomän försöker Service Fabric se till att replikuppsättningen har kvorum. Service Fabric väntar på att ett kvorum ska vara tillgängligt, upp till ett maximalt timeout-värde (angivet av egenskapen *replica-set-check-timeout).* Om time-out upphör att gälla fortsätter Service Fabric med uppgraderingen, oavsett kvorum. Den här inställningen ställs in som aldrig (oändlig) när du rullar framåt och 1200 sekunder när du rullar tillbaka. |
service-hälso-politik | JSON kodade kartan med hälsoprincip för tjänsttyp per tjänsttypsnamn. Kartan är tom som standard. [Parameter JSON-format.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). JSON för "Värde"-delen innehåller **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService**och **MaxPercentUnhealthyReplicasPerPartition**. Mer information om dessa parametrar finns i avsnittet Valfria parametrar i Visual Studio och PowerShell.
timeout | Anger time-out-perioden i sekunder för operationen. Standard: 60. |
timeout för uppgradering-domän | Den tid som varje uppgraderingsdomän måste slutföra innan *FailureAction* körs. Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. Standardvärdet är aldrig (Oändligt) och bör anpassas på lämpligt sätt för ditt program. Standard: P10675199DT02H48M05.4775807S. |
timeout för uppgradering | Den tid som varje uppgraderingsdomän måste slutföra innan *FailureAction* körs. Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. Standardvärdet är aldrig (Oändligt) och bör anpassas på lämpligt sätt för ditt program. Standard: P10675199DT02H48M05.4775807S.|
varning som fel | Tillåtna värden är **Sant** och **Falskt**. Standardvärdet är **Falskt**. Kan skickas in som en flagga. Behandla varningshälsohändelser för programmet som fel när du utvärderar programmets hälsotillstånd under uppgraderingen. Som standard utvärderar Service Fabric inte varningshälsohändelser som fel (fel), så uppgraderingen kan fortsätta även om det finns varningshändelser. |

## <a name="next-steps"></a>Nästa steg
[Om du uppgraderar programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md) går du igenom en programuppgradering med Visual Studio.

[Om du uppgraderar programmet med Powershell](service-fabric-application-upgrade-tutorial-powershell.md) får du en programuppgradering med PowerShell.

[Om du uppgraderar ditt program med Service Fabric CLI på Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) går du igenom en programuppgradering med Service Fabric CLI.

[Uppgradera ditt program med Hjälp av Service Fabric Eclipse Plugin](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Gör dina programuppgraderingar kompatibla genom att lära dig hur du använder [data serialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar programmet genom att referera till [Avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i programuppgraderingar genom att referera till stegen i [Felsökning av programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

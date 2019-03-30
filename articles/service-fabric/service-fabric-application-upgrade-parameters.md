---
title: 'Programuppgradering: Uppgraderingsparametrar | Microsoft Docs'
description: Beskriver parametrar som är relaterade till uppgraderingen av ett Service Fabric-program, inklusive hälsokontroller att utföra och principer för att automatiskt återställa uppgraderingen.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2018
ms.author: subramar
ms.openlocfilehash: 9a93c0993ee45e72b11b023982dfbbe8c6528272
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670625"
---
# <a name="application-upgrade-parameters"></a>Programuppgraderingsparametrar
Den här artikeln beskrivs de olika parametrar som gäller under uppgraderingen av en Azure Service Fabric-program. Programuppgraderingsparametrar styra timeout och hälsokontroller av slutpunkter som tillämpas under uppgraderingen och de ange de principer som måste användas när uppgraderingen misslyckas. Programparametrar gäller för uppgraderingar med hjälp av:
- PowerShell
- Visual Studio
- SFCTL
- [REST](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Programuppgraderingar startas via ett av tre lägen för användaren valbara uppgradering. Varje läge har en egen uppsättning parametrar för program:
- Övervakad
- Oövervakade automatiskt
- Oövervakade manuell

De tillämpliga obligatoriska och valfria parametrarna beskrivs i varje avsnitt på följande sätt.

## <a name="visual-studio-and-powershell-parameters"></a>Visual Studio och PowerShell-parametrar

Service Fabric programuppgraderingar med hjälp av PowerShell-användning i [Start ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) kommando. Uppgraderingsläget väljs genom att skicka antingen den **övervakade**, **UnmonitoredAuto**, eller **UnmonitoredManual** parameter [ Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

Visual Studio Service Fabric-programuppgraderingsparametrar anges via dialogrutan Inställningar för uppgradering av Visual Studio. Uppgraderingsläge för Visual Studio har valts med hjälp av den **uppgradera läge** listrutan antingen **övervakade**, **UnmonitoredAuto**, eller **UnmonitoredManual** . Mer information finns i [konfigurera uppgraderingen av ett Service Fabric-program i Visual Studio](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Obligatoriska parametrar
(PS=PowerShell, VS=Visual Studio)

| Parameter | Gäller | Beskrivning |
| --- | --- | --- |
ApplicationName |PS| Namnet på det program som ska uppgraderas. Exempel: fabric: / VisualObjects fabric: / ClusterMonitor. |
ApplicationTypeVersion|PS|Versionen av programmet skriver som mål för uppgradering. |
FailureAction |PS, VS|Tillåtna värden är **återställning**, **manuell**, och **ogiltigt**. Den kompenserande åtgärden som ska utföras när en *övervakade* uppgradera möten övervakning principöverträdelser principen eller hälsotillstånd. <br>**Rollback** anger att uppgraderingen automatiskt rullar tillbaka till den förberedande versionen. <br>**Manuell** anger att uppgraderingen växlar till den *UnmonitoredManual* Uppgraderingsläge. <br>**Ogiltig** anger att den misslyckade åtgärden är ogiltig.|
Övervakad |PS|Anger att uppgraderingsläget är övervakad. När cmdleten har en uppgradering för en uppgraderingsdomän om hälsotillståndet för uppgraderingsdomänen och klustret uppfyller hälsoprinciper som du definierar, uppgraderar Service Fabric med nästa uppgraderingsdomän. Om uppgraderingsdomän eller kluster inte kan uppfylla hälsoprinciper, misslyckas uppgraderingen och Service Fabric återställer uppgraderingen för uppgraderingsdomänen eller återgår till manuellt läge per de principer som anges. Det här är det rekommenderade läget för programuppgraderingar i en produktionsmiljö. |
UpgradeMode | VS | Tillåtna värden är **övervakade** (standard), **UnmonitoredAuto**, eller **UnmonitoredManual**. Se PowerShell-parametrar för varje steg i den här artikeln för information. |
UnmonitoredAuto | PS | Anger att uppgraderingsläget oövervakade automatiskt. När Service Fabric en uppgraderingsdomän har uppgraderats, uppgraderar Service Fabric med nästa uppgraderingsdomän oavsett hälsotillstånd för systemprogram. Det här läget rekommenderas inte för produktion och används endast under utvecklingen av ett program. |
UnmonitoredManual | PS | Anger att uppgraderingsläget oövervakade manuell. När Service Fabric uppgraderar en uppgraderingsdomän, väntar den att uppgradera med nästa uppgraderingsdomän med hjälp av den *återuppta ServiceFabricApplicationUpgrade* cmdlet. |

### <a name="optional-parameters"></a>Valfria parametrar

Hälsotillstånd utvärdering parametrar är valfria. Om utvärderingskriterierna health inte anges när en uppgradering startar, använder Service Fabric programmet hälsoprinciper som anges i ApplicationManifest.xml av programinstansen.

Använd den vågräta rullningslisten längst ned i tabellen om du vill visa fullständig beskrivningsfältet.

(PS=PowerShell, VS=Visual Studio)

| Parameter | Gäller | Beskrivning |
| --- | --- | --- |
| ApplicationParameter |PS, VS| Anger åsidosättningar för programparametrar.<br>PowerShell-programparametrar har angetts som hash-tabell namn/värde-par. For example, @{ "VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1" }.<br>Visual Studio-programparametrar kan anges i dialogrutan Publicera Service Fabric-program i den **Programparameterfil** fält.
| Bekräfta |PS| Tillåtna värden är **SANT** och **FALSKT**. Frågar efter bekräftelse innan du kör cmdlet: en. |
| ConsiderWarningAsError |PS, VS |Tillåtna värden är **SANT** och **FALSKT**. Standardvärdet är **Falskt**. Hantera varningshändelser för hälsotillståndet för programmet som fel vid utvärdering av hälsotillståndet för programmet under uppgraderingen. Som standard utvärderar inte Service Fabric health varningshändelser för att vara fel (fel), så uppgraderingen kan fortsätta även om det finns händelser. |
| DefaultServiceTypeHealthPolicy | PS, VS |Anger hälsoprincip för den typ av standardtjänst för övervakade uppgraderingen i formatet MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. Till exempel anger 5,10,15 följande värden: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
| Force | PS, VS | Tillåtna värden är **SANT** och **FALSKT**. Anger att uppgraderingsprocessen hoppar över varningsmeddelandet och tvingar uppgraderingen även om versionsnumret inte har ändrats. Detta är användbart för lokal testning men rekommenderas inte för användning i en produktionsmiljö, eftersom den kräver att ta bort den befintliga distributionen som orsakar driftstopp och potentiella dataförluster. |
| ForceRestart |PS, VS |Om du uppdaterar en konfiguration eller datapaketet utan att uppdatera kod som tjänsten startas endast om ForceRestart egenskapen är inställd på **SANT**. När uppdateringen är klar meddelar Service Fabric tjänsten och att en ny konfigurationspaket eller datapaketet är tillgänglig. Tjänsten ansvarar för att tillämpa ändringarna. Om det behövs tjänsten kan starta om sig själv. |
| HealthCheckRetryTimeoutSec |PS, VS |Varaktighet (i sekunder) fortsätter som Service Fabric att utföra hälsotillstånd utvärderingen innan du deklarerar uppgraderingen som misslyckades. Standardvärdet är 600 sekunder. Den här varaktigheten inleds när *HealthCheckWaitDurationSec* har nåtts. På den här *HealthCheckRetryTimeout*, Service Fabric kan utföra flera hälsokontroller av programmets hälsotillstånd. Standardvärdet är 10 minuter och bör anpassas korrekt för ditt program. |
| HealthCheckStableDurationSec |PS, VS |Varaktighet (i sekunder) kontrollera att programmet är stabil innan du flyttar till nästa uppgraderingsdomän eller uppgraderingen är klar. Vänta varaktigheten används för att förhindra hälsotillståndsförändringar direkt efter att hälsokontrollen har utförts. Standardvärdet är 120 sekunder och bör anpassas korrekt för ditt program. |
| HealthCheckWaitDurationSec |PS, VS | Väntetiden (i sekunder) när uppgraderingen har slutförts på uppgraderingsdomänen innan Service Fabric utvärderar hälsotillståndet för programmet. Varaktigheten kan också ses som den tid som ett program ska köras innan den kan betraktas som felfri. Om hälsokontrollen godkänns, fortsätter uppgraderingen till med nästa uppgraderingsdomän.  Om det inte går att hälsokontrollen, Service Fabric väntar för [UpgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) innan du försöker hälsotillståndet Kontrollera igen förrän den *HealthCheckRetryTimeoutSec* har nåtts. Rekommenderade standardvärdet är 0 sekunder. |
| MaxPercentUnhealthyDeployedApplications|PS, VS |Rekommenderade standardvärdet är 0. Ange det maximala antalet distribuerade program (se den [hälsotillstånd avsnittet](service-fabric-health-introduction.md)) som kan vara felaktiga innan programmet betraktas som defekt och misslyckas uppgraderingen. Den här parametern definierar programmets hälsotillstånd på noden och hjälper dig identifiera problem under uppgraderingen. Repliker av programmet kommer vanligtvis, Utjämning av nätverksbelastning till den andra noden, vilket gör att programmet syns felfritt, vilket medför att uppgraderingen kan fortsätta. Genom att ange en strikt *MaxPercentUnhealthyDeployedApplications* hälsa, Service Fabric kan identifiera problem med programpaketet snabbt och hjälpa att skapa en snabb uppgraderingen misslyckas. |
| MaxPercentUnhealthyServices |PS, VS |En parameter till *DefaultServiceTypeHealthPolicy* och *ServiceTypeHealthPolicyMap*. Rekommenderade standardvärdet är 0. Ange det maximala antalet tjänster i programinstansen som kan vara felaktiga innan programmet betraktas som defekt och misslyckas uppgraderingen. |
| MaxPercentUnhealthyPartitionsPerService|PS, VS |En parameter till *DefaultServiceTypeHealthPolicy* och *ServiceTypeHealthPolicyMap*. Rekommenderade standardvärdet är 0. Ange det maximala antalet partitioner i en tjänst som kan vara felaktiga innan tjänsten betraktas som defekt. |
| MaxPercentUnhealthyReplicasPerPartition|PS, VS |En parameter till *DefaultServiceTypeHealthPolicy* och *ServiceTypeHealthPolicyMap*. Rekommenderade standardvärdet är 0. Ange det maximala antalet repliker i partition som kan vara felaktiga innan partitionen betraktas som defekt. |
| ServiceTypeHealthPolicyMap | PS, VS | Representerar hälsoprincip som används för att utvärdera hälsotillståndet för tjänster som hör till en typ av tjänst. Tar en hash-tabell som indata i följande format: @ {”ServiceTypeName”: ”MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices”} till exempel: @{”ServiceTypeName01” = ”5,10,5”; ”ServiceTypeName02” = ”5,5,5”} |
| TimeoutSec | PS, VS | Anger tidsgränsen i sekunder för åtgärden. |
| UpgradeDomainTimeoutSec |PS, VS |Längsta tid (i sekunder) för att uppgradera en enda uppgraderingsdomän. Om den här timeout har uppnåtts kan uppgraderingen avbryts och fortsätter baserat på inställningen för *FailureAction*. Standardvärdet är aldrig (obegränsat) och bör anpassas korrekt för ditt program. |
| UpgradeReplicaSetCheckTimeoutSec |PS, VS |Mätt i sekunder.<br>**Tillståndslös tjänst**--inom en enda uppgraderingsdomän Service Fabric försöker se till att ytterligare instanser av tjänsten är tillgänglig. Om målet instansantalet finns mer än en väntar Service Fabric för fler än en instans ska vara tillgängligt, upp till ett högsta timeout-värde. Den här timeout anges med hjälp av den *UpgradeReplicaSetCheckTimeoutSec* egenskapen. Om tidsgränsen överskrids fortsätter Service Fabric med uppgraderingen, oavsett antalet instanser av tjänsten. Om målet instansantalet finns en Service Fabric väntar inte och omedelbart fortsätter med uppgraderingen.<br><br>**Tillståndskänslig tjänst**--inom en enda uppgraderingsdomän Service Fabric försöker se till att replikuppsättningen har ett kvorum. Service Fabric väntar för ett kvorum ska vara tillgängligt, upp till ett högsta timeout-värde (anges av den *UpgradeReplicaSetCheckTimeoutSec* egenskapen). Om tidsgränsen överskrids fortsätter Service Fabric med uppgraderingen, oavsett kvorum. Den här inställningen har angetts som aldrig (oändligt) när du återställer och 1200 sekunder när återtagning. |
| UpgradeTimeoutSec |PS, VS |En timeout (i sekunder) som gäller för hela uppgraderingen. Om den här tidsgränsen uppnås stoppas uppgraderingen och *FailureAction* utlöses. Standardvärdet är aldrig (obegränsat) och bör anpassas korrekt för ditt program. |
| WhatIf | PS | Tillåtna värden är **SANT** och **FALSKT**. Visar vad som skulle hända om cmdleten kördes. Cmdleten körs inte. |

Den *MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService*, och *MaxPercentUnhealthyReplicasPerPartition* villkor kan anges typ av tjänst för en programinstans. Om dessa parametrar per tjänst kan för ett program som innehåller olika tjänster typer med principer för olika utvärdering. En typ av tillståndslösa gateway kan till exempel ha en *MaxPercentUnhealthyPartitionsPerService* som skiljer sig från en tillståndskänslig engine service-typ för en programinstans.

## <a name="sfctl-parameters"></a>SFCTL parametrar

Service Fabric programuppgraderingar med hjälp av Service Fabric CLI-användning i [sfctl Programuppgradering](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) kommandot tillsammans med följande nödvändiga och valfria parametrar.

### <a name="required-parameters"></a>Obligatoriska parametrar

| Parameter | Beskrivning |
| --- | --- |
| program-id  |ID för det program som ska uppgraderas. <br> Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric”: URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den '\~' tecken. Om programnamnet är till exempel ”fabric: / myapp/app1 ', programidentiteten skulle vara” myapp\~app1' i 6.0 + och ”myapp/app1' i tidigare versioner.|
application-version |Versionen av programmet skriver som mål för uppgradering.|
parameters  |En JSON-kodad lista över program parametern åsidosättningar för att användas när du uppgraderar programmet.|

### <a name="optional-parameters"></a>Valfria parametrar

| Parameter | Beskrivning |
| --- | --- |
Standard-service-hälsa-policy | [JSON](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) kodad specifikation av hälsoprincip som används som standard för att utvärdera hälsan för en typ av tjänst. Kartan är tomt som standard. |
åtgärd vid uppgraderingsfel | Tillåtna värden är **återställning**, **manuell**, och **ogiltigt**. Den kompenserande åtgärden som ska utföras när en *övervakade* uppgradera möten övervakning principöverträdelser principen eller hälsotillstånd. <br>**Rollback** anger att uppgraderingen automatiskt rullar tillbaka till den förberedande versionen. <br>**Manuell** anger att uppgraderingen växlar till den *UnmonitoredManual* Uppgraderingsläge. <br>**Ogiltig** anger att den misslyckade åtgärden är ogiltig.|
force-restart | Om du uppdaterar en konfiguration eller datapaketet utan att uppdatera kod som tjänsten startas endast om ForceRestart egenskapen är inställd på **SANT**. När uppdateringen är klar meddelar Service Fabric tjänsten och att en ny konfigurationspaket eller datapaketet är tillgänglig. Tjänsten ansvarar för att tillämpa ändringarna. Om det behövs tjänsten kan starta om sig själv. |
health-check-retry-timeout | Hur lång tid att försöka igen hälsotillstånd utvärdering när programmet eller klustret är i feltillstånd innan *FailureAction* körs. Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. Standard: PT0H10M0S. |
-stabil-hälsokontroll | Hur lång tid att programmet eller klustret måste vara felfria innan uppgraderingen fortsätter du med nästa uppgraderingsdomän. Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. Standard: PT0H2M0S. |
health-check-wait-duration | Hur lång tid att vänta efter att du har slutfört en uppgraderingsdomän innan du tillämpar hälsoprinciper. Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. Standard: 0.|
Maximalt antal defekta appar | Rekommenderade standardvärdet är 0. Ange det maximala antalet distribuerade program (se den [hälsotillstånd avsnittet](service-fabric-health-introduction.md)) som kan vara felaktiga innan programmet betraktas som defekt och misslyckas uppgraderingen. Den här parametern definierar programmets hälsotillstånd på noden och hjälper dig identifiera problem under uppgraderingen. Repliker av programmet kommer vanligtvis, Utjämning av nätverksbelastning till den andra noden, vilket gör att programmet syns felfritt, vilket medför att uppgraderingen kan fortsätta. Genom att ange en strikt *max felaktiga appar* hälsa, Service Fabric kan identifiera problem med programpaketet snabbt och hjälpa att skapa en snabb uppgraderingen misslyckas. Visas som ett tal mellan 0 och 100. |
läge | Tillåtna värden är **övervakade**, **UpgradeMode**, **UnmonitoredAuto**, **UnmonitoredManual**. Standardvärdet är **UnmonitoredAuto**. Se Visual Studio och PowerShell *krävs parametrar* för beskrivningar av dessa värden.|
replica-set-check-timeout |Mätt i sekunder. <br>**Tillståndslös tjänst**--inom en enda uppgraderingsdomän Service Fabric försöker se till att ytterligare instanser av tjänsten är tillgänglig. Om målet instansantalet finns mer än en väntar Service Fabric för fler än en instans ska vara tillgängligt, upp till ett högsta timeout-värde. Den här timeout anges med hjälp av den *replik-set-kontroll-timeout* egenskapen. Om tidsgränsen överskrids fortsätter Service Fabric med uppgraderingen, oavsett antalet instanser av tjänsten. Om målet instansantalet finns en Service Fabric väntar inte och omedelbart fortsätter med uppgraderingen.<br><br>**Tillståndskänslig tjänst**--inom en enda uppgraderingsdomän Service Fabric försöker se till att replikuppsättningen har ett kvorum. Service Fabric väntar för ett kvorum ska vara tillgängligt, upp till ett högsta timeout-värde (anges av den *replik-set-kontroll-timeout* egenskapen). Om tidsgränsen överskrids fortsätter Service Fabric med uppgraderingen, oavsett kvorum. Den här inställningen har angetts som aldrig (oändligt) när du återställer och 1200 sekunder när återtagning. |
tjänsten hälsoprincip | JSON-kodad karta med tjänsten typ hälsoprincip per typ tjänstnamn. Kartan är tom vara standard. [Parametern JSON-format. ](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). JSON för ”Value”-delen innehåller **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService**, och **MaxPercentUnhealthyReplicasPerPartition**. Se avsnittet Visual Studio och PowerShell valfria parametrar finns beskrivningar av dessa parametrar.
timeout | Anger tidsgränsen i sekunder för åtgärden. Standard: 60. |
uppgraderingen domäntidsgräns | Hur lång tid varje domän har slutförts innan *FailureAction* körs. Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. Standardvärdet är aldrig (obegränsat) och bör anpassas korrekt för ditt program. Standard: P10675199DT02H48M05.4775807S. |
Tidsgräns för uppgradering | Hur lång tid varje domän har slutförts innan *FailureAction* körs. Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. Standardvärdet är aldrig (obegränsat) och bör anpassas korrekt för ditt program. Standard: P10675199DT02H48M05.4775807S.|
warning-as-error | Tillåtna värden är **SANT** och **FALSKT**. Standardvärdet är **Falskt**. Kan skickas i som en flagga. Hantera varningshändelser för hälsotillståndet för programmet som fel vid utvärdering av hälsotillståndet för programmet under uppgraderingen. Som standard utvärderar inte Service Fabric health varningshändelser för att vara fel (fel), så uppgraderingen kan fortsätta även om det finns händelser. |

## <a name="next-steps"></a>Nästa steg
[Uppgradera ditt program med hjälp av Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med Visual Studio.

[Uppgradera ditt program med hjälp av Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.

[Uppgradering av program med Service Fabric CLI på Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) vägleder dig genom en uppgradering av programmet med hjälp av Service Fabric CLI.

[Uppgradering av program med hjälp av Service Fabric Eclipse-plugin-programmet](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Gör din programuppgraderingar kompatibel genom att lära dig hur du använder [dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att referera till [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i programuppgraderingar genom att referera till stegen i [felsöka programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

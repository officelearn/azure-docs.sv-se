---
title: Felsöka programuppgraderingar | Microsoft Docs
description: Den här artikeln beskriver några vanliga problem runt att uppgradera ett Service Fabric-program och hur du löser dem.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 9e4989f61741d317e78a613c8c8fac312d1568c2
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666961"
---
# <a name="troubleshoot-application-upgrades"></a>Felsök programuppgraderingar
Den här artikeln beskriver några vanliga problem runt att uppgradera ett Azure Service Fabric-program och hur du löser dem.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Felsöka en misslyckad Programuppgradering
När uppgraderingen misslyckas, resultatet av den **Get-ServiceFabricApplicationUpgrade** kommando innehåller ytterligare information för felsökning av felet.  I följande lista anger hur informationen kan användas:

1. Identifiera fel-typen.
2. Identifiera orsaken till felet.
3. Isolera en eller flera misslyckade komponenter för vidare studier.

Den här informationen är tillgänglig när Service Fabric identifierar misslyckandet oavsett om den **FailureAction** är att återställa eller pausa uppgraderingen.

### <a name="identify-the-failure-type"></a>Identifiera feltyp
I utdata från **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifierar tidsstämpel (i UTC) då en uppgradering fel upptäcktes av Service Fabric och  **FailureAction** utlöstes. **FailureReason** identifierar en av tre möjliga övergripande orsaker till problemet:

1. UpgradeDomainTimeout - anger att en viss uppgraderingsdomän tog för lång tid att slutföra och **UpgradeDomainTimeout** har upphört att gälla.
2. OverallUpgradeTimeout - anger att övergripande uppgraderingen tog för lång tid att slutföra och **UpgradeTimeout** har upphört att gälla.
3. HealthCheck - anger att programmet när du har uppgraderat en uppdateringsdomän legat feltillstånd enligt de angivna hälsoprinciper och **HealthCheckRetryTimeout** har upphört att gälla.

De här posterna endast visas i utdata när uppgraderingen misslyckas och startar återtagning. Ytterligare information visas beroende på vilken typ av fel.

### <a name="investigate-upgrade-timeouts"></a>Undersöka uppgradera tidsgränser
Uppgradera timeout-fel orsakas oftast av problem med tjänsters tillgänglighet. Utdata efter detta stycke är typiska för uppgraderingar där tjänsten repliker eller instanser inte startas i den nya kodversionen. Den **UpgradeDomainProgressAtFailure** fältet samlar in en ögonblicksbild av alla pågående uppgradering arbetet vid tidpunkten för felet.

```
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

I det här exemplet misslyckades uppgraderingen på uppgraderingsdomänen *MYUD1* och två partitioner (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* och *4b43f4d8-b26b-424e-9307-7a7a62e79750*) har fastnat. Partitionerna har fastnat eftersom körningen inte gick att placera primära repliker (*WaitForPrimaryPlacement*) på målnoder *Node1* och *nod4*.

Den **Get-ServiceFabricNode** kommando kan användas för att verifiera att det är dessa två noder i uppgraderingsdomän *MYUD1*. Den *UpgradePhase* säger *PostUpgradeSafetyCheck*, vilket innebär att kontrollerna säkerhet sker när alla noder i uppgraderingsdomänen är klar med uppgraderingen. Den här informationen pekar på ett potentiellt problem med den nya versionen av programkoden. De vanligaste problemen är fel i tjänsten i öppen eller uppgradering till primära kodsökvägar.

En *UpgradePhase* av *PreUpgradeSafetyCheck* innebär att det uppstod problem förbereda uppgraderingsdomänen innan den utfördes. De vanligaste problemen är i det här fallet fel i tjänsten i slutet eller degradering från primära kodsökvägar.

Aktuellt **UpgradeState** är *RollingBackCompleted*, så att den ursprungliga uppgraderingen måste ha utförts med en återställning **FailureAction**, som automatiskt återkallas tillbaka uppgraderingen vid fel. Om ursprungliga uppgraderingen utfördes med en manuell **FailureAction**, och sedan uppgraderingen skulle istället i ett pausat tillstånd att tillåta live-felsökning av programmet.

I sällsynta fall kan den **UpgradeDomainProgressAtFailure** fält kan vara tomt om övergripande uppgraderingen tidsgränsen precis som systemet fyller allt arbete för aktuell uppgraderingsdomän. Om detta inträffar kan försöka att öka den **UpgradeTimeout** och **UpgradeDomainTimeout** uppgradera parametervärden och gör om uppgraderingen.

### <a name="investigate-health-check-failures"></a>Undersök hälsotillstånd kontrollera fel
Kontrollera hälsofel kan utlösas av olika problem som kan inträffa när alla noder i en uppgraderingsdomän Slutför uppgraderingen och för att skicka alla kontroller för säkerhet. Utdata efter detta stycke är typiska för en uppgradering misslyckades på grund av misslyckad hälsokontroller. Den **UnhealthyEvaluations** fältet samlar in en ögonblicksbild av hälsokontroller misslyckades vid tidpunkten för uppgraderingen enligt den angivna [hälsoprincip](service-fabric-health-introduction.md).

```
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Undersöka Kontrollera hälsofel först kräver en förståelse av hälsomodellen för Service Fabric. Men även om du inte sådan en djup förståelse kan vi se att två tjänster är skadad: *fabric: / DemoApp/Svc3* och *fabric: / DemoApp/Svc2*, tillsammans med fel hälsorapporter (”InjectedFault” i det här fallet). I det här exemplet två av de fyra tjänster är skadade, vilket är under standard-målet på 0% ohälsosamt (*MaxPercentUnhealthyServices*).

Uppgraderingen avbröts vid misslyckas genom att ange en **FailureAction** över manuella när du startar uppgraderingen. Det här läget låter oss undersöka det aktiva systemet i felaktigt tillstånd innan du vidtar ytterligare åtgärder.

### <a name="recover-from-a-suspended-upgrade"></a>Återställa från en pausad uppgradering
Med en återställning **FailureAction**, det finns inga recovery behövs eftersom uppgraderingen automatiskt återställer vid misslyckas. Med en manuell **FailureAction**, det finns flera återställningsalternativ:

1.  utlösa en återställning
2. Gå igenom resten av uppgraderingen manuellt
3. Återuppta övervakade uppgraderingen

Den **Start ServiceFabricApplicationRollback** kommando kan användas när som helst för att starta programmet att återställa. När kommandot returnerar har, rollback-förfrågan har registrerats i systemet och startar strax därefter.

Den **återuppta ServiceFabricApplicationUpgrade** kommando kan användas för att gå igenom resten av uppgraderingen manuellt, en uppgraderingsdomän i taget. I det här läget utförs endast säkerhet kontroller av systemet. Inga fler hälsokontroller utförs. Det här kommandot kan endast vara används när den *UpgradeState* visar *RollingForwardPending*, vilket innebär att den aktuella uppgraderingsdomänen har uppgraderats, men nästa har inte startats (väntar).

Den **uppdatering ServiceFabricApplicationUpgrade** kommando kan användas för att återuppta den övervakade uppgraderingen med både säkerhet och hälsokontroller som utförs.

```
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
```

Uppgraderingen fortsätter från den domän där det pausades senast och använder samma uppgradera parametrar och hälsoprinciper som innan. Om det behövs kan Uppgraderingsparametrar och hälsoprinciper som visas i föregående utdata ändras i samma kommando när du fortsätter med uppgraderingen. I det här exemplet återupptogs uppgraderingen i övervakad läge med parametrarna och hälsoprinciper oförändrade.

## <a name="further-troubleshooting"></a>Ytterligare felsökning
### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric följer inte de angivna hälsoprinciper
Möjlig orsak 1:

Service Fabric omvandlar alla värden till aktuellt antal entiteter (till exempel repliker, partitioner och tjänster) för utvärdering av hälsotillstånd och Avrundar alltid uppåt till hela entiteter. Till exempel om maximalt *MaxPercentUnhealthyReplicasPerPartition* är 21% och det finns fem repliker, och sedan Service Fabric kan upp till två repliker som är felaktiga (det vill säga`Math.Ceiling (5*0.21)`). Därför ska hälsoprinciper fastställas.

Möjlig orsak 2:

Hälsoprinciper anges som procentandelar av totalt antal tjänster och inte specifika tjänstinstanser. Till exempel före en uppgradering om ett program har fyra tjänsten instanser A, B, C och D, där tjänsten D är i feltillstånd, men med liten inverkan till programmet. Vi vill ignorera kända felaktig tjänst D under uppgraderingen och ange parametern *MaxPercentUnhealthyServices* 25%, förutsatt att endast A B och C måste att, vara felfritt.

Men under uppgraderingen bli D felfri medan C blir ohälsosamt. Uppgraderingen kan ändå lyckas eftersom endast 25% av tjänsterna inte är felfria. Men kan det resultera i oväntade fel på grund av C som oväntat feltillstånd i stället för D. I det här fallet bör D modelleras som en annan typ från A, B och C. Eftersom hälsoprinciper anges per typ av tjänst, kan olika feltillstånd procentsatser tröskelvärden tillämpas på olika tjänster. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Jag har inte angett en hälsoprincip för uppgradering av programmet, men fortfarande misslyckas uppgraderingen för vissa tidsgränser som jag angav aldrig
När hälsoprinciper inte förutsatt att uppgradera begäran, hämtas de från den *ApplicationManifest.xml* av den aktuella versionen av programmet. Till exempel om du uppgraderar programmet X från version 1.0 till version 2.0, hälsoprinciper för program som angetts för version 1.0 används. Om en annan hälsoprincip bör användas för uppgraderingen, måste principen som anges som en del av uppgraderingen-API-anrop för programmet. De principer som angetts som en del av API-anrop gäller endast under uppgraderingen. När uppgraderingen är klar, principerna som angetts i den *ApplicationManifest.xml* används.

### <a name="incorrect-time-outs-are-specified"></a>Felaktig timeout har angetts
Du har funderat över om vad som händer när timeout är inställda inkonsekvent. Du kan till exempel ha en *UpgradeTimeout* som är mindre än värdet *UpgradeDomainTimeout*. Svaret är att ett fel returneras. Fel returneras om det *UpgradeDomainTimeout* är mindre än summan av *HealthCheckWaitDuration* och *HealthCheckRetryTimeout*, eller om  *UpgradeDomainTimeout* är mindre än summan av *HealthCheckWaitDuration* och *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Min uppgraderingar tar för lång
Tiden för en uppgradering till slutföra beror på hälsokontroller och tidsgränser som angetts. Hälsokontroller och tidsgränser beror på hur lång tid det tar att kopiera, distribuera och stabilisera programmet. Som för aggressiva med tidsgränser kan innebära mer misslyckade uppgraderingar, så vi rekommenderar att du börjar var med längre timeout.

Här är Fräscha upp på hur tidsgränser interagerar med uppgradera gånger:

Uppgraderingar för en uppgraderingsdomän inte kan slutföra snabbare än *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Uppgraderingen skulle misslyckas kan inte ske snabbare än *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

Tiden för uppgraderingen för en uppgraderingsdomän begränsas av *UpgradeDomainTimeout*.  Om *HealthCheckRetryTimeout* och *HealthCheckStableDuration* båda är noll och hälsotillståndet för programmet som ser till att växla fram och tillbaka och sedan uppgraderingen så småningom tidsgränsen på *UpgradeDomainTimeout*. *UpgradeDomainTimeout* börjar räkna ned en gång av uppgraderingen för aktuell uppgraderingsdomän börjar.

## <a name="next-steps"></a>Nästa steg
[Uppgradera ditt program med hjälp av Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med Visual Studio.

[Uppgradera ditt program med hjälp av Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.

Styra hur programmet uppgraderas med hjälp av [uppgradera parametrar](service-fabric-application-upgrade-parameters.md).

Gör din programuppgraderingar kompatibel genom att lära dig hur du använder [dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att referera till [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

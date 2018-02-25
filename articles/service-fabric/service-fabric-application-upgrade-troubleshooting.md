---
title: "Felsökning av programuppgraderingar | Microsoft Docs"
description: "Den här artikeln beskriver några vanliga problem runt att uppgradera ett Service Fabric-program och hur du löser dem."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: beacf34ae0cf7cfd55b63a862d3dabce99248939
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="troubleshoot-application-upgrades"></a>Felsök programuppgraderingar
Den här artikeln beskriver några vanliga problem runt ett Azure Service Fabric-program och hur du löser dem uppgraderas.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Felsöka en misslyckad Programuppgradering
När uppgraderingen misslyckas, utdata från den **Get-ServiceFabricApplicationUpgrade** kommando innehåller ytterligare information för felsökning för felet.  I följande lista anger hur den ytterligare informationen kan användas:

1. Identifiera feltyp.
2. Identifiera felorsaken.
3. Isolera en eller flera misslyckas komponenter för ytterligare undersökning.

Den här informationen är tillgänglig när Service Fabric identifierar misslyckandet oavsett om den **FailureAction** är att återställa eller avbryta uppgraderingen.

### <a name="identify-the-failure-type"></a>Identifiera vilken feltyp
I produktion **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifierar tidsstämpeln (i UTC) då en uppgradering fel upptäcktes av Service Fabric och  **FailureAction** utlöstes. **FailureReason** identifierar en av tre övergripande möjliga orsaker till felet:

1. UpgradeDomainTimeout - anger att en viss domän tog för lång tid att slutföra och **UpgradeDomainTimeout** upphört att gälla.
2. OverallUpgradeTimeout - anger att uppgraderingen av övergripande tog för lång tid att slutföra och **UpgradeTimeout** upphört att gälla.
3. Hälsokontroll - anger att programmet när du har uppgraderat en uppdateringsdomän legat ohälsosamt enligt angivna hälsoprinciper och **HealthCheckRetryTimeout** upphört att gälla.

Dessa poster endast visas i utdata när uppgraderingen misslyckas och startar återställs. Ytterligare information visas beroende på vilken typ av felet.

### <a name="investigate-upgrade-timeouts"></a>Undersök uppgradera tidsgränser
Uppgradera timeout fel orsakas oftast av problem med tjänsters tillgänglighet. Utdata följande stycke är typiska för uppgraderingar där tjänsten repliker eller instanser inte startas i den nya versionen i koden. Den **UpgradeDomainProgressAtFailure** fältet samlar in en ögonblicksbild av alla väntande uppgradera arbete vid tiden för felet.

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

I det här exemplet uppgraderingen misslyckades på uppgraderingsdomänen *MYUD1* och två partitioner (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* och *4b43f4d8-b26b-424e-9307-7a7a62e79750*) har fastnat. Partitionerna har fastnat eftersom körningen kunde inte placera primära repliker (*WaitForPrimaryPlacement*) på målnoder *Node1* och *nod4*.

Den **Get-ServiceFabricNode** kommando kan användas för att kontrollera att dessa två noder i uppgraderingsdomänen *MYUD1*. Den *UpgradePhase* står *PostUpgradeSafetyCheck*, vilket innebär att kontrollerna säkerhet sker när alla noder i domänen uppgraderingen är klar med uppgraderingen. Den här informationen pekar på ett potentiella problem med den nya versionen av programkoden. De flesta vanliga problem är service-fel i öppen eller uppgradering till primära kodsökvägar.

En *UpgradePhase* av *PreUpgradeSafetyCheck* innebär det fanns problem förbereda domänen uppgraderingen innan den har utförts. De flesta vanliga problem finns i det här fallet service-fel i slutet eller degraderingen från primära kodsökvägar.

Aktuellt **UpgradeState** är *RollingBackCompleted*, så den ursprungliga uppgraderingen måste ha utförts med en återställning **FailureAction**, som automatiskt upplyfta Säkerhetskopiera uppgraderingen vid fel. Om den ursprungliga uppgraderingen har utförts med en manuell **FailureAction**, och sedan uppgraderingen skulle i stället i ett pausat tillstånd att live-felsökning av programmet.

I sällsynta fall kan den **UpgradeDomainProgressAtFailure** fältet kan vara tom om uppgraderingen av övergripande tidsgränsen precis som systemet fyller allt arbete för den aktuella uppgraderingsdomänen. Om det händer kan du prova med att öka den **UpgradeTimeout** och **UpgradeDomainTimeout** uppgradera parametervärden och gör om uppgraderingen.

### <a name="investigate-health-check-failures"></a>Undersök health check-fel
Health check fel kan utlösas av olika problem som kan inträffa när alla noder i en uppgraderingsdomän Slutför uppgraderingen och skicka alla säkerhet kontroller. Följande stycke utdata är typiska för en uppgradering misslyckades på grund av misslyckad hälsokontroller. Den **UnhealthyEvaluations** fältet samlar in en ögonblicksbild av hälsokontroller som misslyckades under uppgraderingen enligt den angivna [hälsoprincip](service-fabric-health-introduction.md).

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

Undersöka health check fel först kräver en förståelse av Service Fabric-hälsomodell. Men även om ingen sådan en djupgående förståelse kan vi se att två tjänster är felfria: *fabric: / DemoApp/Svc3* och *fabric: / DemoApp/Svc2*, tillsammans med fel hälsorapporter (”InjectedFault” i detta fall). I det här exemplet två av de fyra tjänster är i feltillstånd, vilket är mindre än 0% ohälsosamt standard mål (*MaxPercentUnhealthyServices*).

Uppgraderingen pausades på misslyckas genom att ange en **FailureAction** över manuella när du startar uppgraderingen. Det här läget kan vi att undersöka live-systemet i felaktigt tillstånd innan du vidta ytterligare åtgärder.

### <a name="recover-from-a-suspended-upgrade"></a>Återställa från en pausade uppgradering
Med en återställning **FailureAction**, det finns ingen återställning behövs eftersom uppgraderingen automatiskt återställs vid misslyckas. Med en manuell **FailureAction**, det finns flera återställningsalternativ:

1.  Starta en återställning
2. Gå igenom resten av uppgraderingen manuellt
3. Återuppta övervakade uppgraderingen

Den **Start ServiceFabricApplicationRollback** kommando kan användas när som helst för att starta att återställa programmet. När kommandot returnerar har Återställningsbegäran har registrerats i systemet och startar strax därefter.

Den **återuppta ServiceFabricApplicationUpgrade** kommando kan användas för att gå igenom resten av uppgraderingen manuellt, en domän i taget. I det här läget utförs endast säkerhet kontroller av systemet. Inga fler systemhälsokontroller utförs. Endast det här kommandot kan användas när den *UpgradeState* visar *RollingForwardPending*, vilket innebär att den aktuella uppgraderingsdomänen har uppgraderats men nästa har inte startats (väntar).

Den **uppdatering ServiceFabricApplicationUpgrade** kommando kan användas för att återuppta övervakade uppgraderingen med både säkerhet och kontroll utförs.

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

Uppgraderingen fortsätter från den domän där det pausades senast och använder samma uppgradera parametrar och hälsoprinciper som innan. Om det behövs kan uppgradera parametrar och hälsoprinciper som visas i föregående utdata ändras i samma kommando när du fortsätter med uppgraderingen. I det här exemplet återupptogs uppgraderingen i övervakade-läge med parametrarna och hälsoprinciper oförändrade.

## <a name="further-troubleshooting"></a>Felsökningsinformation
### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric följer inte de angivna hälsoprinciper
Möjlig orsak 1:

Service Fabric översätter alla procenttal till faktiska antal entiteter (till exempel repliker, partitioner och tjänster) för klienthälsa och alltid Avrundar uppåt till hela entiteter. Till exempel om maximalt *MaxPercentUnhealthyReplicasPerPartition* är 21% och det finns fem repliker, och sedan Service Fabric kan upp till två ohälsosamt repliker (det vill säga`Math.Ceiling (5*0.21)`). Därför ska hälsoprinciper fastställas.

Möjlig orsak 2:

Hälsoprinciper anges som procent av totalt antal tjänster och inte är specifika tjänstinstanser. Till exempel före en uppgradering, om ett program har fyra tjänsten instanser A, B, C och D, där tjänsten D är felfri men med liten inverkan till programmet. Vi vill ignorera kända ohälsosamt tjänsten D under uppgraderingen och ange parametern *MaxPercentUnhealthyServices* 25%, under förutsättning att endast A och B och C måste att vara felfritt.

Dock under uppgraderingen bli D felfri medan C blir ohälsosamt. Uppgraderingen kan ändå lyckas eftersom endast 25% av tjänsterna är felfria. Men kan det resultera i oväntade fel på grund av C ohälsosamt oväntat i stället för D. I det här fallet bör D modelleras som en annan typ från A och B och C. Eftersom hälsoprinciper anges per typ av tjänst, kan annan ohälsosamt procentsats tröskelvärden tillämpas på olika tjänster. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Angav inte en hälsoprincip för uppgradering av programmet, men fortfarande misslyckas uppgraderingen för vissa timeout som jag aldrig har angetts
När hälsoprinciper inte under förutsättning att uppgraderingsbegäran, hämtas de från den *ApplicationManifest.xml* av den aktuella versionen av programmet. Till exempel om du uppgraderar programmet X från version 1.0 till version 2.0, hälsoprinciper för program som angetts för version 1.0 används. Om en annan hälsoprincip ska användas för uppgraderingen, sedan måste principen anges som en del av uppgraderingen-API-anrop för programmet. De principer som angetts som en del av API-anrop gäller endast under uppgraderingen. När uppgraderingen är klar, principerna som angetts i den *ApplicationManifest.xml* används.

### <a name="incorrect-time-outs-are-specified"></a>Felaktig timeout har angetts
Du har funderat över om vad som händer när för timeout är inkonsekvent. Du kan till exempel ha en *UpgradeTimeout* som understiger *UpgradeDomainTimeout*. Svaret är att ett fel returneras. Fel returneras om den *UpgradeDomainTimeout* är mindre än summan av *HealthCheckWaitDuration* och *HealthCheckRetryTimeout*, eller om  *UpgradeDomainTimeout* är mindre än summan av *HealthCheckWaitDuration* och *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Min uppgraderingar tar för lång
Tid för en uppgradering till slutföra beror på hälsokontroller och timeout anges. Hälsokontroller och tidsgränser beror på hur lång tid det tar att kopiera, distribuera och hålla programmet. Som för aggressivt med timeout kan betyda flera misslyckade uppgraderingar, så vi rekommenderar att du börjar restriktiva med längre timeout.

Här är en snabb uppdaterare på hur timeout interagera med uppgraderingen gånger:

Uppgraderar för en uppgraderingsdomän inte kan slutföra snabbare än *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Du uppgraderar kan inte ske snabbare än *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

Uppgradera tiden för en uppgraderingsdomän begränsas av *UpgradeDomainTimeout*.  Om *HealthCheckRetryTimeout* och *HealthCheckStableDuration* är båda inte är noll och hälsotillståndet för programmet för att hålla växla fram och tillbaka och sedan uppgraderingen slutligen timeout på *UpgradeDomainTimeout*. *UpgradeDomainTimeout* börjar räkna ned när uppgraderingen för den aktuella uppgraderingsdomänen börjar.

## <a name="next-steps"></a>Nästa steg
[Uppgradera ditt program med hjälp av Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med hjälp av Visual Studio.

[Uppgradera ditt program med hjälp av Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.

Styra hur programmet ska uppgraderas med hjälp av [uppgradera parametrar](service-fabric-application-upgrade-parameters.md).

Gör din programuppgraderingar kompatibla genom att lära dig hur du använder [dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att referera till [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

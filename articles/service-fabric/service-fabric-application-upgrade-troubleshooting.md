---
title: Felsöka programuppgraderingar
description: Den här artikeln innehåller några vanliga problem kring uppgradering av ett Service Fabric-program och hur du löser dem.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d462f2c2482e0fbb4d252967754a9675ed362674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377930"
---
# <a name="troubleshoot-application-upgrades"></a>Felsök programuppgraderingar

Den här artikeln innehåller några av de vanligaste problemen med att uppgradera ett Azure Service Fabric-program och hur du löser dem.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Felsöka en misslyckad programuppgradering

När en uppgradering misslyckas innehåller utdata från kommandot **Get-ServiceFabricApplicationUpgrade** ytterligare information för felsökning av felet.  I följande lista anges hur ytterligare information kan användas:

1. Identifiera feltypen.
2. Identifiera orsaken till fel.
3. Isolera en eller flera felaktiga komponenter för vidare undersökning.

Den här informationen är tillgänglig när Service Fabric upptäcker felet oavsett om **FailureAction** är att återställa eller avbryta uppgraderingen.

### <a name="identify-the-failure-type"></a>Identifiera feltypen

I utdata från **Get-ServiceFabricApplicationUpgrade**identifierar **FailureTimestampUtc** tidsstämpeln (i UTC) där ett uppgraderingsfel upptäcktes av Service Fabric och **FailureAction** utlöstes. **FailureReason** identifierar en av tre potentiella orsaker på hög nivå till felet:

1. UpgradeDomainTimeout - Anger att en viss uppgraderingsdomän tog för lång tid att slutföra och **UpgradeDomainTimeout** upphörde att gälla.
2. OverallUpgradeTimeout - Anger att den totala uppgraderingen tog för lång tid att slutföra och **UpgradeTimeout** upphörde att gälla.
3. HealthCheck - Anger att efter uppgradering av en uppdateringsdomän förblev programmet felfritt enligt de angivna hälsoprinciperna och **HealthCheckRetryTimeout** har upphört att gälla.

Dessa poster visas bara i utdata när uppgraderingen misslyckas och börjar rulla tillbaka. Ytterligare information visas beroende på typen av fel.

### <a name="investigate-upgrade-timeouts"></a>Undersöka timeout för uppgradering

Timeout-fel för uppgradering orsakas oftast av problem med tjänstens tillgänglighet. Utdata efter det här stycket är typiskt för uppgraderingar där tjänstrepliker eller instanser inte kan starta i den nya kodversionen. Fältet **UpgradeDomainProgressAtFailure** fångar en ögonblicksbild av ett väntande uppgraderingsarbete vid tidpunkten för felet.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
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

I det här exemplet misslyckades uppgraderingen vid uppgraderingsdomänen *MYUD1* och två partitioner *(744c8d9f-1d26-417e-a60e-cd48f5c098f0* och *4b43f4d8-b26b-424e-9307-7a7a62e79750*) har fastnat. Partitionerna har fastnat eftersom körningen inte kunde placera primära repliker *(WaitForPrimaryPlacement)* på målnoderna *Nod1* och *Nod4*.

Kommandot **Get-ServiceFabricNode** kan användas för att kontrollera att dessa två noder finns i uppgraderingsdomänen *MYUD1*. *UpgradePhase* säger *PostUpgradeSafetyCheck*, vilket innebär att dessa säkerhetskontroller sker efter att alla noder i uppgraderingsdomänen har uppgraderats. All denna information pekar på ett potentiellt problem med den nya versionen av programkoden. De vanligaste problemen är servicefel i öppna eller befordra till primära kodsökvägar.

En *UpgradePhase* av *PreUpgradeSafetyCheck* innebär att det fanns problem med att förbereda uppgraderingsdomänen innan den utfördes. De vanligaste problemen i det här fallet är servicefel i stängning eller degradering från primära kodsökvägar.

Den aktuella **UpgradeState** är *RollingBackCompleted*, så den ursprungliga uppgraderingen måste ha utförts med en återställning **FailureAction**, som automatiskt rullade tillbaka uppgraderingen vid fel. Om den ursprungliga uppgraderingen utfördes med en manuell **FailureAction**, skulle uppgraderingen i stället vara i ett pausat tillstånd för att tillåta live felsökning av programmet.

I sällsynta fall kan fältet **UpgradeDomainProgressAtFailure** vara tomt om den totala uppgraderingen time out precis som systemet slutför allt arbete för den aktuella uppgraderingsdomänen. Om detta inträffar kan du prova att öka parametervärdena **för UpgradeTimeout** och **UpgradeDomainTimeout** och försöka uppgradera igen.

### <a name="investigate-health-check-failures"></a>Undersöka fel vid hälsokontroll

Hälsokontrollfel kan utlösas av olika problem som kan inträffa när alla noder i en uppgraderingsdomän har uppgraderat och godkänt alla säkerhetskontroller. Utdata efter det här stycket är typiskt för ett uppgraderingsfel på grund av misslyckade hälsokontroller. Fältet **Felvärderingar** fångar en ögonblicksbild av hälsokontroller som misslyckades vid tidpunkten för uppgraderingen enligt den angivna [hälsoprincipen](service-fabric-health-introduction.md).

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
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

Undersöka hälsokontroll fel kräver först en förståelse av Service Fabric hälsomodell. Men även utan en sådan djupgående förståelse kan vi se att två tjänster är ohälsosamma: *tyg:/DemoApp/Svc3* och *tyg:/DemoApp/Svc2*, tillsammans med felhälsorapporterna ("InjectedFault" i det här fallet). I det här exemplet är två av fyra tjänster felaktiga, vilket är lägre än standardmålet på 0 % ohälsosamt (*MaxPercentUnhealthyServices*).

Uppgraderingen avbröts när den misslyckades genom att ange en **FailureAction** av manuell när du startar uppgraderingen. Detta läge gör det möjligt för oss att undersöka live-systemet i feltillstånd innan du vidtar några ytterligare åtgärder.

### <a name="recover-from-a-suspended-upgrade"></a>Återställa från en pausad uppgradering

Med en återställning **FailureAction**behövs ingen återställning eftersom uppgraderingen automatiskt återställs när den misslyckas. Med en manuell **FailureAction**finns det flera återställningsalternativ:

1.  utlösa en återställning
2. Gå igenom resten av uppgraderingen manuellt
3. Återuppta den övervakade uppgraderingen

**Kommandot Start-ServiceFabricApplicationRollback** kan användas när som helst för att starta återställningen av programmet. När kommandot har returnerats har återställningsbegäran registrerats i systemet och startar kort därefter.

**Kommandot Resume-ServiceFabricApplicationUpgrade** kan användas för att gå igenom resten av uppgraderingen manuellt, en uppgraderingsdomän i taget. I det här läget utförs endast säkerhetskontroller av systemet. Inga fler hälsokontroller utförs. Det här kommandot kan bara användas när *UpgradeState* visar *RollingForwardPending*, vilket innebär att den aktuella uppgraderingsdomänen har uppgraderat men nästa har inte startats (väntar).

**Kommandot Update-ServiceFabricApplicationUpgrade** kan användas för att återuppta den övervakade uppgraderingen med både säkerhets- och hälsokontroller som utförs.

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
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
```

Uppgraderingen fortsätter från uppgraderingsdomänen där den senast avbröts och använder samma uppgraderingsparametrar och hälsoprinciper som tidigare. Om det behövs kan någon av uppgraderingsparametrarna och hälsoprinciperna som visas i föregående utdata ändras i samma kommando när uppgraderingen återupptas. I det här exemplet återupptogs uppgraderingen i övervakat läge, med parametrarna och hälsoprinciperna oförändrade.

## <a name="further-troubleshooting"></a>Ytterligare felsökning

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric följer inte de angivna hälsoprinciperna

Möjlig orsak 1:

Service Fabric översätter alla procentsatser till faktiskt antal entiteter (till exempel repliker, partitioner och tjänster) för hälsoutvärdering och avrundar alltid upp till hela entiteter. Om till exempel den maximala *MaxPercentUnhealthyReplicasPerPartition* är 21% och det finns fem repliker, då`Math.Ceiling (5*0.21)`Service Fabric tillåter upp till två ohälsosamma repliker (det vill). Hälsopolitiken bör därför fastställas i enlighet med detta.

Möjlig orsak 2:

Hälsoprinciper anges i procent av de totala tjänsterna och inte specifika tjänstinstanser. Till exempel före en uppgradering, om ett program har fyra tjänstinstanser A, B, C och D, där tjänsten D är fel men med liten inverkan på programmet. Vi vill ignorera den kända felaktiga tjänsten D under uppgraderingen och ställa in parametern *MaxPercentUnhealthyServices* till 25%, förutsatt att endast A, B och C måste vara felfri.

Men under uppgraderingen kan D bli felfri medan C blir ohälsosamt. Uppgraderingen skulle fortfarande lyckas eftersom endast 25% av tjänsterna är ohälsosamma. Det kan dock resultera i oförutsedda fel på grund av att C oväntat är ohälsosamt i stället för D. I det här fallet bör D modelleras som en annan tjänsttyp än A, B och C. Eftersom hälsoprinciper anges per tjänsttyp kan olika felaktiga procenttröskelvärden tillämpas på olika tjänster. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Jag har inte angett en hälsoprincip för programuppgradering, men uppgraderingen misslyckas fortfarande för vissa time-outs som jag aldrig angett

När hälsoprinciper inte anges i uppgraderingsbegäran hämtas de från *ApplicationManifest.xml* för den aktuella programversionen. Om du till exempel uppgraderar Program X från version 1.0 till version 2.0 används programhälsoprinciper som anges i version 1.0. Om en annan hälsoprincip ska användas för uppgraderingen måste principen anges som en del av API-anropet för programuppgradering. De principer som anges som en del av API-anropet gäller endast under uppgraderingen. När uppgraderingen är klar används de principer som anges i *ApplicationManifest.xml.*

### <a name="incorrect-time-outs-are-specified"></a>Felaktiga time-outs anges

Du kanske har undrat om vad som händer när time-outs ställs inkonsekvent. Du kan till exempel ha en *UpgradeTimeout* som är mindre än *UpgradeDomainTimeout*. Svaret är att ett fel returneras. Fel returneras om *UpgradeDomainTimeout* är mindre än summan av *HealthCheckWaitDuration* och *HealthCheckRetryTimeout*, eller om *UpgradeDomainTimeout* är mindre än summan av *HealthCheckWaitDuration* och *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Mina uppgraderingar tar för lång tid

Tiden för en uppgradering att slutföra beror på hälsokontroller och time-outs anges. Hälsokontroller och time-out beror på hur lång tid det tar att kopiera, distribuera och stabilisera programmet. Att vara för aggressiv med time-outs kan innebära fler misslyckade uppgraderingar, så vi rekommenderar att du börjar försiktigt med längre time-outs.

Här är en snabb uppdatering om hur time-outs interagerar med uppgraderingstider:

Uppgraderingar för en uppgraderingsdomän kan inte slutföras snabbare än *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Uppgraderingsfel kan inte inträffa snabbare än *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

Uppgraderingstiden för en uppgraderingsdomän begränsas av *UpgradeDomainTimeout*.  Om *HealthCheckRetryTimeout* och *HealthCheckStableDuration* är både icke-noll och hälsotillståndet för programmet fortsätter att växla fram och tillbaka, sedan uppgraderingen så småningom timeout på *UpgradeDomainTimeout*. *UpgradeDomainTimeout* börjar räkna ned när uppgraderingen för den aktuella uppgraderingsdomänen börjar.

## <a name="next-steps"></a>Nästa steg

[Om du uppgraderar programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md) går du igenom en programuppgradering med Visual Studio.

[Om du uppgraderar programmet med Powershell](service-fabric-application-upgrade-tutorial-powershell.md) får du en programuppgradering med PowerShell.

Styr hur programmet uppgraderas med hjälp av [uppgraderingsparametrar](service-fabric-application-upgrade-parameters.md).

Gör dina programuppgraderingar kompatibla genom att lära dig hur du använder [data serialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar programmet genom att referera till [Avancerade ämnen](service-fabric-application-upgrade-advanced.md).
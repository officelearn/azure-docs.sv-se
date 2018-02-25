---
title: 'Uppgradering av programmet: Uppgraderingsparametrar | Microsoft Docs'
description: "Beskriver parametrar som uppgraderar ett Service Fabric-program, inklusive hälsokontroller att utföra och principer för att automatiskt återställa uppgraderingen."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 4dafedb3fef821cd2562cf47937d034fdc59d92e
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="application-upgrade-parameters"></a>Programuppgraderingsparametrar
Den här artikeln beskriver de olika parametrar som gäller under uppgraderingen av ett program i Azure Service Fabric. Parametrarna inkluderar namnet och versionen av programmet. De är rattar som styr timeout och hälsokontroller som tillämpas under uppgraderingen och de anger de principer som måste tillämpas när uppgraderingen misslyckas.

<br>

| Parameter | Beskrivning |
| --- | --- |
| ApplicationName |Namnet på det program som ska uppgraderas. Exempel: fabric: / VisualObjects fabric: / ClusterMonitor |
| TargetApplicationTypeVersion |Versionen av programmet typ som uppgradering mål. |
| FailureAction |Den åtgärd som Service Fabric när uppgraderingen misslyckas. Programmet kan återställas till före uppdateringen version (rollback) eller uppgraderingen kan stoppas när den aktuella uppgraderingsdomänen. I det senare fallet ändras även uppgraderingsläget till manuell. Tillåtna värden är återställning och manuell. |
| HealthCheckWaitDurationSec |Väntetiden (i sekunder) när uppgraderingen har slutförts på uppgraderingsdomänen innan Service Fabric utvärderar hälsotillståndet för programmet. Varaktigheten kan också ses som den tid som ett program ska köras innan den kan betraktas som felfritt. Om hälsotillståndskontroll klarat, fortsätter uppgraderingen till med nästa uppgraderingsdomän.  Om hälsokontrollen misslyckas, väntar Service Fabric på ett intervall (UpgradeHealthCheckInterval) innan du försöker hälsotillståndskontroll igen tills HealthCheckRetryTimeout har uppnåtts. Standard och rekommenderat värde är 0 sekunder. |
| HealthCheckRetryTimeoutSec |Varaktigheten (i sekunder) fortsätter som Service Fabric att utföra hälsoutvärderingen innan du deklarerar uppgraderingen som misslyckades. Standardvärdet är 600 sekunder. Varaktigheten startar när HealthCheckWaitDuration har nåtts. I den här HealthCheckRetryTimeout kan Service Fabric utföra flera hälsokontroller av programmets hälsotillstånd. Standardvärdet är 10 minuter och bör anpassas på lämpligt sätt för programmet. |
| HealthCheckStableDurationSec |Varaktigheten (i sekunder) kontrollera att programmet är stabil innan du flyttar till nästa uppgraderingsdomän eller uppgraderingen är klar. Vänta varaktigheten används för att förhindra oupptäckt ändringar av hälsa rätt när hälsotillståndskontroll utförs. Standardvärdet är 120 sekunder och bör anpassas på lämpligt sätt för ditt program. |
| UpgradeDomainTimeoutSec |Längsta tid (i sekunder) för att uppgradera en uppgradering domän. Om den här timeout uppnås uppgraderingen stoppar och fortsätter baserat på inställningen för UpgradeFailureAction. Standardvärdet är aldrig (obegränsat) och bör anpassas på lämpligt sätt för programmet. |
| UpgradeTimeout |En timeout (i sekunder) som gäller för hela uppgraderingen. Om den här timeout har uppnåtts utlöses uppgraderingen avbryts och UpgradeFailureAction. Standardvärdet är aldrig (obegränsat) och bör anpassas på lämpligt sätt för programmet. |
| UpgradeHealthCheckInterval |Frekvensen att hälsostatus är markerad. Den här parametern anges i avsnittet ClusterManager i den *klustret* *manifest*, och inte har angetts som en del av uppgraderingen cmdlet. Standardvärdet är 60 sekunder. |

<br>

## <a name="service-health-evaluation-during-application-upgrade"></a>Tjänsten hälsoutvärderingen under uppgradering av programmet
<br>
Utvärderingskriterierna hälsa är valfria. Om utvärderingskriterierna hälsa inte anges när en uppgradering startar, använder Service Fabric programmet hälsoprinciper som anges i ApplicationManifest.xml på programinstansen.

<br>

| Parameter | Beskrivning |
| --- | --- |
| ConsiderWarningAsError |Standardvärdet är False. Behandla varningshändelser för hälsotillståndet för programmet som fel vid utvärdering av hälsotillståndet för programmet under uppgraderingen. Som standard utvärderas inte hälsa varningshändelser för att vara fel (fel), så att uppgraderingen kan fortsätta även om det finns händelser för Service Fabric. |
| MaxPercentUnhealthyDeployedApplications |Rekommenderade standardvärdet är 0. Ange det maximala antalet distribuerade program (se den [hälsa avsnittet](service-fabric-health-introduction.md)) som kan vara felaktiga innan programmet anses vara felaktig och misslyckas uppgraderingen. Den här parametern definierar programmets hälsotillstånd på noden och hjälper dig identifiera problem under uppgraderingen. Normalt hämta repliker av programmet belastningsutjämnade till den andra noden, vilket gör att programmet ska visas felfri, vilket ger uppgraderingen kan fortsätta. Genom att ange en strikt MaxPercentUnhealthyDeployedApplications health Service Fabric identifiera problem med programpaketet snabbt och kunna skapa en snabb uppgraderingen misslyckas. |
| MaxPercentUnhealthyServices |Rekommenderade standardvärdet är 0. Ange det maximala antalet tjänster i programinstansen som kan vara felaktiga innan programmet anses vara felaktig och misslyckas uppgraderingen. |
| MaxPercentUnhealthyPartitionsPerService |Rekommenderade standardvärdet är 0. Ange det maximala antalet partitioner i en tjänst som kan vara felaktiga innan tjänsten anses vara felaktig. |
| MaxPercentUnhealthyReplicasPerPartition |Rekommenderade standardvärdet är 0. Ange det maximala antalet repliker i partitionen kan vara felaktiga innan partitionen anses vara felaktig. |
| UpgradeReplicaSetCheckTimeout |<p>**Tillståndslösa tjänsten**--inom en enskild uppgraderingsdomänen Service Fabric försöker att se till att det finns ytterligare instanser av tjänsten. Om mål-instanser är mer än ett väntar Service Fabric på mer än en instans ska vara tillgängligt, upp till ett högsta timeout-värde. Timeout har angetts med egenskapen UpgradeReplicaSetCheckTimeout. Om tidsgränsen överskrids, fortsätter Service Fabric med uppgraderingen, oavsett antalet instanser av tjänsten. Om mål-instanser är en, Service Fabric väntar inte och omedelbart fortsätter med uppgraderingen.</p><p>**Tillståndskänslig service**--inom en enskild uppgraderingsdomänen Service Fabric försöker att kontrollera att repliken har ett kvorum. Service Fabric väntar på att ett kvorum ska vara tillgängligt, upp till ett högsta timeout-värde (som anges av egenskapen UpgradeReplicaSetCheckTimeout). Om tidsgränsen överskrids, fortsätter Service Fabric med uppgraderingen, oavsett kvorum. Den här inställningen är uppsättningen som aldrig (oändligt) när du återställer och 1200 sekunder när du återställer.</p> |
| ForceRestart |Om du uppdaterar en konfiguration eller datapaketet utan att uppdatera kod som tjänsten startas om egenskapen ForceRestart har angetts till true. När uppdateringen är klar, meddelar Service Fabric tjänsten och att en ny konfigurationspaket eller datapaketet är tillgänglig. Tjänsten ansvarar för att tillämpa ändringarna. Om det behövs tjänsten kan starta om sig själv. |

<br>
<br>
MaxPercentUnhealthyServices och MaxPercentUnhealthyPartitionsPerService MaxPercentUnhealthyReplicasPerPartition villkor kan anges per service-typen för en instans av programmet. Ange dessa parametrar per-tjänsten tillåter för ett program som innehåller olika tjänster typer med principer för olika utvärdering. En typ av tillståndslösa gateway kan till exempel ha en MaxPercentUnhealthyPartitionsPerService som skiljer sig från en tillståndskänslig engine service-typen för en programinstans.

## <a name="next-steps"></a>Nästa steg
[Uppgradera ditt program med hjälp av Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med hjälp av Visual Studio.

[Uppgradera ditt program med hjälp av Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.

[Uppgradera ditt program med hjälp av Service Fabric CLI på Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) vägleder dig genom en uppgradering av programmet med hjälp av Service Fabric CLI.

[Uppgradera ditt program med hjälp av Service Fabric Eclipse plugin-program](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Gör din programuppgraderingar kompatibla genom att lära dig hur du använder [dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att referera till [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Lösa vanliga problem i programuppgraderingar genom att referera till stegen i [felsökning programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

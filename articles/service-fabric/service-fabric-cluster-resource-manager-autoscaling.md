---
title: Azure Service Fabric automatisk skalning tjänster och behållare | Microsoft Docs
description: Azure Service Fabric kan du ställa in automatisk skalning principer för tjänster och behållare.
services: service-fabric
documentationcenter: .net
author: radicmilos
manager: ''
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2018
ms.author: miradic,nipuzovi
ms.openlocfilehash: 741d77998e9f3405ca6d3863e80cb9125705eefe
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="introduction-to-auto-scaling"></a>Introduktion till automatisk skalning
Automatisk skalning är en ytterligare funktion för Service Fabric att dynamiskt skala dina tjänster utifrån belastningen som tjänster rapporterar eller baserat på deras användning av resurser. Automatisk skalning ger bra elasticitet och gör etablering av ytterligare instanser eller partitioner för tjänsten på begäran. När du har konfigurerat dina principer för en tjänst för det behövs ingen manuell skalning åtgärder på tjänstnivå hela automatisk skalning processen är automatiskt och transparent. Automatisk skalning kan aktiveras antingen vid tidpunkten för skapandet av tjänsten, eller när som helst genom att uppdatera tjänsten.

Ett vanligt scenario där automatisk skalning är användbar är när belastningen på en viss tjänst varierar över tid. Till exempel baserat en tjänst som en gateway kan skala på mängden resurser som krävs för att hantera inkommande begäranden. Låt oss ta en titt på ett exempel på hur reglerna skalning gick ut:
* Om alla instanser av min gateway använder mer än två kärnor i genomsnitt, skala du gatewaytjänsten ut genom att lägga till en mer instans. Göra varje timme, men har aldrig mer än sju instanser totalt.
* Om alla instanser av min gateway använder mindre än 0,5 kärnor i genomsnitt, skala du tjänsten i genom att ta bort en instans. Göra varje timme, men aldrig har färre än tre instanser totalt.

Automatisk skalning har stöd för både behållare och vanliga Service Fabric-tjänster. Resten av den här artikeln beskriver skalning principerna sätt att aktivera eller inaktivera automatisk skalning och ger exempel på hur du använder den här funktionen.

## <a name="describing-auto-scaling"></a>Om automatisk skalning
Automatisk skalning principer kan definieras för varje tjänst i ett Service Fabric-kluster. Varje princip för skalning består av två delar:
* **Skalning utlösaren** beskriver när skalning av tjänsten kommer att utföras. Villkoren som är definierade i utlösaren kontrolleras regelbundet för att avgöra om en tjänst ska skalas.

* **Skalning mekanism** beskriver hur skalning ska utföras när den utlöses. Mekanism används bara om de från utlösarens villkor.

Alla utlösare som stöds för närvarande fungerar antingen med [logiska belastningen mått](service-fabric-cluster-resource-manager-metrics.md), eller med fysiska mått som förbrukning av CPU eller minne. Oavsett hur Service Fabric övervakas rapporterade belastningen på måttet och utvärderas utlösaren med jämna mellanrum för att avgöra om skalning krävs.

Det finns två metoder som stöds för närvarande för automatisk skalning. Den första som är avsedd för tillståndslösa tjänster eller för behållare där automatisk skalning utförs genom att lägga till eller ta bort [instanser](service-fabric-concepts-replica-lifecycle.md). För både tillståndskänsliga och tillståndslösa tjänster Autoskala kan också utföras genom att lägga till eller ta bort namngivna [partitioner](service-fabric-concepts-partitioning.md) av tjänsten.

> [!NOTE]
> Det finns för närvarande stöd för endast en skalning princip per tjänst.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Genomsnittlig partition belastningen utlösare med instanser baserade skalning
Den första typen av utlösare baseras på inläsningen av instanser i en partition tillståndslösa tjänsten. Mått belastningar utjämnas först för att erhålla belastningen för varje instans av en partition och sedan dessa värden är ett genomsnitt över alla instanser av partitionen. Det finns tre faktorer som bestämmer när tjänsten skalas:

* _Lägre tröskelvärde_ är ett värde som anger när tjänsten ska vara **skalas i**. Om genomsnittlig belastning över alla instanser av partitionerna är lägre än det här värdet kommer tjänsten att skala i.
* _Övre tröskelvärde_ är ett värde som anger när tjänsten ska vara **utskalad**. Om genomsnittlig belastning över alla instanser av partitionen är lägre än det här värdet kommer tjänsten att skala ut.
* _Skalning intervall_ avgör hur ofta utlösaren ska kontrolleras. När utlösaren kontrolleras om skalning krävs mekanismen tillämpas. Om skalning inte krävs vidtas ingen åtgärd. I båda fallen kontrolleras utlösaren inte igen innan skalning intervallet har gått ut igen.

Den här utlösaren kan användas med tillståndslösa tjänster (tillståndslös behållare eller Service Fabric-tjänster). När en tjänst har flera partitioner, utlösaren ska utvärderas för varje partition separat, och varje partition har angivna mekanismen tillämpas oberoende av varandra. Då är det därför möjligt att vissa av partitionerna för tjänsten att skala ut några skalas i och vissa inte skalas alls samtidigt, baserat på deras belastningen.

Den enda funktionen som kan användas med den här utlösaren är PartitionInstanceCountScaleMechanism. Det finns tre faktorer som bestämmer hur den här mekanismen tillämpas:
* _Skala ökning_ avgör hur många instanser ska läggas till eller tas bort när mekanism utlöses.
* _Maximalt antal instanser_ definierar den övre gränsen för skalning. Om antalet instanser av partitionen når den här gränsen, kommer sedan tjänsten inte att skala ut, oavsett belastningen. Det är möjligt att utelämna den här gränsen genom att ange värdet -1 och att fallet tjänsten skalas ut så mycket som möjligt (gränsen är antalet noder som är tillgängliga i klustret).
* _Minimiantal instanser_ definierar den nedre gränsen för skalning. Om antalet instanser av partitionen når den här gränsen, sedan skalas tjänsten inte i oavsett belastningen.

## <a name="setting-auto-scaling-policy"></a>Ställa in automatisk skalning princip

### <a name="using-application-manifest"></a>Med hjälp av programmanifestet
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Med hjälp av C#-API: er
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>Med hjälp av Powershell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Genomsnittlig service belastningen utlösare med partition baserat skalning
Andra utlösaren utifrån belastningen på alla partitioner i en tjänst. Mått belastningar utjämnas först för att erhålla belastningen för varje replik eller en instans av en partition. För tillståndskänsliga tjänster belastningen på partitionen betraktas som belastningen på den primära repliken, även för tillståndslösa tjänster är belastningen på partitionen genomsnittlig belastning över alla instanser av partitionen. Dessa värden var i genomsnitt för alla partitioner för tjänsten och det här värdet används för att utlösa automatisk skalning. Samma som i föregående mekanism, det finns tre faktorer som styr när tjänsten skalas:

* _Lägre tröskelvärde_ är ett värde som anger när tjänsten ska vara **skalas i**. Om genomsnittlig belastning för alla partitioner för tjänsten är lägre än det här värdet kommer tjänsten att skala i.
* _Övre tröskelvärde_ är ett värde som anger när tjänsten ska vara **utskalad**. Om genomsnittlig belastning för alla partitioner för tjänsten är lägre än det här värdet kommer tjänsten att skala ut.
* _Skalning intervall_ avgör hur ofta utlösaren ska kontrolleras. När utlösaren kontrolleras om skalning krävs mekanismen tillämpas. Om skalning inte krävs vidtas ingen åtgärd. I båda fallen kontrolleras utlösaren inte igen innan skalning intervallet har gått ut igen.

Den här utlösaren kan vara användas både med tillståndskänsliga och tillståndslösa tjänster. Den enda funktionen som kan användas med den här utlösaren är AddRemoveIncrementalNamedParitionScalingMechanism. När tjänsten skalas ut och sedan en ny partition har lagts till, och när tjänsten skalas i någon av befintliga partitioner tas bort. Det finns begränsningar som ska kontrolleras när tjänsten skapas eller uppdateras och tjänsten skapa/uppdatera misslyckas om dessa villkor inte uppfylls:
* Namngivna partitionsschema måste användas för tjänsten.
* Partitionsnamnen måste vara i följd heltal som ”0”, ”1”,...
* Första partitionsnamnet måste vara ”0”.

Till exempel är en tjänst skapas med tre partitioner endast giltigt möjligheten för partitionsnamnen ”0”, ”1” och ”2”.

Den här namngivningsschemat kommer värnar om faktiska automatisk skalning som utförs:
* Om aktuella partitioner för tjänsten kallas ”0”, ”1” och ”2”, sedan namnet den partition som ska läggas till för att skala ut ”3”.
* Om aktuella partitioner för tjänsten kallas ”0”, ”1” och ”2”, är den partition som kommer att tas bort för att skala i partitionen med namnet ”2”.

Samma som mekanism som använder skalning genom att lägga till eller ta bort instanser, det finns tre parametrar som bestämmer hur den här mekanismen tillämpas:
* _Skala ökning_ avgör hur många partitioner ska läggas till eller tas bort när mekanism utlöses.
* _Högsta antal partitioner_ definierar den övre gränsen för skalning. Om antalet partitioner för tjänsten når den här gränsen, kommer sedan tjänsten inte att skala ut, oavsett belastningen. Det är möjligt att utelämna den här gränsen genom att ange värdet -1 och att fallet tjänsten skalas ut så mycket som möjligt (gränsen är den faktiska kapaciteten i klustret).
* _Minimiantal instanser_ definierar den nedre gränsen för skalning. Om antalet partitioner för tjänsten når den här gränsen, sedan skalas tjänsten inte i oavsett belastningen.

## <a name="setting-auto-scaling-policy"></a>Ställa in automatisk skalning princip

### <a name="using-application-manifest"></a>Med hjälp av programmanifestet
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedParitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Med hjälp av C#-API: er
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedParitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>Med hjälp av Powershell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedParitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>Automatisk skalning baserat på resurser

För att aktivera tjänsten resurs Övervakaren för att skala baserat på faktiska resurser

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Det finns två mått som representerar verkliga fysiska resurser. En av dem är servicefabric: / _CpuCores som representerar den faktiska cpu-användningen (så 0,5 representerar halva en kärna) och det andra som servicefabric: / _MemoryInMB som visar minne i MB.
ResourceMonitorService ansvarar för att spåra processor- och minnesanvändning av användaren tjänster. Den här tjänsten gäller viktat glidande medelvärde för att redovisa tillfällig ökad. Resursövervakning stöds både av och inte av program på Windows och av dem på Linux. Autoskala resurser är bara aktiverad för tjänster som har aktiverats i [exklusiv processmodellen](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [programmet skalbarhet](service-fabric-concepts-scalability.md).
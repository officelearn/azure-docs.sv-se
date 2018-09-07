---
title: Azure Service Fabric automatisk skalning tjänster och behållare | Microsoft Docs
description: Azure Service Fabric kan du ställa in automatisk skalning principer för tjänster och behållare.
services: service-fabric
documentationcenter: .net
author: radicmilos
manager: ''
editor: nipuzovi
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: db4f83d0d407ad3d9e895759ea2a687662f5620a
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053303"
---
# <a name="introduction-to-auto-scaling"></a>Introduktion till automatisk skalning
Automatisk skalning är en ytterligare funktion av Service Fabric dynamiskt skala dina tjänster baserat på den belastning som tjänster rapporterar eller baserat på deras användning av resurser. Automatisk skalning ger bra elasticitet och gör etablering av ytterligare instanser eller partitioner av din tjänst på begäran. Hela processen för autoskalning är automatiserade och transparent och när du har konfigurerat dina principer på en tjänst det finns inget behov av manuell skalningsåtgärder på tjänstnivå. Automatisk skalning kan aktiveras antingen när tjänsten skapas eller när som helst genom att uppdatera tjänsten.

Det är ett vanligt scenario där automatisk skalning är användbart när belastningen på en viss tjänst varierar över tid. Exempelvis kan baserat en tjänst som en gateway kan skala på mängden resurser som behövs för att hantera inkommande begäranden. Låt oss ta en titt på ett exempel på hur dessa skalningsregler kan se ut:
* Om alla instanser av min gateway använder fler än två kärnor i genomsnitt, skala du gateway-tjänsten ut genom att lägga till en mer instans. Gör detta varje timme, men har aldrig mer än sju instanser totalt.
* Om alla instanser av min gateway använder mindre än 0,5 kärnor i genomsnitt kan du sedan skala tjänsten i genom att ta bort en instans. Gör detta varje timme, men aldrig har färre än tre instanser totalt.

Automatisk skalning har stöd för både behållare och regelbundna Service Fabric-tjänster. Resten av den här artikeln beskriver skalningsprinciper sätt att aktivera eller inaktivera automatisk skalning och ger exempel på hur du använder den här funktionen.

## <a name="describing-auto-scaling"></a>Som beskriver automatisk skalning
Automatisk skalning principer kan definieras för varje tjänst i ett Service Fabric-kluster. Varje skalningsprincip består av två delar:
* **Skala utlösaren** beskriver när skalning av tjänsten kommer att utföras. Villkoren som är definierade i utlösaren kontrolleras regelbundet för att avgöra om en tjänst ska skalas eller inte.

* **Skala mekanism** beskriver hur skalning ska utföras när den utlöses. Mekanism tillämpas endast när villkor från utlösaren är uppfyllda.

Alla utlösare som stöds för närvarande fungerar med antingen [logiska inläsningsmåtten](service-fabric-cluster-resource-manager-metrics.md), eller med fysiska mått som CPU eller minne användning. I båda fallen Service Fabric ska övervaka rapporterade belastningen på måttet och utvärderar utlösaren med jämna mellanrum för att avgöra om skalning krävs.

Det finns två metoder som stöds för närvarande för automatisk skalning. Den första som är avsedd för tillståndslösa tjänster eller för behållare där automatisk skalning utförs genom att lägga till eller ta bort [instanser](service-fabric-concepts-replica-lifecycle.md). För både tillståndskänsliga och tillståndslösa tjänster, automatisk skalning kan också utföras genom att lägga till eller tar bort det namngivna [partitioner](service-fabric-concepts-partitioning.md) av tjänsten.

> [!NOTE]
> Det finns för närvarande stöd för endast en skalningsprincip per tjänst.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Genomsnittlig partition belastningen utlösare med instanser baserat skalning
Den första typen av utlösare baseras på belastningen på instanser i en tillståndslös tjänst-partition. Metrisk belastningar jämnas först för att hämta belastningen för varje instans av en partition och sedan dessa värden är i genomsnitt i alla instanser av partitionen. Det finns tre faktorer som bestämmer när tjänsten kommer att skalas:

* _Lägre tröskelvärde_ är ett värde som anger när tjänsten kommer att vara **skalas i**. Om den genomsnittliga belastningen över alla instanser av partitionerna är lägre än det här värdet, kommer tjänsten att skalas i.
* _Övre tröskelvärdet för inläsning_ är ett värde som anger när tjänsten kommer att vara **utskalad**. Om den genomsnittliga belastningen över alla instanser av partitionen är högre än det här värdet, kommer tjänsten att skalas ut.
* _Skalningsintervall_ avgör hur ofta utlösaren ska kontrolleras. När utlösaren kontrolleras om skalning krävs mekanismen används. Om skalning inte krävs vidtas någon åtgärd. I båda fallen kontrolleras utlösaren inte igen innan skalningsintervall går ut igen.

Den här utlösaren kan användas endast med tillståndslösa tjänster (tillståndslös behållare eller Service Fabric-tjänster). Om utlösaren ska utvärderas för varje partition separat när en tjänst har flera partitioner, och varje partition har den angivna mekanism som tillämpas oberoende av varandra. Därför kan då är det möjligt att vissa partitioner av tjänsten kommer att skalas ut några kommer att skalas i och vissa inte skalas på alla på samma gång, baserat på deras belastning.

Den enda funktionen som kan användas med den här utlösaren är PartitionInstanceCountScaleMechanism. Det finns tre faktorer som avgör hur den här mekanismen används:
* _Skala ökningen_ avgör hur många instanser ska läggas till eller tas bort när mekanism utlöses.
* _Maximalt antal instanser_ definierar den övre gränsen för skalning. Om antalet instanser av partitionen når den här gränsen kan sedan skalas tjänsten inte ut, oavsett belastningen. Det är möjligt att utelämna den här gränsen genom att ange värdet -1 och på så sätt att fallet tjänsten kommer att skalas ut så mycket som möjligt (gränsen är antalet noder som är tillgängliga i klustret).
* _Minimiantal instanser_ definierar den nedre gränsen för skalning. Om antalet instanser av partitionen når den här gränsen kan sedan skalas tjänsten inte i oavsett belastningen.

## <a name="setting-auto-scaling-policy"></a>Ange princip för autoskalning

### <a name="using-application-manifest"></a>Med hjälp av programmanifest
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
### <a name="using-c-apis"></a>Med hjälp av C# API: er
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

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Genomsnittlig tjänsten load utlösare med baserat partitionsskalning
Andra utlösare baseras på belastningen på alla partitioner i en tjänst. Metrisk belastningar jämnas först för att hämta belastningen för varje replik eller en instans av en partition. För tillståndskänsliga tjänster anses belastningen på partitionen vara belastningen på den primära repliken, även för tillståndslösa tjänster belastningen på partitionen är den genomsnittliga belastningen över alla instanser av partitionen. Dessa värden är genomsnitt för alla partitioner i tjänsten, och det här värdet används för att utlösa automatisk skalning. Samma som i föregående mekanism, det finns tre faktorer som styr när tjänsten kommer att skalas:

* _Lägre tröskelvärde_ är ett värde som anger när tjänsten kommer att vara **skalas i**. Om den genomsnittliga belastningen för alla partitioner i tjänsten är lägre än det här värdet, kommer tjänsten att skalas i.
* _Övre tröskelvärdet för inläsning_ är ett värde som anger när tjänsten kommer att vara **utskalad**. Om den genomsnittliga belastningen för alla partitioner i tjänsten är högre än det här värdet, kommer tjänsten att skalas ut.
* _Skalningsintervall_ avgör hur ofta utlösaren ska kontrolleras. När utlösaren kontrolleras om skalning krävs mekanismen används. Om skalning inte krävs vidtas någon åtgärd. I båda fallen kontrolleras utlösaren inte igen innan skalningsintervall går ut igen.

Den här utlösaren kan vara används för både med tillståndskänsliga och tillståndslösa tjänster. Den enda funktionen som kan användas med den här utlösaren är AddRemoveIncrementalNamedParitionScalingMechanism. När tjänsten har skalats ut och sedan en ny partition har lagts till och när tjänsten skalas i ett av befintliga partitioner tas bort. Det finns begränsningar som ska kontrolleras när tjänsten skapas eller uppdateras och skapande/uppdatering av tjänsten kommer att misslyckas om dessa villkor inte uppfylls:
* Namngivna partitionsschema måste användas för tjänsten.
* Partitionsnamnen måste vara i följd heltal som ”0”, ”1”,...
* Första partitionsnamnet måste vara ”0”.

Till exempel är en tjänst skapas med tre partitioner den enda giltiga möjligheten för partitionsnamn ”0”, ”1” och ”2”.

Den faktiska automatisk skalning som utförs anpassas efter den här namngivningsschemat:
* Om aktuella partitioner av tjänsten kallas ”0”, ”1” och ”2”, sedan namnet den partition som kommer att läggas till för att skala ut ”3”.
* Om aktuella partitioner av tjänsten kallas ”0”, ”1” och ”2”, är den partition som kommer att tas bort för att skala i partitionen med namnet ”2”.

Samma som med mekanism som använder skalning genom att lägga till eller ta bort instanser, det finns tre parametrarna som avgör hur den här mekanismen används:
* _Skala ökningen_ avgör hur många partitioner ska läggas till eller tas bort när mekanism utlöses.
* _Högsta antal partitioner_ definierar den övre gränsen för skalning. Om antalet partitioner i tjänsten når den här gränsen kan sedan skalas tjänsten inte ut, oavsett belastningen. Det är möjligt att utelämna den här gränsen genom att ange värdet -1 och på så sätt att fallet tjänsten kommer att skalas ut så mycket som möjligt (gränsen är faktiska kapaciteten för klustret).
* _Minimiantal instanser_ definierar den nedre gränsen för skalning. Om antalet partitioner i tjänsten når den här gränsen kan sedan skalas tjänsten inte i oavsett belastningen.

> [!WARNING] 
> När AddRemoveIncrementalNamedParitionScalingMechanism används med tillståndskänsliga tjänster, Service Fabric lägger du till eller ta bort partitioner **utan avisering eller varning**. Ompartitionering av data utförs inte när skalning mekanism utlöses. Om för att skala upp igen, nya partitioner är tom och när det gäller skala ned igen, **partition kommer att tas bort tillsammans med alla data som den innehåller**.

## <a name="setting-auto-scaling-policy"></a>Ange princip för autoskalning

### <a name="using-application-manifest"></a>Med hjälp av programmanifest
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedParitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Med hjälp av C# API: er
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

För att aktivera resource monitor-tjänsten att skala baserat på verkliga resurser

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Det finns två mått som representerar verkliga fysiska resurser. En av dem är Service fabric: / _CpuCores som motsvarar den faktiska cpu-användning (så 0,5 representerar en halv kärna) och det andra är Service fabric: / _MemoryInMB som representerar minnesanvändning i MB.
ResourceMonitorService ansvarar för att spåra cpu och minne användning av tjänster. Den här tjänsten tillämpar viktat glidande medelvärde för att hänsyn till eventuella tillfällig toppar. Resursövervakning stöds både behållare och icke-behållarbaserade program på Windows och för behållare som i Linux. Autoskala resurser är bara aktiverad för tjänster aktiveras i [exklusiv processmodell](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Nästa steg
Läs mer om [program skalbarhet](service-fabric-concepts-scalability.md).

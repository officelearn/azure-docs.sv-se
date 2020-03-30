---
title: Azure Service Fabric Automatisk skalning tjänster och behållare
description: Med Azure Service Fabric kan du ange principer för automatisk skalning för tjänster och behållare.
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: 3660ece7add8f279292340aae9ab445b682fe045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452090"
---
# <a name="introduction-to-auto-scaling"></a>Introduktion till automatisk skalning
Automatisk skalning är en ytterligare funktion för Service Fabric för att dynamiskt skala dina tjänster baserat på den belastning som tjänsterna rapporterar eller baserat på deras användning av resurser. Automatisk skalning ger stor elasticitet och möjliggör etablering av ytterligare instanser eller partitioner av tjänsten på begäran. Hela den automatiska skalningsprocessen är automatiserad och transparent, och när du har ställt in dina principer för en tjänst finns det inget behov av manuell skalning på servicenivå. Automatisk skalning kan aktiveras antingen vid skapande av tjänster eller när som helst genom att uppdatera tjänsten.

Ett vanligt scenario där automatisk skalning är användbart är när belastningen på en viss tjänst varierar över tiden. En tjänst som en gateway kan till exempel skalas baserat på mängden resurser som krävs för att hantera inkommande begäranden. Låt oss ta en titt på ett exempel på hur dessa skalningsregler kan se ut:
* Om alla instanser av min gateway använder mer än två kärnor i genomsnitt skalar du gateway-tjänsten genom att lägga till ytterligare en instans. Gör detta varje timme, men aldrig har mer än sju fall totalt.
* Om alla instanser av min gateway använder mindre än 0,5 kärnor i genomsnitt skalar du tjänsten genom att ta bort en instans. Gör detta varje timme, men aldrig har färre än tre fall totalt.

Automatisk skalning stöds för både behållare och vanliga servicetjänster för serviceinfrastruktur. För att kunna använda automatisk skalning måste du köras på version 6.2 eller högre av Service Fabric-körningen. 

I resten av den här artikeln beskrivs skalningsprinciper, sätt att aktivera eller inaktivera automatisk skalning och exempel på hur du använder den här funktionen.

## <a name="describing-auto-scaling"></a>Beskriva automatisk skalning
Principer för automatisk skalning kan definieras för varje tjänst i ett Service Fabric-kluster. Varje skalningsprincip består av två delar:
* **Skalningsutlösaren** beskriver när skalning av tjänsten ska utföras. Villkor som definieras i utlösaren kontrolleras regelbundet för att avgöra om en tjänst ska skalas eller inte.

* **Skalningsmekanism** beskriver hur skalning ska utföras när den utlöses. Mekanismen tillämpas endast när villkoren från utlösaren är uppfyllda.

Alla utlösare som för närvarande stöds fungerar antingen med [logiska inläsningsmått](service-fabric-cluster-resource-manager-metrics.md)eller med fysiska mått som CPU eller minnesanvändning. Hur som helst övervakar Service Fabric den rapporterade belastningen för måttet och utvärderar utlösaren regelbundet för att avgöra om skalning behövs.

Det finns två mekanismer som för närvarande stöds för automatisk skalning. Den första är avsedd för tillståndslösa tjänster eller för behållare där automatisk skalning utförs genom att lägga till eller ta bort [instanser](service-fabric-concepts-replica-lifecycle.md). För både tillståndskänsliga och tillståndslösa tjänster kan automatisk skalning också utföras genom att lägga till eller ta bort namngivna [partitioner](service-fabric-concepts-partitioning.md) för tjänsten.

> [!NOTE]
> För närvarande finns det stöd för endast en skalningsprincip per tjänst och endast en skalningsutlösare per skalningsprincip.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Genomsnittlig partitionsbelastningsutlösare med instansbaserad skalning
Den första typen av utlösare baseras på belastningen av instanser i en tillståndslös tjänstpartition. Måttbelastningar utjämnas först för att hämta belastningen för varje instans av en partition, och sedan beräknas dessa värden i genomsnitt över alla instanser av partitionen. Det finns tre faktorer som avgör när tjänsten ska skalas:

* _Lägre inläsningströskel_ är ett värde som avgör när tjänsten ska **skalas i**. Om den genomsnittliga belastningen för alla instanser av partitionerna är lägre än det här värdet skalas tjänsten in.
* _Övre inläsningströskel_ är ett värde som avgör när tjänsten ska **skalas ut**. Om den genomsnittliga belastningen för alla instanser av partitionen är högre än det här värdet skalas tjänsten ut.
* _Skalningsintervallet_ avgör hur ofta utlösaren ska kontrolleras. När utlösaren har markerats, om skalning behövs, kommer mekanismen att tillämpas. Om skalning inte behövs, kommer ingen åtgärd att vidtas. I båda fallen kontrolleras inte utlösaren igen innan skalningsintervallet går ut igen.

Den här utlösaren kan endast användas med tillståndslösa tjänster (antingen tillståndslösa behållare eller Service Fabric-tjänster). Om en tjänst har flera partitioner utvärderas utlösaren för varje partition separat och varje partition kommer att ha den angivna mekanismen tillämpas på den oberoende av varandra. Därför är det i det här fallet möjligt att vissa av partitionerna i tjänsten kommer att skalas ut, vissa kommer att skalas in, och vissa kommer inte att skalas alls samtidigt, baserat på deras belastning.

Den enda mekanism som kan användas med den här utlösaren är PartitionInstanceCountScaleMechanism. Det finns tre faktorer som avgör hur denna mekanism tillämpas:
* _Skala steg avgör_ hur många instanser som ska läggas till eller tas bort när mekanismen utlöses.
* _Maximalt antal instanser_ definierar den övre gränsen för skalning. Om antalet instanser av partitionen når den här gränsen skalas inte tjänsten ut, oavsett belastningen. Det är möjligt att utelämna den här gränsen genom att ange värdet -1, och i så fall skalas tjänsten ut så mycket som möjligt (gränsen är antalet noder som är tillgängliga i klustret).
* _Minsta antal instans_ definierar den nedre gränsen för skalning. Om antalet instanser av partitionen når den här gränsen skalas inte tjänsten in oavsett belastningen.

## <a name="setting-auto-scaling-policy"></a>Ange princip för automatisk skalning

### <a name="using-application-manifest"></a>Använda programmanifest
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
### <a name="using-c-apis"></a>Använda C# API:er
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
### <a name="using-powershell"></a>Använda Powershell
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

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Genomsnittlig tjänstbelastningsutlösare med partitionsbaserad skalning
Den andra utlösaren baseras på belastningen av alla partitioner för en tjänst. Måttbelastningar utjämnas först för att hämta belastningen för varje replik eller instans av en partition. För tillståndskänsliga tjänster anses inläsningen av partitionen vara belastningen på den primära repliken, medan belastningen för partitionen för tillståndslösa tjänster är den genomsnittliga belastningen för alla instanser av partitionen. Dessa värden är medelvärden i alla partitioner i tjänsten och det här värdet används för att utlösa den automatiska skalningen. Samma som i föregående mekanism finns det tre faktorer som avgör när tjänsten ska skalas:

* _Lägre inläsningströskel_ är ett värde som avgör när tjänsten ska **skalas i**. Om den genomsnittliga belastningen för alla partitioner i tjänsten är lägre än det här värdet skalas tjänsten in.
* _Övre inläsningströskel_ är ett värde som avgör när tjänsten ska **skalas ut**. Om den genomsnittliga belastningen för alla partitioner i tjänsten är högre än det här värdet skalas tjänsten ut.
* _Skalningsintervallet_ avgör hur ofta utlösaren ska kontrolleras. När utlösaren har markerats, om skalning behövs, kommer mekanismen att tillämpas. Om skalning inte behövs, kommer ingen åtgärd att vidtas. I båda fallen kontrolleras inte utlösaren igen innan skalningsintervallet går ut igen.

Den här utlösaren kan användas både med tillståndskänsliga och tillståndslösa tjänster. Den enda mekanism som kan användas med den här utlösaren är AddRemoveIncrementalNamedPartitionScalingMechanism. När tjänsten skalas ut läggs en ny partition till och när tjänsten skalas i en av befintliga partitioner tas bort. Det finns begränsningar som kommer att kontrolleras när tjänsten skapas eller uppdateras och skapandet/uppdateringen av tjänsten misslyckas om dessa villkor inte uppfylls:
* Namngivet partitionsschema måste användas för tjänsten.
* Partitionens namn måste vara på varandra följande heltalsnummer, som "0", "1", ...
* Förpartitionens namn måste vara "0".

Om en tjänst till exempel skapas med tre partitioner är den enda giltiga möjligheten för partitionsnamn "0", "1" och "2".

Den faktiska automatiska skalningsåtgärden som utförs respekterar även det här namngivningsschemat:
* Om aktuella partitioner av tjänsten heter "0", "1" och "2", kommer partitionen som kommer att läggas till för skalning ut att heta "3".
* Om aktuella partitioner av tjänsten heter "0", "1" och "2", då partitionen som kommer att tas bort för skalning i är partition med namnet "2".

På samma sätt som med mekanism som använder skalning genom att lägga till eller ta bort instanser finns det tre parametrar som avgör hur den här mekanismen används:
* _Skala steg avgör_ hur många partitioner som ska läggas till eller tas bort när mekanismen utlöses.
* _Maximalt antal partitioner_ definierar den övre gränsen för skalning. Om antalet partitioner för tjänsten når den här gränsen skalas inte tjänsten ut, oavsett belastningen. Det är möjligt att utelämna denna gräns genom att ange värdet -1, och i så fall ska tjänsten skalas ut så mycket som möjligt (gränsen är klustrets faktiska kapacitet).
* _Minsta antal instans_ definierar den nedre gränsen för skalning. Om antalet partitioner för tjänsten når den här gränsen skalas inte tjänsten in oavsett belastning.

> [!WARNING] 
> När AddRemoveIncrementalNamedPartitionScalingMechanism används med tillståndskänsliga tjänster, kommer Service Fabric att lägga till eller ta bort partitioner **utan anmälan eller varning**. Omfördelning av data utförs inte när skalningsmekanism utlöses. Vid uppskalningsåtgärd kommer nya partitioner att vara tomma, och vid **nedskalningsåtgärd tas partitionen bort tillsammans med alla data som den innehåller**.

## <a name="setting-auto-scaling-policy"></a>Ange princip för automatisk skalning

### <a name="using-application-manifest"></a>Använda programmanifest
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Använda C# API:er
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
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
### <a name="using-powershell"></a>Använda Powershell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
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

För att resursövervakartjänsten ska kunna skalas baserat på faktiska resurser

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Det finns två mått som representerar faktiska fysiska resurser. En av dem är servicefabric:/_CpuCores som representerar den faktiska cpu-användningen (så 0,5 representerar en halv kärna) och den andra är servicefabric:/_MemoryInMB som representerar minnesanvändningen i MB.
ResourceMonitorService ansvarar för att spåra cpu och minnesanvändning av användartjänster. Den här tjänsten kommer att tillämpa viktat glidande medelvärde för att ta hänsyn till potentiella kortlivade toppar. Resursövervakning stöds för både behållarprogram och icke-behållare i Windows och för behållarprogram på Linux. Automatisk skalning av resurser är endast aktiverad för tjänster som aktiveras i [exklusiv processmodell](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Nästa steg
Läs mer om [programmets skalbarhet](service-fabric-concepts-scalability.md).

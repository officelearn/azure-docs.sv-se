---
title: 'Service Fabric klustret Resource Manager: flytt kostnad | Microsoft Docs'
description: Översikt över förflyttningskostnad för Service Fabric-tjänster
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 74b61967a796fca22ab86918235f1def27a22f91
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="service-movement-cost"></a>Tjänsten förflyttningskostnad
En faktor som tar hänsyn till Service Fabric klustret Resource Manager när försök att fastställa vilka ändringar du gör i ett kluster är kostnaden för dessa ändringar. Begreppet ”kostnad” säljs ut mot hur mycket klustret kan förbättras. Kostnaden är inberäknade vid flytt av tjänster för belastningsutjämning, defragmentering och andra krav. Målet är att uppfylla kraven på minst störande och dyr sätt. 

Flytta services kostnader CPU-tid och nätverksbandbredd på minst. För tillståndskänsliga tjänster kräver kopiera tillståndet för de tjänster som förbrukar mer minne och diskutrymme. Minimerar kostnaden för lösningar som Azure Service Fabric klustret Resource Manager har säkerställer att klustrets resurser inte har använt för att i onödan. Men vill du dessutom inte att ignorera lösningar som skulle förbättrar allokeringen av resurser i klustret.

Klustret Resource Manager har två sätt att beräkna kostnader och begränsa dem medan den försöker hantera klustret. Den första mekanismen är helt enkelt inventering varje gång den blir. Om två lösningar skapas med om samma balansera (poäng), och sedan klustret Resource Manager föredrar en med lägst kostnad (totalt antal flyttar).

Den här metoden fungerar bra. Men som standard eller statiska laster, är det osannolikt i ett komplext system att alla flyttar är lika. Vissa sannolikt kommer att vara mycket dyrare.

## <a name="setting-move-costs"></a>Inställningen flytta kostnader 
Du kan ange standard flytta kostnaden för en tjänst när den har skapats:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#: 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Du kan också ange eller uppdatera MoveCost dynamiskt för en tjänst när tjänsten har skapats: 

PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Ange dynamiskt flytta kostnaden på grundval av per replik

Föregående kodfragment är alla för att ange MoveCost för en hel tjänst på en gång från utanför själva tjänsten. Dock flytta kostnaden är mest användbara är när flytta kostnaden för en specifik tjänstobjekt ändras under dess livslängd. Eftersom tjänsterna själva har antagligen den bästa uppfattning om hur kostsamma de är att flytta en given tidpunkt, är en API för tjänster som rapporten sina egna individuella flytta kostnad under körning. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Effekten av flytta kostnad
MoveCost har fyra nivåer: noll, lågt, Medium och hög. MoveCosts är i förhållande till varandra, utom noll. Noll flytta innebär att flytt är ledig och bör inte räknas av mot poängen för lösningen. Ange flytten kostnad för hög har *inte* garanti för att repliken håller sig kvar på samma plats.

<center>
![Flytta kostnad som en faktor att välja repliker för flytt][Image1]
</center>

MoveCost hjälper dig att hitta lösningar som stör den minst övergripande och som är enklast att uppnå när fortfarande anländer till motsvarande saldo. En tjänst begreppet kostnaden kan vara i förhållande till många saker. De vanligaste faktorerna för att beräkna kostnaden flytta är:

- Mängden tillstånd eller data som har tjänsten för att flytta.
- Kostnaden för frånkoppling av klienter. Flytta en primär replik är vanligtvis kostar mer än kostnaden för att flytta en sekundär replik.
- Kostnaden för att avbryta en pågående åtgärd. Vissa åtgärder på data lagra nivå eller åtgärder som utförs i svar på en klient är kostsamma. Du vill inte stoppa dem om du inte behöver efter en viss punkt. Så medan åtgärden pågår, ökar du flytta kostnaden för den här tjänsten objekt för att minska sannolikheten för att flyttas. När åtgärden är klar kan ange du kostnaden till normal.

## <a name="enabling-move-cost-in-your-cluster"></a>Aktivera flytta kostnaden i klustret
För mer detaljerade MoveCosts till beaktas måste MoveCost aktiveras i klustret. Standardläget för inventering flyttar används för beräkning av MoveCost utan den här inställningen och MoveCost rapporter ignoreras.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

värdbaserade kluster via ClusterConfig.json för fristående distributioner eller Template.json för Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Nästa steg
- Resurshanteraren för Service Fabric-kluster använder mått för att hantera förbrukning och kapacitet i klustret. Mer information om mått och hur du konfigurerar dem kolla [hantera resursförbrukning och belastningen i Service Fabric med](service-fabric-cluster-resource-manager-metrics.md).
- Mer information om hur klustret Resource Manager hanterar och balanserar belastningen i klustret, ta en titt [NLB Service Fabric-kluster](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

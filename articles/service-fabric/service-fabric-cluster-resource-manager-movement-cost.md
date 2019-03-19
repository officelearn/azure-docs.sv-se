---
title: 'Service Fabric Cluster Resource Manager: Kostnader för tjänsteflytt | Microsoft Docs'
description: Översikt över förflyttningskostnad för Service Fabric-tjänster
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: a4431f1d2e9a63ee7797100cc1092244d9a8b880
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58101524"
---
# <a name="service-movement-cost"></a>Kostnader för tjänsteflytt
En faktor som Service Fabric Cluster Resource Manager tar hänsyn till när försök att fastställa vad ändras till att göra en klustret är kostnaden för dessa ändringar. Begreppet ”cost” säljs av mot hur mycket klustret kan förbättras. Kostnaden är inberäknade när du flyttar tjänster för belastningsutjämning, defragmentering och andra krav. Målet är att uppfylla kraven på minst störande och dyr sätt. 

Flytta tjänster kostnader CPU-tid och nätverkets bandbredd ett minimum. För tillståndskänsliga tjänster krävs kopiera tillståndet för dessa tjänster, förbrukar mer minne och. Minimerar kostnaden för lösningar som Azure Service Fabric Cluster Resource Manager medföljer hjälper till att säkerställa att klustrets resurser inte har använt onödan. Men vill du även ignorera lösningar som skulle förbättrar allokeringen av resurser i klustret.

Klusterresurshanteraren har två sätt att beräkna kostnaderna och på så vis medan den försöker att hantera klustret. Den första mekanismen bara räknar antalet varje gång som det gör. Om två lösningar genereras med ungefär samma balansera (poäng), och sedan Klusterresurshanteraren föredrar ett med lägst pris (totalt antal flyttar).

Den här strategin fungerar bra. Men precis som med standard eller statiska laster, det är inte troligt i ett komplext system att alla flyttar är lika. Vissa är sannolikt att vara mycket mer dyr.

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

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Ange dynamiskt flytta kostnaden på basis av per replik

Föregående kodfragment är alla för att ange MoveCost för en hel tjänst på en gång från utanför själva tjänsten. Dock flytta kostnaden är mest användbara är när flytta kostnaden för en specifik tjänstobjekt ändras över dess livslängd. Eftersom tjänsterna själva har antagligen den bästa uppfattning av hur kostsamma de är att flytta en given tidpunkt, finns det ett API för tjänster att rapportera sina egna person flytta kostnad under körning. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Konsekvenser av att flytta kostnad
MoveCost har fyra nivåer: Noll, låg, Medium och hög. MoveCosts är i förhållande till varandra, förutom noll. Utan kostnad för flytten innebär att flytt är gratis och bör inte räknas av mot poängen för lösningen. Ange flytten kostnad för hög har *inte* garanti för att repliken håller sig kvar på samma ställe.

<center>

![Flytta kostnad som en faktor för att välja repliker för flytt][Image1]
</center>

MoveCost hjälper dig att hitta de lösningar som stör den lägsta övergripande och som är enklast att uppnå när du fortfarande kommer motsvarande saldo. En tjänst begreppet kostnaden kan vara i förhållande till många saker. De vanligaste faktorerna för att beräkna dina kostnader för flytten är:

- Mängden tillstånd eller data som har tjänsten för att flytta.
- Kostnaden för frånkoppling av klienter. Flytta en primär replik är vanligtvis dyrare än kostnaden för att flytta en sekundär replik.
- Kostnaden för att avbryta en pågående åtgärd. Vissa åtgärder på data lagra nivå eller åtgärder som utförs i svar på en klient är dyra. Efter en viss punkt vill du stoppa dem om du inte behöver. Så medan åtgärden pågår ökar du flytta kostnaden för den här tjänsten objekt för att minska sannolikheten för att flyttas. När åtgärden är klar kan ange du kostnaden till normalt läge.

## <a name="enabling-move-cost-in-your-cluster"></a>Aktivera flytta kostnaden i klustret
För mer detaljerade MoveCosts till beaktas måste MoveCost aktiveras i klustret. Standardläget räkna flyttar används för att beräkna MoveCost utan den här inställningen och MoveCost rapporter ignoreras.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

via ClusterConfig.json för distribution av fristående eller Template.json för Azure som värd kluster:

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
- Resurshanteraren för Service Fabric-kluster använder mått för att hantera förbrukning och kapacitet i klustret. Om du vill veta mer om mått och hur du konfigurerar dem kan du kolla in [hantera resursförbrukning och belastning i Service Fabric med mått](service-fabric-cluster-resource-manager-metrics.md).
- Mer information om hur Cluster Resource Manager hanterar och balanserar belastningen i klustret, ta en titt [belastningsutjämning Service Fabric-klustret](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

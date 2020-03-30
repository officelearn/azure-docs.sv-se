---
title: 'Resurshanteraren för servicevävnadskluster: Rörelsekostnad'
description: Lär dig mer om rörelsekostnaden för Service Fabric-tjänster och hur den kan anges för att passa alla arkitektoniska behov, inklusive dynamisk konfiguration.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: af3e01d0d5a605c052be24eed8e14ee3449e2c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563351"
---
# <a name="service-movement-cost"></a>Kostnad för tjänsterörelser
En faktor som Service Fabric Cluster Resource Manager tar hänsyn till när du försöker avgöra vilka ändringar som ska görs i ett kluster är kostnaden för dessa ändringar. Begreppet "kostnad" handlas bort mot hur mycket klustret kan förbättras. Kostnaden räknas in när du flyttar tjänster för balansering, defragmentering och andra krav. Målet är att uppfylla kraven på det minst störande eller dyra sättet.

Flytta tjänster kostar CPU-tid och nätverksbandbredd på ett minimum. För tillståndskänsliga tjänster kräver det att du kopierar tillståndet för dessa tjänster och förbrukar ytterligare minne och disk. Minimera kostnaden för lösningar som Azure Service Fabric Cluster Resource Manager kommer med hjälper till att säkerställa att klustrets resurser inte spenderas i onödan. Men du vill inte heller ignorera lösningar som avsevärt skulle förbättra fördelningen av resurser i klustret.

Cluster Resource Manager har två sätt att beräkna kostnader och begränsa dem medan den försöker hantera klustret. Den första mekanismen är helt enkelt att räkna varje steg som den skulle göra. Om två lösningar genereras med ungefär samma saldo (poäng) föredrar Klusterresurshanteraren den med den lägsta kostnaden (totalt antal drag).

Denna strategi fungerar bra. Men som med standard eller statiska belastningar, det är osannolikt i något komplext system att alla drag är lika. Vissa kommer sannolikt att bli mycket dyrare.

## <a name="setting-move-costs"></a>Ställa in flyttkostnader 
Du kan ange standardflyttkostnaden för en tjänst när den skapas:

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

Du kan också ange eller uppdatera MoveCost dynamiskt för en tjänst efter att tjänsten har skapats: 

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

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Dynamiskt ange flyttkostnad per replik

De föregående kodavsnitten är alla för att ange MoveCost för en hel tjänst på en gång från utanför själva tjänsten. Flyttkostnaden är dock mest användbar när flyttkostnaden för ett visst serviceobjekt ändras under dess livslängd. Eftersom tjänsterna själva förmodligen har den bästa idén om hur kostsamma de är att flytta en viss tid, det finns ett API för tjänster för att rapportera sina egna individuella flytta kostnad under körning. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Inverkan av flyttkostnad
MoveCost har fem nivåer: Noll, Låg, Medium, Hög och VeryHigh. Följande regler gäller:

* MoveCosts är relativa till varandra, med undantag för Zero och VeryHigh. 
* Noll flyttkostnad innebär att rörelsen är fri och inte bör räknas mot lösningens poäng.
* Att ställa in flyttkostnaden till Hög eller VeryHigh ger *ingen* garanti för att repliken *aldrig* kommer att flyttas.
* Repliker med VeryHigh flytta kostnaden kommer att flyttas endast om det finns en begränsning kränkning i klustret som inte kan fastställas på något annat sätt (även om det kräver att flytta många andra repliker för att åtgärda överträdelsen)



<center>

![Flytta kostnad som en faktor vid val av repliker för rörelse][Image1]
</center>

MoveCost hjälper dig att hitta de lösningar som orsakar minst störningar totalt och är lättast att uppnå samtidigt som du kommer fram till motsvarande balans. En tjänst s begreppet kostnad kan vara i förhållande till många saker. De vanligaste faktorerna vid beräkning av flyttkostnaden är:

- Mängden tillstånd eller data som tjänsten måste flytta.
- Kostnaden för frånkoppling av klienter. Flytta en primär replik är oftast dyrare än kostnaden för att flytta en sekundär replik.
- Kostnaden för att avbryta en flygning. Vissa åtgärder på datalagernivå eller åtgärder som utförs som svar på ett klientanrop är kostsamma. Efter en viss punkt, vill du inte stoppa dem om du inte behöver. Så medan åtgärden pågår, du öka flyttkostnaden för denna tjänst objekt för att minska sannolikheten för att den flyttar. När åtgärden är klar ställer du in kostnaden igen till det normala.

> [!IMPORTANT]
> Använda VeryHigh flytta kostnaden bör noga övervägas eftersom det avsevärt begränsar möjligheten för Cluster Resource Manager att hitta en globalt optimal placering lösning i klustret. Repliker med VeryHigh flytta kostnaden kommer att flyttas endast om det finns en begränsning kränkning i klustret som inte kan fastställas på något annat sätt (även om det kräver att flytta många andra repliker för att åtgärda överträdelsen)

## <a name="enabling-move-cost-in-your-cluster"></a>Aktivera flyttkostnad i klustret
För att de mer detaljerade MoveCosts ska kunna beaktas måste MoveCost aktiveras i klustret. Utan den här inställningen används standardläget för inventeringsrörelser för beräkning av MoveCost och MoveCost-rapporter ignoreras.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

via ClusterConfig.json för fristående distributioner eller Template.json för Azure-värdkluster:

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
- Service Fabric Cluster Resource Manger använder mått för att hantera förbrukning och kapacitet i klustret. Om du vill veta mer om mått och hur du konfigurerar dem finns i [Hantera resursförbrukning och inläsning i Service Fabric med mått](service-fabric-cluster-resource-manager-metrics.md).
- Om du vill veta mer om hur Cluster Resource Manager hanterar och balanserar belastning i klustret finns i [Balansera ditt Service Fabric-kluster](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

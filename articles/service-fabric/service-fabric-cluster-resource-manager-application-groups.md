---
title: Service Fabric klustret Resource Manager - programgrupper | Microsoft Docs
description: Översikt över funktionen för programgruppen i Service Fabric klustret Resource Manager
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 215efc1f0597f5199dd37baf4b109d7e76040aae
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="introduction-to-application-groups"></a>Introduktion till programgrupper
Resurshanteraren för Service Fabric-klustret hanterar vanligtvis klusterresurser genom att sprida belastningen (representeras [mått](service-fabric-cluster-resource-manager-metrics.md)) jämnt i hela klustret. Service Fabric hanterar kapacitet av noderna i klustret och klustret som helhet via [kapacitet](service-fabric-cluster-resource-manager-cluster-description.md). Mått och kapacitet fungerar bra för många arbetsbelastningar, men mönster som använder olika Service Fabric-programinstanser ibland hämtas ytterligare krav. Till exempel kanske du vill:

- Reservera vissa kapacitet på noderna i klustret för tjänster i vissa namngivna programinstansen
- Begränsa det totala antalet noder som kör services i en namngiven programinstansen på (i stället för att sprida ut dem över hela klustret)
- Definiera kapacitet på namngiven programinstansen fristående för att begränsa antalet tjänster eller totalt antal resurser som används för tjänster i den

För att uppfylla kraven, stöder Service Fabric klustret Resource Manager en funktion som kallas programgrupper.

## <a name="limiting-the-maximum-number-of-nodes"></a>Begränsar det maximala antalet noder
När en instans av programmet ska begränsas till ett maximalt antal noder är det enklaste användningsfallet för programmet kapacitet. Detta konsoliderar alla tjänster inom den programinstansen till ett visst antal datorer. Konsolidering är användbart när du försöker att förutsäga eller cap fysisk Resursanvändning av tjänsterna i den namngivna programinstansen. 

Följande bild visar en programinstans med och utan ett maximalt antal noder som definierats:

<center>
![Definiera maximalt antal noder programinstansen][Image1]
</center>

Programmet har inte ett maximalt antal noder som definierats i exemplet vänstra och den har tre tjänster. Klustret Resource Manager har sprids ut alla repliker sex tillgängliga noder för att uppnå bästa balans i klustret (standardinställning). I högra exempel visas samma program begränsad till tre noder.

Parametern som styr det här beteendet kallas MaximumNodes. Denna parameter kan anges när programmet skulle skapas eller uppdateras för en programinstans som redan körs.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

I uppsättningen noder garanterar inte klustret Resource Manager vilka tjänstobjekt placeras tillsammans eller vilka noder hämta används.

## <a name="application-metrics-load-and-capacity"></a>Programmet mått, belastning och kapacitet
Programgrupper kan du definiera mätvärden som är associerade med en viss namngiven programinstansen och den programinstansen kapacitet för dessa mått. Programmet mått kan du spåra reservera och begränsa resursförbrukning tjänster i den programinstansen.

Det finns två värden som kan anges för varje mått:

- **Total kapacitet för programmet** – den här inställningen som representerar den totala kapaciteten för programmet för ett viss mått. Klustret Resource Manager tillåter inte skapandet av nya tjänster i den här programinstansen som skulle orsaka totala belastningen överskrider värdet. Anta att till exempel programinstansen hade en kapacitet på 10 och redan hade belastningen på fem. Skapandet av en tjänst med belastning totala standard 10 skulle inte tillåts.
- **Maximal kapacitet för noden** – den här inställningen anger den maximala totala belastningen för programmet på en enda nod. Om belastningen över denna kapacitet, flyttar klustret Resource Manager repliker till andra noder så att belastningen minskar.


PowerShell:

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#:

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Reserverar kapacitet
Ett annat vanligt användningsområde för programgrupper är att säkerställa att resurser i klustret är reserverade för ett visst program-instans. Utrymmet reserveras alltid när programinstansen skapas.

Reservera diskutrymme i klustret för programmet sker omedelbart även om:
- programinstansen har skapats men har inte några tjänster inom det ännu
- antal tjänster inom programinstansen ändras varje gång 
- tjänsterna finns men förbrukar inte resurser 

Reservera resurser för en instans av programmet kräver att ange två ytterligare parametrar: *MinimumNodes* och *NodeReservationCapacity*

- **MinimumNodes** -definierar det minsta antalet noder som programinstansen ska köras på.  
- **NodeReservationCapacity** -den här inställningen är per mått för programmet. Värdet är mängden det mått som reserverats för programmet på någon nod där som tjänsterna i programmet körs.

Kombinera **MinimumNodes** och **NodeReservationCapacity** garanterar en minsta belastningen reservation för program i klustret. Om det har mindre återstående kapacitet i klustret än den totala reservationen krävs, inte skapandet av programmet. 

Nu ska vi titta på ett exempel på kapacitet reservation:

<center>
![Programinstanser definiera reserverad kapacitet][Image2]
</center>

I den vänstra exempelvis har program inte några program kapacitet har definierats. Klustret Resource Manager balanserar allt enligt normala regler.

I exemplet på höger anta att Application1 har skapats med följande inställningar:

- MinimumNodes inställd på två
- Ett program mått som definieras med
  - NodeReservationCapacity 20

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric reserverar kapacitet på två noder för Application1 och tillåter inte tjänster från Application2 att använda den kapaciteten även om det finns ingen belastning som förbrukas av tjänsterna i Application1 samtidigt. Denna kapacitet för reserverade programmet betraktas som förbrukats och räknas av mot de återstående kapaciteten på noden och inom klustret.  Reservationen dras från återstående klustret kapacitet omedelbart, men reserverade förbrukningen dras av från kapaciteten för en viss nod endast när minst en tjänstobjekt placeras i den. Denna senare reservation ger flexibilitet och bättre resursutnyttjande eftersom resurser som endast är reserverad på noder vid behov.

## <a name="obtaining-the-application-load-information"></a>Hämta belastningen programinformation
För varje program som har ett program kapacitet har definierats för ett eller flera mått som du kan hämta information om sammanställd belastning som rapporterats av repliker av dess tjänster.

PowerShell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

ApplicationLoad frågan returnerar grundläggande information om kapacitet för program som har angetts för programmet. Informationen omfattar information om lägsta noder och maximalt antal noder och hur många som programmet för närvarande använder. Det innehåller även information om varje belastningen mått, inklusive:

* Måttnamnet: Namnet på måttet.
* Reservation Capacity: Kapacitet för kluster som är reserverade i klustret för det här programmet.
* Programinläsning: Totalt antal belastningen på det här programmet underordnade repliker.
* Kapacitet för programmet: Högsta tillåtna värdet för programinläsning.

## <a name="removing-application-capacity"></a>Kapacitet för att ta bort program
Efter att programmet kapacitet parametrar har angetts för ett program, kan de tas bort med hjälp av API: er för Update-programmet eller PowerShell-cmdlets. Exempel:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Det här kommandot tar bort alla parametrar för program-kapacitet från programinstansen. Detta inkluderar MinimumNodes, MaximumNodes och programmets statistik, om sådana finns. Effekten av kommandot sker omedelbart. När det här kommandot har slutförts använder kluster Resource Manager standardbeteendet för att hantera program. Programmet kapacitet parametrar kan anges igen `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`.

### <a name="restrictions-on-application-capacity"></a>Begränsningar för programmet kapacitet
Det finns flera begränsningar för programmet kapacitet parametrar som måste följas. Om det finns valideringsfel görs inga ändringar.

- Alla måste heltal vara icke-negativt tal.
- MinimumNodes måste aldrig vara större än MaximumNodes.
- Om kapaciteten för ett mått för belastningen definieras måste de följa reglerna:
  - Noden Reservation Capacity får inte vara större än den maximala nod kapaciteten. Du kan till exempel begränsa kapacitet för måttet ”CPU” på noden till två enheter och försök att reservera tre enheter på varje nod.
  - Om MaximumNodes anges får sedan produkten av MaximumNodes och maximumkapacitet för noden inte vara större än den Totalkapaciteten för programmet. Anta till exempel noden maximumkapacitet för belastningen mått ”CPU” anges till åtta. Anta också att du har angett maximalt antal noder till 10. I det här fallet måste totalkapacitet för programmet vara större än 80 för mätvärdet belastningen.

Begränsningarna tillämpas både under skapa program och uppdateringar.

## <a name="how-not-to-use-application-capacity"></a>Hur inte att använda programmet kapacitet
- Försök inte att använda funktioner i programgruppen du vill begränsa programmet till en _specifika_ del noder. Med andra ord kan du ange att programmet körs på högst fem noder, men inte vilka specifika fem noder i klustret. Begränsa ett program till specifika noder kan ske med hjälp av placeringsbegränsningar för tjänster.
- Försök inte att använda programmet kapacitet för att säkerställa att två tjänster från samma program är placerade på samma noder. I stället använda [tillhörighet](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) eller [placeringen](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster, [Lär dig mer om hur du konfigurerar tjänster](service-fabric-cluster-resource-manager-configure-services.md)
- Om du vill veta mer om hur klustret Resource Manager hanterar och balanserar belastningen i klustret kan ta en titt i artikeln på [belastningsutjämning](service-fabric-cluster-resource-manager-balancing.md)
- Börja från början och [få en introduktion till Service Fabric klustret Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Mer information om hur mått fungerar normalt läsa på [Service Fabric belastningen mått](service-fabric-cluster-resource-manager-metrics.md)
- Klustret Resource Manager har många alternativ för att beskriva klustret. Kolla in den här artikeln om du vill veta mer om dem på [som beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

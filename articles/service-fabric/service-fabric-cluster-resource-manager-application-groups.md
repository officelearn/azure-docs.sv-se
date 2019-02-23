---
title: Service Fabric Cluster Resource Manager - programgrupper | Microsoft Docs
description: Översikt över funktionen för programgruppen i Service Fabric Cluster Resource Manager
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: e4f446ff67408ef390ba817de935c286c5b2a47e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727612"
---
# <a name="introduction-to-application-groups"></a>Introduktion till programgrupper
Service Fabric Cluster Resource Manager hanterar vanligtvis klusterresurser genom att sprida belastningen (representeras [mått](service-fabric-cluster-resource-manager-metrics.md)) jämnt i hela klustret. Service Fabric hanterar kapaciteten för noderna i klustret och klustret som helhet via [kapacitet](service-fabric-cluster-resource-manager-cluster-description.md). Mått och kapacitetsbehov fungerar bra för många arbetsbelastningar, men mönster som använder olika Service Fabric-programinstanser ibland hämta ytterligare krav. Till exempel vilja:

- Reservera vissa kapacitet på noderna i klustret för tjänster inom vissa namngivna programinstansen
- Begränsa det totala antalet noder som tjänster inom en namngiven programinstans som körs på (i stället för sprida ut dem över hela klustret)
- Definiera kapaciteter på namngivna programinstansen fristående för att begränsa antalet tjänster eller totalt antal resurser som används för tjänster i den

Service Fabric Cluster Resource Manager stöder funktionen programgrupper som uppfyller dina krav.

## <a name="limiting-the-maximum-number-of-nodes"></a>Begränsar det maximala antalet noder
Det enklaste användningsfallet för programkapacitet är när en programinstans måste vara begränsad till ett högsta antal noder. Det här samlas alla tjänster inom den programinstansen till ett visst antal datorer. Konsolidering är användbart när du försöker att förutspå eller cap fysisk Resursanvändning av tjänster inom den namngivna programinstansen. 

Följande bild visar en programinstans med och utan ett maximalt antal noder som har definierats:

<center>
![Programinstans som definierar högsta antalet noder][Image1]
</center>

Programmet har en högsta antalet noder som definierats i exemplet vänstra och den har tre tjänster. Klusterresurshanteraren har sprids ut alla repliker över sex tillgängliga noder för att uppnå bästa balans i klustret (standardinställningen). Vi kan se att samma program som är begränsad till tre noder i rätt exempel.

Parametern som styr det här beteendet kallas MaximumNodes. Den här parametern kan ange när programmet skapas eller uppdateras för en programinstans som redan kör.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –ApplicationName fabric:/AppName –MaximumNodes 5
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

Inom en uppsättning noder garanterar inte Klusterresurshanteraren vilka serviceobjekt placeras tillsammans eller vilka noder får användas.

## <a name="application-metrics-load-and-capacity"></a>Mätvärden för Application, belastning och kapacitet
Programgrupper kan du definiera mått som är associerade med en viss namngivna programinstans och den programinstans kapacitet för dessa mått. Mätvärden för Application kan du spåra, reservera och begränsa de resurser som används för tjänster i den programinstansen.

Det finns två värden som kan ställas in för varje program mått:

- **Total kapacitet för programmet** – den här inställningen motsvarar den totala kapaciteten för programmet för ett visst mått. Cluster Resource Manager tillåts inte skapandet av alla nya tjänster inom den här instansen av programmet som skulle orsaka totala belastningen överskrider värdet. Exempel: Anta programinstansen hade en kapacitet på 10 och redan hade belastningen på fem. Skapandet av en tjänst med en total standard belastning på 10 skulle tillåts inte.
- **Maximal kapacitet för noden** – den här inställningen anger den maximala totala belastningen för programmet på en enda nod. Om belastningen över den här kapaciteten, flyttar Klusterresurshanteraren repliker till andra noder så att belastningen minskar.


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

## <a name="reserving-capacity"></a>Reservation av kapacitet
Ett annat vanligt användningsområde för programgrupper är att säkerställa att resurser i klustret har reserverats för ett visst program-instans. Utrymmet reserveras alltid när instansen för programmet har skapats.

Reservering av utrymme i klustret för programmet händer omedelbart även om:
- programinstansen skapas, men har inte några tjänster inom den ännu
- antalet tjänster inom programinstansen ändras varje gång 
- tjänsterna finns men förbrukar inte resurser 

Reservera resurser för en programinstans måste du specificera två ytterligare parametrar: *MinimumNodes* och *NodeReservationCapacity*

- **MinimumNodes** -definierar det minsta antalet noder som instansen för programmet ska köras på.  
- **NodeReservationCapacity** -den här inställningen är per mått för programmet. Värdet är mängden som mått som är reserverade för programmet på alla noder där som kör tjänsterna i programmet.

Kombinera **MinimumNodes** och **NodeReservationCapacity** garanterar en minsta load-reservation för programmet i klustret. Om det har mindre återstående kapacitet i klustret än den totala reservationen krävs, misslyckas skapandet av programmet. 

Låt oss titta på ett exempel på kapacitetsreservationen:

<center>
![Programinstanser definiera reserverad kapacitet][Image2]
</center>

Program har inte någon Programkapacitet som definierats i vänstra exempel. Klusterresurshanteraren balanserar allt enligt normal regler.

I det här exemplet till höger anta att Application1 har skapats med följande inställningar:

- MinimumNodes inställd på två
- Ett program mått som definieras med
  - NodeReservationCapacity of 20

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

Service Fabric reserverar kapacitet på två noder för Application1 och tillåter tjänster från Application2 förbruka kapaciteten även om det finns ingen belastning som förbrukas av tjänster i Application1 på gång. Den här reserverade programkapacitet betraktas som förbrukas och räknas mot den återstående kapaciteten på noden och inom klustret.  Reservationen dras från de återstående klusterkapacitet direkt, men reserverade förbrukningen dras av från kapaciteten för en viss nod endast när minst en tjänstobjekt placeras på den. Den här senare reservationen ger flexibilitet och bättre resursutnyttjande eftersom resurser som reserveras endast på noder när det behövs.

## <a name="obtaining-the-application-load-information"></a>Hämta belastningen programinformation
För varje program som har en Programkapacitet som definierats för ett eller flera mått som du kan hämta information om den sammanställda belastningen som rapporterats av repliker av dess tjänster.

PowerShell:

``` posh
Get-ServiceFabricApplicationLoadInformation –ApplicationName fabric:/MyApplication1
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

ApplicationLoad frågan returnerar grundläggande information om kapacitet för program som har angetts för programmet. Denna information innehåller information om noder för minsta och högsta antalet noder och hur många som programmet för närvarande några. Det innehåller även information om varje belastning mått, inklusive:

* Måttnamn: Namnet på måttet.
* Reservation kapacitet: Klusterkapacitet som är reserverade i klustret för det här programmet.
* Programinläsning: Totalt antal belastningen på det här programmet underordnade repliker.
* Kapacitet för programmet: Högsta tillåtna värdet för Programbelastningen.

## <a name="removing-application-capacity"></a>Ta bort Programkapacitet
När parametern Programkapacitet har angetts för ett program, kan de tas bort med hjälp av uppdatera program-API: er eller PowerShell-cmdlets. Exempel:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Det här kommandot tar bort alla parametrar för program-kapacitet från programinstansen. Detta inkluderar MinimumNodes MaximumNodes och programmått, om sådana. Effekten av kommandot sker omedelbart. När det här kommandot har slutförts använder Cluster Resource Manager standardbeteendet för att hantera program. Programparametrar kapacitet kan anges igen `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`.

### <a name="restrictions-on-application-capacity"></a>Begränsningar för Programkapacitet
Det finns flera begränsningar på Programkapacitet parametrar som måste följas. Om det finns verifieringsfel görs inga ändringar.

- Alla heltalsparametrar måste vara icke-negativt tal.
- MinimumNodes måste aldrig vara större än MaximumNodes.
- Om de kapaciteter för ett mått för load definieras måste de följa reglerna:
  - Reservation Nodkapaciteten får inte vara större än den maximala noden kapaciteten. Du kan exempelvis begränsa kapacitet för mått ”processor” på noden till två enheter och försöker tre reservenheter på varje nod.
  - Om MaximumNodes anges måste produkten av MaximumNodes och maximumkapacitet för noden inte vara större än totalkapacitet för programmet. Anta till exempel noden maximumkapacitet för inläsning mått ”processor” är inställt på åtta. Anta också att du angett det högsta antalet noder till 10. I det här fallet måste totalkapacitet för programmet vara större än 80 för mätvärdet belastningen.

Begränsningarna tillämpas både under skapa program och uppdateringar.

## <a name="how-not-to-use-application-capacity"></a>Hur inte ska använda Programkapacitet
- Försök inte att använda programgruppen funktioner för att begränsa programmet till en _specifika_ del noder. Med andra ord kan du ange att programmet körs på högst fem noder, men inte vilka specifika fem noder i klustret. Begränsa ett program till specifika noder kan ske med placeringsbegränsningar för tjänster.
- Försök inte att använda programmet kapacitet för att säkerställa att två tjänster från samma program är placerade på samma noderna. I stället använda [tillhörighet](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) eller [placeringsbegränsningar](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster, [Lär dig mer om hur du konfigurerar tjänster](service-fabric-cluster-resource-manager-configure-services.md)
- Om du vill veta mer om hur Cluster Resource Manager hanterar och balanserar belastningen i klustret kan du läsa artikeln på [belastningsutjämning](service-fabric-cluster-resource-manager-balancing.md)
- Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Mer information om hur mått fungerar vanligtvis Läs igenom [Inläsningsmåtten för Service Fabric](service-fabric-cluster-resource-manager-metrics.md)
- Klusterresurshanteraren har många alternativ för att beskriva klustret. Om du vill veta mer om dem, Kolla in den här artikeln på [som beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

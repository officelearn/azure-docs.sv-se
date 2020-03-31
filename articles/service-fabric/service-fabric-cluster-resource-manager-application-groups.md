---
title: Resurshanteraren för service fabric-kluster – programgrupper
description: Översikt över programgruppsfunktionen i Resurshanteraren för serviceinfrastrukturkluster
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 988c7ce52125800c16aa785d5b1458604a927ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452157"
---
# <a name="introduction-to-application-groups"></a>Introduktion till programgrupper
Service Fabric's Cluster Resource Manager hanterar vanligtvis klusterresurser genom att sprida belastningen (representerad via mått) jämnt i hela [klustret.](service-fabric-cluster-resource-manager-metrics.md) Service Fabric hanterar kapaciteten för noderna i klustret och klustret som helhet via [kapacitet](service-fabric-cluster-resource-manager-cluster-description.md). Mått och kapacitet fungerar utmärkt för många arbetsbelastningar, men mönster som använder olika tjänstinfrastrukturprograminstanser ger ibland ytterligare krav. Du kanske till exempel vill:

- Reservera viss kapacitet på noderna i klustret för tjänsterna inom någon namngiven programinstans
- Begränsa det totala antalet noder som tjänsterna i en namngiven programinstans körs på (i stället för att sprida ut dem över hela klustret)
- Definiera kapacitet för själva den namngivna programinstansen för att begränsa antalet tjänster eller den totala resursförbrukningen för tjänsterna i den

För att uppfylla dessa krav stöder Service Fabric Cluster Resource Manager en funktion som kallas programgrupper.

## <a name="limiting-the-maximum-number-of-nodes"></a>Begränsa det maximala antalet noder
Det enklaste användningsfallet för programkapacitet är när en programinstans måste begränsas till ett visst maximalt antal noder. Detta konsoliderar alla tjänster inom den programinstansen till ett antaget antal datorer. Konsolidering är användbart när du försöker förutsäga eller begränsa fysisk resursanvändning av tjänsterna inom den namngivna programinstansen. 

Följande bild visar en programinstans med och utan ett maximalt antal definierade noder:

<center>

![Programinstans definierat maximalt antal noder][Image1]
</center>

I det vänstra exemplet har programmet inte ett maximalt antal definierade noder och tre tjänster. Klusterresurshanteraren har spridit ut alla repliker över sex tillgängliga noder för att uppnå bästa möjliga balans i klustret (standardbeteendet). I rätt exempel ser vi samma program begränsat till tre noder.

Parametern som styr det här beteendet kallas MaximumNodes. Den här parametern kan ställas in när programmet skapas eller uppdateras för en programinstans som redan kördes.

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

Inom uppsättningen noder garanterar klusterresurshanteraren inte vilka serviceobjekt som placeras tillsammans eller vilka noder som används.

## <a name="application-metrics-load-and-capacity"></a>Programmått, belastning och kapacitet
Med programgrupper kan du också definiera mått som är associerade med en angiven namngiven programinstans och programinstansens kapacitet för dessa mått. Med programmått kan du spåra, reservera och begränsa resursförbrukningen för tjänsterna i programinstansen.

För varje programmått finns det två värden som kan anges:

- **Total programkapacitet** – Den här inställningen representerar programmets totala kapacitet för ett visst mått. Klusterresurshanteraren tillåter inte att nya tjänster skapas i den här programinstansen som gör att den totala belastningen överskrider det här värdet. Anta till exempel att programinstansen hade en kapacitet på 10 och redan hade en belastning på fem. Skapandet av en tjänst med en total standardbelastning på 10 skulle inte tillåtas.
- **Maximal nodkapacitet** – Den här inställningen anger den maximala totala belastningen för programmet på en enda nod. Om belastningen överskrider den här kapaciteten flyttar Cluster Resource Manager repliker till andra noder så att belastningen minskar.


Powershell:

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

## <a name="reserving-capacity"></a>Reservera kapacitet
En annan vanlig användning för programgrupper är att se till att resurser i klustret är reserverade för en viss programinstans. Utrymmet reserveras alltid när programinstansen skapas.

Reservera utrymme i klustret för programmet sker omedelbart även när:
- programinstansen skapas men har inga tjänster i den ännu
- antalet tjänster i programinstansen ändras varje gång 
- tjänsterna finns men inte förbrukar resurser 

Reservera resurser för en programinstans kräver att du anger ytterligare två parametrar: *MinimumNodes* och *NodeReservationCapacity*

- **MinimumNodes** - Definierar det minsta antalet noder som programinstansen ska köras på.  
- **NodeReservationCapacity** - Den här inställningen är per mått för programmet. Värdet är beloppet för det mått som reserverats för programmet på en nod där tjänsterna i det programmet körs.

Att kombinera **MinimumNodes** och **NodeReservationCapacity** garanterar en minsta belastningsreservation för programmet i klustret. Om det finns mindre återstående kapacitet i klustret än den totala reservation som krävs misslyckas skapandet av programmet. 

Låt oss titta på ett exempel på kapacitetsreservation:

<center>

![Programinstanser som definierar reserverad kapacitet][Image2]
</center>

I det vänstra exemplet har program inte någon programkapacitet definierad. Klusterresurshanteraren balanserar allt enligt normala regler.

I exemplet till höger kan vi säga att Application1 har skapats med följande inställningar:

- MinimumNodes inställd på två
- Ett programmått som definierats med
  - NodeReservationKapacitet på 20

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

Service Fabric reserverar kapacitet på två noder för Application1 och tillåter inte att tjänster från Application2 förbrukar den kapaciteten även om det inte finns någon belastning som förbrukas av tjänsterna i Application1 vid den tidpunkten. Den här reserverade programkapaciteten anses förbrukas och räknas mot den återstående kapaciteten på den noden och inom klustret.  Reservationen dras omedelbart av från den återstående klusterkapaciteten, men den reserverade förbrukningen dras av från kapaciteten för en viss nod endast när minst ett serviceobjekt placeras på den. Den senare reservationen möjliggör flexibilitet och bättre resursutnyttjande eftersom resurser endast reserveras på noder när det behövs.

## <a name="obtaining-the-application-load-information"></a>Hämta information om programinläsning
För varje program som har en programkapacitet definierad för ett eller flera mått kan du hämta information om den sammanlagda belastningen som rapporteras av repliker av dess tjänster.

Powershell:

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

ApplicationLoad-frågan returnerar den grundläggande informationen om programkapacitet som angavs för programmet. Den här informationen innehåller information om minsta noder och högsta noder och det nummer som programmet för närvarande upptar. Den innehåller också information om varje mätvärde för programbelastning, inklusive:

* Måttnamn: Måttets namn.
* Reservationskapacitet: Klusterkapacitet som är reserverad i klustret för det här programmet.
* Programinläsning: Total belastning för programmets underordnade repliker.
* Användningskapacitet: Maximalt tillåtet värde för programbelastning.

## <a name="removing-application-capacity"></a>Ta bort programkapacitet
När parametrarna för programkapacitet har angetts för ett program kan de tas bort med hjälp av Api:er för uppdateringsprogram eller PowerShell-cmdlets. Ett exempel:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Det här kommandot tar bort alla programkapacitetshanteringsparametrar från programinstansen. Detta inkluderar MinimumNodes, MaximumNodes och programmets mått, om sådana finns. Effekten av kommandot är omedelbar. När det här kommandot är klart använder Klusterresurshanteraren standardbeteendet för att hantera program. Parametrar för programkapacitet kan `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`anges igen via .

### <a name="restrictions-on-application-capacity"></a>Begränsningar av applikationskapacitet
Det finns flera begränsningar för parametrar för programkapacitet som måste respekteras. Om det finns valideringsfel sker inga ändringar.

- Alla heltalsparametrar måste vara icke-negativa tal.
- MinimumNodes får aldrig vara större än MaximumNodes.
- Om kapacitet för ett belastningsmått definieras måste de följa dessa regler:
  - Nodreserveringskapacitet får inte vara större än maximal nodkapacitet. Du kan till exempel inte begränsa kapaciteten för måttet "CPU" på noden till två enheter och försöka reservera tre enheter på varje nod.
  - Om MaximumNodes anges får produkten av MaximumNodes och Maximum Node Capacity inte vara större än total programkapacitet. Anta till exempel att den maximala nodkapaciteten för inläsningsmått "CPU" är inställd på åtta. Anta också att du ställer in maxnoderna till 10. I det här fallet måste den totala programkapaciteten vara större än 80 för det här belastningsmåttet.

Begränsningarna tillämpas både när programmet skapas och uppdateringar.

## <a name="how-not-to-use-application-capacity"></a>Hur man inte använder applikationskapacitet
- Försök inte använda funktionerna i programgruppen för att begränsa programmet till en _viss_ delmängd av noder. Med andra ord kan du ange att programmet körs på högst fem noder, men inte vilka specifika fem noder i klustret. Begränsa ett program till specifika noder kan uppnås med hjälp av placeringsbegränsningar för tjänster.
- Försök inte använda programkapaciteten för att säkerställa att två tjänster från samma program placeras på samma noder. Använd i stället [tillhörighets-](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) eller [placeringsbegränsningar](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster [finns i Läs mer om att konfigurera tjänster](service-fabric-cluster-resource-manager-configure-services.md)
- Om du vill veta mer om hur Cluster Resource Manager hanterar och balanserar belastning i klustret kan du läsa artikeln om [utjämningsbelastning](service-fabric-cluster-resource-manager-balancing.md)
- Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Mer information om hur mått fungerar i allmänhet finns i [Service Fabric Load Metrics](service-fabric-cluster-resource-manager-metrics.md)
- Klusterresurshanteraren har många alternativ för att beskriva klustret. Om du vill veta mer om dem kan du läsa den här artikeln om [hur du beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

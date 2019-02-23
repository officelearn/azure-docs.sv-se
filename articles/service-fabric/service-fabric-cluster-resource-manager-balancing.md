---
title: Balansera ditt Azure Service Fabric-kluster | Microsoft Docs
description: En introduktion till belastningsutjämning ditt kluster med Service Fabric Cluster Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 534a9584427efd15b8119f8421fb041199b97fbf
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731590"
---
# <a name="balancing-your-service-fabric-cluster"></a>Belastningsutjämning service fabric-kluster
Service Fabric Cluster Resource Manager har stöd för dynamisk ändringar, reagera på tillägg eller borttagning av noder eller tjänster. Den korrigerar också automatiskt begränsningen överträdelser och balanserar proaktivt klustret. Men hur ofta tas de här åtgärderna och vad utlöser dem?

Det finns tre olika kategorier av arbete som utför Cluster Resource Manager. De är:

1. Placering – det här steget innehåller placera alla tillståndskänsliga repliker eller tillståndslösa instanser som saknas. Placering innehåller nya tjänster och hantering av tillståndskänsliga repliker eller tillståndslösa instanser som har misslyckats. Ta bort och släppa repliker eller instanser hanteras här.
2. Begränsningen kontrollerar – det här steget kontrollerar och korrigerar brott mot olika placeringen (regler) i systemet. Exempel på regler är till exempel att säkerställa att noderna inte är överbelastade och att placeringsbegränsningar för en tjänst är uppfyllda.
3. Belastningsutjämning – det här steget kontrollerar om du vill se om ombalansering krävs baserat på konfigurerade önskad nivå för balans för olika mått. Fall i så försök görs att hitta upplägget i klustret som är mer balanserad.

## <a name="configuring-cluster-resource-manager-timers"></a>Konfigurera Timers för Cluster Resource Manager
Den första uppsättningen kontroller runt belastningsutjämning är en uppsättning timers. Dessa timers styr hur ofta Klusterresurshanteraren undersöker klustret och tar korrigerande åtgärder.

Var och en av dessa olika typer av ändringar Cluster Resource Manager kan göra styrs av en annan timer som styr frekvensen. När varje utlöses har uppgiften schemalagts. Som standard Resource Manager:

* söker igenom tillståndet och tillämpar uppdateringar (till exempel spela in som en nod är nere) var 1/10 av en sekund
* Anger flaggan för kontroll av placering 
* Anger att begränsningen kontrollera varje sekund
* Anger flaggan belastningsutjämning var femte sekund.

Exempel på konfigurationen reglerar dessa timers är nedan:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

via ClusterConfig.json för distribution av fristående eller Template.json för Azure som värd kluster:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Idag utför Cluster Resource Manager endast en av dessa åtgärder samtidigt, sekventiellt. Det är därför vi refererar till dessa timers som ”minsta intervall” och de åtgärder som får vidtas när timers går som ”inställningen flaggor”. Till exempel hand Cluster Resource Manager tar om väntande begäranden om att skapa tjänster innan du belastningsutjämning i klustret. Som du ser av standard-tidsintervall som angetts söker Cluster Resource Manager efter allt som behövs för att göra ofta. Detta innebär normalt att uppsättning ändringar som görs under varje steg är små. Att göra små ändringar ofta kan Cluster Resource Manager kunna svara när saker i klustret. Standard-timers ger vissa batchbearbetning eftersom många av samma typer av händelser brukar sker samtidigt. 

Till exempel när noderna misslyckas de kan göra så hela feldomäner i taget. Alla de här felen samlas under nästa tillstånd uppdatera efter den *PLBRefreshGap*. Korrigeringarna som fastställs under placeringen kontroll av begränsning, och belastningsutjämning körs. Som standard Cluster Resource Manager inte genomsöker via timmars ändringar i klustret och försök att åtgärda alla ändringar på samma gång. Detta leder till ökningar av omsättning.

Cluster Resource Manager måste också ytterligare information för att avgöra om klustret imbalanced. För att vi har två andra typer av konfiguration: *BalancingThresholds* och *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Belastningsutjämning tröskelvärden
Ett tröskelvärde för belastningsutjämning är huvudkontrollen för att utlösa ombalansering. Tröskelvärde för belastningsutjämning för ett mått är ett _förhållandet_. Om belastningen för ett mått på noden mest inlästa dividerat med mängden belastningen på noden minst inlästa överskrider det måttet *BalancingThreshold*, och sedan klustret är imbalanced. Som ett resultat av nätverksbelastning utlöses vid nästa Cluster Resource Manager kontrollerar. Den *MinLoadBalancingInterval* timer definierar hur ofta Cluster Resource Manager ska kontrollera om ombalansering krävs. Kontrollerar innebär inte att något händer. 

Tröskelvärden för belastningsutjämning har definierats på basis av per mått som en del av definitionen för klustret. Mer information om mått, Kolla in [i den här artikeln](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

via ClusterConfig.json för distribution av fristående eller Template.json för Azure som värd kluster:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>
![Belastningsutjämning tröskelvärde-exempel][Image1]
</center>

I det här exemplet förbrukar en enhet av vissa mått för varje tjänst. Den maximala belastningen på en nod är fem i övre exempel och minimivärdet är två. Anta att belastningsutjämning tröskelvärdet för det här måttet är tre. Eftersom kvoten i klustret är 5/2 = 2,5 och som är mindre än det angivna tröskelvärdet på tre, klustret är belastningsutjämnat. Ingen belastningsutjämning utlöses när Cluster Resource Manager kontrollerar.

I exemplet längst ned är den maximala belastningen på en nod 10, medan minimivärdet är två, vilket resulterar i ett förhållande på fem. Fem är större än tröskelvärdet för avsedda belastningsutjämning på tre för det måttet. Därför kan blir en ombalansering kör schemalagda nästa gång belastningsutjämning utlöses. I den här situationen är vanligtvis vissa belastningen distribueras till Nod3. Eftersom Service Fabric Cluster Resource Manager inte använder en girig metod, kan även vissa belastningen distribueras till nod2. 

<center>
![Belastningsutjämning tröskelvärdet exempel åtgärder][Image2]
</center>

> [!NOTE]
> ”Belastningsutjämning” hanterar två olika strategier för att hantera belastningen i klustret. Standardstrategi som använder Cluster Resource Manager är att fördela belastningen över noder i klustret. Den andra strategin är [defragmentering](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Defragmentering utförs under samma fördelningen kör. Strategier för belastningsutjämning och defragmentering kan användas för olika mått i samma kluster. En tjänst kan ha både belastningsutjämning och defragmentering mått. Förhållandet mellan belastningar i klustret för defragmentering mått utlöser ombalansering när den är _nedan_ tröskelvärdet för belastningsutjämning. 
>

Hämta under tröskelvärdet för belastningsutjämning är inte en explicit målet. Tröskelvärden för belastningsutjämning är bara en *utlösaren*. När belastningsutjämning körningar avgör Cluster Resource Manager vilka förbättringar som det kan göra om några. Bara för att en belastningsutjämning sökning har startats innebär inte något flyttas. Klustret är ibland imbalanced men begränsad för att åtgärda. Du kan också förbättringarna kräver förflyttningar som är för [kostsamma](service-fabric-cluster-resource-manager-movement-cost.md)).

## <a name="activity-thresholds"></a>Tröskelvärden för aktivitet
Ibland, men noderna är relativt imbalanced den *totala* belastningen i klustret är låg. Bristen på belastning kan vara ett tillfälligt dip eller eftersom klustret är nytt och bara hämta startade om. I båda fallen kan du inte vill lägga tid på nätverksbelastning klustret eftersom det är onödigt att uppnås. Om klustret genomgick belastningsutjämning, skulle du ägna åt nätverket och beräkningsresurser för att flytta runt objekt utan att göra några stora *absolut* skillnaden. För att undvika flyttar onödiga, det finns en annan kontroll som kallas tröskelvärden för aktiviteten. Aktivitet tröskelvärden kan du ange vissa absolut nedre gränsen för aktiviteten. Om någon nod är över tröskeln, utlöses belastningsutjämning inte även om belastningsutjämning-tröskelvärdet har uppnåtts.

Anta att vi behåller vår tröskelvärdet för belastningsutjämning av tre för det här måttet. Anta också att vi har ett tröskelvärde för aktivitet på 1536. I det första fallet när klustret är imbalanced per belastningsutjämning tröskelvärdet som det finns uppfyller någon nod aktivitet gränsen, så händer ingenting. I exemplet längst ned är Nod1 över tröskelvärdet för aktiviteten. Eftersom både tröskelvärdet för belastningsutjämning och aktivitet tröskelvärdet för måttet överskrids, har belastningsutjämning schemalagts. Exempelvis kan du ska vi titta på diagrammet nedan: 

<center>
![Aktivitet tröskelvärdet exempel][Image3]
</center>

Tröskelvärden för aktiviteten är per-mått via klusterdefinitionen precis som tröskelvärden för belastningsutjämning:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

via ClusterConfig.json för distribution av fristående eller Template.json för Azure som värd kluster:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Tröskelvärden för belastningsutjämning och aktivitet är båda är kopplad till ett specifikt mått - belastningsutjämning aktiveras bara om både tröskelvärde för belastningsutjämning och aktivitet tröskelvärde har överskridits för samma mått.

> [!NOTE]
> Om inget värde anges belastningsutjämning tröskelvärdet för ett mått är 1 och aktivitet tröskelvärdet är 0. Det innebär att Cluster Resource Manager försöker att hålla det mått som belastningsutjämnade perfekt för all viss belastning. Om du använder anpassade mått rekommenderar vi att du explicit definierar din egen belastningsutjämning och aktivitet tröskelvärden för dina mått. 
>

## <a name="balancing-services-together"></a>Balancing services tillsammans
Om klustret är imbalanced eller inte är ett beslut i hela klustret. Dock flyttas på sätt som vi går om hur du löser den enskilda tjänstens repliker och instanser runt. Det låter väl logiskt, rätt? Om minnet är liggande på en nod, kan flera repliker eller instanser bidra till den. Åtgärda obalansen kan det krävas flytta någon av tillståndskänsliga repliker eller tillståndslösa instanser som använder imbalanced mått.

Ibland om en tjänst som inte är själva imbalanced flyttas (Kom ihåg diskussion i lokala och globala viktas tidigare). Varför skulle en tjänst flyttas när allt som tjänstens mått har balanserade? Nu ska vi se ett exempel:

- Vi antar att det finns fyra tjänster, Service1, plats2, tjänst3 och Service4. 
- Service1 rapporterar mått Metric1 och Metric2. 
- Plats2 rapporterar mått Metric2 och Metric3. 
- Tjänst3 rapporterar mått Metric3 och Metric4.
- Service4 rapporterar mått Metric99. 

Du ser sitta där vi här: Det finns en kedja! Vi har inte riktigt fyra oberoende tjänster, har vi tre tjänster som är relaterade och en som är inaktiverad på egen hand.

<center>
![Balancing Services tillsammans][Image4]
</center>

På grund av kedjan är det möjligt att en obalans i metrics 1-4 kan orsaka repliker eller instanser som hör till 1 – 3 flytta omkring-tjänster. Vi vet att en obalans i Metrics 1, 2 eller 3 inte kan orsakar förflyttningar i Service4. Blir det inte finns några sedan flytta replikerna eller instanser som hör till Service4 runt kan göra absolut ingenting om du vill påverka balans mått 1-3.

Klusterresurshanteraren räknat automatiskt ut vilka tjänster som är relaterade. Att lägga till, ta bort eller ändra måtten för tjänster som kan påverka deras relationer. Till exempel kan mellan två körningar av belastningsutjämning plats2 ha uppdaterats för att ta bort Metric2. Detta bryter kedjan mellan Service1 och plats2. Nu i stället för två grupper av relaterade tjänster finns det tre:

<center>
![Balancing Services tillsammans][Image5]
</center>

## <a name="next-steps"></a>Nästa steg
* Mått är hur Service Fabric-kluster Resource Manager hanterar förbrukning och kapacitet i klustret. Om du vill veta mer om mått och hur du konfigurerar dem kan du kolla in [i den här artikeln](service-fabric-cluster-resource-manager-metrics.md)
* Förflyttningskostnad är ett sätt att signalering till Cluster Resource Manager att vissa tjänster är dyrare att flytta än andra. Mer information om kostnader för tjänsteflytt avser [i den här artikeln](service-fabric-cluster-resource-manager-movement-cost.md)
* Klusterresurshanteraren har flera begränsningar som du kan konfigurera långsammare kärnan i klustret. De inte normalt krävs, men om du behöver dem. Du kan lära dig om dem [här](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

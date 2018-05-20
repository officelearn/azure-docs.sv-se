---
title: Balansera Azure Service Fabric-kluster | Microsoft Docs
description: En introduktion till NLB-klustret med Service Fabric klustret Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5d2f195c50750a5c7685f62c909f77b2960613e6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="balancing-your-service-fabric-cluster"></a>NLB service fabric-kluster
Service Fabric klustret Resource Manager stöder dynamisk ändringar reagera på tillägg eller borttagning av noder eller tjänster. Den korrigerar också automatiskt begränsningen överträdelser och balanserar proaktivt klustret. Hur ofta dessa åtgärder vidtas för men vad utlöser dem?

Det finns tre olika kategorier av arbete som utförs av klustret Resource Manager. De är:

1. Placering – det här steget behandlar placera alla tillståndskänslig repliker eller tillståndslösa instanser som saknas. Placering innehåller både nya tjänster och hanterar tillståndskänslig repliker eller tillståndslösa instanser som har misslyckats. Ta bort och släppa repliker eller instanser hanteras här.
2. Begränsningen kontrollerar – det här steget kontrollerar och korrigerar överträdelser av olika placeringen (regler) i systemet. Exempel på regler är till exempel att säkerställa att noderna inte är överbelastade och att en tjänst placeringen är uppfyllda.
3. Belastningsutjämning – det här steget kontrollerar om det finns ombalansering nödvändiga baserat på konfigurerad önskad tjänstnivå balansera för olika mått. I så fall försök görs att hitta en ordning i klustret som är mer balanserad.

## <a name="configuring-cluster-resource-manager-timers"></a>Konfigurera Timers för hanteraren för filserverresurser
Den första uppsättningen kontroller runt belastningsutjämning är en uppsättning timers. Dessa timers styr hur ofta klustret Resource Manager undersöker klustret och tar korrigerande åtgärder.

Var och en av dessa olika typer av klustret Resource Manager kan göra korrigeringar styrs av en annan timer som styr frekvensen. När varje timer utlöses har uppgiften schemalagts. Som standard Resource Manager:

* genomsöker dess tillstånd och tillämpar uppdateringar (till exempel inspelningen som en nod nedåt) var 1/10 sekunder
* Anger att kontrollera placering 
* Anger att begränsningen kontrollera varje sekund
* ställer in flaggan belastningsutjämning var femte sekund.

Exempel på konfigurationen för de här räknarna är nedan:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

värdbaserade kluster via ClusterConfig.json för fristående distributioner eller Template.json för Azure:

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

Idag utför klustret Resource Manager endast en av dessa åtgärder samtidigt, sekventiellt. Det är därför vi refererar till dessa timers som ”minsta intervall” och de åtgärder som får vidtas när timers går som ”inställningen flaggor”. Till exempel sköter klustret Resource Manager väntande begäranden om att skapa tjänster innan NLB-klustret. Som du ser standard tidsintervall som angetts skannar Cluster Resource Manager för allt som behövs för att göra ofta. Detta innebär normalt att uppsättning ändringar som gjorts under varje steg är liten. Ändrar mindre ofta kan klustret Resource Manager kunna svara när saker i klustret. Standard-timers ange vissa batchbearbetning eftersom många av samma typ av händelser tenderar att ske samtidigt. 

Till exempel när noder misslyckas de kan göra så att hela feldomäner i taget. Alla dessa fel avbildas under nästa tillstånd uppdatera efter den *PLBRefreshGap*. Korrigeringarna som fastställs under placeringen begränsningen kontroll och belastningsutjämning körs. Som standard klustret Resource Manager inte genomsöka via timmar för ändringar i klustret och försök att åtgärda alla ändringar på en gång. Detta leder till belastning av omsättning.

Resurshanteraren för klustret måste också ytterligare information för att avgöra om klustret imbalanced. Som vi har två delar i konfigurationen: *BalancingThresholds* och *ActivityThresholds*.

## <a name="balancing-thresholds"></a>NLB tröskelvärden
Ett tröskelvärde för belastningsutjämning är den viktigaste kontrollen för att utlösa ombalansering. NLB tröskelvärdet för ett mått är ett _förhållandet_. Om belastningen för ett mått på noden mest inlästa dividerat med mängden belastningen på noden minst inlästa överskrider den måtten *BalancingThreshold*, då är imbalanced klustret. Som ett resultat av nätverksbelastning utlöses nästa gång klustret Resource Manager kontrollerar. Den *MinLoadBalancingInterval* timer definierar hur ofta klustret Resource Manager ska kontrollera om ombalansering krävs. Kontrollera innebär inte att något händer. 

Tröskelvärden för belastningsutjämning definieras på grundval av per mått som en del av definitionen för klustret. Mer information om mått kolla [i den här artikeln](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

värdbaserade kluster via ClusterConfig.json för fristående distributioner eller Template.json för Azure:

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

I det här exemplet förbrukar en enhet av vissa mått för varje tjänst. I exemplet högsta maximala belastningen på en nod är fem och minst är två. Anta att tröskelvärdet för belastningsutjämning för det här måttet är tre. Eftersom förhållandet i klustret är 5/2 = 2,5 och som är mindre än det angivna tröskelvärdet på tre, klustret är belastningsutjämnat. Ingen belastningsutjämning utlöses när klustret Resource Manager kontrollerar.

I exemplet längst ned är maximala belastningen på en nod 10, medan minst två, vilket resulterar i ett förhållande på fem. Fem är större än tröskelvärdet för avsedda belastningsutjämning på tre för att mått. Detta innebär är en ombalansering kör schemalagda nästa gång belastningsutjämning timern utlöses. I den här situationen är vanligtvis vissa belastningen distribueras till Nod3. Eftersom Service Fabric klustret Resource Manager inte använder en girig metod, kan även vissa belastningen distribueras till nod 2. 

<center>
![Belastningsutjämning tröskelvärdet exempel åtgärder][Image2]
</center>

> [!NOTE]
> ”NLB” hanterar två olika strategier för att hantera belastningen i klustret. Standard-strategin som klustret Resource Manager använder är att fördela belastningen över noderna i klustret. Strategin som helst är [defragmentering](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Defragmentering utförs under samma fördelningen kör. Belastningsutjämning och defragmentering strategier kan användas för olika mått i samma kluster. En tjänst kan ha belastningsutjämning och defragmentering mått. Förhållandet mellan belastningar i klustret för defragmentering mått utlöser ombalansering när det är _nedan_ tröskelvärdet för belastningsutjämning. 
>

Hämta under tröskelvärdet för belastningsutjämning är inte en explicit målet. Tröskelvärden för belastningsutjämning är bara en *utlösaren*. När NLB körs avgör klustret Resource Manager vilka förbättringar som det kan göra eventuella. Bara för att en belastningsutjämning sökning har inletts innebär inte något flyttas. Ibland är klustringen imbalanced men begränsad för att åtgärda. Alternativt förbättringar kräver förflyttningar för [kostsamma](service-fabric-cluster-resource-manager-movement-cost.md)).

## <a name="activity-thresholds"></a>Tröskelvärden för aktiviteten
Ibland, men noder är relativt imbalanced den *totala* belastningen i klustret är låg. Bristen på belastning kan vara tillfälliga dip eller eftersom klustret är ny och bara hämtning startade. I båda fallen kan du inte vill ägna tid NLB klustret eftersom det är onödigt att. Om klustret genomgått belastningsutjämning, du ägnar åt nätverket och beräkna resurser för att flytta runt objekt utan att göra några stora *absolut* skillnaden. För att undvika flyttar onödiga, det finns en annan kontroll kallas tröskelvärden för aktiviteten. Aktiviteten tröskelvärden kan du ange en absolut nedre gräns för aktiviteten. Om ingen nod är över tröskeln, är inte belastningsutjämning utlösas även om tröskelvärdet för NLB är uppfyllt.

Anta att vi behåller våra tröskelvärdet för belastningsutjämning av tre för det här måttet. Anta också att vi har ett tröskelvärde för aktiviteten på 1536. I det första fallet när klustret är imbalanced per NLB tröskelvärdet som det finns uppfyller ingen nod att tröskelvärdet för aktiviteten så händer ingenting. I exemplet längst ned är Nod1 över tröskelvärdet för aktiviteten. NLB har schemalagts eftersom både tröskelvärdet för belastningsutjämning och aktivitet tröskelvärdet för måttet överskrids. Exempelvis ska vi titta på följande diagram: 

<center>
![Aktiviteten tröskelvärdet exempel][Image3]
</center>

Precis som tröskelvärden för NLB är aktiviteten tröskelvärden definierade per-mått via definitionen för klustret:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

värdbaserade kluster via ClusterConfig.json för fristående distributioner eller Template.json för Azure:

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

Tröskelvärden för belastningsutjämning och aktivitet är både knutna till ett specifikt mått - belastningsutjämning aktiveras bara om både NLB tröskelvärde och aktivitet tröskelvärde har överskridits för samma mått.

> [!NOTE]
> Om inget värde anges NLB tröskelvärdet för ett mått är 1 och aktivitet tröskelvärdet är 0. Det innebär att klustret Resource Manager försöker hålla den belastningsutjämnade perfekt för alla angivna mått. Om du använder anpassade mått rekommenderas att du explicit definiera egna belastningsutjämning och aktivitet tröskelvärden för dina. 
>

## <a name="balancing-services-together"></a>NLB tjänster tillsammans
Om klustret är imbalanced eller inte är ett hela beslut. Hur vi gå om hur du löser det flyttas enskild tjänst repliker och instanser runt. Det låter väl logiskt, rätt? Om minnet är Staplad på en nod, kan flera repliker eller instanser bidra till den. Åtgärda obalansen kan behöva flytta alla tillståndskänslig repliker och tillståndslösa instanser som använder imbalanced mått.

Ibland om en tjänst som inte är själva imbalanced flyttas (Kom ihåg diskussion av lokala och globala viktas tidigare). Varför skulle en tjänst flyttas när allt som tjänstens mått har balanserade? Låt oss se ett exempel:

- Anta att det finns fyra tjänster, Service1, plats2, tjänst3 och Service4. 
- Service1 rapporterar mått Metric1 och Metric2. 
- Plats2 rapporterar mått Metric2 och Metric3. 
- Tjänst3 rapporterar mått Metric3 och Metric4.
- Service4 rapporterar mått Metric99. 

Du kan visserligen se där vi här: det finns en kedja! Vi har inte fyra oberoende tjänster har vi tre tjänster som är relaterade och ett som är inaktiverat på sin egen.

<center>
![NLB tjänster tillsammans][Image4]
</center>

På grund av kedjan är det möjligt att obalans i mått 1-4 kan orsaka repliker eller instanser som tillhör services 1-3 för att flytta. Vi vet att en obalans i mått 1, 2 eller 3 kan orsaka förflyttningar i Service4. Det är ingen sedan flytta replikerna eller instanser som tillhör Service4 runt kan absolut ingenting om du vill påverka balans mellan mått 1-3.

Klustret Resource Manager siffror automatiskt reda på vilka tjänster som är relaterade. Lägga till, ta bort eller ändra måtten för tjänster som kan påverka deras relationer. Till exempel kan mellan två körningar av belastningsutjämning plats2 ha uppdaterats för att ta bort Metric2. Detta innebär att kedjan mellan Service1 och plats2. Nu i stället för två grupper av relaterade tjänster finns tre:

<center>
![NLB tjänster tillsammans][Image5]
</center>

## <a name="next-steps"></a>Nästa steg
* Mått är hur Service Fabric-kluster Resource Manager hanterar förbrukning och kapacitet i klustret. Mer information om mått och hur du konfigurerar dem kolla [den här artikeln](service-fabric-cluster-resource-manager-metrics.md)
* Förflyttningskostnad är ett sätt att signalering till klustret Resource Manager att vissa tjänster är dyrare att flytta än andra. Mer information om förflyttningskostnad avser [i den här artikeln](service-fabric-cluster-resource-manager-movement-cost.md)
* Klustret Resource Manager har flera begränsningar som du kan konfigurera långsammare kärnan i klustret. De inte normalt krävs, men om du behöver dem kan du läsa om dem [här](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

---
title: Balansera ditt Azure Service Fabric-kluster
description: En introduktion till att balansera klustret med Service Fabric Cluster Resource Manager.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 8e170c27923d2bb091c4121e350809b85e4c48a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081700"
---
# <a name="balancing-your-service-fabric-cluster"></a>Balansera ditt serviceinfrastrukturkluster
Service Fabric Cluster Resource Manager stöder dynamiska belastningsändringar som reagerar på tillägg eller borttagningar av noder eller tjänster. Det korrigerar också automatiskt begränsningsöverträdelser och balanserar proaktivt om klustret. Men hur ofta vidtas dessa åtgärder, och vad utlöser dem?

Det finns tre olika kategorier av arbete som Klusterresurshanteraren utför. De är:

1. Placering – det här steget handlar om att placera alla tillståndskänsliga repliker eller tillståndslösa instanser som saknas. Placering omfattar både nya tjänster och hantering av tillståndskänsliga repliker eller tillståndslösa instanser som har misslyckats. Här hanteras och släpps repliker eller instanser.
2. Begränsningskontroller – det här steget söker efter och korrigerar överträdelser av de olika placeringsbegränsningarna (reglerna) i systemet. Exempel på regler är saker som att se till att noder inte är över kapacitet och att en tjänsts placeringsbegränsningar uppfylls.
3. Balansering – det här steget kontrollerar om ombalansering är nödvändig baserat på den konfigurerade önskade balansnivån för olika mått. Om så är fallet försöker hitta ett arrangemang i klustret som är mer balanserat.

## <a name="configuring-cluster-resource-manager-timers"></a>Konfigurera timerer för klusterresurshanteraren
Den första uppsättningen kontroller runt balansering är en uppsättning timers. Dessa timers styr hur ofta Cluster Resource Manager undersöker klustret och vidtar korrigerande åtgärder.

Var och en av dessa olika typer av korrigeringar som Cluster Resource Manager kan göra styrs av en annan timer som styr dess frekvens. När varje timer utlöses schemaläggs aktiviteten. Som standard Resurshanteraren:

* skannar sitt tillstånd och tillämpar uppdateringar (som inspelning av att en nod är nere) var 1/10:e sekund
* ställer in flaggan för placeringskontroll varje sekund
* ställer in flaggan för begränsningskontroll varje sekund
* ställer in balanseringsflaggan var femte sekund

Exempel på konfigurationen som styr dessa timers är nedan:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

via ClusterConfig.json för fristående distributioner eller Template.json för Azure-värdkluster:

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

I dag utför Klusterresurshanteraren bara en av dessa åtgärder åt gången, sekventiellt. Det är därför vi kallar dessa timers som "minsta intervall" och de åtgärder som vidtas när timers går ut som "ställa flaggor". Klusterresurshanteraren tar till exempel hand om väntande begäranden om att skapa tjänster innan klustret balanseras. Som du kan se med de angivna standardtidsintervallen söker Klusterresurshanteraren efter allt som behövs för att göra ofta. Normalt innebär detta att uppsättningen ändringar som görs under varje steg är liten. Genom att göra små ändringar ofta kan Klusterresurshanteraren reagera när saker händer i klustret. Standard timers ger vissa batchbearbetning eftersom många av samma typer av händelser tenderar att inträffa samtidigt. 

Till exempel, när noder misslyckas de kan göra det hela fel domäner i taget. Alla dessa fel fångas under nästa tillstånd uppdatering efter *PLBRefreshGap*. Korrigeringarna bestäms under följande placering, begränsningskontroll och balanseringskörningar. Som standard söker inte Cluster Resource Manager igenom timmar av ändringar i klustret och försöker åtgärda alla ändringar samtidigt. Detta skulle leda till skurar av churn.

Klusterresurshanteraren behöver också ytterligare information för att avgöra om klustret har obalanserat. För att vi har två andra delar av konfiguration: *BalancingThresholds* och *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Balansera tröskelvärden
En balanseringströskel är huvudkontrollen för att utlösa ombalansering. Balanseringströskeln för ett mått är ett _förhållande_. Om belastningen för ett mått på den mest inlästa noden dividerat med mängden belastning på den minst inlästa noden överskrider måttets *BalancingThreshold*, är klustret obalanserat. Som en resultatbalansering utlöses nästa gång Cluster Resource Manager checkar. *MinLoadBalancingInterval-timern* definierar hur ofta Klusterresurshanteraren ska kontrollera om ombalansering är nödvändig. Kontroll betyder inte att något händer. 

Balanseringströsklar definieras per mått som en del av klusterdefinitionen. Mer information om mått finns i [den här artikeln](service-fabric-cluster-resource-manager-metrics.md).

KlusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

via ClusterConfig.json för fristående distributioner eller Template.json för Azure-värdkluster:

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

![Exempel på balanseringströskel][Image1]
</center>

I det här exemplet förbrukar varje tjänst en enhet med vissa mått. I det översta exemplet är den maximala belastningen på en nod fem och den minsta är två. Anta att balanseringströskeln för det här måttet är tre. Eftersom förhållandet i klustret är 5/2 = 2,5 och det är mindre än det angivna balanseringströskeln på tre, balanseras klustret. Ingen avvägning utlöses när Cluster Resource Manager checkar.

I det nedre exemplet är den maximala belastningen på en nod 10, medan den minsta är två, vilket resulterar i ett förhållande på fem. Fem är större än den angivna balanseringströskeln på tre för det måttet. Därför schemaläggs en ombalanseringskörning nästa gång balanserings timern utlöses. I en situation som denna delas viss belastning vanligtvis ut till Node3. Eftersom Service Fabric Cluster Resource Manager inte använder en girig metod kan en viss belastning också distribueras till Node2. 

<center>

![Exempel på utjämningströskel][Image2]
</center>

> [!NOTE]
> "Balansering" hanterar två olika strategier för att hantera belastning i klustret. Standardstrategin som Klusterresurshanteraren använder är att distribuera belastningen över noderna i klustret. Den andra strategin är [defragmentering](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Defragmentering utförs under samma balanseringskörning. Strategier för balansering och defragmentering kan användas för olika mått i samma kluster. En tjänst kan ha både balansera och defragmenteringsmått. För defragmenteringsmått utlöser förhållandet mellan belastningarna i klustret ombalansering när det ligger _under_ balanseringströskeln. 
>

Att komma under balanseringströskeln är inte ett uttryckligt mål. Balanseringströsklar är bara en *utlösare*. När du balanserar körs avgör Klusterresurshanteraren vilka förbättringar den kan göra, om några. Bara för att en balansering sökning sparkas igång betyder inte något rör sig. Ibland är klustret obalanserat men för begränsat för att korrigera. Alternativt kräver förbättringarna för [kostsamma](service-fabric-cluster-resource-manager-movement-cost.md)rörelser).

## <a name="activity-thresholds"></a>Tröskelvärden för aktivitet
Ibland, även om noder är relativt obalanserade, är den *totala* mängden belastning i klustret låg. Bristen på belastning kan vara en övergående dopp, eller för att klustret är nytt och bara få bootstrapped. I båda fallen kanske du inte vill spendera tid på att balansera klustret eftersom det finns lite att vinna. Om klustret genomgick balansering, skulle du spendera nätverk och beräkna resurser för att flytta runt saker utan att göra någon *stor absolut* skillnad. För att undvika onödiga rörelser finns det en annan kontroll som kallas aktivitetströsklar. Med aktivitetströskelvärden kan du ange några absolut nedre gräns för aktivitet. Om ingen nod överskrider det här tröskelvärdet utlöses inte avvägning även om balanseringströskeln uppfylls.

Låt oss säga att vi behåller vår balanseringströskel på tre för det här måttet. Låt oss också säga att vi har en aktivitetströskel på 1536. I det första fallet, medan klustret är obalanserat per balanseringströskeln, finns det ingen nod som uppfyller det aktivitetströskeln, så ingenting händer. I det nedre exemplet ligger Nod1 över aktivitetströskeln. Eftersom både balanseringströskeln och aktivitetströskeln för måttet överskrids, schemaläggs avvägning. Låt oss till exempel titta på följande diagram: 

<center>

![Exempel på aktivitetströskel][Image3]
</center>

Precis som balanseringströsklar definieras aktivitetströsklar per mått via klusterdefinitionen:

KlusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

via ClusterConfig.json för fristående distributioner eller Template.json för Azure-värdkluster:

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

Balanserings- och aktivitetströsklar är båda kopplade till ett visst mått - avvägning utlöses endast om både balanseringströskeln och aktivitetströskeln överskrids för samma mått.

> [!NOTE]
> När det inte anges är balanseringströskeln för ett mått 1 och aktivitetströskeln 0. Det innebär att Cluster Resource Manager försöker hålla måttet perfekt balanserat för en viss belastning. Om du använder anpassade mått rekommenderar vi att du uttryckligen definierar dina egna balanserings- och aktivitetströsklar för dina mått. 
>

## <a name="balancing-services-together"></a>Balansera tjänster tillsammans
Om klustret är obalanserat eller inte är ett klusteromfattande beslut. Men det sätt vi går om fastställande det är att flytta enskilda tjänst repliker och instanser runt. Det här låter vettigt, eller hur? Om minnet är staplat på en nod kan flera repliker eller instanser bidra till det. Åtgärda obalansen kan kräva att någon av de tillståndskänsliga replikerna eller tillståndslösa instanser som använder det obalanserade måttet flyttas.

Ibland dock, en tjänst som inte var i sig obalanser flyttas (kom ihåg diskussionen om lokala och globala vikter tidigare). Varför skulle en tjänst flyttas när alla tjänstens mått var balanserade? Låt oss se ett exempel:

- Anta att det finns fyra tjänster, Service1, Service2, Service3 och Service4. 
- Service1 rapporterar mått1 och Metric2. 
- Service2 rapporterar mått12 och Metric3. 
- Service3 rapporterar mått3 och Metric4.
- Service4 rapporterar måttmått199. 

Visst kan du se vart vi är på väg här: Det finns en kedja! Vi har egentligen inte fyra oberoende tjänster, vi har tre tjänster som är relaterade och en som är avstängd på egen hand.

<center>

![Balansera tjänster tillsammans][Image4]
</center>

På grund av den här kedjan är det möjligt att en obalans i mått 1-4 kan orsaka repliker eller instanser som tillhör tjänster 1-3 att flytta runt. Vi vet också att en obalans i Metrisk 1, 2 eller 3 inte kan orsaka rörelser i Service4. Det skulle inte vara någon mening eftersom flytta repliker eller instanser som tillhör Service4 runt kan göra absolut ingenting för att påverka balansen i Mått 1-3.

Klusterresurshanteraren räknar automatiskt ut vilka tjänster som är relaterade. Om du lägger till, tar du bort eller ändrar måtten för tjänster kan det påverka deras relationer. Mellan två körningar av balanseringstjänst2 kan till exempel ha uppdaterats för att ta bort Metric2. Detta bryter kedjan mellan Service1 och Service2. Nu i stället för två grupper av relaterade tjänster, det finns tre:

<center>

![Balansera tjänster tillsammans][Image5]
</center>

## <a name="next-steps"></a>Nästa steg
* Mått är hur Service Fabric Cluster Resource Manger hanterar förbrukning och kapacitet i klustret. Om du vill veta mer om mått och hur du konfigurerar dem kan du läsa [den här artikeln](service-fabric-cluster-resource-manager-metrics.md)
* Rörelsekostnad är ett sätt att signalera till Cluster Resource Manager att vissa tjänster är dyrare att flytta än andra. Mer information om rörelsekostnader finns i [den här artikeln](service-fabric-cluster-resource-manager-movement-cost.md)
* Klusterresurshanteraren har flera begränsningar som du kan konfigurera för att sakta ned omsättningen i klustret. De är normalt inte nödvändigt, men om du behöver dem kan du lära dig om dem [här](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

---
title: Defragmentering av mätvärden i Azure Service Fabric | Microsoft Docs
description: En översikt över använder defragmentering eller packa som en strategi för mått i Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: a35ae5933729615d634359e64e31d43536d81431
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205084"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentering av mätvärden och belastningen i Service Fabric
Service Fabric klustret resurshanterare standard strategi för att hantera belastningen mått i klustret är att fördela belastningen. Se till att noderna är jämnt utnyttjade undviker varm eller kall punkter som leda till både konkurrens och oanvänt resurser. Distribution av arbetsbelastningar i klustret är också den säkraste vad gäller kvarvarande fel eftersom det garanterar att fel inte ta ut en stor del av en viss arbetsbelastning. 

Service Fabric klustret Resource Manager stöder en annan strategi för att hantera belastningen som är defragmentering. Defragmentering innebär att i stället att distribuera användningen av ett mått i klustret, det konsolideras. Konsolidering är bara en inverteras standard strategi – i stället för att minimera genomsnittlig standardavvikelsen för mått load balancing, klustret Resource Manager försöker öka den.

## <a name="when-to-use-defragmentation"></a>När du ska använda defragmentering
Distribuera belastning i klustret förbrukar resurser på varje nod. Vissa arbetsbelastningar skapa tjänster som är ovanligt stort och använda de flesta eller alla av en nod. Det är möjligt att när det finns stora arbetsbelastningar komma skapas som det finns inte tillräckligt med utrymme på någon nod för att kunna köra dem i dessa fall. Stora arbetsbelastningar är inte ett problem i Service Fabric; i dessa fall anger resurshanteraren klustret måste ordna om klustret för att göra plats för stora arbetsbelastningen. Under tiden har att arbetsbelastningar dock att vänta på att schemaläggas i klustret.

Om det finns många tjänster och tillstånd för att flytta runt kan ta det lång tid för stora arbetsbelastningen ska placeras i klustret. Det är troligt om andra arbetsbelastningar i klustret är också stora och så tar längre tid att ordna om. Service Fabric-teamet mätt skapa gånger under simulering av det här scenariot. Påträffades att skapa stora services tog mycket längre så snart klusteranvändning fick ovan mellan 30 och 50%. Om du vill hantera det här scenariot, introduceras vi defragmentering som en strategi för belastningsutjämning. Påträffades att för stora arbetsbelastningar, särskilt de som där skapelsetid var viktigt defragmentering verkligen hjälpt dessa nya arbetsbelastningar få schemalagda i klustret.

Du kan konfigurera defragmentering mått om du vill att klustret Resource Manager försöker att minska belastningen på tjänsterna i färre noder. Detta säkerställer att det finns nästan alltid plats för stora tjänster utan att organisera om klustret. Inte behöver ordna om klustret kan du snabbt skapa stora arbetsbelastningar.

De flesta användare behöver inte defragmentering. Tjänster är vanligtvis vara liten, så det inte är svårt att hitta utrymme för dem i klustret. När det är möjligt om, går snabbt igen eftersom de flesta tjänster är små och kan flyttas snabbt och parallellt. Om du har stora tjänster och behöver dem snabbt skapa sedan defragmentering strategin är för dig. Diskuterar vi nackdelar med att använda defragmentering nästa. 

## <a name="defragmentation-tradeoffs"></a>Defragmentering kompromisser
Defragmentering kan öka impactfulness fel, eftersom flera tjänster som körs på noder som misslyckas. Defragmentering kan också öka kostnader, eftersom resurser i klustret måste hållas i reserv, väntar på att skapa stora arbetsbelastningar.

Följande diagram ger en bild av två kluster, ett som är defragmenteras och ett som inte är. 

<center>
![Jämföra belastningsutjämnade och defragmenteras kluster][Image1]
</center>

Överväg att antalet förflyttningar som behövs för att placera en av de största serviceobjekt i det belastningsutjämnade fallen. I klustret defragmenteras kan stora arbetsbelastningen placeras på noder fyra eller fem utan att behöva vänta på att andra tjänster att flytta.

## <a name="defragmentation-pros-and-cons"></a>Defragmentering- och nackdelar
Vad är så de konceptuella kompromisser? Här är en snabb tabell med saker att tänka på:

| Defragmentering tekniker | Defragmentering nackdelar |
| --- | --- |
| Tillåter snabbare skapandet av stora tjänster |Koncentrat laddas till färre noder, öka konkurrens |
| Aktiverar lägre dataflyttning under skapande av |Fel kan påverka flera tjänster och orsaka mer omsättning |
| Tillåter omfattande beskrivning av kraven och frigöring av utrymme |Mer komplexa övergripande resurshantering-konfiguration |

Du kan blanda defragmenteras och normal mått i samma kluster. Klustret Resource Manager försöker konsolidera defragmentering mätvärdena så mycket som möjligt och samtidigt sprider ut de andra. Resultaten av blanda defragmentering och belastningsutjämning strategier beror på flera faktorer, inklusive:
  - antalet NLB mått jämfört med antal defragmentering mått
  - Om alla tjänster som använder båda typerna av mått 
  - mått vikterna
  - aktuella måttet läses in
  
Experiment krävs för att bestämma den exakta konfigurationen som krävs. Vi rekommenderar noggrann mätning av dina arbetsbelastningar innan du aktiverar defragmentering mått i produktion. Detta gäller särskilt när blanda defragmentering och belastningsutjämnade mått inom samma tjänst. 

## <a name="configuring-defragmentation-metrics"></a>Konfigurera defragmentering mått
Konfigurera defragmentering mått är ett globalt beslut i klustret och enskilda mått kan väljas för defragmentering. Följande config kodavsnitt visar hur du konfigurerar mätvärden för defragmentering. I det här fallet är ”Metric1” konfigurerad som en defragmentering mått ”Metric2” kommer att fortsätta att balansera normalt. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

värdbaserade kluster via ClusterConfig.json för fristående distributioner eller Template.json för Azure:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Nästa steg
- Klustret Resource Manager har man alternativ för att beskriva klustret. Kolla in den här artikeln om du vill veta mer om dem på [som beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
- Mått är hur Service Fabric-kluster Resource Manager hanterar förbrukning och kapacitet i klustret. Mer information om mått och hur du konfigurerar dem kolla [den här artikeln](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png

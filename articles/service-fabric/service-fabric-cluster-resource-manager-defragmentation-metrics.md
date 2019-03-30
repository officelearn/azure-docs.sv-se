---
title: Defragmentering av mått i Azure Service Fabric | Microsoft Docs
description: En översikt över med hjälp av defragmentering eller späcka som en strategi för mått i Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6e041e41372c72c6792c1fb4a1fbdc3bbe475b21
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661658"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentering av mått och belastningen i Service Fabric
Den Service Fabric Cluster Resource Manager standardstrategi för att hantera inläsningsmåtten i klustret är att fördela belastningen. Se till att noder används jämnt undviker heta och kalla vinklar som leder till både konkurrens och oanvänt resurser. Distribuera arbetsbelastningar i klustret är också den säkraste när det gäller kvarvarande fel eftersom det garanterar att ett fel inte ta ut en stor del av en viss arbetsbelastning. 

Service Fabric Cluster Resource Manager stöder en egen strategi för att hantera belastningen, vilket är defragmentering. Defragmentering innebär att i stället att distribuera användningen av ett mått i klustret, den konsolideras. Konsolidering är bara en spegelvändning av standard belastningsutjämning strategi – i stället för att minimera genomsnittlig standardavvikelsen för metriska belastning, Cluster Resource Manager försöker öka den.

## <a name="when-to-use-defragmentation"></a>När du ska använda defragmentering
Distribuera belastningen i klustret förbrukar resurser på varje nod. Vissa arbetsbelastningar skapa tjänster som är ovanligt stora och använda de flesta eller alla av en nod. Det är möjligt att när det finns stora arbetsbelastningar som skapas som det finns inte tillräckligt med utrymme på någon nod för att köra dem i dessa fall. Stora arbetsbelastningar är inte ett problem i Service Fabric; i dessa fall anger Cluster Resource Manager att den behöver att ordna om klustret för att göra plats för stora arbetsbelastningen. Men har under tiden den arbetsbelastningen väntetiden för att schemaläggas i klustret.

Om det finns många tjänster och tillstånd för att flytta runt kan ta det lång tid för stora arbetsbelastningar kan placeras i klustret. Det är mer troligt om andra arbetsbelastningar i klustret är också stora och så tar längre tid att ordna om. Service Fabric-teamet mätt skapa gånger under simuleringar av det här scenariot. Vi har upptäckt att skapa stora tjänster tog mycket längre tid när klusteranvändning fick ovan mellan 30% respektive 50%. För att hantera det här scenariot introducerade vi defragmentering som en strategi för belastningsutjämning. Vi har upptäckt att för stora arbetsbelastningar, särskilt de som där Skapandetid var viktigt, defragmentering verkligen hjälpte dessa nya arbetsbelastningar få schemalagda i klustret.

Du kan konfigurera defragmentering mått om du vill att Cluster Resource Manager att proaktivt försöka minska belastningen på tjänsterna i färre noder. Detta hjälper till att säkerställa att det är nästan alltid utrymme för stora tjänster utan att organisera klustret. Inte behöver ordna om klustret kan att snabbt skapa stora arbetsbelastningar.

De flesta användare behöver inte defragmentering. Tjänster är vanligtvis vara liten, så det inte är svårt att hitta utrymme för dem i klustret. När det är möjligt om, går snabbt, igen eftersom de flesta tjänster är små och kan flyttas snabbt och parallellt. Om du har stora tjänster och behöver dem skapas sedan snabbt defragmentering strategin är för dig. Vi kommer att diskutera nackdelar med att använda defragmentering bredvid. 

## <a name="defragmentation-tradeoffs"></a>Defragmentering kompromisser
Defragmentering kan öka impactfulness fel, eftersom flera tjänster som körs på noder som misslyckas. Defragmentering kan också öka kostnaderna, eftersom resurserna i klustret måste hållas reserv, väntar på att skapa stora arbetsbelastningar.

Följande diagram ger en visuell representation av två kluster, ett som är defragmenteras och som inte är. 

<center>

![Jämföra balanserade och defragmenteras kluster][Image1]
</center>

Överväg att antalet förflyttningar som skulle vara nödvändigt att placera en av de största serviceobjekt i belastningsutjämnade fall. I defragmenteras klustret, kan arbetsbelastningen stora placeras på noder fyra eller fem utan att behöva vänta på att andra tjänster att flytta.

## <a name="defragmentation-pros-and-cons"></a>Defragmentering- och nackdelar
Så vilka är de konceptuella kompromisser? Här är en snabb tabell med saker att tänka på:

| Defragmentering-tekniker | Defragmentering nackdelar |
| --- | --- |
| Får snabbare skapas av stora tjänster |Koncentrat läsa in till färre noder, vilket ökar konkurrensen |
| Aktiverar lägre dataförflyttning när skapas |Fel kan påverka fler tjänster och orsaka mer omsättning |
| Tillåter omfattande beskrivning av krav och frigöring av utrymme |Mer komplex övergripande Resource Management-konfiguration |

Du kan blanda defragmenteras och normal mått i samma kluster. Cluster Resource Manager försöker konsolidera defragmentering måtten så mycket som möjligt samtidigt som sprider ut de andra. Resultatet av blanda defragmentering och belastningsutjämning strategier beror på flera faktorer, bland annat:
  - antalet belastningsutjämning mått jämfört med antal defragmentering mått
  - Om någon tjänst använder båda typerna av mått 
  - Metrisk vikterna
  - aktuella mått har lästs in
  
Experimentering krävs för att bestämma den exakta konfigurationen som krävs. Vi rekommenderar noggrann mätning av dina arbetsbelastningar innan du aktiverar defragmentering mått i produktion. Detta gäller särskilt när blanda defragmentering och belastningsutjämnade mått inom samma tjänst. 

## <a name="configuring-defragmentation-metrics"></a>Konfigurera defragmentering mått
Konfigurera defragmentering mått är en global beslut i klustret och enskilda mått kan väljas för defragmentering. Config följande kodfragment visar hur du konfigurerar mått för defragmentering. I det här fallet är ”Metric1” konfigurerad som en defragmentering mått ”Metric2” kommer att fortsätta att balansera normalt. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

via ClusterConfig.json för distribution av fristående eller Template.json för Azure som värd kluster:

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
- Klusterresurshanteraren har man alternativ för att beskriva klustret. Om du vill veta mer om dem, Kolla in den här artikeln på [som beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
- Mått är hur Service Fabric-kluster Resource Manager hanterar förbrukning och kapacitet i klustret. Om du vill veta mer om mått och hur du konfigurerar dem kan du kolla in [i den här artikeln](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png

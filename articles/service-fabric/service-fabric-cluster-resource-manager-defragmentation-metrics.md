---
title: Defragmentering av mått i Azure Service Fabric
description: Lär dig mer om hur du använder defragmentering eller packning som en strategi för mått i Service Fabric. Denna teknik är användbar för mycket stora tjänster.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bba459be4408f4a4bc438bb33b0570a91e84f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563368"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentering av mätvärden och belastning i Service Fabric
Service Fabric Cluster Resource Manager standardstrategi för hantering av belastningsmått i klustret är att distribuera belastningen. Se till att noder används jämnt undviker varma och kalla fläckar som leder till både stridigheter och bortkastade resurser. Att distribuera arbetsbelastningar i klustret är också det säkraste när det gäller att överleva fel eftersom det säkerställer att ett fel inte tar ut en stor andel av en viss arbetsbelastning. 

Service Fabric Cluster Resource Manager stöder en annan strategi för att hantera belastning, vilket är defragmentering. Defragmentering innebär att i stället för att försöka distribuera användningen av ett mått över klustret konsolideras det. Konsolidering är bara en inversion av standardbalanseringsstrategin – i stället för att minimera den genomsnittliga standardavvikelsen för måttbelastning försöker Klusterresurshanteraren öka den.

## <a name="when-to-use-defragmentation"></a>När ska defragmentering
Distribuera belastning i klustret förbrukar några av resurserna på varje nod. Vissa arbetsbelastningar skapar tjänster som är exceptionellt stora och förbrukar de flesta eller alla av en nod. I dessa fall är det möjligt att när det finns stora arbetsbelastningar skapas att det inte finns tillräckligt med utrymme på någon nod för att köra dem. Stora arbetsbelastningar är inte ett problem i Service Fabric; I dessa fall avgör Cluster Resource Manager att den måste omorganisera klustret för att göra plats för den här stora arbetsbelastningen. Under tiden måste dock arbetsbelastningen vänta på att schemaläggas i klustret.

Om det finns många tjänster och tillstånd att flytta runt, kan det ta lång tid för den stora arbetsbelastningen att placeras i klustret. Detta är mer troligt om andra arbetsbelastningar i klustret också är stora och så tar längre tid att omorganisera. Service Fabric-teamet mätte skapandetider i simuleringar av det här scenariot. Vi fann att skapa stora tjänster tog mycket längre tid så fort klusterutnyttjandet kom över mellan 30% och 50%. För att hantera det här scenariot introducerade vi defragmentering som en balanseringsstrategi. Vi fann att för stora arbetsbelastningar, särskilt de där skapande tid var viktigt, defragmentering verkligen hjälpt dessa nya arbetsbelastningar få schemaläggas i klustret.

Du kan konfigurera defragmenteringsmått så att klusterresurshanteraren proaktivt försöker kondensera belastningen av tjänsterna till färre noder. Detta bidrar till att säkerställa att det nästan alltid finns utrymme för stora tjänster utan att omorganisera klustret. Om du inte behöver omorganisera klustret kan du snabbt skapa stora arbetsbelastningar.

De flesta människor behöver inte defragmentering. Tjänsterna är vanligtvis små, så det är inte svårt att hitta utrymme för dem i klustret. När omorganisation är möjlig går den snabbt, igen eftersom de flesta tjänster är små och kan flyttas snabbt och parallellt. Men om du har stora tjänster och behöver dem skapas snabbt då defragmentering strategi är för dig. Vi diskuterar kompromisserna med att använda defragmentering nästa gång. 

## <a name="defragmentation-tradeoffs"></a>Avvägningar för defragmentering
Defragmentering kan öka effekten av fel, eftersom fler tjänster körs på noder som misslyckas. Defragmentering kan också öka kostnaderna, eftersom resurser i klustret måste hållas i reserv, i väntan på att skapa stora arbetsbelastningar.

Följande diagram ger en visuell representation av två kluster, ett som defragmenteras och en som inte är det. 

<center>

![Jämföra balanserade och defragmenterade kluster][Image1]
</center>

I det balanserade fallet bör du tänka på hur många rörelser som skulle vara nödvändiga för att placera ett av de största serviceobjekten. I det defragmenterade klustret kan den stora arbetsbelastningen placeras på noderna fyra eller fem utan att behöva vänta på att andra tjänster ska flyttas.

## <a name="defragmentation-pros-and-cons"></a>Defragmentering för-och nackdelar
Så vad är de andra konceptuella kompromisser? Här är en snabb tabell över saker att tänka på:

| Defragmentering Fördelar | Defragmentering Nackdelar |
| --- | --- |
| Möjliggör snabbare skapande av stora tjänster |Koncentrerar belastningen på färre noder, vilket ökar |
| Möjliggör lägre datarörelser när du skapar |Fel kan påverka fler tjänster och orsaka mer omsättning |
| Tillåter omfattande beskrivning av krav och återvinning av utrymme |Mer komplex övergripande konfiguration av resurshantering |

Du kan blanda defragmenterade och normala mått i samma kluster. Klusterresurshanteraren försöker konsolidera defragmenteringsmåtten så mycket som möjligt samtidigt som de andra sprids. Resultaten av att blanda defragmentering och balanseringsstrategier beror på flera faktorer, bland annat:
  - antalet balanserande mått jämfört med antalet defragmenteringsmått
  - Om någon tjänst använder båda typerna av mått 
  - de metriska vikterna
  - aktuella måttbelastningar
  
Experiment krävs för att bestämma den exakta konfiguration som krävs. Vi rekommenderar noggrann mätning av dina arbetsbelastningar innan du aktiverar defragmenteringsmått i produktionen. Detta gäller särskilt vid blandning av defragmentering och balanserade mått inom samma tjänst. 

## <a name="configuring-defragmentation-metrics"></a>Konfigurera defragmenteringsmått
Konfigurera defragmenteringsmått är ett globalt beslut i klustret och enskilda mått kan väljas för defragmentering. Följande konfigurationskodavsnitt visar hur du konfigurerar mått för defragmentering. I det här fallet är "Metric1" konfigurerat som ett defragmenteringsmått, medan "Metric2" fortsätter att vara balanserat normalt. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

via ClusterConfig.json för fristående distributioner eller Template.json för Azure-värdkluster:

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
- Klusterresurshanteraren har man-alternativ för att beskriva klustret. Om du vill veta mer om dem kan du läsa den här artikeln om [hur du beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
- Mått är hur Service Fabric Cluster Resource Manger hanterar förbrukning och kapacitet i klustret. Om du vill veta mer om mått och hur du konfigurerar dem kan du läsa [den här artikeln](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png

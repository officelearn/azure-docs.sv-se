---
title: Balansering av subclustered mått
description: Effekten av placeringsbegränsningar på balansering och hur man hanterar det
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 23782a86d31251cb1a3474e0395df716a2e832df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430648"
---
# <a name="balancing-of-subclustered-metrics"></a>Balansering av subclustered mått

## <a name="what-is-subclustering"></a>Vad är subclustering

Subclustering inträffar när tjänster med olika placeringsbegränsningar har ett gemensamt mått och de båda rapporterar belastning för det. Om belastningen som rapporteras av tjänsterna skiljer sig avsevärt, kommer den totala belastningen på noderna att ha en stor standardavvikelse och det skulle se ut som om klustret är obalanserat, även när det har bästa möjliga balans.

## <a name="how-subclustering-affects-load-balancing"></a>Hur delkluseringen påverkar belastningsutjämning

Om belastningen som rapporteras av tjänsterna på olika noder skiljer sig avsevärt, kan det se ut som det finns en stor obalans där det inte finns någon. Om den falska obalansen som orsakas av delkluseringen är större än den faktiska obalansen, har den också potential att förvirra resurshanterarens balanseringsalgoritm och skapa suboptimal balans i klustret.

Anta till exempel att vi har fyra tjänster och att alla rapporterar en belastning för måttet Metric1:

* Tjänst A – har ett placeringsvillkor "NodeType==Type1", rapporterar en belastning på 10
* Tjänst B – har ett placeringsvillkor "NodeType==Type1", rapporterar en belastning på 10
* Tjänst C – har ett placeringsvillkor "NodeType==Type2", rapporterar en belastning på 100
* Service D – har ett placeringsvillkor "NodeType==Type2", rapporterar en belastning på 100
* Och vi har fyra noder. Två av dem har NodeType som "Type1" och de andra två är "Type2"

Och vi har följande placering:

<center>

![Exempel på subkluserad placering][Image1]
</center>

Klustret kan se obalanserat ut, vi har en stor belastning på noderna 3 och 4, men den här placeringen skapar bästa möjliga balans i den här situationen.

Resource Manager kan känna igen subclustering situationer och i nästan alla fall kan det ge den optimala balansen för den givna situationen.

För vissa exceptionella situationer när Resource Manager inte kan optimalt balansera ett subclustered mått det kommer fortfarande att upptäcka subclustering och det kommer att generera en hälsorapport för att råda dig att åtgärda problemet.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Typer av delklusive och hur de hanteras

Subclustering situationer kan delas in i tre olika kategorier. Kategorin för en specifik subclustering situation avgör hur den ska hanteras av Resource Manager.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>Första kategorin – platt delklus med osammanhängande nodgrupper

Den här kategorin har den enklaste formen av subclustering där noder kan delas upp i olika grupper och varje tjänst endast kan placeras på noder i en av dessa grupper. Varje nod tillhör endast en grupp och en grupp. Den situation som beskrivs ovan hör hemma i denna kategori liksom de flesta av de subclustering situationer. 

För situationerna i den här kategorin kan Resurshanteraren skapa den optimala balansen och inga ytterligare åtgärder behövs.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Andra kategorin – subclustering med hierarkiska nodgrupper

Den här situationen inträffar när en grupp noder som tillåts för en tjänst är en delmängd av den grupp noder som tillåts för en annan tjänst. Det vanligaste exemplet på den här situationen är när vissa tjänster har en definierad placeringsbegränsning och en annan tjänst inte har någon placeringsbegränsning och kan placeras på vilken nod som helst.

Exempel:

* Tjänst A: ingen placeringsbegränsning
* Tjänst B: placeringsvillkor "NodeType==Type1"
* Tjänst C: placeringsvillkor "NodeType==Type2"

Den här konfigurationen skapar en delmängds-supersetrelation mellan nodgrupper för olika tjänster.

<center>

![Delmängd superset subkluster][Image2]
</center>

I denna situation finns det en chans att en suboptimal balans blir gjord.

Resource Manager kommer att känna igen den här situationen och producera en hälsorapport som ger dig råd att dela upp tjänst A i två tjänster – Service A1 som kan placeras på typ1-noder och tjänst A2 som kan placeras på Type2-noder. Detta kommer att föra oss tillbaka till första kategorin situation som kan balanseras optimalt.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Tredje kategorin – subclustering med partiell överlappning mellan noduppsättningar

Den här situationen inträffar när det finns en partiell överlappning mellan uppsättningar av noder som vissa tjänster kan placeras på.

Om vi till exempel har en nodegenskap som heter NodeColor och vi har tre noder:

* Nod 1: NodeColor=Röd
* Nod 2: NodeColor=Blå
* Nod 2: NodeColor=Grön

Och vi har två tjänster:

* Tjänst A: med placeringsvillkor "Color==Red || Färg==Blå"
* Tjänst B: med placeringsvillkor "Color==Blue || Färg==Grön"

På grund av detta kan service A placeras på noderna 1 och 2 och service B kan placeras på noderna 2 och 3.

I denna situation finns det en chans att en suboptimal balans blir gjord.

Resource Manager kommer att känna igen den här situationen och ta fram en hälsorapport som rekommenderar dig att dela upp några av tjänsterna.

I det här fallet kan Resurshanteraren inte ge ett förslag om hur tjänsterna ska delas upp, eftersom flera delningar kan göras och det inte finns något sätt att uppskatta vilken väg som skulle vara den optimala för att dela upp tjänsterna.

## <a name="configuring-subclustering"></a>Konfigurera underkräpning

Resurshanterarens beteende vid delklustering kan ändras genom att ändra följande konfigurationsparametrar:
* SubclusteringEnabled - parametern avgör om Resource Manager tar med sig att ta hänsyn till delkluseringen när belastningsutjämningen sker. Om den här parametern är inaktiverad ignorerar Resource Manager underkval och försöker uppnå optimal balans på global nivå. Standardvärdet för den här parametern är falskt.
* SubclusteringReportingPolicy - bestämmer hur Resource Manager ska avge hälsorapporter för hierarkisk och partiell överlappningsunderklystna. Värdet noll innebär att hälsorapporter om delkluseringen är inaktiverade, "1" innebär att varningshälsorapporter kommer att tas fram för suboptimala subclustering situationer och ett värde av "2" kommer att producera "OK" hälsorapporter. Standardvärdet för den här parametern är "1".

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

via ClusterConfig.json för fristående distributioner eller Template.json för Azure-värdkluster:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>Nästa steg
* Om du vill veta mer om hur Cluster Resource Manager hanterar och balanserar belastning i klustret kan du läsa artikeln om [utjämningsbelastning](service-fabric-cluster-resource-manager-balancing.md)
* Om du vill veta mer om hur dina tjänster kan begränsas till att endast placeras på vissa noder se [Nodegenskaper och placeringsbegränsningar](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png

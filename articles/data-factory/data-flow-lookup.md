---
title: Uppslagstransformation vid mappning av dataflöde
description: Referensdata från en annan källa med hjälp av uppslagsomvandlingen vid mappning av dataflöde.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 78c6c1363af011a90865770d88c0037e50e958c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240420"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Uppslagstransformation vid mappning av dataflöde

Använd uppslagsomvandlingen för att referera till data från en annan källa i en dataflödesström. Uppslagsomvandlingen lägger till kolumner från matchade data till källdata.

En uppslagsomvandling liknar en vänster yttre koppling. Alla rader från den primära strömmen finns i utdataströmmen med ytterligare kolumner från uppslagsströmmen. 

## <a name="configuration"></a>Konfiguration

![Omvandling av uppslag](media/data-flow/lookup1.png "Sökning")

**Primär ström:** Den inkommande dataströmmen. Den här strömmen motsvarar den vänstra sidan av en koppling.

**Uppslagsström:** De data som läggs till i den primära strömmen. Vilka data som läggs till bestäms av uppslagsvillkoren. Den här strömmen motsvarar den högra sidan av en koppling.

**Matcha flera rader:** Om det är aktiverat returneras flera rader med flera matchningar i den primära strömmen. Annars returneras endast en enda rad baserat på villkoret "Matcha på".

**Match på:** Visas bara om "Matcha flera rader" är aktiverat. Välj om du vill matcha på en rad, den första matchen eller den sista matchen. Alla rader rekommenderas när den körs snabbast. Om första raden eller sista raden är markerad måste du ange sorteringsvillkor.

**Uppslagsvillkor:** Välj vilka kolumner som ska matchas. Om likhetsvillkoret uppfylls betraktas raderna som en matchning. Hovra och välj "Beräknad kolumn" för att extrahera ett värde med hjälp av [dataflödesuttrycksspråket](data-flow-expression-functions.md).

Uppslagsomvandlingen stöder bara likhetsmatchningar. Om du vill anpassa uppslagsuttrycket så att det omfattar andra operatorer, till exempel större än, rekommenderas att använda en [korskoppling i kopplingsomvandlingen](data-flow-join.md#custom-cross-join). En korskoppling undviker eventuella cartesianska produktfel vid utförande.

Alla kolumner från båda strömmarna ingår i utdata. Om du vill släppa dubbletter eller oönskade kolumner lägger du till en [markera omformning](data-flow-select.md) efter uppslagsomvandlingen. Kolumner kan också tas bort eller döpas om i en sink-omformning.

## <a name="analyzing-matched-rows"></a>Analysera matchade rader

Efter uppslagsomvandlingen `isMatch()` kan funktionen användas för att se om sökningen matchas för enskilda rader.

![Uppslagsmönster](media/data-flow/lookup111.png "Uppslagsmönster")

Ett exempel på det här mönstret är `isMatch()` att använda den villkorliga delningsomformningen för att dela på funktionen. I exemplet ovan går matchande rader genom den översta strömmen och ```NoMatch``` rader som inte matchar flödar genom strömmen.

## <a name="testing-lookup-conditions"></a>Testa uppslagsvillkor

När du testar uppslagsomvandlingen med förhandsgranskning av data i felsökningsläge använder du en liten uppsättning kända data. När du provar rader från en stor datauppsättning kan du inte förutsäga vilka rader och nycklar som ska läsas för testning. Resultatet är icke-deterministiskt, vilket innebär att dina kopplingsvillkor kanske inte returnerar några matchningar.

## <a name="broadcast-optimization"></a>Sändningsoptimering

I Azure Data Factory körs dataflöden i utskalade Spark-miljöer. Om datauppsättningen får plats i minnesutrymmet för arbetsnoder kan uppslagsprestanda optimeras genom att aktivera sändning.

![Sända koppling](media/data-flow/broadcast.png "Sända koppling")

Om du aktiverar sändningssändningar skjuts hela datauppsättningen in i minnet. För mindre datauppsättningar som bara innehåller några tusen rader kan sändning avsevärt förbättra uppslagsprestandan. För stora datauppsättningar kan det här alternativet leda till ett undantag för på minne.

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Exempel

![Omvandling av uppslag](media/data-flow/lookup-dsl-example.png "Sökning")

Dataflödesskriptet för ovanstående uppslagskonfiguration finns i kodavsnittet nedan.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'none')~> LookupKeys
```
## 
Nästa steg

* Kopplingen [join](data-flow-join.md) och [finns](data-flow-exists.md) omvandlingar både ta i flera strömmande ingångar
* Använd en villkorlig ```isMatch()``` [delningsomformning](data-flow-conditional-split.md) med för att dela rader på matchande och icke-matchande värden

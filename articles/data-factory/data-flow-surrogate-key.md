---
title: Ersättningsnyckelomvandling vid mappning av dataflöde
description: Så här använder du Azure Data Factorys mappningsdataflöde Surrogatnyckelomvandling för att generera sekventiella nyckelvärden
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: ade2fd6011bbcdaed4ce31ce70bfb4235429bb0d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606301"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Ersättningsnyckelomvandling vid mappning av dataflöde 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd omformningen av surrogatnyckeln för att lägga till ett stegvis nyckelvärde till varje datarad. Detta är användbart när du utformar dimensionstabeller i en analysdatamodell för stjärnschema. I ett stjärnschema kräver varje medlem i dimensionstabellerna en unik nyckel som är en icke-affärsnyckel.

## <a name="configuration"></a>Konfiguration

![Transformera surrogatnyckel](media/data-flow/surrogate.png "Omvandling av surrogatnyckel")

**Nyckelkolumn:** Namnet på den genererade kolumnen för surrogatnyckel.

**Startvärde:** Det lägsta nyckelvärdet som kommer att genereras.

## <a name="increment-keys-from-existing-sources"></a>Öka nycklar från befintliga källor

Om du vill starta sekvensen från ett värde som finns i en källa använder du en härledd kolumnomvandling efter surrogatnyckelomvandlingen för att lägga till de två värdena tillsammans:

![SK lägga till Max](media/data-flow/sk006.png "Summering av surrogatnyckel")

### <a name="increment-from-existing-maximum-value"></a>Öka från befintligt högsta värde

För att så nyckelvärdet med föregående max finns det två tekniker som du kan använda baserat på var källdata finns.

#### <a name="database-sources"></a>Databaskällor

Använd ett SQL-frågealternativ för att välja MAX() från källan. Till exempel kan`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Fråga om surrogatnyckel](media/data-flow/sk002.png "Fråga om omvandling av surrogatnyckel")

#### <a name="file-sources"></a>Filkällor

Om ditt tidigare maxvärde finns i `max()` en fil använder du funktionen i den sammanlagda omvandlingen för att få det tidigare maxvärdet:

![Nyckelfil för surrogat](media/data-flow/sk008.png "Nyckelfil för surrogat")

I båda fallen måste du koppla dina inkommande nya data tillsammans med källan som innehåller det tidigare maxvärdet.

![Anslutning av surrogatnyckel](media/data-flow/sk004.png "Anslutning av surrogatnyckel")

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Exempel

![Transformera surrogatnyckel](media/data-flow/surrogate.png "Omvandling av surrogatnyckel")

Dataflödesskriptet för konfigurationen av ovanstående surrogatnyckel finns i kodavsnittet nedan.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Nästa steg

I de här exemplen används omvandlingarna [Koppla och](data-flow-join.md) [härledd kolumn.](data-flow-derived-column.md)

---
title: Transformering av surrogat nyckel i mappnings data flödet
description: Så här använder du Azure Data Factory mappningen av data Flow surrogat Key för att generera sekventiella nyckel värden
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: ade2fd6011bbcdaed4ce31ce70bfb4235429bb0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "81606301"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Transformering av surrogat nyckel i mappnings data flödet 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd surrogat Key Transformation för att lägga till ett ökande nyckel värde till varje datarad. Detta är användbart när du skapar dimensions tabeller i en analys data modell med stjärn schema. I ett stjärn schema kräver varje medlem i dimensions tabellerna en unik nyckel som inte är en företags nyckel.

## <a name="configuration"></a>Konfiguration

![Transformering av surrogat nyckel](media/data-flow/surrogate.png "Transformering av surrogat nyckel")

**Nyckel kolumn:** Namnet på den genererade surrogat nyckel kolumnen.

**Start värde:** Det lägsta nyckel värde som ska genereras.

## <a name="increment-keys-from-existing-sources"></a>Öka nycklar från befintliga källor

Om du vill starta sekvensen från ett värde som finns i en källa använder du en härledd kolumn omvandling efter surrogat nyckel omvandlingen för att lägga till de två värdena tillsammans:

![SK Lägg till max](media/data-flow/sk006.png "Lägg till max i surrogat Key Transformation")

### <a name="increment-from-existing-maximum-value"></a>Öka från det befintliga Max värdet

För att dirigera nyckelvärdet med föregående Max, finns det två tekniker som du kan använda baserat på var dina källdata finns.

#### <a name="database-sources"></a>Databas källor

Använd ett alternativ för SQL-fråga för att välja MAX () från källan. Till exempel `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Surrogat nyckel fråga](media/data-flow/sk002.png "Omvandlings fråga för surrogat nyckel")

#### <a name="file-sources"></a>Fil källor

Om ditt föregående Max värde finns i en fil använder du `max()` funktionen i den sammanställda omvandlingen för att hämta det föregående Max värdet:

![Surrogat nyckel fil](media/data-flow/sk008.png "Surrogat nyckel fil")

I båda fallen måste du ansluta inkommande nya data tillsammans med din källa som innehåller det föregående Max värdet.

![Surrogat nyckel koppling](media/data-flow/sk004.png "Surrogat nyckel koppling")

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

![Transformering av surrogat nyckel](media/data-flow/surrogate.png "Transformering av surrogat nyckel")

Data flödes skriptet för ovanstående surrogat nyckel konfiguration finns i kodfragmentet nedan.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Nästa steg

I de här exemplen används [kopplings](data-flow-join.md) -och [härledda kolumn](data-flow-derived-column.md) omvandlingar.

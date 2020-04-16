---
title: Sortera omformning i mappning av dataflöde
description: Azure Data Factory Mapping Data Sortera omvandling
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 381c6573dff1b3f1638af9090a535d9a1e59b2b5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413174"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Sortera omformning i mappning av dataflöde

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Med sorteringsomformningen kan du sortera inkommande rader i den aktuella dataströmmen. Du kan välja enskilda kolumner och sortera dem i stigande eller fallande ordning.

> [!NOTE]
> Mappning av dataflöden körs på spark-kluster som distribuerar data över flera noder och partitioner. Om du väljer att partitionera om dina data i en efterföljande omvandling kan du förlora din sortering på grund av omfördelning av data.

## <a name="configuration"></a>Konfiguration

![Sorteringsinställningar](media/data-flow/sort.png "Sortera")

**Okänsligt fall:** Om du vill ignorera ärende vid sortering av sträng- eller textfält

**Sortera endast inom partitioner:** När dataflöden körs på spark delas varje dataström in i partitioner. Den här inställningen sorterar endast data i inkommande partitioner i stället för att sortera hela dataströmmen. 

**Sorteringsvillkor:** Välj vilka kolumner du sorterar efter och i vilken ordning sorteringen sker. Ordningen bestämmer sorteringsprioriteten. Välj om nulls ska visas i början eller slutet av dataströmmen eller inte.

### <a name="computed-columns"></a>Beräknade kolumner

Om du vill ändra eller extrahera ett kolumnvärde innan du använder sorteringen hovrar du över kolumnen och väljer "beräknad kolumn". Då öppnas uttrycksverktyget för att skapa ett uttryck för sorteringsåtgärden i stället för att använda ett kolumnvärde.

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Exempel

![Sorteringsinställningar](media/data-flow/sort.png "Sortera")

Dataflödesskriptet för ovanstående sorteringskonfiguration finns i kodavsnittet nedan.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Nästa steg

Efter sortering kanske du vill använda [den sammanlagda omvandlingen](data-flow-aggregate.md)

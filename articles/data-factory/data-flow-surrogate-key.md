---
title: Azure Data Factory mappning Flow Surrogate viktiga Dataomvandling
description: Hur du använder Azure Data Factory mappning Flow Surrogate nyckel transformering av Data för att generera sekventiella nyckelvärden
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: eaa1c577f7e208400d3430222b006e0dbbd7956a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61350616"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mappning av Flow Surrogate viktiga Dataomvandling

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använd Surrogate nyckel omvandlingen att lägga till en stegvis ökande-business godtyckliga nyckelvärdet i dina data flow raduppsättningen. Detta är användbart när du utformar dimensionstabeller i en star-schema analysdata modell där varje medlem i din dimensionstabeller måste ha en unik nyckel som tillhör en annan nyckel, Kimball DW-metod.

![Surrogat viktiga transformeringen](media/data-flow/surrogate.png "surrogat omvandling")

”Nyckelkolumn” är det namn som du ska ge din nya surrogate nyckelkolumn.

”Start värdet” är startpunkten för inkrementell värdet.

## <a name="increment-keys-from-existing-sources"></a>Öka nycklar från befintliga källor

Om du vill starta din ordning från ett-värde som finns i en källa kan du använda en härledd kolumn omvandling direkt efter den surrogatnyckel omvandlingen och lägga till de två värdena tillsammans:

![SK Lägg till Max](media/data-flow/sk006.png "Surrogate nyckel omvandling Lägg till Max")

Om du vill dirigera nyckelvärdet med föregående max, finns det två metoder som du kan använda:

### <a name="database-sources"></a>Databaskällor

Använd alternativet ”Query” för att välja MAX() från källan med hjälp av käll-transformeringen:

![Surrogat viktiga fråga](media/data-flow/sk002.png "surrogat viktiga Transformationsfrågan")

### <a name="file-sources"></a>Filen källor

Om din tidigare högsta värdet är i en fil, kan du använda den källa omvandlingen tillsammans med en sammanställd omvandling och Använd funktionen MAX() uttryck för att hämta det tidigare högsta värdet:

![Surrogat nyckelfilen](media/data-flow/sk008.png "surrogat nyckelfil")

I båda fallen måste du ansluta din inkommande nya data tillsammans med din källa som innehåller det tidigare högsta värdet:

![Surrogat viktiga Join](media/data-flow/sk004.png "surrogat viktiga Join")

## <a name="next-steps"></a>Nästa steg

De här exemplen använder den [ansluta](data-flow-join.md) och [härledd kolumn](data-flow-derived-column.md) transformationer.

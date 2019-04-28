---
title: Dataflöde för Azure Data Factory mappning normalisera omvandling
description: Dataflöde för Azure Data Factory mappning normalisera omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 160ff8bbcb8ce5a4f1f32245cc366281640c5919
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61348313"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory normalisera omvandling

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använd Unpivot i ADF mappning dataflöde som ett sätt att göra en Onormaliserad datauppsättning till en mer normaliserade version genom att expandera värden från flera kolumner i en enda post till flera poster med samma värden i en enda kolumn.

![Normalisera omvandling](media/data-flow/unpivot1.png "normalisera alternativ 1")

## <a name="ungroup-by"></a>Dela upp av

![Normalisera omvandling](media/data-flow/unpivot5.png "normalisera alternativ 2")

Ange först de kolumner som du vill gruppera efter för pivot-aggregering. Ange en eller flera kolumner för att dela upp med den + bredvid kolumnlistan.

## <a name="unpivot-key"></a>Normalisera nyckel

![Normalisera omvandling](media/data-flow/unpivot6.png "normalisera alternativ 3")

Pivot-nyckeln är den kolumn som ADF kommer växla över från rader till kolumnen. Som standard kommer varje unikt värde i datauppsättningen för det här fältet växla över till en kolumn. Du kan också ange värdena för från den datauppsättning som du vill växla över till kolumnvärdena.

## <a name="unpivoted-columns"></a>Icke-Pivotkolumner

![Normalisera omvandling](media/data-flow//unpivot7.png "normalisera alternativ 4")

Välj till sist sammansättning som du vill använda för pivoterat värden och hur du vill att kolumnerna som ska visas i den nya utdata-projektionen från omvandlingen.

(Valfritt) Du kan ange ett namngivningsmönster från en med ett prefix, mitten och suffixet som ska läggas till varje nytt kolumnnamn från radvärdena.

Till exempel skulle pivotera ”försäljning” av ”Region” bara ger dig nya kolumnvärdena från varje försäljning värde. Exempel: "25", "50", "1000", ... Men om du anger ett prefixvärdet för ”försäljning” kan kommer sedan ”försäljning” föregås till värden.

<img src="media/data-flow/unpivot3.png" width="400">

Ange kolumnordning till ”Normal” grupperas alla pivoterat kolumner med deras aggregerade värden. Ange hur kolumnerna till ”laterala” kommer växla mellan kolumn och värde.

![Normalisera omvandling](media/data-flow//unpivot7.png "normalisera alternativ 5")

I sista normaliserades data resultatet set visas kolumnsummorna avpivoterade nu i separata radvärden.

## <a name="next-steps"></a>Nästa steg

Använd den [pivotera omvandling](data-flow-pivot.md) till pivot rader till kolumner.

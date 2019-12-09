---
title: Avpivotera transformering av data flöde
description: Azure Data Factory mappning av unpivot-transformering för data flöde
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: b207012335e68d389a07b54408e840dbb305a30c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930138"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory unpivot-transformering



Använd unpivot i data flöde för ADF-mappning som ett sätt att omvandla en normaliserad data uppsättning till en mer normaliserad version genom att expandera värden från flera kolumner i en enskild post till flera poster med samma värden i en enda kolumn.

![Avpivotera transformering](media/data-flow/unpivot1.png "Avpivotera alternativ 1")

## <a name="ungroup-by"></a>Dela upp efter

![Avpivotera transformering](media/data-flow/unpivot5.png "Avpivotera alternativ 2")

Ange först de kolumner som du vill gruppera efter för din Pivot-aggregering. Ange en eller flera kolumner för att dela upp med +-tecknet bredvid kolumn listan.

## <a name="unpivot-key"></a>Unpivot-nyckel

![Avpivotera transformering](media/data-flow/unpivot6.png "Avpivotera alternativ 3")

Pivot-tangenten är den kolumn som ADF kommer från rad till kolumn. Som standard kommer varje unikt värde i data uppsättningen för det här fältet att pivoteras till en kolumn. Du kan också ange värden från data uppsättningen som du vill pivotera till kolumn värden.

## <a name="unpivoted-columns"></a>Kolumner som inte har pivoteras

![Avpivotera transformering](media/data-flow//unpivot7.png "Avpivotera alternativ 4")

Slutligen väljer du den agg regering som du vill använda för de pivoterade värdena och hur du vill att kolumnerna ska visas i den nya utdata-projektionen från omvandlingen.

Valfritt Du kan ange ett namn mönster med ett prefix, mellan och suffix som ska läggas till i varje nytt kolumn namn från rad värden.

Om du till exempel pivoterar "Sales" efter "region" får du bara nya kolumn värden från varje försäljnings värde. Exempel: "25", "50", "1000",... Men om du anger ett prefixvärde för "försäljning" kommer värdet "Sales" att föregås av värdena.

<img src="media/data-flow/unpivot3.png" width="400">

Om du anger kolumn ordningen till "normal" grupperas alla de pivoterade kolumnerna med deras sammanlagda värden. Att ange kolumnernas ordning till "lateral" kommer att alternera mellan kolumn och värde.

![Avpivotera transformering](media/data-flow//unpivot7.png "Avpivotera alternativ 5")

I den sista pivottabellen med nedpivoterade data visas kolumn summor nu i separata rad värden.

## <a name="next-steps"></a>Nästa steg

Använd [Pivot-transformeringen](data-flow-pivot.md) för att pivotera rader till kolumner.

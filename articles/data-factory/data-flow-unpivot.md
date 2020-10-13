---
title: Avpivotera transformering i data flöde för mappning
description: Azure Data Factory mappning av unpivot-transformering för data flöde
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: 38986c3f93856981e903ae93ed7788ae01fc6d5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91823580"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Avpivotera transformering i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd unpivot i data flöde för ADF-mappning som ett sätt att omvandla en normaliserad data uppsättning till en mer normaliserad version genom att expandera värden från flera kolumner i en enskild post till flera poster med samma värden i en enda kolumn.

![Avpivotera transformering](media/data-flow/unpivot1.png "Avpivotera alternativ 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

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

![Bild som visar kolumnerna PO, Vendor och frukt före och efter en unipivot-omvandling med hjälp av kolumnen frukt som unipivot-nyckeln.](media/data-flow/unpivot3.png)

Om du anger kolumn ordningen till "normal" grupperas alla de pivoterade kolumnerna med deras sammanlagda värden. Att ange kolumnernas ordning till "lateral" kommer att alternera mellan kolumn och värde.

![Avpivotera transformering](media/data-flow//unpivot7.png "Avpivotera alternativ 5")

I den sista pivottabellen med nedpivoterade data visas kolumn summor nu i separata rad värden.

## <a name="next-steps"></a>Nästa steg

Använd [Pivot-transformeringen](data-flow-pivot.md) för att pivotera rader till kolumner.

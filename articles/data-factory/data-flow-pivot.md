---
title: Azure Data Factory Dataomvandling Flow Pivot-mappning
description: Pivotera data från rader till kolumner med hjälp av Azure Data Factory mappning Flow Pivot Dataomvandling
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: e16cac281b77f3ca93d9ef358ae806203bc8b663
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794371"
---
# <a name="azure-data-factory-pivot-transformation"></a>Azure data factory pivot-transformering
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använd Pivot i ADF dataflöde som en aggregering där en eller flera grupperade kolumner har dess distinkta radvärden omvandlas till enskilda kolumner. Du kan i princip pivotera radvärdena i nya kolumner (omvandla data till metadata).

![Alternativ för pivotpunktens](media/data-flow/pivot1.png "pivotera 1")

## <a name="group-by"></a>Gruppera efter

![Alternativ för pivotpunktens](media/data-flow/pivot2.png "pivotera 2")

Ange först de kolumner som du vill gruppera efter för pivot-aggregering. Du kan ange fler än 1 kolumn här med den + bredvid kolumnlistan.

## <a name="pivot-key"></a>Pivot-nyckel

![Alternativ för pivotpunktens](media/data-flow/pivot3.png "pivotera 3")

Pivot-nyckeln är den kolumn som ADF kommer växla över från rader till kolumnen. Som standard kommer varje unikt värde i datauppsättningen för det här fältet växla över till en kolumn. Du kan också ange värdena för från den datauppsättning som du vill växla över till kolumnvärdena. Detta är den kolumn som avgör de nya kolumnerna som ska skapas.

## <a name="pivoted-columns"></a>Pivoterad kolumner

![Alternativ för pivotpunktens](media/data-flow/pivot4.png "pivotera 4")

Till sist väljer du den aggregering som du vill använda för pivoterat värden och hur du vill att kolumnerna som ska visas i den nya utdata-projektionen från omvandlingen.

(Valfritt) Du kan ange ett namngivningsmönster från en med ett prefix, mitten och suffixet som ska läggas till varje nytt kolumnnamn från radvärdena.

Exempelvis skulle pivotering ”försäljning” av ”Region” leda till nya kolumnvärdena från varje försäljningsvärde, dvs. "25", "50", "1000", etc. Men om du ställer in prefixvärdet ”Sales-” varje kolumnvärde skulle lägga till ”Sales-” i början av värdet.

![Alternativ för pivotpunktens](media/data-flow/pivot5.png "pivotera 5")

Ange kolumnordning till ”Normal” grupperas alla pivoterat kolumner med deras aggregerade värden. Ange hur kolumnerna till ”laterala” kommer växla mellan kolumn och värde.

### <a name="aggregation"></a>Sammansättning

Om du vill ange den aggregering som du vill använda för pivot-värden, klickar du på fältet längst ned i fönstret pivoteras kolumner. Du ska ange i Uttrycksverktyget ADF Data flöda där du kan skapa ett uttryck för aggregering och ange ett beskrivande aliasnamn för din nya sammanställda värden.

Använda Flow Uttrycksspråk för ADF Data för att beskriva pivoterad kolumn omvandlingar i Uttrycksverktyget: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Pivot-metadata

Pivot-transformeringen skapas nya kolumnnamn som är dynamiska baserat på inkommande data. Pivot-nyckeln ger värden för varje nytt kolumnnamn. Om du inte ange enskilda värden och vill skapa dynamiska kolumnnamn för varje unikt värde i Pivot-nyckeln, metadata i Granska visas inte i Användargränssnittet och det blir inga kolumnen spridning till mottagare transformeringen. Om du anger värden för nyckeln Pivot sedan ADF kan fastställa de nya kolumnnamnen och de kolumnnamn ska vara tillgängliga för dig i granska och mappning för mottagare.

### <a name="landing-new-columns-in-sink"></a>Hamnar nya kolumner i mottagare

Även med dynamiska kolumnnamnen i Pivot, kan du fortfarande mottagare din nya kolumnnamn och värden till ditt målarkiv. Ange ”Tillåt schemat Drift” till på i inställningarna för mottagare. Nya dynamiska namn visas inte i kolumnmetadata för, men drift schemaalternativet gör att du kan få data.

### <a name="view-metadata-in-design-mode"></a>Visa metadata i designläge

Om du vill visa de nya kolumnnamnen som metadata i granska och du vill se kolumnerna som uttryckligen spridas till mottagare transformeringen, ange explicita värden på fliken Pivot-nyckel.

### <a name="how-to-rejoin-original-fields"></a>Hur du återansluta till ursprungliga fält
Pivot-transformeringen kommer endast att projicera kolumner som används i aggregeringar, gruppering och pivot-åtgärd. Om du vill ta med de andra kolumnerna från föregående steg i ditt flöde, Använd en ny gren i föregående steg och mönstret självkoppling ansluta flödet med metadata.

## <a name="next-steps"></a>Nästa steg

Prova den [normalisera omvandling](data-flow-unpivot.md) att kolumnvärden radvärden. 

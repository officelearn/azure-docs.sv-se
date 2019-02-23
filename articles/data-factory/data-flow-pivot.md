---
title: Azure Data Factory Dataomvandling Flow Pivot-mappning
description: Azure Data Factory Dataomvandling Flow Pivot-mappning
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: fd7308e7ecb6e086dffb4bae0cc99c57dd699861
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729830"
---
# <a name="azure-data-factory-mapping-data-flow-pivot-transformation"></a>Azure Data Factory Dataomvandling Flow Pivot-mappning

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använd Pivot i ADF dataflöde som en aggregering där en eller flera grupperade kolumner har dess distinkta radvärden omvandlas till enskilda kolumner. Du kan i princip pivotera radvärdena i nya kolumner (omvandla data till metadata).

![Alternativ för pivotpunktens](media/data-flow/pivot1.png "pivotera 1")

## <a name="group-by"></a>Gruppera efter

![Alternativ för pivotpunktens](media/data-flow/pivot2.png "pivotera 2")

Ange först de kolumner som du vill gruppera efter för pivot-aggregering. Du kan ange fler än 1 kolumn här med den + bredvid kolumnlistan.

## <a name="pivot-key"></a>Pivot-nyckel

![Alternativ för pivotpunktens](media/data-flow/pivot3.png "pivotera 3")

Pivot-nyckeln är den kolumn som ADF kommer växla över från rader till kolumnen. Som standard kommer varje unikt värde i datauppsättningen för det här fältet växla över till en kolumn. Du kan också ange värdena för från den datauppsättning som du vill växla över till kolumnvärdena.

## <a name="pivoted-columns"></a>Pivoterad kolumner

![Alternativ för pivotpunktens](media/data-flow/pivot4.png "pivotera 4")

Till sist väljer du den aggregering som du vill använda för pivoterat värden och hur du vill att kolumnerna som ska visas i den nya utdata-projektionen från omvandlingen.

(Valfritt) Du kan ange ett namngivningsmönster från en med ett prefix, mitten och suffixet som ska läggas till varje nytt kolumnnamn från radvärdena.

Exempelvis skulle pivotering ”försäljning” av ”Region” leda till nya kolumnvärdena från varje försäljningsvärde, dvs. "25", "50", "1000", etc. Men om du ställer in prefixvärdet ”försäljning” 

![Alternativ för pivotpunktens](media/data-flow/pivot5.png "pivotera 5")

Ange kolumnordning till ”Normal” grupperas alla pivoterat kolumner med deras aggregerade värden. Ange hur kolumnerna till ”laterala” kommer växla mellan kolumn och värde.

### <a name="aggregation"></a>Sammansättning

Om du vill ange den aggregering som du vill använda för pivot-värden, klickar du på fältet längst ned i fönstret pivoteras kolumner. Du ska ange i Uttrycksverktyget ADF Data flöda där du kan skapa ett uttryck för aggregering och ange ett beskrivande aliasnamn för din nya sammanställda värden.

Använda Flow Uttrycksspråk för ADF Data för att beskriva pivoterad kolumn omvandlingar i Uttrycksverktyget: https://aka.ms/dataflowexpressions.

### <a name="how-to-rejoin-original-fields"></a>Hur du återansluta till ursprungliga fält
> [!NOTE]
> Pivot-transformeringen kommer endast att projicera kolumner som används i aggregeringar, gruppering och pivot-åtgärd. Om du vill ta med de andra kolumnerna från föregående steg i ditt flöde använder en ny gren i föregående steg och Använd mönstret självkoppling för att ansluta flödet med ursprungliga metadata

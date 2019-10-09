---
title: Azure Data Factory mappar Pivot-transformering för data flöde
description: Pivotera data från rader till kolumner med Azure Data Factory mappa data flödets Pivot-transformering
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 1412f7d822d83a8712d27dd4e86311567d6ac714
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029311"
---
# <a name="azure-data-factory-pivot-transformation"></a>Pivot-transformering för Azure Data Factory


Använd Pivot i ADF-dataflöde som en agg regering där en eller flera grupperade kolumner har sina distinkta rad värden omvandlade till enskilda kolumner. I stort sett kan du pivotera rad värden i nya kolumner (omvandla data till metadata).

![Pivot-alternativ](media/data-flow/pivot1.png "pivottabell 1")

## <a name="group-by"></a>Gruppera efter

![Pivot-alternativ](media/data-flow/pivot2.png "Pivot 2")

Ange först de kolumner som du vill gruppera efter för din Pivot-aggregering. Du kan ange mer än 1 kolumn här med +-tecknet bredvid kolumn listan.

## <a name="pivot-key"></a>Pivot-nyckel

![Pivot-alternativ](media/data-flow/pivot3.png "Pivot 3")

Pivot-tangenten är den kolumn som ADF kommer från rad till kolumn. Som standard kommer varje unikt värde i data uppsättningen för det här fältet att pivoteras till en kolumn. Du kan också ange värden från data uppsättningen som du vill pivotera till kolumn värden. Det här är den kolumn som avgör vilka nya kolumner som ska skapas.

## <a name="pivoted-columns"></a>Pivoterade kolumner

![Pivot-alternativ](media/data-flow/pivot4.png "Pivot 4")

Slutligen väljer du den agg regering som du vill använda för de pivoterade värdena och hur du vill att kolumnerna ska visas i den nya utdata-projektionen från omvandlingen.

Valfritt Du kan ange ett namn mönster med ett prefix, mellan och suffix som ska läggas till i varje nytt kolumn namn från rad värden.

Om du till exempel pivoterar "Sales" efter "region" skulle det leda till nya kolumn värden från varje försäljnings värde, dvs. "25", "50", "1000", etc. Men om du anger ett prefixvärde för "Sales-" lägger varje kolumn värde till "Sales-" i början av värdet.

![Pivot-alternativ](media/data-flow/pivot5.png "Pivot 5")

Om du anger kolumn ordningen till "normal" grupperas alla de pivoterade kolumnerna med deras sammanlagda värden. Att ange kolumnernas ordning till "lateral" kommer att alternera mellan kolumn och värde.

### <a name="aggregation"></a>Sammansättning

Om du vill ange den agg regering som du vill använda för pivottabellens värden klickar du på fältet längst ned i fönstret för pivoterade kolumner. Du kommer att ange i uttrycks verktyget för ADF-dataflöde där du kan skapa ett agg regerings uttryck och tillhandahålla ett beskrivande aliasnamn för dina nya aggregerade värden.

Använd uttrycks språket för ADF-dataflödet för att beskriva de pivoterade kolumn omvandlingarna i uttrycks verktyget: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Pivotera metadata

I Pivot-transformeringen skapas nya kolumn namn som är dynamiska utifrån dina inkommande data. Pivot-nyckeln genererar värdena för varje nytt kolumn namn. Om du inte anger enskilda värden och vill skapa dynamiska kolumn namn för varje unikt värde i din Pivot-nyckel, visar användar gränssnittet inte de metadata som granskas och det kommer inte att finnas någon kolumn spridning till Sink-omvandlingen. Om du anger värden för Pivot-nyckeln kan ADF identifiera de nya kolumn namnen och de kolumn namn som är tillgängliga för dig i inspektions-och mottagar mappningen.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Generera en ny modell från dynamiska kolumner

Pivot genererar nya kolumn namn dynamiskt baserat på rad värden. Du kan omvandla dessa nya kolumner till metadata som kan refereras till senare i ditt data flöde. Det gör du genom att klicka på fliken Data förhands granskning. Alla nya kolumner som genereras av din Pivot-transformering visas med en "förbockad" ikon i tabell rubriken. Klicka på knappen "Mappa förbrukade" för att omvandla de nya kolumnerna till metadata, vilket gör dem till en del av data flödets modell.

![Pivotera kolumner](media/data-flow/newpivot1.png "karta med staplade pivottabeller")

### <a name="landing-new-columns-in-sink"></a>Landning av nya kolumner i mottagare

Även med dynamiska kolumn namn i Pivot kan du fortfarande skapa nya kolumn namn och-värden i mål lagret. Ställ bara in "Tillåt schema drift" i i dina Sink-inställningar. Du kommer inte att se de nya dynamiska namnen i metadata för kolumnen, men du kan använda alternativet schema drifts alternativ för att landa data.

### <a name="view-metadata-in-design-mode"></a>Visa metadata i design läge

Om du vill visa de nya kolumn namnen som metadata i inspektionen och du vill se att kolumnerna sprids direkt till Sink-omvandlingen, anger du explicita värden på fliken för Pivot-tangenten.

### <a name="how-to-rejoin-original-fields"></a>Så här återansluter du ursprungliga fält
Pivot-transformeringen projicerar bara de kolumner som används i instruktionen agg regerings-, grupperings-och Pivot-åtgärd. Om du vill inkludera de andra kolumnerna från föregående steg i ditt flöde använder du en ny gren från föregående steg och använder själv kopplings mönstret för att ansluta flödet med de ursprungliga metadata.

## <a name="next-steps"></a>Nästa steg

Prova [unpivot-transformeringen](data-flow-unpivot.md) för att omvandla kolumn värden till rad värden. 

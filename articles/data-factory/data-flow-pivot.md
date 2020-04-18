---
title: Mappa pivotomvandling för dataflöde
description: Pivotdata från rader till kolumner med hjälp av Azure Data Factory-mappningsdataflöde Pivot transformation
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 980d7c3e1b1f69e76c091e2a4a74c8e5a4d0bb64
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606364"
---
# <a name="azure-data-factory-pivot-transformation"></a>Pivotomvandling för Azure-datafabrik

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd Pivot i ADF-dataflöde som en aggregering där en eller flera grupperingskolumner har sina distinkta radvärden omdömda till enskilda kolumner. I huvudsak kan du pivotera radvärden till nya kolumner (förvandla data till metadata).

![Alternativ för pivot](media/data-flow/pivot1.png "pivot 1")

## <a name="group-by"></a>Gruppera efter

![Alternativ för pivot](media/data-flow/pivot2.png "pivot 2")

Ange först de kolumner som du vill gruppera efter för pivotaggregering. Du kan ange mer än en kolumn här med +-tecknet bredvid kolumnlistan.

## <a name="pivot-key"></a>Pivot-tangenten

![Alternativ för pivot](media/data-flow/pivot3.png "pivot 3")

Pivottangenten är den kolumn som ADF ska pivotera från rad till kolumn. Som standard pivoterar varje unikt värde i datauppsättningen för det här fältet till en kolumn. Du kan dock ange värdena från den datauppsättning som du vill pivotera till kolumnvärden. Det här är kolumnen som avgör vilka nya kolumner som ska skapas.

## <a name="pivoted-columns"></a>Pivoterade kolumner

![Alternativ för pivot](media/data-flow/pivot4.png "pivot 4")

Slutligen väljer du den aggregering som du vill använda för de pivoterade värdena och hur du vill att kolumnerna ska visas i den nya utdataprojektionen från omvandlingen.

(Valfritt) Du kan ange att ett namngivningsmönster med ett prefix, ett mitten- och suffix ska läggas till i varje nytt kolumnnamn från radvärdena.

Pivotering "Försäljning" efter "Region" skulle till exempel resultera i nya kolumnvärden från varje försäljningsvärde, dvs .e. "25", "50", "1000", etc. Om du anger ett prefixvärde för "Försäljning– lägger varje kolumnvärde till "Försäljning" i början av värdet.

![Alternativ för pivot](media/data-flow/pivot5.png "pivot 5")

Om du ställer in kolumnarrangemanget till "Normal" grupperas alla pivoterade kolumner med sina aggregerade värden. Om du ställer in kolumnarrangemanget på "Lateral" växlar mellan kolumn och värde.

### <a name="aggregation"></a>Sammansättning

Om du vill ange den aggregering som du vill använda för pivotvärdena klickar du på fältet längst ned i fönstret Pivoterade kolumner. Du kommer att gå in i ADF Data Flow-uttrycksverktyget där du kan skapa ett aggregeringsuttryck och ange ett beskrivande aliasnamn för dina nya aggregerade värden.

Använd ADF-dataflödesuttrycksspråket för att beskriva de https://aka.ms/dataflowexpressionspivoterade kolumnomformningerna i Uttrycksverktyget: .

## <a name="pivot-metadata"></a>Pivot metadata

Pivot-omvandlingen skapar nya kolumnnamn som är dynamiska baserat på inkommande data. Pivottangenten skapar värdena för varje nytt kolumnnamn. Om du inte anger enskilda värden och vill skapa dynamiska kolumnnamn för varje unikt värde i pivotnyckeln, kommer användargränssnittet inte att visa metadata i Inspektera och det kommer inte att finnas någon kolumnspridning till sink-omvandlingen. Om du anger värden för pivotnyckeln kan ADF bestämma vilka nya kolumnnamnen som ska fastställas och kolumnnamnen ska vara tillgängliga för dig i mappningen Inspektera och sänk.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Generera en ny modell från dynamiska kolumner

Pivot genererar nya kolumnnamn dynamiskt baserat på radvärden. Du kan förvandla de nya kolumnerna till metadata som kan refereras senare i dataflödet. Det gör du genom att klicka på fliken Förhandsgranskning av data. Alla nya kolumner som genereras av pivotomvandlingen visas med en "drivad" ikon i tabellhuvudet. Klicka på knappen "Karta drev" för att omvandla dessa nya kolumner till metadata, vilket gör dem till en del av dataflödets modell.

![Pivotera kolumner](media/data-flow/newpivot1.png "Kartdriven pivotkolumner")

### <a name="landing-new-columns-in-sink"></a>Landa nya kolumner i Sink

Även med dynamiska kolumnnamn i Pivot kan du fortfarande sänka in dina nya kolumnnamn och värden i målarkivet. Ställ bara in "Tillåt schemadrift" på i dina Sink-inställningar. Du kommer inte att se de nya dynamiska namnen i kolumnmetadata, men schemadriftalternativet gör att du kan landa data.

### <a name="view-metadata-in-design-mode"></a>Visa metadata i designläge

Om du vill visa de nya kolumnnamnen som metadata i Inspektera och du vill se kolumnerna spridas explicit till sink-omvandlingen, ange sedan explicita värden på fliken Pivotnyckel.

### <a name="how-to-rejoin-original-fields"></a>Så här återansluter du till ursprungliga fält
Pivotomvandlingen projicerar bara de kolumner som används i aggregerings-, grupperings- och pivotåtgärden. Om du vill inkludera de andra kolumnerna från föregående steg i flödet använder du en ny gren från föregående steg och använder självkopplingsmönstret för att ansluta flödet till de ursprungliga metadata.

## <a name="next-steps"></a>Nästa steg

Prova den [unpivot-omvandlingen](data-flow-unpivot.md) för att omvandla kolumnvärden till radvärden. 

---
title: Pivotomvandling vid mappning av dataflöde
description: Pivotdata från rader till kolumner med hjälp av Azure Data Factory-mappningsdataflöde Pivot transformation
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a58444f81f60b48f9c2c76f13257a6a2431158a8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686460"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Pivotomvandling vid mappning av dataflöde


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd pivotomvandlingen för att skapa flera kolumner från de unika radvärdena i en enskild kolumn. Pivot är en aggregeringsomformning där du markerar grupp efter kolumner och genererar pivotkolumner med hjälp av [mängdfunktioner](data-flow-expression-functions.md#aggregate-functions).

## <a name="configuration"></a>Konfiguration

Pivotomvandlingen kräver tre olika indata: gruppera efter kolumner, pivottangenten och hur du genererar de pivoterade kolumnerna

### <a name="group-by"></a>Gruppera efter

![Gruppera efter alternativ](media/data-flow/pivot2.png "[Gruppera efter alternativ")

Markera vilka kolumner som ska aggregeras över de pivoterade kolumnerna. Utdata grupperar alla rader med samma grupp efter värden i en rad. Aggregeringen som görs i den pivoterade kolumnen sker över varje grupp.

Det här avsnittet är valfritt. Om ingen grupp efter kolumner markeras sammanställs hela dataströmmen och endast en rad skrivs ut.

### <a name="pivot-key"></a>Pivot-tangenten

![Pivot-tangenten](media/data-flow/pivot3.png "Pivot-tangenten")

Pivottangenten är den kolumn vars radvärden vrids till nya kolumner. Som standard skapar pivotomvandlingen en ny kolumn för varje unikt radvärde.

I avsnittet **Värde**kan du ange specifika radvärden som ska pivoteras. Endast de radvärden som anges i det här avsnittet pivoteras. Om du aktiverar **Null-värdet** skapas en pivoterad kolumn för null-värdena i kolumnen.

### <a name="pivoted-columns"></a>Pivoterade kolumner

![Pivoterade kolumner](media/data-flow/pivot4.png "Pivoterade kolumner")

För varje unikt pivotnyckelvärde som blir en kolumn genererar du ett aggregerat radvärde för varje grupp. Du kan skapa flera kolumner per pivotnyckel. Varje pivotkolumn måste innehålla minst en [mängdfunktion](data-flow-expression-functions.md#aggregate-functions).

**Namnmönster för kolumn:** Välj hur kolumnnamnet för varje pivotkolumn ska formateras. Det utdataade kolumnnamnet är en kombination av pivotnyckelvärdet, kolumnprefixet och valfritt prefix, räcker, mellantecken. 

**Kolumn arrangemang:** Om du genererar mer än en pivotkolumn per pivotnyckel väljer du hur du vill att kolumnerna ska ordnas. 

**Kolumnprefix:** Om du genererar mer än en pivotkolumn per pivotnyckel anger du ett kolumnprefix för varje kolumn. Den här inställningen är valfri om du bara har en pivoterad kolumn.

## <a name="help-graphic"></a>Hjälpbild

Hjälpbilden nedan visar hur de olika pivotkomponenterna interagerar med varandra

![Pivot-hjälpgrafik](media/data-flow/pivot5.png "Pivot-hjälpbild")

## <a name="pivot-metadata"></a>Pivot metadata

Om inga värden anges i pivotnyckelkonfigurationen genereras de pivoterade kolumnerna dynamiskt vid körning. Antalet pivoterade kolumner är lika med antalet unika pivotnyckelvärden multiplicerat med antalet pivotkolumner. Eftersom detta kan vara ett föränderligt nummer kommer användarupplevelsen inte att visa kolumnmetadata på fliken **Inspektera** och det blir ingen kolumnspridning. Om du vill omvandla dessa kolumner använder du [kolumnmönsterfunktionerna](concepts-data-flow-column-pattern.md) för att mappa dataflöde. 

Om specifika pivotnyckelvärden har angetts visas de pivoterade kolumnerna i metadata.e kolumnnamn kommer att vara tillgängliga för dig i mappningen Inspektera och sänk.

### <a name="generate-metadata-from-drifted-columns"></a>Generera metadata från borttrollade kolumner

Pivot genererar nya kolumnnamn dynamiskt baserat på radvärden. Du kan lägga till dessa nya kolumner i de metadata som kan refereras senare i dataflödet. Det gör du genom att använda [den snabbåtgärd](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) som utlöstes av kartan i förhandsgranskningen av data. 

![Pivotera kolumner](media/data-flow/newpivot1.png "Kartdriven pivotkolumner")

### <a name="sinking-pivoted-columns"></a>Sjunkande pivoterade kolumner

Även om pivoterade kolumner är dynamiska kan de fortfarande skrivas in i måldatalagret. Aktivera **Tillåt schemadrift** i sink-inställningarna. På så sätt kan du skriva kolumner som inte ingår i metadata. din kolumn metadata, men schemat drift alternativet gör att du kan landa data.

### <a name="rejoin-original-fields"></a>Återansluta till ursprungliga fält

Pivotomvandlingen projicerar bara gruppen efter och pivoterade kolumner. Om du vill att utdata ska innehålla andra indatakolumner använder du ett [självkopplingsmönster.](data-flow-join.md#self-join)

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>Exempel

Skärmarna som visas i konfigurationsavsnittet har följande dataflödesskript:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Nästa steg

Prova den [unpivot-omvandlingen](data-flow-unpivot.md) för att omvandla kolumnvärden till radvärden. 

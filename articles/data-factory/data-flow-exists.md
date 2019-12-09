---
title: Exists-transformering i mappnings data flödet
description: Sök efter befintliga rader med hjälp av exists-omvandlingen i Azure Data Factory mappa data flöde
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: efcc45dcf3565b70305323701810c49c4a720394
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930415"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Exists-transformering i mappnings data flödet

Exists-omvandlingen är en omvandling för rad filtrering som kontrollerar om dina data finns i en annan källa eller Stream. Utdataströmmen innehåller alla rader i den vänstra strömmen som antingen finns eller inte finns i den högra strömmen. Exists-omvandlingen liknar ```SQL WHERE EXISTS``` och ```SQL WHERE NOT EXISTS```.

## <a name="configuration"></a>Konfiguration

1. Välj vilken data ström du vill söka efter i list rutan till **höger ström** .
1. Ange om du vill att data ska finnas eller inte finns i inställningen för **exist-typ** .
1. Välj om du vill använda ett **anpassat uttryck**eller inte.
1. Välj vilka nyckel kolumner du vill jämföra som dina villkor. Som standard söker data flödet efter likhet mellan en kolumn i varje data ström. Om du vill jämföra via ett beräknat värde hovrar du över kolumn List rutan och väljer **beräknad kolumn**.

![Finns inställningar](media/data-flow/exists.png "finns 1")

### <a name="multiple-exists-conditions"></a>Det finns flera villkor

Om du vill jämföra flera kolumner från varje data ström, lägger du till ett nytt exists-villkor genom att klicka på plus ikonen bredvid en befintlig rad. Varje ytterligare villkor är anslutet av en "och"-sats. Jämförelse mellan två kolumner är samma som följande uttryck:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Anpassat uttryck

Om du vill skapa ett uttryck för en fri form som innehåller andra operatorer än "och" och "lika med", väljer du fältet **anpassat uttryck** . Ange ett anpassat uttryck via data flödets uttrycks verktyg genom att klicka på den blå rutan.

![Finns anpassade inställningar](media/data-flow/exists1.png "finns anpassad")

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Exempel

Exemplet nedan är en transformation med namnet `checkForChanges` som tar vänster ström `NameNorm2` och direkt uppspelnings `TypeConversions`.  Exists-villkoret är uttrycket `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` som returnerar true om både kolumnen `EMPID` och `Region` i varje data ström matchar. När vi söker efter existens är `negate` falskt. Vi aktiverar inte sändning på fliken optimera så `broadcast` har värdet `'none'`.

I Data Factory UX ser den här omvandlingen ut som på bilden nedan:

![Finns exempel](media/data-flow/exists-script.png "Finns exempel")

Data flödes skriptet för den här omvandlingen är i kodfragmentet nedan:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Nästa steg

Liknande omvandlingar är [Lookup](data-flow-lookup.md) och [Join](data-flow-join.md).

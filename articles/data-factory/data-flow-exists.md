---
title: Finns omvandling vid mappning av dataflöde
description: Sök efter befintliga rader med hjälp av den befintliga omvandlingen i Azure Data Factory-mappningsdataflödet
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: efcc45dcf3565b70305323701810c49c4a720394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930415"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Finns omvandling vid mappning av dataflöde

Den finns omvandlingen är en radfiltrering omvandling som kontrollerar om dina data finns i en annan källa eller ström. Utdataströmmen innehåller alla rader i den vänstra strömmen som antingen finns eller inte finns i rätt ström. Den finns omvandlingen ```SQL WHERE EXISTS``` ```SQL WHERE NOT EXISTS```liknar och .

## <a name="configuration"></a>Konfiguration

1. Välj vilken dataström du söker efter tillvaro i listrutan **Högerström.**
1. Ange om du letar efter att data ska finnas eller inte finns i inställningen **Finns.**
1. Välj om du vill ha ett **anpassat uttryck**eller inte .
1. Välj vilka nyckelkolumner du vill jämföra när det finns villkor. Som standard söker dataflödet efter likhet mellan en kolumn i varje flöde. Om du vill jämföra via ett beräknat värde hovrar du över kolumnrullgardermenyn och väljer **Beräknad kolumn**.

![Finns inställningar](media/data-flow/exists.png "finns 1")

### <a name="multiple-exists-conditions"></a>Flera finns villkor

Om du vill jämföra flera kolumner från varje ström lägger du till ett nytt tillstånd genom att klicka på plusikonen bredvid en befintlig rad. Varje ytterligare villkor får sällskap av en "och"-sats. Att jämföra två kolumner är samma som följande uttryck:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Anpassat uttryck

Om du vill skapa ett fritt uttryck som innehåller andra operatorer än "och" och "lika med" markerar du fältet **Anpassat uttryck.** Ange ett anpassat uttryck via dataflödesuttrycksverktyget genom att klicka på den blå rutan.

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

Exemplet nedan är en `checkForChanges` befintlig omvandling `NameNorm2` som heter `TypeConversions`som tar vänster ström och höger ström .  Tillståndet finns är `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` det uttryck som `EMPID` returnerar sant om både kolumnerna och `Region` kolumnerna i varje ström matchar. När vi letar efter `negate` existens, är falskt. Vi är inte aktivera några sändningar i `broadcast` optimera `'none'`fliken så har värde .

I Data Factory UX ser den här omvandlingen ut som bilden nedan:

![Finns exempel](media/data-flow/exists-script.png "Finns exempel")

Dataflödesskriptet för den här omvandlingen finns i kodavsnittet nedan:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Nästa steg

Liknande omvandlingar är [Uppslag och](data-flow-lookup.md) [Gå med](data-flow-join.md).

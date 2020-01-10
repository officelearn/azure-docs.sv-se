---
title: Alter Row-transformering i mappnings data flödet
description: Så här uppdaterar du databas målet med hjälp av Alter Row-omvandlingen i mappnings data flödet
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 0798a3f9ab45ce68086681e7aea96deeb9639f94
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834545"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Alter Row-transformering i mappnings data flödet

Använd transformeringen Alter Row för att ange INSERT-, DELETE-, Update-och upsert-principer på rader. Du kan lägga till ett-till-många-villkor som uttryck. Dessa villkor måste anges i prioritetsordning, eftersom varje rad markeras med principen som motsvarar det första matchnings uttrycket. Vart och ett av dessa villkor kan resultera i att en rad (eller rader) infogas, uppdateras, tas bort eller upserted. Alter Row kan producera både DDL-& DML-åtgärder mot databasen.

![Ändra rad inställningar](media/data-flow/alter-row1.png "Ändra rad inställningar")

Alter Row-transformeringar fungerar bara på databas-eller CosmosDB-mottagare i ditt data flöde. De åtgärder som du tilldelar till rader (Insert, Update, DELETE, upsert) sker inte under debug-sessioner. Kör en aktivitet för att köra data flöde i en pipeline för att införa Alter Row-principerna på dina databas tabeller.

## <a name="specify-a-default-row-policy"></a>Ange en standard rads princip

Skapa en Alter Row-omvandling och ange en rad princip med ett villkor för `true()`. Varje rad som inte matchar något av de tidigare definierade uttrycken kommer att markeras för den angivna rad principen. Som standard markeras varje rad som inte matchar ett villkors uttryck för `Insert`.

![Ändra rad princip](media/data-flow/alter-row4.png "Ändra rad princip")

> [!NOTE]
> Om du vill markera alla rader med en princip kan du skapa ett villkor för principen och ange villkoret som `true()`.

## <a name="view-policies-in-data-preview"></a>Visa principer i förhands granskning av data

Använd [fel söknings läge](concepts-data-flow-debug-mode.md) för att visa resultatet av dina Alter Row-principer i fönstret för förhands granskning. En data förhands granskning av en Alter Row-omvandling genererar inte DDL-eller DML-åtgärder mot målet.

![Ändra rad principer](media/data-flow/alter-row3.png "Ändra rad principer")

Varje Alter Row-princip representeras av en ikon som anger om åtgärden INSERT, Update, upsert eller Delete ska utföras. Den översta rubriken visar hur många rader som påverkas av varje princip i förhands granskningen.

## <a name="allow-alter-row-policies-in-sink"></a>Tillåt Alter Row-principer i mottagare

För att Alter Row-principerna ska fungera måste data strömmen skriva till en databas eller Cosmos-mottagare. På fliken **Inställningar** i din mottagare aktiverar du vilka Alter Row-principer som tillåts för mottagaren.

![Ändra rad mottagare](media/data-flow/alter-row2.png "Ändra rad mottagare")

 Standard beteendet är att endast tillåta infogningar. Om du vill tillåta uppdateringar, upsertar eller borttagningar markerar du kryss rutan i den mottagare som motsvarar det villkoret. Om uppdateringar, upsertar eller, borttagningar är aktiverade, måste du ange vilka nyckel kolumner i sinken som ska matchas.

> [!NOTE]
> Om dina infogningar, uppdateringar eller upsertar ändrar schemat för mål tabellen i sinken kommer data flödet inte att fungera. Om du vill ändra mål schema i databasen väljer du **Återskapa tabell** som tabell åtgärd. Detta tar bort och återskapar din tabell med den nya schema definitionen.

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>Exempel

Exemplet nedan är en Alter Row-omvandling med namnet `CleanData` som tar emot en inkommande data ström `SpecifyUpsertConditions` och skapar tre Alter Row-villkor. I föregående omvandling beräknas en kolumn med namnet `alterRowCondition` som avgör om en rad infogas, uppdateras eller tas bort i databasen. Om värdet för kolumnen har ett sträng värde som matchar Alter Row-regeln tilldelas den principen.

I Data Factory UX ser den här omvandlingen ut som på bilden nedan:

![Exempel på Alter Row](media/data-flow/alter-row4.png "Exempel på Alter Row")

Data flödes skriptet för den här omvandlingen är i kodfragmentet nedan:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Nästa steg

Efter omvandlingen av Alter Row kanske du vill lägga till [data i ett mål data lager](data-flow-sink.md).

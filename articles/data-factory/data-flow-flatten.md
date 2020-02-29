---
title: Koppla samman transformering av data flöde
description: Sammanslagen transformering av data flöde för Azure Data Factory mappning
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 415a093fd8a8fbe27e1d240b061548e18f2ca6b6
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164737"
---
# <a name="azure-data-factory-flatten-transformation"></a>Azure Data Factory förenkla transformering

Den sammanslagna omvandlingen kan användas för att pivotera mat ris värden i en hierarkisk struktur i nya rader, som i princip avnormaliserar dina data.

![Verktyg för omvandling](media/data-flow/flatten5.png "Verktyg för omvandling")

![Förenkla omvandling 1](media/data-flow/flatten7.png "Förenkla omvandling 1")

## <a name="unroll-by"></a>Avregistrera av

Först väljer du den matris kolumn som du vill ångra och pivotera.

![Förenkla omvandlings inställningar](media/data-flow/flatten1.png "Förenkla omvandlings inställningar")

## <a name="unroll-root"></a>Avregistrera rot

Som standard kommer ADF att förenkla strukturen på den avrullnings mat ris som du väljer ovan. Eller så kan du välja en annan del av hierarkin för att ångra. "Avregistrering av rot" är en valfri inställning.

## <a name="input-columns"></a>Inmatade kolumner

Slutligen väljer du projektionen av den nya strukturen baserat på inkommande fält samt den normaliserade kolumn som du har avregistrerat.

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Exempel

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source2
source2 foldDown(unroll(goods.orders.shipped.orderItems),
    mapColumn(
        name,
        each(goods.orders, match(type == 'integer')),
        each(goods.orders.shipped.orderItems, match(true())),
        location
    )) ~> Flatten1
Flatten1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```    

## <a name="next-steps"></a>Nästa steg

* Använd [Pivot-transformeringen](data-flow-pivot.md) för att pivotera rader till kolumner.
* Använd [unpivot-transformeringen](data-flow-unpivot.md) för att pivotera kolumner till rader.

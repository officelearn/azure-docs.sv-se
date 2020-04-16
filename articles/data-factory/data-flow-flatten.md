---
title: Förenkla omformningen i mappning av dataflöde
description: Denormalisera hierarkiska data med hjälp av förenklingsomformningen
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413678"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Förenkla omformningen i mappning av dataflöde

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd förenklingsomvandlingen för att ta matrisvärden inuti hierarkiska strukturer som JSON och rulla ut dem till enskilda rader. Denna process kallas denormalisering.

## <a name="configuration"></a>Konfiguration

Förenklingsomformningen innehåller följande konfigurationsinställningar

![Förenkla inställningar](media/data-flow/flatten1.png "Förenkla inställningar")

### <a name="unroll-by"></a>Avregistrera dig

Välj en matris som ska avregistreras. Utdata kommer att ha en rad per objekt i varje matris. Om avrullningen efter matris på indataraden är null eller tom, kommer det att finnas en utdatarad med ej registrerade värden som null.

### <a name="unroll-root"></a>Avrollrot

Som standard avregistrerar sig förenklingsomformningen en matris överst i hierarkin som den finns i. Du kan också välja en matris som avrullningsrot. Avrollningsroten måste vara en matris med komplexa objekt som antingen är eller innehåller avrullningen efter matris. Om en avrollningsrot är markerad innehåller utdata minst en rad per objekt i avrollningsroten. Om indataraden inte har några objekt i avrollningsroten tas de bort från utdata. Om du väljer en avrollningsrot matas alltid ut mindre än eller lika många rader än standardbeteendet.

### <a name="flatten-mapping"></a>Förenkla mappning

I likhet med den valda omformningen väljer du projektionen av den nya strukturen från inkommande fält och den denormaliserade matrisen. Om en denormaliserad array mappas kommer utdatakolumnen att vara samma datatyp som matrisen. Om avrullningen efter matris är en matris med komplexa objekt som innehåller underordningar, kommer mappning av ett objekt i det delarrytet att mata ut en matris.

Se fliken kontrollera och förhandsgranskning av data för att verifiera mappningsutdata.

## <a name="examples"></a>Exempel

Se följande JSON-objekt för nedanstående exempel på förenklingsomformningen

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Ingen avrollningsrot med strängmatris

| Avregistrera dig | Avrollrot | Projektion |
| --------- | ----------- | ---------- |
| varor.kunder | Ingen | namn <br> kund = goods.customer |

#### <a name="output"></a>Resultat

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Ingen avrollningsrot med komplex matris

| Avregistrera dig | Avrollrot | Projektion |
| --------- | ----------- | ---------- |
| varor.orders.shipped.orderDepartement | Ingen | namn <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> plats = plats |

#### <a name="output"></a>Resultat

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Samma rot som avväljande matris

| Avregistrera dig | Avrollrot | Projektion |
| --------- | ----------- | ---------- |
| varor.order | varor.order | namn <br> varor.orders.shipped.orderItems.itemName <br> varor.kunder <br> location |

#### <a name="output"></a>Resultat

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Rulla rot med komplex matris

| Avregistrera dig | Avrollrot | Projektion |
| --------- | ----------- | ---------- |
| varor.orders.shipped.orderItem | varor.order |namn <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> plats = plats |

#### <a name="output"></a>Resultat

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

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
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>Nästa steg

* Använd [pivotomvandlingen](data-flow-pivot.md) för att pivotera rader till kolumner.
* Använd [unpivot-omformningen](data-flow-unpivot.md) för att pivotera kolumner till rader.

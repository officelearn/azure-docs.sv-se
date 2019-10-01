---
title: Azure Data Factory mappa JSON-koncept för data flöde
description: Data Factory mappnings data flöde har inbyggda funktioner för att hantera JSON-dokument med hierarkier
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 69dce46052c18eec7c3f1fa2082907ed62b367d6
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703333"
---
# <a name="mapping-data-flow-json-handling"></a>Mappa JSON-hantering för data flöde

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="creating-json-structures-in-expression-editor"></a>Skapa JSON-strukturer i uttrycks redigeraren
### <a name="derived-column-transformation"></a>Transformering av härledd kolumn
Det blir enklare att lägga till en komplex kolumn i ditt data flöde genom den härledda kolumn uttrycks redigeraren. När du har lagt till en ny kolumn och öppnat redigeraren finns det två alternativ: Ange JSON-strukturen manuellt eller Använd användar gränssnittet för att lägga till under kolumner interaktivt.

#### <a name="interactive-ui-json-design"></a>Design av interaktiva GRÄNSSNITTs-JSON
Från fönstret utdata schema sida kan nya under kolumner läggas till med hjälp av `+`-menyn: ![Lägg]till under kolumn(media/data-flow/addsubcolumn.png "Lägg till under kolumn")

Därifrån kan du lägga till nya kolumner och under kolumner på samma sätt. För varje icke-komplext fält kan ett uttryck läggas till i uttrycks redigeraren till höger.

Komplex ![kolumn](media/data-flow/complexcolumn.png "komplex kolumn")

#### <a name="manual-json-design"></a>Manuell JSON-design
Om du vill lägga till en JSON-struktur manuellt lägger du till en ny kolumn och anger uttrycket i redigeraren. Uttrycket följer följande allmänna format:
```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```
Om det här uttrycket har angetts för en kolumn med namnet "complexColumn" skrivs det till mottagaren som följande JSON:
```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Exempel på manuellt skript för fullständig hierarkisk definition
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>Käll format alternativ
### <a name="default"></a>Standard
```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Enstaka dokument
* Alternativ ett
```
[
    {
        "json": "record 1"
    },
    {
        "json": "record 2"
    },
    {
        "json": "record 3"
    }
]
```

* Alternativ två
```
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>Icke-citerade kolumn namn
```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Har kommentarer
```
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Enkelt citat tecken
```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Omvänt snedstreck Escaped
```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Funktioner med högre ordning
## <a name="filter"></a>filter
Filtrerar element utanför matrisen som inte uppfyller det angivna predikatet. Filter förväntar sig en referens till ett element i predikatet-funktionen som #item.

### <a name="examples"></a>Exempel
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

## <a name="map"></a>map
Mappar varje element i matrisen till ett nytt element med det angivna uttrycket. Kartan förväntar sig en referens till ett element i uttrycks funktionen som #item.

### <a name="examples"></a>Exempel
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

## <a name="reduce"></a>minska
Ackumulerar element i en matris. Om du minskar förväntas en referens till ett ackumulator värde och ett element i den första uttrycks funktionen som #acc och #item och det förväntar sig att det resulterande värdet är #result som ska användas i den andra uttrycks funktionen.

### <a name="examples"></a>Exempel
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

## <a name="sort"></a>Ordning
Sorterar matrisen med den angivna predikatet-funktionen. Sortering förväntar sig en referens till två på varandra följande element i uttrycks funktionen som #item1 och #item2.

### <a name="examples"></a>Exempel
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

## <a name="contains"></a>contains
Returnerar true om något element i den angivna matrisen utvärderas som sant i angivet predikat. Contains förväntar sig en referens till ett element i predikatet-funktionen som #item.

### <a name="examples"></a>Exempel
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Nästa steg

* [Använd omvandlingen för härledd kolumn för att bygga dina hierarkiska strukturer](data-flow-derived-column.md)

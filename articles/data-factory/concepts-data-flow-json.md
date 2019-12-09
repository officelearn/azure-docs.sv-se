---
title: JSON i data flöde för mappning
description: Azure Data Factory mappnings data flöde har inbyggda funktioner för att hantera JSON-dokument med hierarkier
author: kromerm
ms.author: makromer
ms.review: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/30/2019
ms.openlocfilehash: 153c7a1003c68526c960644bebcc4800e92edc3c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928331"
---
# <a name="mapping-data-flow-json-handling"></a>Mappa JSON-hantering för data flöde

## <a name="creating-json-structures-in-derived-column"></a>Skapa JSON-strukturer i härledd kolumn

Du kan lägga till en komplex kolumn i ditt data flöde via uttrycks verktyget härledd kolumn. I den härledda kolumn omvandlingen lägger du till en ny kolumn och öppnar uttrycks verktyget genom att klicka på den blå rutan. Om du vill göra en kolumn komplex kan du ange JSON-strukturen manuellt eller använda UX för att lägga till under kolumner interaktivt.

### <a name="using-the-expression-builder-ux"></a>Använda Expression Builder UX

Hovra över en kolumn i fönstret utdata schema och klicka på plus ikonen. Välj **Lägg till under kolumn** för att göra kolumnen till en komplex typ.

![Lägg till under kolumn](media/data-flow/addsubcolumn.png "Lägg till under kolumn")

Du kan lägga till ytterligare kolumner och under kolumner på samma sätt. För varje icke-komplext fält kan ett uttryck läggas till i uttrycks redigeraren till höger.

![Komplex kolumn](media/data-flow/complexcolumn.png "Komplex kolumn")

### <a name="entering-the-json-structure-manually"></a>Ange JSON-strukturen manuellt

Om du vill lägga till en JSON-struktur manuellt lägger du till en ny kolumn och anger uttrycket i redigeraren. Uttrycket följer följande allmänna format:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Om det här uttrycket angavs för en kolumn med namnet "complexColumn", skrivs det till mottagaren som följande JSON:

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

Genom att använda en JSON-datauppsättning som källa i ditt data flöde kan du ange fem ytterligare inställningar. De här inställningarna finns under inställningen för **JSON-inställningar** på fliken **käll alternativ** .  

![JSON-inställningar](media/data-flow/json-settings.png "JSON-inställningar")

### <a name="default"></a>Standard

Som standard läses JSON-data in i följande format.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Enstaka dokument

Om **ett enstaka dokument** är markerat, kan mappning av data flöda ett JSON-dokument från varje fil. 

``` json
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

Om du väljer **icke-citerade kolumn namn** , läser mappningen av data flöden JSON-kolumner som inte omges av citat tecken. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Har kommentarer

Välj **innehåller kommentarer** om JSON-data har C- C++ eller stil kommentarer.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Enkelt citat tecken

Välj **enkelt citat tecken** om JSON-fälten och-värdena använder enkla citat tecken i stället för dubbla citat tecken.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Omvänt snedstreck Escaped

Välj **enkelt citat** tecken om omvända snedstreck används för att undanta tecken i JSON-data.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Funktioner med högre ordning

En funktion med högre ordning är en funktion som tar i en eller flera funktioner som ett argument. Nedan visas en lista över funktioner med högre ordning som stöds i mappa data flöden som aktiverar mat ris åtgärder.

### <a name="filter"></a>filter
Filtrerar element utanför matrisen som inte uppfyller det angivna predikatet. Filter förväntar sig en referens till ett element i predikatet-funktionen som #item.

#### <a name="examples"></a>Exempel
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

### <a name="map"></a>map
Mappar varje element i matrisen till ett nytt element med det angivna uttrycket. Kartan förväntar sig en referens till ett element i uttrycks funktionen som #item.

#### <a name="examples"></a>Exempel
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

### <a name="reduce"></a>minska
Ackumulerar element i en matris. Om du minskar förväntas en referens till ett ackumulator värde och ett element i den första uttrycks funktionen som #acc och #item och det förväntar sig att det resulterande värdet är #result som ska användas i den andra uttrycks funktionen.

#### <a name="examples"></a>Exempel
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

### <a name="sort"></a>sortera
Sorterar matrisen med den angivna predikatet-funktionen. Sortering förväntar sig en referens till två på varandra följande element i uttrycks funktionen som #item1 och #item2.

#### <a name="examples"></a>Exempel
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

### <a name="contains"></a>contains
Returnerar true om något element i den angivna matrisen utvärderas som sant i angivet predikat. Contains förväntar sig en referens till ett element i predikatet-funktionen som #item.

#### <a name="examples"></a>Exempel
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Nästa steg

* [Använd omvandlingen för härledd kolumn för att bygga dina hierarkiska strukturer](data-flow-derived-column.md)

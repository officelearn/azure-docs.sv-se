---
title: Arbeta med JavaScript språkintegrerade frågorna API i Azure Cosmos DB
description: Den här artikeln introducerar koncept för JavaScript språkintegrerade frågorna API att skapa lagrade procedurer och utlösare i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 101b5382eaa01ed87f05d83c82002fa1b93144b7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55463947"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Fråga JavaScript API i Azure Cosmos DB

Förutom att skicka frågor med hjälp av SQL-API i Azure Cosmos DB i [Cosmos DB SDK för serversidan](https://azure.github.io/azure-cosmosdb-js-server/) kan du utföra optimerade frågor med hjälp av ett JavaScript-gränssnitt. Du behöver känna till SQL-språket att använda den här JavaScript-gränssnitt. Frågan JavaScript API kan du programmässigt skapa frågor genom att skicka predikat funktioner i sekvens med funktionen anropas med en syntax som är bekanta dig av ECMAScript5's matris built-ins och populära JavaScript-bibliotek som Lodash. Frågor parsas av JavaScript-körning och utförs effektivt med hjälp av Azure Cosmos DB.

## <a name="supported-javascript-functions"></a>JavaScript-funktioner som stöds

| **Funktionen** | **Beskrivning** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Startar ett länkat anrop som måste avslutas med value().|
|`filter(predicateFunction [, options] [, callback])`|Filtrerar indata med hjälp av en predikat funktion som returnerar SANT/FALSKT för att filtrera inkommande dokument in/ut till den resulterande uppsättningen. Den här funktionen fungerar ungefär som en WHERE-satsen i SQL.|
|`flatten([isShallow] [, options] [, callback])`|Kombinerar och plattar ut matriser från varje inkommande objekt i en matris. Den här funktionen fungerar ungefär som SelectMany i LINQ.|
|`map(transformationFunction [, options] [, callback])`|Gäller en projektion får en transformation-funktion som mappar varje inkommande objekt till ett JavaScript-objekt eller ett värde. Den här funktionen fungerar ungefär som en SELECT-satsen i SQL.|
|`pluck([propertyName] [, options] [, callback])`|Det här är en genväg till en karta som extraherar värdet för en enskild egenskap från varje inkommande objekt.|
|`sortBy([predicate] [, options] [, callback])`|Skapar en ny uppsättning dokument genom att sortera dokument i strömmen indata-dokument i stigande ordning med hjälp av angivna predikatet. Den här funktionen fungerar ungefär som en ORDER BY-satsen i SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Skapar en ny uppsättning dokument genom att sortera dokument i strömmen indata-dokument i fallande ordning med hjälp av angivna predikatet. Den här funktionen fungerar ungefär som en x DESC ORDER BY-satsen i SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Utför en självkoppling med inre matris och lägger till resultat från båda sidorna som tupplar i projektionen resultatet. Till exempel generera ansluta till en person dokument med person.pets [namn, Husdjurets] tupplar. Detta liknar SelectMany i .NET-länk.|

När ingår i predikatet och/eller väljare funktioner, hämta följande JavaScript-konstruktioner automatiskt optimerade för att köra direkt i Azure Cosmos DB index:

- Enkel operatorer: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- Literaler, inklusive literal objektet: {}
- var, gå tillbaka

Följande JavaScript-konstruktioner inte hämta har optimerats för Azure Cosmos DB index:

- Kontrollera flödet (till exempel om efter, medan)
- Funktionsanrop

Mer information finns i den [Cosmos DB Server sida JavaScript-dokumentationen](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>SQL till facit för JavaScript

I följande tabell visas olika SQL-frågor och motsvarande JavaScript-frågor. Precis som med SQL-frågor, är egenskaper (till exempel item.id) skiftlägeskänsliga.

> [!NOTE]
> `__` (dubbelunderstreck) är ett alias till `getContext().getCollection()` när du använder frågan JavaScript API.

|**SQL**|**Fråga JavaScript API**|**Beskrivning**|
|---|---|---|
|VÄLJ *<br>FRÅN docs| __.Map(Function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;returnera doc;<br>});|Resultat i alla dokument (sidnumrerade med fortsättningstoken) som är.|
|VÄLJ <br>&nbsp;&nbsp;&nbsp;docs.ID,<br>&nbsp;&nbsp;&nbsp;docs.Message som msg,<br>&nbsp;&nbsp;&nbsp;docs.Actions <br>FRÅN docs|__.Map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projekt-id, meddelandet (ett alias till msg) och åtgärden från alla dokument.|
|VÄLJ *<br>FRÅN docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera doc.id === ”X998_Y998”;<br>});|Frågor för dokument med predikatet: id = ”X998_Y998”.|
|VÄLJ *<br>FRÅN docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (docs. Taggar 123)|__.filter(Function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera x.Tags & & x.Tags.indexOf(123) > -1;<br>});|Frågor för dokument som har en property-taggar och taggarna är en matris som innehåller värdet 123.|
|VÄLJ<br>&nbsp;&nbsp;&nbsp;docs.ID,<br>&nbsp;&nbsp;&nbsp;docs.Message som msg<br>FRÅN docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera doc.id === ”X998_Y998”;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.Map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.Value();|Frågor för dokument med ett predikat, id = ”X998_Y998” och sedan projekt-id och meddelanden (ett alias till msg).|
|SELECT VALUE-tagg<br>FRÅN docs<br>Anslut taggen i docs. Taggar<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera dokument. Taggar & & Array.isArray (doc. Taggar);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Value()|Filter för dokument som innehåller en matrisegenskap, taggar, och sorterar dokumenten av _ts tidsstämpelsegenskapen system och projekt + plattar ut taggar matrisen.|

## <a name="next-steps"></a>Nästa steg

Lär dig mer begrepp och how-to skriva och använda lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB:

- [Hur du skriver lagrade procedurer och utlösare med hjälp av Javascript fråge-API](how-to-write-javascript-query-api.md)
- [Arbeta med Azure Cosmos DB lagrade procedurer, utlösare och användardefinierade funktioner](stored-procedures-triggers-udfs.md)
- [Hur du använder lagrade procedurer, utlösare, användardefinierade funktioner i Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB JavaScript serversidan API-referens](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)

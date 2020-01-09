---
title: Arbeta med Java Script Integrated Query API i Azure Cosmos DB
description: Den här artikeln beskriver begreppen för JavaScript-språk – integrerat fråge-API för att skapa lagrade procedurer och utlösare i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 8396608cdbc5638a3640f94c94b44ad7c5f52a73
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445317"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Java Script-fråge-API i Azure Cosmos DB

Förutom att skicka frågor med hjälp av SQL-API: et i Azure Cosmos DB kan du utföra optimerade frågor med hjälp av ett JavaScript-gränssnitt i [Cosmos DB server sidans SDK](https://azure.github.io/azure-cosmosdb-js-server/) . Du behöver inte vara medveten om SQL-språket för att kunna använda det här JavaScript-gränssnittet. Med Java Script-API: et kan du skapa frågor genom programmering genom att skicka predikat till sekvenser med funktions anrop, med en syntax som är välbekant för ECMAScript5's-matriser och populära JavaScript-bibliotek som Lodash. Frågor parsas av JavaScript-körningen och körs effektivt med Azure Cosmos DB index.

## <a name="supported-javascript-functions"></a>JavaScript-funktioner som stöds

| **Funktion** | **Beskrivning** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Startar ett länkat anrop som måste avslutas med value().|
|`filter(predicateFunction [, options] [, callback])`|Filtrerar indata med hjälp av en predikat funktion som returnerar SANT/FALSKT för att filtrera inkommande dokument in/ut till den resulterande uppsättningen. Den här funktionen fungerar ungefär som en WHERE-satsen i SQL.|
|`flatten([isShallow] [, options] [, callback])`|Kombinerar och fören klar matriser från varje inobjekt till en enda matris. Den här funktionen fungerar ungefär som SelectMany i LINQ.|
|`map(transformationFunction [, options] [, callback])`|Gäller en projektion får en transformation-funktion som mappar varje inkommande objekt till ett JavaScript-objekt eller ett värde. Den här funktionen fungerar ungefär som en SELECT-satsen i SQL.|
|`pluck([propertyName] [, options] [, callback])`|Det här är en genväg till en karta som extraherar värdet för en enskild egenskap från varje inkommande objekt.|
|`sortBy([predicate] [, options] [, callback])`|Skapar en ny uppsättning dokument genom att sortera dokumenten i indata-dataströmmen i stigande ordning genom att använda det angivna predikatet. Den här funktionen fungerar ungefär som en ORDER BY-satsen i SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Skapar en ny uppsättning dokument genom att sortera dokumenten i indata-dataströmmen i fallande ordning med det angivna predikatet. Den här funktionen fungerar ungefär som en x DESC ORDER BY-satsen i SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Utför en själv koppling med en inre matris och lägger till resultat från båda sidor som tupler till resultat projektionen. Till exempel kan du ansluta till ett person dokument med person. hus djur skulle producera [person, PET]-tupler. Detta liknar SelectMany i .NET-länken.|

När ingår i predikatet och/eller väljare funktioner, hämta följande JavaScript-konstruktioner automatiskt optimerade för att köra direkt i Azure Cosmos DB index:

- Enkla operatorer: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- Literaler, inklusive literal objektet: {}
- var, gå tillbaka

Följande JavaScript-konstruktioner inte hämta har optimerats för Azure Cosmos DB index:

- Kontrollera flödet (till exempel om efter, medan)
- Funktionsanrop

Mer information finns på sidan om [Cosmos DB på Server sidans JavaScript-dokumentation](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>SQL till JavaScript lathund-blad

I följande tabell visas olika SQL-frågor och motsvarande JavaScript-frågor. Som med SQL-frågor är egenskaper (till exempel item.id) Skift läges känsliga.

> [!NOTE]
> `__` (dubbelunderstreck) är ett alias till `getContext().getCollection()` när du använder frågan JavaScript API.

|**SQL**|**Java Script-fråge-API**|**Beskrivning**|
|---|---|---|
|VÄLJ *<br>FRÅN docs| __.Map(Function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;returnera doc;<br>});|Resultat i alla dokument (sidnumrerade med fortsättningstoken) som är.|
|VÄLJ <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;dok. Message som MSG,<br>&nbsp;&nbsp;&nbsp;dokument. åtgärder <br>FRÅN docs|__.Map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projekt-id, meddelandet (ett alias till msg) och åtgärden från alla dokument.|
|VÄLJ *<br>FRÅN docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera doc.id === ”X998_Y998”;<br>});|Frågor för dokument med predikatet: id = ”X998_Y998”.|
|VÄLJ *<br>FRÅN docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (dokument. Taggar, 123)|__.filter(Function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera x.Tags & & x.Tags.indexOf(123) > -1;<br>});|Frågor för dokument som har en property-taggar och taggarna är en matris som innehåller värdet 123.|
|VÄLJ<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;dok. Message som MSG<br>FRÅN docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera doc.id === ”X998_Y998”;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.Map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.Value();|Frågor för dokument med ett predikat, id = ”X998_Y998” och sedan projekt-id och meddelanden (ett alias till msg).|
|SELECT VALUE-tagg<br>FRÅN docs<br>Anslut taggen i docs. Taggar<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera dokument. Taggar & & Array.isArray (doc. Taggar);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Value()|Filter för dokument som innehåller en matrisegenskap, taggar, och sorterar dokumenten av _ts tidsstämpelsegenskapen system och projekt + plattar ut taggar matrisen.|

## <a name="next-steps"></a>Nästa steg

Lär dig mer om begrepp och hur du skriver och använder lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB:

- [Skriva lagrade procedurer och utlösare med Java Script-fråga-API](how-to-write-javascript-query-api.md)
- [Arbeta med Azure Cosmos DB lagrade procedurer, utlösare och användardefinierade funktioner](stored-procedures-triggers-udfs.md)
- [Använda lagrade procedurer, utlösare, användardefinierade funktioner i Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB JavaScript serversidan API-referens](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)

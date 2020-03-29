---
title: Arbeta med JavaScript-integrerat fråge-API i Azure Cosmos DB-lagrade procedurer och utlösare
description: Den här artikeln introducerar begreppen för JavaScript språkintegrerat fråge-API för att skapa lagrade procedurer och utlösare i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 7b7ad470b3330224e80a7160fc1a37bb5ee1cde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76901833"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>JavaScript-fråge-API i Azure Cosmos DB

Förutom att utfärda frågor med SQL API i Azure Cosmos DB, tillhandahåller [Cosmos DB-serversidan SDK](https://azure.github.io/azure-cosmosdb-js-server/) ett JavaScript-gränssnitt för att utföra optimerade frågor i Cosmos DB-lagrade procedurer och utlösare. Du behöver inte vara medveten om SQL-språket för att kunna använda det här JavaScript-gränssnittet. Med JavaScript-fråge-API:et kan du programmässigt skapa frågor genom att skicka predikatfunktioner till sekvens av funktionsanrop, med en syntax som är bekant för ECMAScript5:s inbyggda matriser och populära JavaScript-bibliotek som Lodash. Frågor tolkas av JavaScript-körningen och körs effektivt med Azure Cosmos DB-index.

## <a name="supported-javascript-functions"></a>JavaScript-funktioner som stöds

| **Funktion** | **Beskrivning** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Startar ett kedjat anrop som måste avslutas med value().|
|`filter(predicateFunction [, options] [, callback])`|Filtrerar indata med hjälp av en predikatfunktion som returnerar sant/falskt för att filtrera in/ut indatadokument i den resulterande uppsättningen. Den här funktionen liknar en WHERE-sats i SQL.|
|`flatten([isShallow] [, options] [, callback])`|Kombinerar och plattar matriser från varje indataobjekt till en enda matris. Den här funktionen fungerar som SelectMany i LINQ.|
|`map(transformationFunction [, options] [, callback])`|Använder en projektion med en omvandlingsfunktion som mappar varje indataobjekt till ett JavaScript-objekt eller -värde. Den här funktionen liknar en SELECT-sats i SQL.|
|`pluck([propertyName] [, options] [, callback])`|Den här funktionen är en genväg för en karta som extraherar värdet för en enskild egenskap från varje indataobjekt.|
|`sortBy([predicate] [, options] [, callback])`|Skapar en ny uppsättning dokument genom att sortera dokumenten i indatadokumentflödet i stigande ordning med hjälp av det angivna predikatet. Den här funktionen liknar en ORDER BY-sats i SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Skapar en ny uppsättning dokument genom att sortera dokumenten i indatadokumentflödet i fallande ordning med hjälp av det angivna predikatet. Den här funktionen liknar en ORDER BY x DESC-sats i SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Utför en självkoppling med inre matris och lägger till resultat från båda sidor som tupplar till resultatprojektionen. Till exempel, gå med en person dokument med person.pets skulle producera [person, husdjur] tupplar. Detta liknar SelectMany i .NET LINK.|

När de ingår i predikat- och/eller väljarfunktionerna optimeras följande JavaScript-konstruktioner automatiskt för att köras direkt på Azure Cosmos DB-index:

- `=` `+` `-` `*` `/` Enkla operatörer: `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- Litteraler, inklusive objektet litteral:{}
- var, retur

Följande JavaScript-konstruktioner optimeras inte för Azure Cosmos DB-index:

- Kontrollflöde (till exempel om, för, medan)
- Funktionsanrop

Mer information finns i [JavaScript-dokumentationen](https://azure.github.io/azure-cosmosdb-js-server/)för Cosmos DB Server Side .

## <a name="sql-to-javascript-cheat-sheet"></a>SQL till JavaScript lathund

I följande tabell visas olika SQL-frågor och motsvarande JavaScript-frågor. Precis som med SQL-frågor är egenskaper (till exempel item.id) skiftlägeskänsliga.

> [!NOTE]
> `__` (dubbelunderstreck) är ett alias till `getContext().getCollection()` när du använder frågan JavaScript API.

|**SQL**|**JavaScript-fråge-API**|**Beskrivning**|
|---|---|---|
|VÄLJ *<br>FRÅN dokument| __.map(funktion(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;returdokument;<br>});|Resulterar i alla dokument (sidnumrerade med fortsättningstoken) som det är.|
|VÄLJ <br>&nbsp;&nbsp;&nbsp;docs.id.<br>&nbsp;&nbsp;&nbsp;docs.message AS msg,<br>&nbsp;&nbsp;&nbsp;docs.åtgärder <br>FRÅN dokument|__.map(funktion(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;åtgärder:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projekt id, meddelande (aliased till msg) och åtgärd från alla dokument.|
|VÄLJ *<br>FRÅN dokument<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(funktion(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera doc.id ==="X998_Y998";<br>});|Frågor om dokument med predikat: id = "X998_Y998".|
|VÄLJ *<br>FRÅN dokument<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(dokument. Taggar, 123)|__.filter(funktion(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera x.Tags && x.Tags.indexOf(123) > -1;<br>});|Frågor om dokument som har egenskapen Tags och Taggar är en matris som innehåller värdet 123.|
|VÄLJ<br>&nbsp;&nbsp;&nbsp;docs.id.<br>&nbsp;&nbsp;&nbsp;docs.message AS msg<br>FRÅN dokument<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(funktion(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|Frågor om dokument med predikat, id = "X998_Y998", och sedan projekt id och meddelande (aliased till msg).|
|VÄLJ VÄRDETAGG<br>FRÅN dokument<br>JOIN tagg i dokument. Taggar<br>BESTÄLLNING AV docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(funktion(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returdokument. Taggar && Array.isArray(doc. Taggar);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(funktion(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Taggar")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.värde()|Filter för dokument som har egenskapen array, Taggar och sorterar de resulterande dokumenten efter egenskapen _ts tidsstämpelsystem och sedan projekt + plattar till matrisen Taggar.|

## <a name="next-steps"></a>Nästa steg

Lär dig fler begrepp och hur du skriver och använder lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB:

- [Så här skriver du lagrade procedurer och utlösare med Javascript Query API](how-to-write-javascript-query-api.md)
- [Arbeta med Azure Cosmos DB-lagrade procedurer, utlösare och användardefinierade funktioner](stored-procedures-triggers-udfs.md)
- [Så här använder du lagrade procedurer, utlösare, användardefinierade funktioner i Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Api-referens på Azure Cosmos DB JavaScript-serversidan](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)

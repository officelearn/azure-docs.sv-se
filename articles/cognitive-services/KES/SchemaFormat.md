---
title: Schemaformat - Knowledge API för tjänst för Kunskapsutveckling
titlesuffix: Azure Cognitive Services
description: Lär dig om schemaformat i den kunskap utforskning Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 51a812762659bcc67762b82e9c120772065aab53
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549695"
---
# <a name="schema-format"></a>Schema Format

Schemat har angetts i en JSON-fil som beskriver attributet strukturen för objekten i datafilen som används för att skapa indexet.  För varje attribut anger schemat namn, datatyp, valfritt operations och valfritt synonymer.  Ett objekt kan ha 0 eller fler värden för varje attribut.  Nedan visas ett förenklat exempel från en akademiska publikationen domän:

``` json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Attributnamn är skiftlägeskänsliga identifierare som börjar med en bokstav och endast bestå av bokstäver (A-Z), siffror (0-9) och understreck (\_).  Reserverade ”logprob”-attributet används för att ange den relativa naturliga logg sannolikheten bland objekt.

## <a name="attribute-type"></a>Attributtyp

Nedan visas en lista över attribut som stöds datatyper:

| Typ | Beskrivning | Åtgärder | Exempel |
|------|-------------|------------|---------|
| `String` | Sträng (1 – 1024 tecken) | lika med, starts_with | ”hello world” |
| `Int32` | Ett signerat 32-bitars heltal | är lika med, starts_with, is_between | 2016 |
| `Int64` | Ett signerat 64-bitars heltal | är lika med, starts_with, is_between | 9876543210 |
| `Double` | Flyttal med dubbel precision | är lika med, starts_with, is_between | 1.602e-19 |
| `Date` | Datum (1400-01-01 till 9999-12-31) | lika med, is_between | '2016-03-14' |
| `Guid` | Globalt unik identifierare | lika med | "602DD052-CC47-4B23-A16A-26B52D30C05B" |
| `Blob` | Internt komprimerade data som inte är indexerade | *Ingen* | ”Hjälpa människor och organisationer i världen för att uppnå mer” |
| `Composite` | Sammansättning av flera underordnade attribut| *Saknas* | { "Name":"harry shum", "Affiliation":"microsoft" } |

Strängattribut används för att representera strängvärden som kan visas som en del av användarfrågan.  De stöder den exakt matchningen *är lika med* åtgärd, samt de *starts_with* åtgärden för frågan slutförande scenarier, till exempel matchar ”micros” med ”microsoft”.  Skiftlägeskänsliga och fuzzy matchning för att hantera stavfel kommer att stödjas i framtida versioner.

Double-Int32/Int64-attribut används för att representera numeriska värden.  Den *is_between* åtgärden aktiverar ojämlikhet-stöd (lt, le, gt, ge) vid körning.  Den *starts_with* åtgärden har stöd för frågan slutförande scenarier, till exempel matchar ”20” med ”2016” eller ”3”. med ”3,14”.

Datumattribut används för att effektivt koda datumvärden.  Den *is_between* åtgärden aktiverar ojämlikhet-stöd (lt, le, gt, ge) vid körning.
  
GUID-attribut används för att effektivt representera GUID-värden med standardstöd för den *är lika med* igen.

BLOB-attribut används för att effektivt koda potentiellt stora data-BLOB för runtime-sökning från motsvarande objekt utan stöd för indexering åtgärder baserat på innehållet i blob-värden.

### <a name="composite-attributes"></a>Sammansatt attribut

Sammansatta attribut används för att representera en gruppering av attributvärden.  Namnet på varje underordnad attribut som börjar med namnet på attributet sammansatta följt av ””.  Värden för sammansatt attribut anges som ett JSON-objekt som innehåller de kapslad attributvärdena.  Sammansatta attribut kan ha flera objektvärden.  Sammansatta attribut kan inte ha underordnade attribut som själva sammansatta attribut.

I akademiska publikationen exemplet ovan gör detta att tjänsten ska fråga efter handlingar ”harry shum” när han är i ”microsoft”.  Tjänsten kan endast utan sammansatta attribut fråga för rapporter där en av författarna är ”harry shum” och en av författarna är ”Microsoft”.  Mer information finns i [sammansatta frågor](SemanticInterpretation.md#composite-function).

## <a name="attribute-operations"></a>Attributet åtgärder

Som standard indexeras varje attribut för att stödja alla åtgärder som är tillgängliga för attributdatatypen.  Om en viss åtgärd inte krävs kan uppsättning indexerade åtgärder anges uttryckligen för att minska storleken på indexet.  I följande kodavsnitt från ovanstående exempel schemat attributet Author.Id indexeras för att stöder bara den *är lika med* igen, men inte ytterligare *starts_with* och *is_between*  åtgärder för Int32 attribut.
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

När ett attribut som refereras i en grammatik i *starts_with* åtgärden måste anges i schemat för tjänsten för att generera Completion-händelser från delar frågor.  

## <a name="attribute-synonyms"></a>Attributet synonymer

Ofta är det klokt att referera till ett visst strängvärde för attribut med en synonym.  Användare kan till exempel referera till ”Microsoft” som ”MSFT” eller ”MS”.  I dessa fall kan attributdefinitionen ange namnet på en schemafil som finns i samma katalog som schemafilen.  Varje rad i filen synonymen representerar en synonym post i följande JSON-format: `["<canonical>", "<synonym>"]`.  I exempel-schema är ”AuthorName.syn” en JSON-fil som innehåller synonymen värden för attributet Author.Name.

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


Ett canonical värde kan ha flera synonymer.  Flera kanoniska värden kan dela vanliga synonymer.  I sådana fall kan löser tjänsten tvetydighet via flera tolkningar.  Nedan är en exempelfil AuthorName.syn synonymer motsvarar enligt schemat ovan:
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```

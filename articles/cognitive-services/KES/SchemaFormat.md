---
title: Schemaformat finns i Knowledge utforskning Service API | Microsoft Docs
description: Lär dig mer om Schemaformatet i den kunskap utforskning Service (KES) API i kognitiva Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 3009392a5acb12a8f4df3d30a2cbe5e74f2172fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351726"
---
# <a name="schema-format"></a>Schemaformat
Schemat har angetts i en JSON-fil som beskriver attributstruktur av objekt i datafilen som används för att skapa index.  För varje attribut anger schemat namn, datatyp, valfria åtgärder och valfria synonymer.  Ett objekt kan ha 0 eller fler värden för varje attribut.  Nedan visas ett förenklat exempel från ett academic publikationen domänen:

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

Attributnamn är skiftlägeskänsliga identifierare som börjar med en bokstav och endast bestå av bokstäver (A-Z), siffror (0-9) och understreck (\_).  Attributet reserverade ”logprob” används för att ange relativa den naturliga logaritmen troliga bland objekt.

## <a name="attribute-type"></a>Attributtyp
Nedan visas en lista över attribut som stöds datatyper:

| Typ | Beskrivning | Åtgärder | Exempel |
|------|-------------|------------|---------|
| Sträng | Sträng (1 till 1 024 tecken) | är lika med starts_with | ”hello world” |
| Int32 | 32-bitars heltal | är lika med, starts_with, is_between | 2016 |
| Int64 | 64-bitars heltal | är lika med, starts_with, is_between | 9876543210 |
| Dubbel | Flyttal med dubbel precision | är lika med, starts_with, is_between | 1.602e-19 |
| Date | Datum (1400-01-01 och 9999-12-31) | är lika med is_between | ' 2016-03-14' |
| GUID | Globalt unik identifierare | lika med | ”602DD052-CC47-4B23-A16A-26B52D30C05B” |
| Blob | Internt komprimerade data för icke-indexerat | *Ingen* | ”Ge varje person och varje organisation på planeten för att uppnå mer” |
| Sammansatt | Sammansättning av flera underordnade attribut| *Saknas* | {”Name”: ”harry shum”, ”anknytningen”: ”microsoft”} |

Strängattribut används för att representera strängvärden som kan visas som en del av användarfrågan.  De stöder den exakt matchningen *är lika med* åtgärd, samt de *starts_with* åtgärd för frågan slutförande scenarier, till exempel matchar ”micros” med ”microsoft”.  Icke-skiftlägeskänsliga och fuzzy matchar för att hantera stavfel stöds i framtida versioner.

Double-Int32/Int64 attribut används för att representera numeriska värden.  Den *is_between* åtgärden aktiverar olikhet stöd (lt, le, gt, ge) vid körning.  Den *starts_with* åtgärden stöder frågan slutförande scenarier, till exempel matchar ”20” med ”2016” eller ”3”. med ”3,14”.

Datumattribut används för att koda effektivt datum.  Den *is_between* åtgärden aktiverar olikhet stöd (lt, le, gt, ge) vid körning.
  
GUID-attribut används för att effektivt representerar GUID-värden med standardstöd för den *är lika med* igen.

BLOB-attribut används för att effektivt koda potentiellt stora mängder data BLOB för runtime sökning i motsvarande objekt utan stöd för indexering åtgärder baserat på innehållet i blob-värden.

### <a name="composite-attributes"></a>Sammansatt attribut
Sammansatta attribut används för att representera en gruppering av attributvärden.  Namnet på varje underordnad attribut som börjar med namnet på attributet sammansatta följt av ””.  Värdena för sammansatta attribut anges som ett JSON-objekt som innehåller kapslade attributvärden.  Sammansatta attribut kan inte ha flera objektvärden.  Sammansatta attribut kan inte ha underordnade attribut som själva sammansatta attribut.

Detta gör tjänsten för att fråga efter handlingar ”harry shum” när han är i ”microsoft” i academic publikationen exemplet ovan.  Tjänsten kan endast utan sammansatta attribut fråga för artiklar där en av författarna är ”harry shum” och en av författarna är ”Microsoft”.  Mer information finns i [sammansatta frågor](SemanticInterpretation.md#composite-function).

## <a name="attribute-operations"></a>Attributet åtgärder
Som standard indexeras varje attribut för att stödja alla åtgärder som är tillgängliga för attributets datatyp.  Om en viss åtgärd inte krävs anges uppsättning indexerade åtgärder uttryckligen att minska storleken på indexet.  I följande kodavsnitt från ovanstående exempel schemat attributet Author.Id indexeras så att den stöder endast den *är lika med* åtgärden, men inte ytterligare *starts_with* och *is_between*  åtgärder för Int32 attribut.
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

När ett attribut som refereras i en grammatik den *starts_with* åtgärden måste anges i schemat för tjänsten för att generera slutföranden från partiella frågor.  

## <a name="attribute-synonyms"></a>Attributet synonymer
Det är ofta önskvärt att referera till en viss sträng attributvärdet genom en synonym.  Användare kan till exempel referera till ”Microsoft” som ”MSFT” eller ”MS”.  I dessa fall kan attributdefinitionen ange namnet på en schemafil som finns i samma katalog som schemafilen.  Varje rad i filen synonymen representerar en synonym post i följande JSON-format: `["<canonical>", "<synonym>"]`.  I exempel-schemat är ”AuthorName.syn” en JSON-fil som innehåller synonymen värden för attributet Author.Name.

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


Kanoniskt värde kan ha flera synonymer.  Flera kanoniska värden kan dela vanliga synonymer.  I sådana fall kommer tjänsten löser tvetydigheten via flera tolkningar.  Nedan finns en exempelfil AuthorName.syn synonymer motsvarar enligt schemat ovan:
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```

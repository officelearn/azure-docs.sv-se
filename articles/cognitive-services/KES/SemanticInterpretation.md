---
title: Semantisk tolkning - Knowledge API för tjänst för Kunskapsutveckling
titlesuffix: Azure Cognitive Services
description: Lär dig mer om att använda semantisk tolkning i den kunskap utforskning Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 2b0065dbdac8e3bdbc535f2d7d103b24110e1d02
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217279"
---
# <a name="semantic-interpretation"></a>Semantisk tolkning

Semantisk tolkning associerar semantiska utdata med varje tolkad sökväg via grammatik.  I synnerhet tjänsten utvärderar de instruktioner i den `tag` element slut genom tolkning att beräkna det slutgiltiga resultatet.  

En instruktion kan vara en tilldelning av en literal eller en variabel till en annan variabel.  Det kan också tilldela en variabel utdata för en funktion med 0 eller fler parametrar.  Varje funktionsparameter kan anges med hjälp av en literal eller en variabel.  Om funktionen inte returnerar några utdata utelämnas tilldelningen.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

En variabel har angetts med hjälp av en namnidentifierare som börjar med en bokstav och endast består av bokstäver (A-Z), siffror (0-9) och understreck (\_).  Typen implicit härleds från literalen eller funktionen utdata värdet som tilldelas till den. 

Nedan visas en lista över typer av data som för närvarande stöds:

|Type|Beskrivning|Exempel|
|----|----|----|
|Sträng|Sekvens med 0 eller flera tecken|"Hello World!"<br/>""|
|Bool|Booleskt värde|sant<br/>falskt|
|Int32|32-bitars heltal.  -2.1e9 2.1e9|123<br/>-321|
|Int64|64-bitars heltal. -9.2e18 och 9.2e18|9876543210|
|Double-värde|Med dubbel precision. 1.7E +/-308 (15 siffror)|123.456789<br/>1.23456789e2|
|GUID|Globalt unik identifierare|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Fråga|Frågeuttryck som anger en delmängd av dataobjekt i indexet|All()<br/>Och (*q1*, *q2*)|

## <a name="semantic-functions"></a>Semantisk funktioner

Det finns en inbyggd uppsättning semantiska funktioner.  De kan konstruktion av avancerade frågor och ger sammanhangsberoende kontroll över grammatik tolkningar.

### <a name="and-function"></a>Och fungerar

`query = And(query1, query2);`

Returnerar en fråga som består av skärningspunkten för två inkommande frågor.

### <a name="or-function"></a>Eller funktion

`query = Or(query1, query2);`

Returnerar en fråga bestå av unionen av två inkommande frågor.

### <a name="all-function"></a>Alla funktion

`query = All();`

Returnerar en fråga som innehåller alla dataobjekt.

I följande exempel använder vi funktionen All() att upprepade gånger bygga upp en fråga baserad på skärningspunkten för 1 eller flera nyckelord.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>Ingen fungerar

`query = None();`

Returnerar en fråga som innehåller inga dataobjekt.

I följande exempel använder vi funktionen None() att upprepade gånger bygga upp en fråga baserat på summan av 1 eller flera nyckelord.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Frågefunktionen

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

Returnerar en fråga som innehåller endast dataobjekt vars attributet *%{attrname/* matchar värdet *värdet* enligt den angivna åtgärden *op*, som standard är ”eq”.  Normalt använder ett `attrref` element att skapa en fråga som baseras på den matchade inkommande frågesträngen.  Om ett värde anges eller erhålls via andra sätt, användas funktionen Query() för att skapa en fråga som matchar det här värdet.

I följande exempel använder vi funktionen Query() för att implementera stöd för att ange academic-publikationer från ett visst årtionde.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Sammansatt funktion

`query = Composite(innerQuery);`

Returnerar en fråga som innehåller en *innerQuery* består av matchningar mot underordnade attribut för ett gemensamt sammansatta attribut *attr*.  Inkapslingen kräver attributet sammansatta *attr* för alla matchande dataobjektet har minst ett värde som uppfyller individuellt i *innerQuery*.  Observera att en fråga på underordnade attribut för ett sammansatt attribut måste vara inkapslade funktionen Composite() innan den kan kombineras med andra frågor.

Följande fråga returnerar till exempel akademiska publikationer efter ”harry shum” medan han var på ”microsoft”:
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

Följande fråga returnerar å andra sidan akademiska publikationer där en av författarna är ”harry shum” och en av anknytningar är ”microsoft”:
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>GetVariable funktion

`value = GetVariable(name, scope);`

Returnerar värdet för variabeln *namn* definierats under den angivna *omfång*.  *namn på* är en identifierare som börjar med en bokstav och består bara av bokstäver (A-Z), siffror (0-9) och understreck (_).  *omfång* kan anges till ”-begäran” eller ”system”.  Observera att variabler som definieras under olika omfång skiljer sig från varandra, inklusive de som definierats via utdata för semantisk funktioner.

Frågevariabler för omfånget är gemensamma för alla tolkningar inom den aktuella tolkningar-begäran.  De kan användas för att styra letar tolkningar över grammatik.

Systemvariabler är fördefinierade av tjänsten och kan användas för att hämta statistik om det aktuella tillståndet för systemet.  Nedan är uppsättningen som för närvarande stöds systemvariabler:

|Name|Typ|Beskrivning|
|----|----|----|
|IsAtEndOfQuery|Bool|SANT om den aktuella tolkningen matchade alla inkommande frågetexten|
|IsBeyondEndOfQuery|Bool|SANT om den aktuella tolkningen har föreslagits slutföranden utöver inkommande frågetexten|

### <a name="setvariable-function"></a>SetVariable funktion

`SetVariable(name, value, scope);`

Tilldelar *värdet* till variabeln *namn* under den angivna *omfång*.  *namn på* är en identifierare som börjar med en bokstav och består bara av bokstäver (A-Z), siffror (0-9) och understreck (_).  För närvarande det enda giltiga värdet för *omfång* är ”begär”.  Det finns inga inställbar systemvariabler.

Frågevariabler för omfånget är gemensamma för alla tolkningar inom den aktuella tolkningar-begäran.  De kan användas för att styra letar tolkningar över grammatik.

### <a name="assertequals-function"></a>AssertEquals funktion

`AssertEquals(value1, value2);`

Om *value1* och *value2* är motsvarande funktionen lyckas och inga sidoeffekter.  Annars misslyckas funktionen och avvisar tolkning.

### <a name="assertnotequals-function"></a>AssertNotEquals funktion

`AssertNotEquals(value1, value2);`

Om *value1* och *value2* är inte likvärdig funktionen lyckas och inga sidoeffekter.  Annars misslyckas funktionen och avvisar tolkning.



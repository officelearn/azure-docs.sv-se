---
title: Semantisk tolkning – Tjänst för kunskapsutveckling-API
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder semantisk tolkning i Tjänst för kunskapsutveckling (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 26f8d885f8cf85ab849ba221392df206e492aac4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220065"
---
# <a name="semantic-interpretation"></a>Semantisk tolkning

Semantisk tolkning associerar semantiska utdata med varje tolkad sökväg genom grammatiken.  Tjänsten utvärderar i synnerhet sekvensen av instruktioner i `tag` element som passerar av tolkningen för att beräkna den slutliga utmatningen.  

En instruktion kan vara en tilldelning av en literal eller en variabel till en annan variabel.  Den kan också tilldela utdata från en funktion med 0 eller fler parametrar till en variabel.  Varje funktions parameter kan anges med en literal eller en variabel.  Om funktionen inte returnerar några utdata utelämnas tilldelningen.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

En variabel anges med en namn identifierare som börjar med en bokstav och endast består av bokstäver (A-Z), siffror (0-9) och under streck (\_).  Dess typ är implicit härledd från det angivna värdet för literal eller funktion. 

Nedan visas en lista över data typer som stöds för närvarande:

|Typ|Beskrivning|Exempel|
|----|----|----|
|String|Sekvens med 0 eller fler tecken|"Hello World!"<br/>""|
|Bool|Booleskt värde|true<br/>false|
|Int32|32-bitars heltal.  -2.1 E9 till 2.1 E9|123<br/>-321|
|Int64|64-bitars heltal. -9.2 E18 och 9.2 E18|9876543210|
|Double-värde|Dubbel precision, svävande punkt. 1.7 e +/-308 (15 siffror)|123,456789<br/>1.23456789e2|
|Guid|Globalt unik identifierare|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Fråga|Frågeuttryck som anger en delmängd av data objekt i indexet|Alla ()<br/>Och (*Q1*, *Q2*)|

## <a name="semantic-functions"></a>Semantiska funktioner

Det finns en inbyggd uppsättning semantiska funktioner.  De tillåter avancerade frågor och ger Sammanhangs beroende kontroll över grammatiska tolkningar.

### <a name="and-function"></a>Och-funktion

`query = And(query1, query2);`

Returnerar en fråga som består av överlappningen av två ingångs frågor.

### <a name="or-function"></a>Eller funktion

`query = Or(query1, query2);`

Returnerar en fråga som består av en union av två ingångs frågor.

### <a name="all-function"></a>All-funktion

`query = All();`

Returnerar en fråga som innehåller alla data objekt.

I följande exempel använder vi funktionen all () för att iterativt skapa en fråga baserat på skärnings punkten av 1 eller flera nyckelord.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>Ingen-funktion

`query = None();`

Returnerar en fråga som inte innehåller några data objekt.

I följande exempel använder vi funktionen ingen () för att upprepa skapandet av en fråga baserat på en union av 1 eller flera nyckelord.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Funktionen fråga

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

Returnerar en fråga som endast innehåller data objekt vars attribut *attrName* matchar värde *svärdet* enligt den angivna åtgärden *op*, som är standardvärdet för "EQ".  Använd vanligt vis ett `attrref`-element för att skapa en fråga baserat på den matchade frågesträngen för indatamängd.  Om ett värde anges eller hämtas på annat sätt kan funktionen Query () användas för att skapa en fråga som matchar det här värdet.

I följande exempel använder vi funktionen Query () för att implementera stöd för att ange akademiska publikationer från en viss årtionde.

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

Returnerar en fråga som kapslar in en *innerQuery* som består av matchningar mot underordnade attribut för ett gemensamt sammansatt *attribut.*  Inkapslingen kräver att det sammansatta attributet *attr* av ett matchande data objekt har minst ett värde som individuellt uppfyller *innerQuery*.  Observera att en fråga om underordnade attribut för ett sammansatt attribut måste kapslas in med funktionen sammansatt () innan den kan kombineras med andra frågor.

Följande fråga returnerar till exempel akademiska publikationer av "Harry Shum" medan han var på "Microsoft":
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

På den andra sidan returnerar följande fråga akademiska publikationer där en av författarna är "Harry Shum" och en av anknytningarna är "Microsoft":
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>Funktionen GetVariable

`value = GetVariable(name, scope);`

Returnerar värdet för variabel *namnet* som definierats i det angivna *omfånget*.  *Name* är en identifierare som börjar med en bokstav och endast består av bokstäver (a-Z), siffror (0-9) och under streck (_).  *omfånget* kan anges till "Request" eller "system".  Observera att variabler som definieras under olika omfång skiljer sig från varandra, inklusive de som definierats via resultatet av semantiska funktioner.

Variabler för begär ande omfång delas över alla tolkningar inom den aktuella tolknings förfrågan.  De kan användas för att styra sökningen efter tolkningar över grammatiken.

Systemvariabler är fördefinierade av tjänsten och kan användas för att hämta olika statistik om systemets aktuella tillstånd.  Nedan visas en uppsättning systemvariabler som stöds för närvarande:

|Namn|Typ|Beskrivning|
|----|----|----|
|IsAtEndOfQuery|Bool|sant om den aktuella tolkningen har matchat all text i inmatade frågor|
|IsBeyondEndOfQuery|Bool|sant om den aktuella tolkningen har föreslagit slut för ande efter text frågan|

### <a name="setvariable-function"></a>Funktionen SetVariable

`SetVariable(name, value, scope);`

Tilldelar *värdet* variabel *namn* under det angivna *omfånget*.  *Name* är en identifierare som börjar med en bokstav och endast består av bokstäver (a-Z), siffror (0-9) och under streck (_).  För närvarande är det enda giltiga värdet för *scopet* "Request".  Det finns inga inställbara systemvariabler.

Variabler för begär ande omfång delas över alla tolkningar inom den aktuella tolknings förfrågan.  De kan användas för att styra sökningen efter tolkningar över grammatiken.

### <a name="assertequals-function"></a>Funktionen AssertEquals

`AssertEquals(value1, value2);`

Om *value1* och *värde2* är likvärdiga slutförs funktionen och saknar sido effekter.  Annars Miss lyckas funktionen och avvisar tolkningen.

### <a name="assertnotequals-function"></a>Funktionen AssertNotEquals

`AssertNotEquals(value1, value2);`

Om *value1* och *värde2* inte är likvärdiga, lyckas funktionen och saknar sido effekter.  Annars Miss lyckas funktionen och avvisar tolkningen.



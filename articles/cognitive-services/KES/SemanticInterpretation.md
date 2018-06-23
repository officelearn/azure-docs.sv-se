---
title: Semantisk tolkning i Knowledge utforskning Service API | Microsoft Docs
description: Lär dig hur du använder semantisk tolkning i den kunskap utforskning Service (KES) API i kognitiva Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 022188464eb7269b69f96a058b444167b587387c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351810"
---
# <a name="semantic-interpretation"></a>Semantisk tolkning
Semantisk tolkning associerar semantiska utdata med varje tolkad sökväg via grammatik.  I synnerhet tjänsten utvärderar sekvensen av instruktionerna i den `tag` element slut genom att beräkna den slutgiltiga utdatan tolkning.  

Ett uttryck kan vara en tilldelning av ett litteralvärde eller en variabel till en annan variabel.  Det kan också tilldela en variabel utdata för en funktion med 0 eller fler parametrar.  Varje funktionsparameter kan anges med ett litteralvärde eller en variabel.  Om funktionen inte returnerar några utdata utelämnas tilldelningen.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

En variabel anges med en identifierare för namn som börjar med en bokstav och endast består av bokstäver (A-Z), siffror (0-9) och understreck (\_).  Dess typ är implicit härledas från literalen eller värdet som tilldelas den funktionen. 

Nedan visas en lista över typer av data som för närvarande stöds:

|Typ|Beskrivning|Exempel|
|----|----|----|
|Sträng|Sekvensen 0 eller fler tecken|”Hello World”!<br/>""|
|Bool|Booleskt värde|true<br/>false|
|Int32|32-bitars heltal.  -2.1e9 2.1e9|123<br/>-321|
|Int64|64-bitars heltal. -9.2e18 och 9.2e18|9876543210|
|Dubbel|Dubbel precision flyttal. 1.7E +/-308 (15 decimaler)|123.456789<br/>1.23456789e2|
|GUID|Globalt unik identifierare|”602DD052-CC47-4B23-A16A-26B52D30C05B”|
|Fråga|Frågeuttryck som anger en delmängd av dataobjekt i indexet|All()<br/>Och (*F1*, *K2*)|

<a name="semantic-functions"></a>
## <a name="semantic-functions"></a>Semantiska funktioner
Det finns en inbyggd uppsättning semantiska funktioner.  De tillåter konstruktionen av avancerade frågor och ger sammanhangsberoende kontroll över grammatik tolkningar.

### <a name="and-function"></a>Och fungerar
`query = And(query1, query2);`

Returnerar en fråga består av skärningspunkten för två inkommande frågor.

### <a name="or-function"></a>Eller funktion
`query = Or(query1, query2);`

Returnerar en fråga består från unionen av två inkommande frågor.

### <a name="all-function"></a>Alla funktion
`query = All();`

Returnerar en fråga som innehåller alla dataobjekt.

I följande exempel tar funktionen vi All() att upprepade gånger bygga upp en fråga baserat på skärningspunkten för 1 eller flera nyckelord.

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

I följande exempel tar funktionen vi None() att upprepade gånger bygga upp en fråga baserat på unionen av 1 eller flera nyckelord.

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

Returnerar en fråga som innehåller endast dataobjekt vars attribut *%{attrname/* matchar värde *värdet* enligt den angivna åtgärden *op*, vilket som standard ”eq”.  Normalt använder ett `attrref` element för att skapa en fråga baserat på matchade inkommande frågesträng.  Om ett värde anges eller hämtas på annat sätt, användas funktionen Query() för att skapa en fråga som matchar det här värdet.

I följande exempel tar funktionen vi Query() för att implementera stöd för att ange academic publikationer från en viss tio åren.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

<a name="composite-function"/>
### <a name="composite-function"></a>Sammansatt funktion
`query = Composite(innerQuery);`

Returnerar en fråga som innehåller en *innerQuery* består av matchningar mot underordnade attribut för ett gemensamt attribut för sammansatta *Line*.  Inkapslingen kräver attributet sammansatta *Line* för alla matchande dataobjektet ha minst ett värde som uppfyller individuellt i *innerQuery*.  Observera att en fråga på underordnade attribut för ett sammansatt attribut måste vara kapslas in med hjälp av funktionen Composite() innan den kan kombineras med andra frågor.

Följande fråga returnerar till exempel academic publikationer efter ”harry shum” när han var på ”microsoft”:
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

Följande fråga returnerar å andra sidan academic publikationer där en av författarna är ”harry shum” och en av anknytningar är ”microsoft”:
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>GetVariable funktion
`value = GetVariable(name, scope);`

Returnerar värdet för variabeln *namn* definierade under den angivna *omfång*.  *namnet* är en identifierare som börjar med en bokstav och endast består av bokstäver (A-Z), siffror (0-9) och understreck (_).  *scope* kan anges till ”begäran” eller ”system”.  Observera att variabler som definieras under olika omfång skiljer sig från varandra, inklusive de som definieras via utdata för semantisk funktioner.

Frågevariabler för scope är gemensamma för alla tolkningar inom den aktuella interpret-begäranden.  De kan användas för att styra sökningen efter tolkningar över grammatik.

Systemvariabler är fördefinierade av tjänsten och kan användas för att hämta statistik om systemets aktuella tillstånd.  Nedan finns system som för närvarande stöds variabler:

|Namn|Typ|Beskrivning|
|----|----|----|
|IsAtEndOfQuery|Bool|TRUE om den aktuella tolkningen matchade alla inkommande frågetexten|
|IsBeyondEndOfQuery|Bool|TRUE om den aktuella tolkningen har förslag slutföranden utöver inkommande frågetexten|

### <a name="setvariable-function"></a>SetVariable funktion
`SetVariable(name, value, scope);`

Tilldelar *värdet* variabel *namn* under den angivna *omfång*.  *namnet* är en identifierare som börjar med en bokstav och endast består av bokstäver (A-Z), siffror (0-9) och understreck (_).  För närvarande det enda giltiga värdet för *omfång* är ”begär”.  Det finns inga går systemvariabler.

Frågevariabler för scope är gemensamma för alla tolkningar inom den aktuella interpret-begäranden.  De kan användas för att styra sökningen efter tolkningar över grammatik.

### <a name="assertequals-function"></a>AssertEquals funktion
`AssertEquals(value1, value2);`

Om *value1* och *value2* är likvärdiga funktionen lyckas och inga sidoeffekter.  Annars misslyckas funktionen och avvisar tolkning.

### <a name="assertnotequals-function"></a>AssertNotEquals funktion
`AssertNotEquals(value1, value2);`

Om *value1* och *value2* är inte likvärdig funktionen lyckas och inga sidoeffekter.  Annars misslyckas funktionen och avvisar tolkning.



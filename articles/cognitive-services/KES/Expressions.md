---
title: Strukturerad frågeuttryck i Knowledge utforskning Service API | Microsoft Docs
description: Lär dig använda strukturerade frågeuttryck i den kunskap utforskning Service (KES) API i kognitiva Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 070ee311a1153bc9fb59870dce68f385a43b15f1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351711"
---
# <a name="structured-query-expression"></a>Structured Query uttryck
Ett strukturerade frågeuttryck anger en uppsättning åtgärder som ska utvärderas mot data-index.  Det består av attributet frågeuttryck och funktioner på högre nivå.  Använd den [ *utvärdera* ](evaluateMethod.md) metod för att beräkna de objekt som matchar uttrycket.  Följande är ett exempel från domänen academic publikationer som returnerar publikationer som skapats av Jaime Teevan sedan år 2013.

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

Strukturerade frågeuttryck kan hämtas från [ *tolka* ](interpretMethod.md) begäranden, där varje tolkning semantiska utdata är ett structured query-uttryck som returnerar index-objekt som matchar den frågan för inkommande naturligt språk.  Du kan också kanske de manuellt skapas med hjälp av syntaxen beskrivs i det här avsnittet.

## <a name="attribute-query-expression"></a>Attributet frågeuttryck
Attributet fråga uttryck identifierar en uppsättning objekt baserat på matchning mot ett specifikt attribut.  Olika matchande åtgärder stöds, beroende på typ av attribut och indexerade åtgärden som angetts i den [schemat](SchemaFormat.md):

| Typ | Åtgärd | Exempel |
|------|-------------|------------|
| Sträng | lika med | Rubrik = Latenta semantiska analysen (kanoniska + synonymer) |
| Sträng | lika med | Author.Name=='susan t dumais (kanoniska endast)|
| Sträng | starts_with | Titel = 'Latenta s'... |
| Double-Int32/Int64 | lika med | År = 2000 |
| Double-Int32/Int64 | starts_with | År = '20'... (ett decimalvärde som börjar med ”20”) |
| Double-Int32/Int64 | is_between | År&lt;2000 <br/> År&lt;= 2000 <br/> År&gt;2000 <br/> År&gt;= 2000 <br/> Year=[2010,2012) *(innehåller endast vänstra gränsvärdet: 2010, 2011)* <br/> År = [2000,2012] *(omfattar både gränsvärden: 2010, 2011, 2012)* |
| Date | lika med | Födelsedatumet ='1984-05-14' |
| Date | is_between | Födelsedatumet&lt;=' 2008/03/14' <br/> PublishDate = ['2000-01-01', ' 2009-12-31'] |
| GUID | lika med | ID = '602DD052-CC47-4B23-A16A-26B52D30C05B' |


Till exempel uttrycket ”Title =” Latenta s'... ”matchar alla academic publikationer vars namn börjar med strängen” Latenta s ”.  Attributet avdelning måste ange åtgärden ”starts_with” i schemat som används för att skapa indexet för att utvärdera uttrycket.

Ett frågeuttryck kan ange objekt vars kanoniskt värde matchar en given sträng med ”==” operator eller objekt där någon av dess kanoniska/synonymen värden matchar med operatorn ”=” för attribut med associerade synonymer.  Kräver båda operatorn ”lika med” anges i attributdefinitionen.


## <a name="functions"></a>Functions
Det finns en inbyggd uppsättning funktioner så att skapa mer avancerade frågeuttryck från grundläggande attributet frågor.

### <a name="and-function"></a>Och fungerar
`And(expr1, expr2)`

Returnerar skärningspunkten för två inkommande frågeuttryck.

I följande exempel returneras academic publikationer som publicerats i år 2000 om hämtning av information:

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Eller funktion
`Or(expr1, expr2)`

Returnerar unionen av två inkommande frågeuttryck.

I följande exempel returneras academic publikationer som publicerats i år 2000 om hämtning av information eller användaren modellering:

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Sammansatt funktion
`Composite(expr)`

Returnerar ett uttryck som kapslar in en inre uttryck består av frågor mot underordnade attribut för ett gemensamt sammansatta attribut.  Inkapslingen kräver attributet sammansatta för alla matchande dataobjekt som ska ha minst ett värde som uppfyller det ursprungliga uttrycket individuellt.  Observera att ett frågeuttryck på underordnade attribut för ett sammansatt attribut måste vara kapslas in med hjälp av funktionen Composite() innan den kan kombineras med andra frågeuttryck.

Följande uttryck returnerar till exempel academic publikationer efter ”harry shum” när han var på ”microsoft”:

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

Följande uttryck returnerar å andra sidan academic publikationer där en av författarna är ”harry shum” och en av anknytningar är ”microsoft”:

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```

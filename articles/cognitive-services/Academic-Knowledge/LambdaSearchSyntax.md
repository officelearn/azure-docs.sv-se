---
title: Lambda-söksyntax – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs mer om Lambda-söksyntax som du kan använda i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 284f1d90f043e2634e143508e2ab0e98cd309f46
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902696"
---
# <a name="lambda-search-syntax"></a>Lambda-Söksyntax

Varje *lambda* sökfråga strängen beskriver ett mönster för diagrammet. En fråga måste ha minst en från nod Anger från vilka graph-noden börjar vi övergången. Om du vill ange en från nod anropa den *MAG. StartFrom()* metoden samt skickar ID i en eller flera noder eller en fråga objekt som anger search-begränsningar. Den *StartFrom()* metod har tre överlagringar. Alla tar två argument med andra är valfria. Det första argumentet kan vara ett heltal, ett långt heltal uppräkningsbara samling eller en sträng som representerar en JSON-objekt, med samma semantik som i *json* search:
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

Framställningen av en lambda-sökfråga är att gå från en nod. Om du vill ange vilken typ av edge att gå igenom använda *FollowEdge()* och ange önskade edge-typer. *FollowEdge()* tar ett valfritt antal strängargument:
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> Om vi inte bryr sig om typerna av edge(s) att följa, bara Uteslut *FollowEdge()* mellan två noder: frågan beskriver alla möjliga kanter mellan dessa två noder.

Vi kan ange edge traversal-åtgärder som ska utföras på en nod via *VisitNode()*, det vill säga om du vill stoppa den här noden och returnerar den aktuella sökvägen som ett resultat eller fortsätta att utforska i diagrammet.  Uppräkningstypen *åtgärd* definierar två typer av åtgärder: *Action.Return* och *Action.Continue*. Vi kan skicka sådana uppräkningsvärdet direkt i *VisitNode()*, eller kombinera dem med bitvis- och operatorn 'och'. När två åtgärd kombineras, innebär det att båda åtgärderna kommer att tas. Obs: Använd inte bitvis- eller operator ' |' på åtgärder. Detta gör att frågan för att avsluta utan att returnera någonting. Hoppar över *VisitNode()* mellan två *FollowEdge()* anrop kommer frågan att utforska ovillkorligt diagrammet efter ankomsten till en nod.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

För *VisitNode()*, vi kan även skicka i ett lambda-uttryck av typen *uttryck\<Func\<AVI, åtgärd\>\>*, som tar en *AVI* och returnerar en genomgång av åtgärd:

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*AVI* 

*AVI* ger *skrivskyddad* dataåtkomst gränssnitt och några inbyggda hjälpfunktioner på en nod. 

### <a name="basic-data-access-interfaces"></a>Gränssnitt för åtkomst av grundläggande data

##### <a name="long-cellid"></a>lång CellID

64-bitars-ID för noden. 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(string fieldName)

Hämtar värdet för den angivna egenskapen. *T* är den önskade typen som fältet ska tolkas som. Typ av automatisk omvandling görs om den önskade typen inte kan implicit konverteras från typen i fältet. Obs: när egenskapen är ett flervärdesattribut, *GetField\<sträng\>*  genereras listan serialiseras till en Json-sträng [”val1”, ”val2”,...]. Om egenskapen inte finns, kommer den utlöser ett undantag och Avbryt den aktuella graph-utforskning.

##### <a name="bool-containsfieldstring-fieldname"></a>bool ContainsField (sträng fältnamn)

Anger om ett fält med det angivna namnet finns i den aktuella noden.

##### <a name="string-getstring-fieldname"></a>Strängen får (sträng fältnamn)

Fungerar som *GetField\<sträng\>(fältnamn)*. Men det inget genereras undantag när fältet inte hittas, returneras en tom string("") i stället.

##### <a name="bool-hasstring-fieldname"></a>bool har (sträng fältnamn)

Anger om en viss egenskap finns i den aktuella noden. Samma som *ContainsField(fieldName)*.

##### <a name="bool-hasstring-fieldname-string-value"></a>bool har (sträng fältnamn, strängvärde)

Anger om egenskapen har det angivna värdet. 

##### <a name="int-countstring-fieldname"></a>int antal (sträng fältnamn)

Få ett värde för värden för en viss egenskap. När egenskapen inte finns, returnerar 0.

### <a name="built-in-helper-functions"></a>Inbyggda hjälpfunktioner

##### <a name="action-returnifbool-condition"></a>Åtgärden return_if (bool villkor)

Returnerar *Action.Return* om villkoret är *SANT*. Om villkoret är *FALSKT* och det här uttrycket inte är ansluten med andra åtgärder med en bitvis- och -operator, graph-utforskning kommer att avbrytas.

##### <a name="action-continueifbool-condition"></a>Åtgärden continue_if (bool villkor)

Returnerar *Action.Continue* om villkoret är *SANT*. Om villkoret är *FALSKT* och det här uttrycket inte är ansluten med andra åtgärder med en bitvis- och -operator, graph-utforskning kommer att avbrytas.

##### <a name="bool-dicedouble-p"></a>bool dice (dubbla p)

Genererar ett slumptal som är större än eller lika med 0,0 och mindre än 1,0. Den här funktionen returnerar *SANT* endast om talet är mindre än eller lika med *p*.

Jämfört med *json* search *lambda* search är mer uttrycksfull: C# lambda-uttryck kan användas direkt att ange frågemönster. Här följer två exempel.

```
MAG.StartFrom(@"{
    type  : ""ConferenceSeries"",
    match : {
        FullName : ""graph""
    }
}", new List<string>{ "FullName", "ShortName" })
.FollowEdge("ConferenceInstanceIDs")
.VisitNode(v => v.return_if(v.GetField<DateTime>("StartDate").ToString().Contains("2014")),
        new List<string>{ "FullName", "StartDate" })
```

```
MAG.StartFrom(@"{
    type  : ""Affiliation"",
    match : {
        Name : ""microsoft""
    }
}").FollowEdge("PaperIDs")
.VisitNode(v => v.return_if(v.get("NormalizedTitle").Contains("graph") || v.GetField<int>("CitationCount") > 100),
        new List<string>{ "OriginalTitle", "CitationCount" })
```

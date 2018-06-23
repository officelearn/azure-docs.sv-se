---
title: Lambda-söksyntax i Academic Knowledge API | Microsoft Docs
description: 'Läs mer om Lambda-söksyntax som du kan använda i Academic Knowledge API: et i kognitiva Microsoft-tjänster.'
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f486368e1d0258087091acb846a84b125712db40
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351480"
---
# <a name="lambda-search-syntax"></a>Lambda-Söksyntax

Varje *lambda* sökfråga strängen beskriver ett mönster för diagrammet. En fråga måste ha minst en första nod som anger från vilken nod som diagrammet vi börjar övergången. Om du vill ange en startande nod anropa den *magnetiska DATAORDSLÄSARE. StartFrom()* metoden och skicka ID i en eller flera noder eller en fråga objekt som anger villkor för sökningen. Den *StartFrom()* metod har tre överlagringar. Alla ta två argument med den andra är valfria. Det första argumentet kan vara ett heltal, ett långt heltal enumerable samling eller en sträng som representerar en JSON-objekt, med samma semantik som i *json* sökning:
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

Processen att skriva en sökfråga lambda är att gå från en nod till en annan. Om du vill ange vilken typ av gräns kan du gå igenom *FollowEdge()* och ange önskade edge-typer. *FollowEdge()* tar ett godtyckligt antal strängargument:
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> Om vi inte bryr dig om typerna av edge(s) ska följa, bara utelämna *FollowEdge()* mellan två noder: frågan ska gå igenom alla kanter som möjligt mellan dessa två noder.

Vi kan ange traversal-åtgärder som ska utföras på en nod via *VisitNode()*, det vill säga om du vill stoppa den här noden och returnera den aktuella sökvägen som ett resultat eller fortsätta att utforska diagrammet.  Uppräkningstypen *åtgärd* definierar två typer av åtgärder: *Action.Return* och *Action.Continue*. Vi kan skicka dessa uppräkningsvärdet direkt i *VisitNode()*, eller kombinera dem med binär- och operator 'och'. När två åtgärd kombineras, innebär det att båda åtgärder vidtas. Obs: Använd inte binärt- eller operator ' |' på åtgärder. Gör frågan för att avsluta utan att returnera något. Hoppar över *VisitNode()* mellan två *FollowEdge()* anrop kommer frågan för att utforska ovillkorligt diagrammet efter inkommer på en nod.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

För *VisitNode()*, vi kan också skicka in ett lambda-uttryck av typen *uttryck\<Func\<AVI-noder, åtgärden\>\>*, som tar en *AVI-noder* och returnerar en traversal-åtgärd:

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*AVI-noder* 

*AVI-noder* ger *skrivskyddad* dataåtkomst gränssnitt och några inbyggda hjälpfunktioner på en nod. 

### <a name="basic-data-access-interfaces"></a>Grundläggande data access-gränssnitt

##### <a name="long-cellid"></a>lång CellID

64-bitars-ID: T för noden. 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(string fältnamn)

Hämtar värdet för den angivna egenskapen. *T* är den önskade typen fältet ska tolkas. Automatisk typen omvandling görs om den önskade typen inte går att implicit konvertera från typen i fältet. Obs: när egenskapen är ett flervärdesattribut, *GetField\<sträng\>*  kommer listan ska serialiseras till Json-strängen [”vär1”, ”vär2”...]. Om egenskapen inte finns, den utlöser ett undantag och avbryta det aktuella diagrammet undersökning.

##### <a name="bool-containsfieldstring-fieldname"></a>bool ContainsField (sträng fältnamn)

Anger om ett fält med det angivna namnet finns i den aktuella noden.

##### <a name="string-getstring-fieldname"></a>Strängen får (sträng fältnamn)

Fungerar som *GetField\<sträng\>(fältnamn)*. Men det genereras inget undantag när fältet inte hittas, returneras en tom string("") i stället.

##### <a name="bool-hasstring-fieldname"></a>bool har (sträng fältnamn)

Anger om den angivna egenskapen finns i den aktuella noden. Samma som *ContainsField(fieldName)*.

##### <a name="bool-hasstring-fieldname-string-value"></a>bool har (sträng fältnamn värde)

Anger om egenskapen har det angivna värdet. 

##### <a name="int-countstring-fieldname"></a>int count (sträng fältnamn)

Hämta antal värden för en viss egenskap. Returnerar 0 när egenskapen inte finns.

### <a name="built-in-helper-functions"></a>Inbyggda hjälpfunktioner

##### <a name="action-returnifbool-condition"></a>Åtgärden return_if (bool villkor)

Returnerar *Action.Return* om villkoret är *SANT*. Om villkoret är *FALSKT* och det här uttrycket inte är ansluten med andra åtgärder med en binär- och operator, diagram utforskning kommer att avbrytas.

##### <a name="action-continueifbool-condition"></a>Åtgärden continue_if (bool villkor)

Returnerar *Action.Continue* om villkoret är *SANT*. Om villkoret är *FALSKT* och det här uttrycket inte är ansluten med andra åtgärder med en binär- och operator, diagram utforskning kommer att avbrytas.

##### <a name="bool-dicedouble-p"></a>bool rapportanvändarna (double p)

Genererar ett slumptal som är större än eller lika med 0,0 och mindre än 1,0. Den här funktionen returnerar *SANT* endast om talet är mindre än eller lika med *p*.

Jämfört med *json* sökning, *lambda* sökningen är mer lättfattliga: C# lambda-uttryck direkt kan användas för att ange frågemönster. Här är två exempel.

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

---
title: Syntax för frågeuttryck – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder syntax för frågeuttryck i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 3b87e04c2d6380a0ee4157e73db0cd4057fadee1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704917"
---
# <a name="query-expression-syntax"></a>Syntax för frågeuttryck

Vi har sett att svaret på en **tolka** -begäran innehåller ett frågeuttryck. Grammatiken som tolkade användarens fråga skapade ett frågeuttryck för varje tolkning. Ett frågeuttryck kan sedan användas för att utfärda en **evaluate** -begäran för att hämta Sök Resultat för enheter.

Du kan också skapa egna frågeuttryck och använda dem i en **evaluate** -begäran. Detta kan vara användbart om du skapar ett eget användar gränssnitt som skapar ett frågeuttryck som svar på användarens åtgärder. För att göra detta måste du känna till syntaxen för frågeuttryck.  

Varje attribut för entiteter som kan ingå i ett frågeuttryck har en speciell datatyp och en uppsättning möjliga fråge operatorer. Uppsättningen entitetsattribut och operatorer som stöds för varje attribut anges i entitetens [attribut](EntityAttributes.md). En enda värde fråga kräver att attributet har stöd för *likhets* åtgärden. En prefix fråga kräver att attributet har stöd för *StartsWith* -åtgärden. Frågor för numeriska intervall kräver att attributet stöder *IsBetween* -åtgärden.

Några av enhets data lagras som sammansatta attribut, som anges av en punkt "." i attributnamnet. Till exempel visas författar-/anknytnings information som ett sammansatt attribut. Den innehåller fyra komponenter: AuN, AuId, AfN, AfId. Dessa komponenter är separata delar av data som utgör ett enskilt attributvärde.


**String-attribut: Enskilt värde** (inklusive matchningar mot synonymer)  
TI = ' indexering efter latens semantisk analys '  
Sammansatt (AA. AuN = ' Sue dumais ')

**String-attribut: Exakt enskilt värde** (matchar endast kanoniska värden)  
TI = = ' indexering efter latens semantisk analys '  
Sammansatt (AA. AuN = = ' Susan t dumais ')
     
**String-attribut: Prefixvärde**   
TI = ' Indexing by latend Seman '...  
Sammansatt (AA. AuN = "Sue"...)

**Numeriskt attribut: Enskilt värde**  
Y=2010
 
**Numeriskt attribut: Intervall värde**  
Y > 2005  
Y > = 2005  
Y<2010  
Y<=2010  
Y =\[2010, 2012\) (innehåller bara det vänstra gränser svärdet: 2010, 2011)  
Y =\[2010, 2012\] (inklusive båda avgränsnings värden: 2010, 2011, 2012)
 
**Numeriskt attribut: Prefixvärde**  
Y = ' 19 '... (alla numeriska värden som börjar med 19) 
 
**Datum-attribut: Enskilt värde**  
D='2010-02-04'

**Datum-attribut: Intervall värde**  
D>'2010-02-03'  
D = [' 2010-02-03 ', ' 2010-02-05 ']

**Och/eller frågor:**  
Och (Y = 1985, ti = ' disordnat elektroniskt system ')  
Eller (TI = ' disordnade elektroniska system ', ti = ' fel tolerans principer och praxis)  
Och (eller (Y = 1985, Y = 2008), ti = ' disordnade elektroniska system ')
 
**Sammansatta frågor:**  
Om du vill fråga efter komponenter i ett sammansatt attribut måste du omge den del av frågeuttrycket som refererar till attributet sammansatt i funktionen sammansatt (). 

Om du till exempel vill fråga efter dokument efter författar namn använder du följande fråga:
```
Composite(AA.AuN='mike smith')
```
<br>Använd följande fråga om du vill fråga efter dokument av en viss författare medan författaren hade en viss institution:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Funktionen sammansatt () binder samman de två delarna av attributet sammansatt. Det innebär att vi bara får arbets blad där en av författarna är "Mike Svensson" medan han var på Harvard. 

Om du vill fråga efter dokument av en viss författare i anknytningar med (andra) författare från en viss institution använder du följande fråga:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>I den här versionen, eftersom sammansatt () tillämpas på författaren och anknytningen individuellt före och (), får vi alla dokument där en av författarna är "Mike Svensson" och en av författarens anknytningar är "Harvard". Detta låter likna föregående exempel, men det är inte samma sak.

I allmänhet bör du tänka på följande exempel: Vi har ett sammansatt attribut C som har två komponenter A och B. En entitet kan ha flera värden för C. Det här är våra entiteter:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>Frågan 
```
Composite(And(C.A=1, C.B=2))
```

<br>matchar endast entiteter som har ett värde för C där komponenten C. A är 1 och komponenten C. B är 2. Endast E1 matchar den här frågan.

Frågan 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>matchar entiteter som har ett värde för C där C. A är 1 och också har ett värde för C där C. B är 2. Både E1 och E2 matchar den här frågan.

Obs!  
- Det går inte att referera till en del av ett sammansatt attribut utanför en sammansatt ()-funktion.
- Det går inte att referera till delar av två olika sammansatta attribut i samma sammansatta () funktion.
- Det går inte att referera till ett attribut som inte är en del av ett sammansatt attribut i en sammansatt ()-funktion.

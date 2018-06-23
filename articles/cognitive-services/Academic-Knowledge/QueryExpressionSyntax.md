---
title: Frågesyntax uttryck i Academic Knowledge API | Microsoft Docs
description: Lär dig mer om att använda uttryck frågesyntaxen i Academic Knowledge API för kognitiva Microsoft-tjänster.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 6ec338fff09954e2f14066ce2b83bc1228794af8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351435"
---
# <a name="query-expression-syntax"></a>Frågesyntaxen uttryck

Vi har sett som svar på en **tolka** begäran innehåller ett frågeuttryck. Grammatik som tolkas användarens fråga skapas ett frågeuttryck för varje tolkning. Ett frågeuttryck kan sedan användas ska utfärdas en **utvärdera** begäran om att hämta entiteten sökresultat.

Du kan också skapa egna frågeuttryck och använda dem i en **utvärdera** begäran. Detta kan vara användbart om du skapar ditt eget användargränssnitt som skapar ett frågeuttryck i svaret på användarens åtgärder. Om du vill göra detta, som du behöver veta syntaxen för-frågeuttryck.  

Varje entitetsattribut som kan ingå i ett frågeuttryck har en viss datatyp och en uppsättning av möjliga frågeoperatorer. Uppsättningen entitetsattribut och stöds operatorer för varje attribut har angetts i [entitetsattribut](EntityAttributes.md). Ett enskilt värde frågan kräver attribut för att stöda den *är lika med* igen. En prefix-fråga kräver attribut för att stöda den *StartsWith* igen. Numeriska intervall frågor kräver attribut för att stöda den *IsBetween* igen.

Vissa av enhetens data lagras som sammansatta attribut som anges med en punkt '.' i attributets namn. Till exempel representeras författare/anknytningen information som en sammansatt attribut. Den innehåller 4-komponenterna: AuN, AuId, AfN, AfId. Dessa komponenter är separata delar av data som bildar ett attributvärde för en enda entitet.


**Strängattribut: Enstaka värde** (inklusive matchningar mot synonymer)  
Ti = 'indexering av latenta semantiska analysen'  
Sammansatt (AA. AuN = 'stämma dumais')

**Strängattribut: Exakt värde** (matchar endast kanoniska värden)  
Ti == ”indexering av latenta semantiska analysen'  
Sammansatt (AA. AuN == ”susan t dumais')
     
**: Sträng Prefix attributvärdet**   
Ti = 'indexering av latenta seman'...  
Sammansatt (AA. AuN = 'stämma du'...)

**Numeriska attribut: Enskilt värde**  
Y = 2010
 
**Numeriska attribut: Intervall**  
Y &GT; 2005  
Y &GT; = 2005  
Y &LT; 2010  
Y &LT; = 2010  
Y =\[2010, 2012\) (innehåller endast vänstra gränsvärdet: 2010, 2011)  
Y =\[2010, 2012\] (omfattar både gränsvärden: 2010, 2011, 2012)
 
**Numeriska attribut: Värdet Prefix**  
Y = '19'... (ett numeriskt värde som börjar med 19) 
 
**Datumattribut: Enskilt värde**  
D = ”2010-02-04-

**Datumattribut: Intervall**  
D &GT; ”2010-02-03-  
D = [”2010-02-03', ' 2010-02-05']

**Och/eller frågor:**  
Och (Y = 1985, Ti = disordered elektroniska system)  
Eller (Ti = 'disordered elektroniska system', Ti = ”fault tolerance principer och praxis”)  
And(or(Y=1985,Y=2008), Ti = disordered elektroniska system)
 
**Sammansatta frågor:**  
Du måste ange del av frågeuttryck som refererar till attributet sammansatta i funktionen Composite() frågan komponenter för ett sammansatt attribut. 

Till exempel vill fråga efter papper av författarens namn, använder du följande fråga:
```
Composite(AA.AuN='mike smith')
```
<br>Om du vill fråga efter dokument från en viss författare medan författaren vid ett visst institut, använder du följande fråga:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Funktionen Composite() innehåller två delar av sammansatta attributet. Det innebär att vi bara hämta rapporter där en av författarna är ”Mike Smith” när han var på Harvard. 

Om du vill fråga efter dokument från en viss författare i anknytningar med (andra) från ett visst institut använder du följande fråga:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>I den här versionen eftersom Composite() tillämpas på författare och anknytningen individuellt innan And(), hämta vi alla rapporter där en av författarna är ”Mike Smith” och en av författarnas anknytningar är ”Harvard”. Detta låter liknar föregående exempel i frågan, men det är inte samma sak.

I allmänhet fundera på följande exempel: Vi har ett sammansatt attribut C som består av två komponenter A och b En entitet kan ha flera värden för C. Dessa är vår enheter:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>Frågan 
```
Composite(And(C.A=1, C.B=2))
```

<br>matchar endast de enheter som har ett värde för C där komponenten C.A är 1 och komponenten C.B 2. Endast E1 matchar den här frågan.

Frågan 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>matchar entiteter som har ett värde för C där C.A är 1 och även ha ett värde för C där C.B är 2. Både E1 och E2 med den här frågan.

Obs!  
- Du kan inte referera till en del av ett sammansatt attribut utanför en Composite() funktion.
- Du kan inte referera till delar av två olika sammansatta attribut i samma Composite() funktion.
- Du kan inte referera till ett attribut som inte är en del av ett sammansatt attribut i en Composite().

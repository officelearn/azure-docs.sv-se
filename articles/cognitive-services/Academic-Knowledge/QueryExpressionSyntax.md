---
title: Fråga uttryckssyntax – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder fråga uttryckssyntax i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: c130c6cd5fcb5191195712f570db66408734200a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150882"
---
# <a name="query-expression-syntax"></a>Fråga uttryckssyntax

Vi har märkt att svaret på en **tolka** begäran innehåller ett frågeuttryck. Grammatik som tolkas användarens fråga skapas ett frågeuttryck för varje tolkning. Ett frågeuttryck kan sedan användas för att utfärda en **utvärdera** begäran att hämta resultat från.

Du kan också konstruera egna frågeuttryck och använda dem i en **utvärdera** begäran. Detta kan vara användbart om du skapar ditt eget användargränssnitt som skapar ett frågeuttryck som svar på användarens åtgärder. Om du vill göra detta måste behöver du veta syntaxen för-frågeuttryck.  

Varje entitetsattribut som kan ingå i ett frågeuttryck har en specifik typ och en uppsättning frågeoperatorer som möjligt. En uppsättning entitetsattribut och operatorer som stöds för varje attribut har angetts i [entitetsattribut](EntityAttributes.md). En fråga med enkla värden kräver attributet för den *är lika med* igen. En prefix-fråga kräver attributet för den *StartsWith* igen. Numeriska intervallfrågor kräver att attributet för den *IsBetween* igen.

Några av entitetsdata lagras som sammansatta attribut som anges med en punkt '.' i attributets namn. Till exempel representeras författare/anknytning till information som en sammansatt attribut. Den innehåller 4 komponenter: AuN AuId, AfN, AfId. Dessa komponenter är separata delar som utgör en enda entitet attribut-värde.


**Strängattribut: Enstaka värde** (inklusive matchningar mot synonymer)  
Ti = 'indexering av latent semantiska analysen'  
Composite(AA.AuN='sue dumais')

**Strängattribut: Exakt värde** (matchar endast kanoniska värden)  
Ti == 'indexering av latent semantiska analysen'  
Composite(AA.AuN=='susan t dumais')
     
**Strängattribut: Prefixvärdet**   
Ti = 'indexering av latent seman'...  
Composite(AA.AuN='sue du'...)

**Numeriska attribut: Enskilt värde**  
Y=2010
 
**Numeriska attribut: Intervallvärdet**  
Y>2005  
Y &GT; = 2005  
Y<2010  
Y<=2010  
Y =\[2010, 2012\) (innehåller endast vänstra gränsens värde: 2010, 2011)  
Y =\[2010, 2012\] (omfattar både gränsvärden: 2010, 2011, 2012)
 
**Numeriska attribut: Prefixvärdet**  
Y = '19'... (ett numeriskt värde som börjar med 19) 
 
**Datumattribut: Enskilt värde**  
D='2010-02-04'

**Datumattribut: Intervallvärdet**  
D>'2010-02-03'  
D = [”2010-02-03” ”, 2010-02-05']

**Och/eller frågor:**  
Och (Y = 1985, Ti = disordered elektroniska system)  
Eller (Ti = 'disordered elektroniska system ”, Ti =” fault tolerance principer och praxis ”)  
And(or(Y=1985,Y=2008), Ti = disordered elektroniska system)
 
**Sammansatta frågor:**  
Du måste ange en del av frågeuttryck som refererar till attributet sammansatta i funktionen Composite() fråga komponenter för ett sammansatt attribut. 

Till exempel för att fråga efter Paper-faktablad av författarens namn, använder du följande fråga:
```
Composite(AA.AuN='mike smith')
```
<br>Om du vill fråga efter rapporter från en viss författare medan författaren var på en viss institution, använder du följande fråga:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Funktionen Composite() binda samman de två delarna av sammansatta attributet. Det innebär att vi bara få rapporter där en av författarna är ”Mike Smith” medan han var på Harvard. 

Om du vill fråga efter rapporter från en viss författare i anknytningar med (andra) från en viss institution, använder du följande fråga:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>I den här versionen eftersom Composite() tillämpas på författaren och anknytning individuellt innan And(), får vi alla rapporter där en av författarna är ”Mike Smith” och en av författarnas anknytningar är ”Harvard”. Detta låter liknar det föregående exemplet i frågan, men det är inte samma sak.

I allmänhet ska du tänka på följande exempel: Vi har ett sammansatt attribut C som består av två komponenter A och B. En entitet kan ha flera värden för C. Det här är våra entiteter:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>Frågan 
```
Composite(And(C.A=1, C.B=2))
```

<br>matchar endast de enheter som har ett värde för C där komponenten C.A är 1 och komponenten C.B är 2. Endast E1 matchar den här frågan.

Frågan 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>matchar entiteter som har ett värde för C där C.A är 1 och även ha ett värde för C där C.B är 2. Både E1 och E2 matchar den här frågan.

Obs!  
- Du kan inte referera till en del av ett sammansatt attribut utanför en Composite() funktion.
- Du kan inte referera till delar av två olika sammansatta attribut i samma Composite() funktion.
- Du kan inte referera till ett attribut som inte är en del av ett sammansatt attribut i en Composite().

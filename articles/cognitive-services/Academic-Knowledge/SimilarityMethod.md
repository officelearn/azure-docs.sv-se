---
title: Likhet metod i Academic Knowledge API | Microsoft Docs
description: Använd metoden likhet för att beräkna academic likheten mellan två strängar i kognitiva Microsoft-tjänster.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 01/18/2017
ms.author: alch
ms.openlocfilehash: 472498d6bfe06ae4477a30f892d44e79c901acf5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351393"
---
# <a name="similarity-method"></a>Likhet metod

Den **likhet** REST API för att beräkna academic likheten mellan två strängar. 
<br>

**REST-slutpunkt:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Begäranparametrar
Parameter        |Datatyp      |Krävs | Beskrivning
----------|----------|----------|------------
**S1**        |Sträng   |Ja  |Sträng * som ska jämföras
**S2**        |Sträng   |Ja  |Sträng * som ska jämföras
<sub> * Strängar som jämför har en största längd på 1MB. </sub>
<br>
## <a name="response"></a>Svar
Namn | Beskrivning
--------|---------
**SimilarityScore**        |En flytande peka värdet som representerar cosinus liknande s1 och s2 med värden närmare 1.0 betydelse mer liknande och värden närmare-1.0 vilket innebär att mindre
<br>

## <a name="successerror-conditions"></a>Lyckade/felvillkor
HTTP-Status | Orsak | Svar
-----------|----------|--------
**200**         |Lyckades | Flyttal
**400**         | Felaktig begäran eller är ogiltig för begäran | Felmeddelande      
**500**         |Internt serverfel | Felmeddelande
**Tidsgränsen uppnåddes**     | Förfrågan uppnådde sin tidsgräns.  | Felmeddelande
<br>
## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Exempel: Beräkna likhet av två delar sammanfattningar
#### <a name="request"></a>Begäran:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
I det här exemplet vi generera poängsättningen likheten mellan två delar sammanfattningar med hjälp av den **likhet** API.
#### <a name="response"></a>Svar:
```
0.520
```
#### <a name="remarks"></a>Anmärkning:
Poängsättningen likhet bestäms av bedöma academic begrepp via word bädda in. I det här exemplet innebär 0.52 att två delar sammanfattningar påminner.
<br>
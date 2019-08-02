---
title: Likhets metod – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Använd likhets metoden för att beräkna den akademiska likheten hos två strängar.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 855d29d2c55b841bbbe4e9eadce8c29ad85fad90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704854"
---
# <a name="similarity-method"></a>Likhets metod

**Likhets** REST API används för att beräkna den akademiska likheten mellan två strängar. 
<br>

**REST-slutpunkt:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Begäranparametrar

Parameter        |Datatyp      |Obligatorisk | Beskrivning
----------|----------|----------|------------
**s1**        |Sträng   |Ja  |Sträng * som ska jämföras
**s2**        |Sträng   |Ja  |Sträng * som ska jämföras

<sub>* Strängarna som ska jämföras har en maximal längd på 1 MB.</sub>
<br>

## <a name="response"></a>Svar

Namn | Beskrivning
--------|---------
**SimilarityScore**        |Ett flytt ALS värde som representerar cosinus på samma sätt som S1 och S2, och värden närmare 1,0 innebär mer likartade värden, närmare-1,0 vilket innebär mindre

<br>

## <a name="successerror-conditions"></a>Lyckade/Felaktiga villkor

HTTP-status | Reason | Svar
-----------|----------|--------
**200**         |Klart | Flytt ALS nummer
**400**         | Felaktig begäran eller begäran ogiltig | Felmeddelande      
**500**         |Internt serverfel | Felmeddelande
**Tids gränsen uppnåddes**     | Förfrågan uppnådde sin tidsgräns.  | Felmeddelande

<br>

## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Exempel: Beräkna likheten mellan två delvis abstrakta
#### <a name="request"></a>Begäran:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
I det här exemplet genererar vi likhets poängen mellan två delar av sammanfattningen med **likhets** -API: et.
#### <a name="response"></a>Svar:
```
0.520
```
#### <a name="remarks"></a>!
Likhets poängen bestäms genom att utvärdera de akademiska begreppen genom Word-inbäddning. I det här exemplet innebär 0,52 att de två partiella abstraktarna är något likartade.
<br>

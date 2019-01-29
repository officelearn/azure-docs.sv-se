---
title: Likhet metoden – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Du kan använda metoden likheter för att beräkna akademiska likheten mellan två strängar.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ms.openlocfilehash: 7484b570784f5f058ebd23b1e3c225c5d858a274
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55183347"
---
# <a name="similarity-method"></a>Likhet metod

Den **likheter** REST-API används för att beräkna academic-likheten mellan två strängar. 
<br>

**REST-slutpunkt:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Parametrar för begäran
Parameter        |Datatyp      |Krävs | Beskrivning
----------|----------|----------|------------
**s1**        |Sträng   |Ja  |Sträng * som ska jämföras
**s2**        |Sträng   |Ja  |Sträng * som ska jämföras
<sub> * Strängar att jämföra har en högsta längd på 1MB. </sub>
<br>
## <a name="response"></a>Svar
Name | Beskrivning
--------|---------
**SimilarityScore**        |En flytande peka värde som representerar cosinus-likheten för s1 och s2 med värden närmare 1.0 betydelsen mer liknande och värden för närmare att-1.0 vilket innebär att mindre
<br>

## <a name="successerror-conditions"></a>Lyckade/felvillkor
HTTP Status | Orsak | Svar
-----------|----------|--------
**200**         |Lyckades | Flyttalsnummer
**400**         | Felaktig begäran eller är ogiltigt för begäran | Felmeddelande      
**500**         |Internt serverfel | Felmeddelande
**Tidsgränsen uppnåddes**     | Förfrågan uppnådde sin tidsgräns.  | Felmeddelande
<br>
## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Exempel: Beräkna likheten mellan två partiella sammanfattningar
#### <a name="request"></a>Begäran:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
I det här exemplet vi genererar poängen likheter mellan två partiella sammanfattningar med hjälp av den **likheter** API.
#### <a name="response"></a>Svar:
```
0.520
```
#### <a name="remarks"></a>Anmärkning:
Likhet poängen bestäms genom att uppskatta akademiska begreppen via word bädda in. I det här exemplet innebär 0.52 att två partiella sammanfattningar något liknande.
<br>

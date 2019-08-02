---
title: Pattern. all entitetstyp – LUIS
titleSuffix: Azure Cognitive Services
description: Mönster. det finns en plats hållare med variabel längd som bara används i ett mönsters mall uttryck för att markera var entiteten börjar och slutar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: cda6c724a36a73dc34c2bf8e7158e3e3ec92d46b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563217"
---
# <a name="patternany-entity"></a>Entiteten Pattern.any 

Mönster. det finns en plats hållare med variabel längd som bara används i ett mönsters mall uttryck för att markera var entiteten börjar och slutar.  

Mönster. alla entiteter måste markeras i exempel på [mönster](luis-how-to-model-intent-pattern.md) mal len, inte avsikten med användar exempel.

**Entiteten passar bra när:**

* Entitetens slut kan förväxlas med den återstående texten i uttryck. 

## <a name="usage"></a>Användning

Ett klient program som söker efter böcker som baseras på rubrik, mönstret. alla extraherar den fullständiga titeln. En mall som uttryck använder mönster. alla för den här bok `Was {BookTitle} written by an American this year[?]`sökningen är. 

I följande tabell har varje rad två versioner av uttryck. Det främsta uttryck är hur LUIS för första gången ser uttryck. Det är inte tydligt var bok rubriken börjar och slutar. Den nedre uttryck använder ett mönster. en entitet för att markera början och slutet av entiteten. 

|Uttryck med entiteten i fetstil|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Var **det man som feltagit sin fru för en hatt och andra kliniska** visare som skrevs av ett amerikanskt år?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Var **hälften av ström spar läge i Frog Pajamas** Skrivet av ett amerikanskt år?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Var **den specifika ledsenhet av citron tårta: En nya** skriven av ett amerikanskt år?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Fanns **det en Wocket i min ficka!** Skrivet av ett amerikanskt år?|
||

## <a name="example-json"></a>Exempel på JSON

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

I den [](luis-tutorial-pattern-any.md)här självstudien använder du **mönstret. en** entitet för att extrahera data från yttranden där yttranden är välformaterade och där data slutet kan lätt förväxlas med de återstående orden i uttryck.
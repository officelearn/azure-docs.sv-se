---
title: Filtrering av svordomar – Translator Text API
titleSuffix: Azure Cognitive Services
description: Använd att filtrera svordomar i Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 114dea098db5c824a1235ba1635f547383bf6743
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595191"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Lägg till svordoms-filtrering med Translator Text API

Tjänsten Translator behåller normalt svordomar som finns i källan i översättningen. Graden av svordomar och kontexten som gör ord svordomar olika mellan kulturer. På grund av detta kan det innebära att svordomar på mål språket förstärks eller minskas.

Om du vill undvika att se svordomar i översättningen, även om det finns svordomar i käll texten, använder du filtrerings alternativet för svordomar i metoden Översätt (). Med det här alternativet kan du välja om du vill se hur svordomar tas bort, markerade med lämpliga taggar eller vidta åtgärder som inte vidtas.

Metoden Översätt () använder parametern "alternativ", som innehåller det nya elementet "ProfanityAction". De godkända värdena för ProfanityAction är "noaction", "märkta" och "Deleted".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Godkända värden för ProfanityAction och exempel
|ProfanityAction-värde | Action | Exempel: Källa-Japansk | Exempel: Mål – engelska|
| :---|:---|:---|:---|
| NoAction | Standard. Samma som att inte ställa in alternativet. Svordomar skickas från källa till mål. | 彼は変態です。 | Han är en Jerk. |
| Klassificera | Svordomar ord omges av XML- \<taggar som är svordomar >... \</Profanity >. | 彼は変態です。 | Han är en \<svordom > Jerk\</Profanity >. |
| Borttagen | Svordomar-ord tas bort från utdata utan ersättning. | 彼は。 | Han är en. |

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Använd svordoms-filtrering med ditt Translator API-anrop](reference/v3-0-translate.md)

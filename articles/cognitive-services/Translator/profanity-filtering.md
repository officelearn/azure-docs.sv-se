---
title: Filtrering av svordomar – Translator Text API
titleSuffix: Azure Cognitive Services
description: Använd funktionen för svordomar för att fastställa nivån på svordomar som har översatts i din text i Azure Cognitive Services Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836231"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Lägg till svordoms-filtrering med Translator Text API

Tjänsten Translator behåller normalt svordomar som finns i källan i översättningen. Graden av svordomar och kontexten som gör ord svordomar olika mellan kulturer. På grund av detta kan det innebära att svordomar på mål språket förstärks eller minskas.

Om du vill undvika att se svordomar i översättningen, även om det finns svordomar i käll texten, använder du filtrerings alternativet för svordomar i metoden Översätt (). Med det här alternativet kan du välja om du vill se hur svordomar tas bort, markerade med lämpliga taggar eller vidta åtgärder som inte vidtas.

Metoden Översätt () använder parametern "alternativ", som innehåller det nya elementet "ProfanityAction". De godkända värdena för ProfanityAction är "noaction", "märkta" och "Deleted".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Godkända värden för ProfanityAction och exempel
|ProfanityAction-värde | Åtgärd | Exempel: källa-Japansk | Exempel: Target – engelska|
| :---|:---|:---|:---|
| Noaction | Standard. Samma som att inte ställa in alternativet. Svordomar skickas från källa till mål. | 彼は変態です från en omfattande | Han är en Jerk. |
| Klassificera | Svordomar ord omges av XML-taggar \<svordomar >... \</Profanity >. | 彼は変態です från en omfattande | Han är \<svordomar > Jerk\</Profanity >. |
| Borttagen | Svordomar-ord tas bort från utdata utan ersättning. | 彼は från en omfattande | Han är en. |

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Använd svordoms-filtrering med ditt Translator API-anrop](reference/v3-0-translate.md)

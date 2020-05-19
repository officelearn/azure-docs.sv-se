---
title: Filtrering av svordomar – Translator
titleSuffix: Azure Cognitive Services
description: Använd funktionen för svordomar för att fastställa nivån på svordomar som har översatts i din text i Azure Cognitive Services Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 864f6a6d92306c40713f66b526c8a8df1683d3c4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586804"
---
# <a name="add-profanity-filtering-with-the-translator"></a>Lägg till svordomar filtrering med Translator

Tjänsten Translator behåller normalt svordomar som finns i källan i översättningen. Graden av svordomar och kontexten som gör ord svordomar olika mellan kulturer. På grund av detta kan det innebära att svordomar på mål språket förstärks eller minskas.

Om du vill undvika att se svordomar i översättningen, även om det finns svordomar i käll texten, använder du filtrerings alternativet för svordomar i metoden Översätt (). Med det här alternativet kan du välja om du vill se hur svordomar tas bort, markerade med lämpliga taggar eller vidta åtgärder som inte vidtas.

Metoden Översätt () använder parametern "alternativ", som innehåller det nya elementet "ProfanityAction". De godkända värdena för ProfanityAction är "noaction", "märkta" och "Deleted".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Godkända värden för ProfanityAction och exempel
|ProfanityAction-värde | Åtgärd | Exempel: källa-Japansk | Exempel: Target – engelska|
| :---|:---|:---|:---|
| Noaction | Standard. Samma som att inte ställa in alternativet. Svordomar skickas från källa till mål. | 彼は変態です från en omfattande | Han är en Jerk. |
| Klassificera | Svordomar ord omges av XML-taggar som är \< svordomar>... \< /Profanity>. | 彼は変態です från en omfattande | Han är en \< svordom>Jerk \< /Profanity>. |
| Borttagen | Svordomar-ord tas bort från utdata utan ersättning. | 彼は från en omfattande | Han är en. |

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Använd svordoms-filtrering med ditt Translator-anrop](reference/v3-0-translate.md)

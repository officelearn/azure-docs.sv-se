---
title: Filtrering av svordomar - Translator Text API
titleSuffix: Azure Cognitive Services
description: Använd svordomarfiltrering för att bestämma nivån på svordomar som översätts i din text i Azure Cognitive Services Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836231"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Lägga till svordomarfiltrering med Translator Text API

Normalt behåller översättaretjänsten svordomar som finns i källan i översättningen. Graden av svordomar och det sammanhang som gör ord profana skiljer sig mellan kulturer. Som ett resultat kan graden av svordomar i målspråket förstärkas eller minskas.

Om du vill undvika att se svordomar i översättningen, även om svordomar finns i källtexten, använder du alternativet svordomarfiltrering som är tillgängligt i metoden Translate(). Med det här alternativet kan du välja om du vill se svordomar borttaget, markerat med lämpliga taggar eller inte vidta några åtgärder.

Metoden Translate() tar parametern "options", som innehåller det nya elementet "ProfanityAction". De accepterade värdena för ProfanityAction är "NoAction", "Marked" och "Deleted".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Accepterade värden för ProfanityAction och exempel
|SvordomarÅtgärdsvärde | Åtgärd | Exempel: Källa - Japanska | Exempel: Target - Engelska|
| :---|:---|:---|:---|
| Ingen åtgärd | Standard. Samma som att inte ange alternativet. Svordomar passerar från källa till mål. | 中ンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンン | Han är en idiot. |
| Markerade | Profana ord är omgivna av XML-taggar \<svordomar> ... \</svordomar>. | 中ンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンン | Han är \<ett svordomar>jerk\</ svordomar>. |
| Borttagen | Profana ord tas bort från utdata utan ersättning. | Det är inte så att jag | Han är en. |

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Tillämpa svordomarfiltrering med ditt Translator API-anrop](reference/v3-0-translate.md)

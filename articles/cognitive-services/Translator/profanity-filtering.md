---
title: Svordomar filtrering – Translator Text API
titlesuffix: Azure Cognitive Services
description: Använd siris filtrering i Translator Text API.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: bd7a05f2f597d1882293387e5aac8e4d7367d051
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916639"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Lägg till svordomar filtrering med Translator Text API

Tjänsten Translator behåller normalt svordomar som finns i källan i översättningen. Graden av svordomar och kontext som gör ord olämpliga skiljer sig åt mellan kulturer. Därför graden av svordomar i målspråket framhävas eller minskas.

Om du vill undvika att se svordomar i översättning, även om svordomar finns i källtext, Använd svordomar filtrering alternativ som är tillgängligt i metoden Translate(). Det här alternativet kan du välja om du vill se svordomar bort, markeras med lämpliga taggar eller vidta någon åtgärd krävs.

Metoden Translate() tar parametern ”alternativ” som innehåller det nya elementet ”ProfanityAction”. De godkända värdena för ProfanityAction är ”NoAction”, ”markerat” och ”har tagits bort”.

## <a name="accepted-values-of-profanityaction-and-examples"></a>Godkända värden för ProfanityAction och exempel
|ProfanityAction värde | Åtgärd | Exempel: Käll - japanska | Exempel: Mål - engelska|
| :---|:---|:---|:---|
| NoAction | Standard. Samma som inte ange alternativet. Svordomar Överför från källan till målet. | 彼は変態です。 | Han är en tölp. |
| Markerad | Olämpliga ord. omges av XML-taggar \<svordomar >... \</profanity >. | 彼は変態です。 | Han är en \<svordomar > jerk\</profanity >. |
| Borttagen | Olämpliga ord. tas bort från utdata utan ersättning. | 彼は。 | Han är en. |

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Tillämpa svordomar filtrering med Translator API-anrop](reference/v3-0-translate.md)

---
title: Svordomar filtrering – Translator Text API
titlesuffix: Azure Cognitive Services
description: Använd siris filtrering i Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 4154950cf8d8b6ec2e47a9f8100cb7983ac127bf
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648045"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Lägg till svordomar filtrering med Translator Text API

Tjänsten Translator behåller normalt svordomar som finns i källan i översättningen. Graden av svordomar och kontext som gör ord olämpliga skiljer sig åt mellan kulturer. Därför graden av svordomar i målspråket framhävas eller minskas.

Om du vill undvika att se svordomar i översättning, även om svordomar finns i källtext, Använd svordomar filtrering alternativ som är tillgängligt i metoden Translate(). Det här alternativet kan du välja om du vill se svordomar bort, markeras med lämpliga taggar eller vidta någon åtgärd krävs.

Metoden Translate() tar parametern ”alternativ” som innehåller det nya elementet ”ProfanityAction”. De godkända värdena för ProfanityAction är ”NoAction”, ”markerat” och ”har tagits bort”.

## <a name="accepted-values-of-profanityaction-and-examples"></a>Godkända värden för ProfanityAction och exempel
|ProfanityAction värde | Åtgärd | Exempel: Source - japanska | Exempel: Mål - engelska|
| :---|:---|:---|:---|
| NoAction | Standard. Samma som inte ange alternativet. Svordomar Överför från källan till målet. | 彼は変態です。 | Han är en tölp. |
| Markerad | Olämpliga ord. omges av XML-taggar \<svordomar >... \</profanity >. | 彼は変態です。 | Han är en \<svordomar > jerk\</profanity >. |
| Borttagen | Olämpliga ord. tas bort från utdata utan ersättning. | 彼は。 | Han är en. |

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Tillämpa svordomar filtrering med Translator API-anrop](reference/v3-0-translate.md)

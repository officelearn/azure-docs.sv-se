---
title: Svordomar filtrering med Microsoft översättare Text API | Microsoft Docs
description: Använd svordomar filtrering i Microsoft översättare Text API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a7172e1e8aa336c011fb06e93fc5c4b54d26a3cd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352590"
---
# <a name="how-to-add-profanity-filtering-with-the-microsoft-translator-text-api"></a>Hur du lägger till svordomar filtrering med Microsoft översättare Text-API

Tjänsten översättare behåller normalt svordomar som finns i datakällan i översättningen. Graden av svordomar och kontext som gör ord vulgärt skiljer sig åt mellan kulturer. Därför graden av svordomar i målspråket framhävas eller minskas.

Om du vill slippa svordomar i översättning (oavsett förekomst av svordomar i texten källa), kan du använda svordomar filtreringsalternativ i metoden Translate(). Alternativet kan du välja om du vill se svordomar bort eller markerats med lämpliga taggar eller ingen åtgärd vidtogs.

Metoden Translate() tar en parameter ”alternativ”, som innehåller det nya elementet ”ProfanityAction”. Accepterade värden för ProfanityAction är ”NoAction”, ”markerat” och ”borttaget”.

## <a name="accepted-values-of-profanityaction-and-examples"></a>Accepterade värden för ProfanityAction och exempel
|ProfanityAction värde | Åtgärd | Exempel: Käll - japanska | Exempel: Mål - engelska|
| :---|:---|:---|:---|
| NoAction | Standard. Samma som inte ange alternativet. Svordomar Överför från källan till målet. | 彼は変態です。 | Han är en tölp. |
| Markerad | Vulgärt ord omges av XML-taggar \<svordomar >... \</Profanity >. | 彼は変態です。 | Han är en \<svordomar > jerk\</profanity >. |
| Borttagen | Vulgärt ord tas bort från utdata utan ersättning. | 彼は。 | Han är en. |

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Tillämpa svordomar filtrering med översättare API-anrop](reference/v3-0-translate.md)


---
title: Hämta v3 förutsägelse slut punkt
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bc6e43faca47e360daa8214e6b9f6e9df4a2f130
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495286"
---
1. I LUIS-portalen i avsnittet hantera (längst upp till höger) på sidan nycklar och slut punkter (menyn till vänster) väljer du slut punkts-URL: en längst ned på sidan.

    Den här åtgärden öppnar fliken webbläsare med slut punkts-URL: en i adress fältet.

    URL: en har ditt app-ID, nyckel och plats namn. URL: en för v3-förutsägelsen ser ut så här:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=UTTERANCE-TEXT.`


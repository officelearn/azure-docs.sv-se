---
title: Så här får du V3 prediction slutpunkt
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80287755"
---
1. I LUIS-portalen, i avsnittet **Hantera** (övre högra menyn), på sidan **Azure Resources** (vänster meny) kopierar du **exempelfrågan** längst ned på sidan på fliken **Förutsägelseresurser.**

    Klistra in webbadressen på en ny webbläsarflik.

    Webbadressen har ditt app-ID, nyckel och platsnamn. Slutpunkts-URL:en för V3-förutsägelse ser ut så här:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`


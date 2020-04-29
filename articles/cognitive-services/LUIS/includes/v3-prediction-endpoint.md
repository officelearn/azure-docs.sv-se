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
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80287755"
---
1. I LUIS-portalen i avsnittet **Hantera** (översta högra menyn) på sidan **Azure-resurser** (menyn till vänster) på fliken **förutsägelse resurser** , kopierar du **exempel frågan** längst ned på sidan.

    Klistra in webb adressen i en ny flik i webbläsaren.

    URL: en har ditt app-ID, nyckel och plats namn. URL: en för v3-förutsägelsen ser ut så här:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`


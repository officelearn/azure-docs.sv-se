---
title: Hämta v3 förutsägelse slut punkt
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: 18f5422202cf972d49349cc04b845c623cabffa3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589176"
---
1. I LUIS-portalen i avsnittet **Hantera** (översta högra menyn) på sidan **Azure-resurser** (menyn till vänster) på fliken **förutsägelse resurser** , kopierar du **exempel frågan** längst ned på sidan.

    Klistra in webb adressen i en ny flik i webbläsaren.

    URL: en har ditt app-ID, nyckel och plats namn. URL: en för v3-förutsägelsen ser ut så här:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`


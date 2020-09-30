---
title: Hämta v3 förutsägelse slut punkt
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: a607ad71915606d6046b4c71291a49b8641bdcc9
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545421"
---
1. I LUIS-portalen i avsnittet **Hantera** (översta högra menyn) på sidan **Azure-resurser** (menyn till vänster) på fliken **förutsägelse resurser** , kopierar du **exempel frågan** längst ned på sidan.

    Klistra in webb adressen i en ny flik i webbläsaren.

    URL: en har ditt app-ID, nyckel och plats namn. URL: en för v3-förutsägelsen ser ut så här:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`


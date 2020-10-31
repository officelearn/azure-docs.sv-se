---
title: Hämta v3 förutsägelse slut punkt
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: 85fcd0308083350a02afd4eadac2b361337f3b33
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128226"
---
1. I LUIS-portalen i avsnittet **Hantera** (översta högra menyn) på sidan **Azure-resurser** (menyn till vänster) på fliken **förutsägelse resurser** , kopierar du **exempel frågan** längst ned på sidan. URL: en har ditt app-ID, nyckel och plats namn. Slutpunkts-URL: en för v3-förutsägelse har formatet: `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="exempel fråga i avsnittet förutsägelse resurser" lightbox="../media/prediction-resources-example-query.png":::
    
    Klistra in webb adressen i en ny flik i webbläsaren. Om du inte ser webb adressen har du ingen förutsägelse resurs och behöver skapa en. 

    

    


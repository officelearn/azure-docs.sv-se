---
title: Python-snabbstart – förutspå avsikter – LUIS
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten lär du dig att anropa en LUIS-app med hjälp av Python.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 8fe39a2876d37e4897c03a5654e500e2e2d3cb9f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51276958"
---
# <a name="quickstart-get-intent-using-python"></a>Snabbstart: Hämta avsikter med hjälp av Python
I den här snabbstarten överför du yttranden till en LUIS-slutpunkt och få avsikt och entiteter tillbaka.

[!INCLUDE[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Python 3.6](https://www.python.org/downloads/) eller senare.
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Hämta LUIS-nyckel

[!INCLUDE[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Hämta avsikter i en webbläsare

[!INCLUDE[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>Hämta avsikter programmatiskt

Du kan använda Python för att komma åt samma resultat som du såg i webbläsarfönstret i föregående steg.

1. Kopiera något av följande kodavsnitt till en fil med namnet `quickstart-call-endpoint.py`:

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Ersätt värdet i fältet `Ocp-Apim-Subscription-Key` med LUIS-slutpunktsnyckeln.

3. Installera beroenden med `pip install requests`.

4. Kör skriptet med `python ./quickstart-call-endpoint.py`. Det visar samma JSON som du såg tidigare i webbläsarfönstret.

## <a name="luis-keys"></a>LUIS-nycklar

[!INCLUDE[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort Python-filen. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till yttranden](luis-get-started-python-add-utterance.md)
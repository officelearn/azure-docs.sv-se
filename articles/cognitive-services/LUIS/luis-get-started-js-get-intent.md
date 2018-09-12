---
title: Analysera text med naturligt språk i Language Understanding Intelligent Service (LUIS) med hjälp av Javascript – Cognitive Services – Azure Cognitive Services | Microsoft Docs
description: I den här snabbstarten använder du en tillgänglig offentlig LUIS-app för att fastställa användarens avsikt i konversationstext. Skicka med hjälp av Javascript användarens avsikt som text till den offentliga appens HTTP-slutpunkt för förutsägelse. Vid slutpunkten tillämpar LUIS den offentliga appens modell för att analysera betydelsen av text med naturligt språk, och fastställa den övergripande avsikten och extrahera data som är relevanta för appens ämnesdomän.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: d787f744ff0fe7315553e9dd6f4465122f7e06b2
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159716"
---
# <a name="quickstart-analyze-text-using-javascript"></a>Snabbstart: Analysera text med hjälp av Javascript

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Visual Studio Code](https://code.visualstudio.com/)
* Offentlig app-ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


## <a name="get-luis-key"></a>Hämta LUIS-nyckel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analysera text med webbläsare

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-javascript"></a>Analysera text med Javascript 

Du kan använda Javascript för att komma åt samma resultat som du såg i webbläsarfönstret i föregående steg. 

1. Kopiera den kod som följer och spara den till en HTML-fil:

   [!code-html[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/javascript/call-endpoint.html)]

2. Öppna filen i en webbläsare. Ange din LUIS-slutpunktsnyckel i formuläret och välj **Skicka**.

    ![Html-exempel visas i en webbläsare med LUIS-resultat för Home Automation-appen](./media/luis-get-started-js-get-intent/html-results.png)

    Resultatet visas under formuläret. 

## <a name="luis-keys"></a>LUIS-nycklar

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort Javascript-filen.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Lägg till yttranden](luis-get-started-javascript-add-utterance.md)

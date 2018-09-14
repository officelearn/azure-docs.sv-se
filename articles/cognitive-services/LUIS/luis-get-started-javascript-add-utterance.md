---
title: Snabbstart om hur du lägger till yttranden till en LUIS-app med hjälp av JavaScript – Azure Cognitive Services | Microsoft Docs
description: I den här snabbstarten lär du dig att anropa en LUIS-app med hjälp av JavaScript.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: ffc19d12c1d3fbb24c514ac87f298d1a52d23eb8
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43771888"
---
# <a name="quickstart-change-model-using-javascript"></a>Snabbstart: Ändra modell med hjälp av JavaScript

[!include[Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

[!include[Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Visual Studio Code](https://code.visualstudio.com/).

[!include[Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>JSON-fil med exempelyttranden

[!include[Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]


## <a name="create-quickstart-code"></a>Kod för att skapa snabbstart

Skapa `add-utterances.html` och lägg till följande kod:

   [!code-html[Html code](~/samples-luis/documentation-samples/quickstarts/change-model/javascript/add-utterance.html "Javascript code")]

## <a name="run-code"></a>Köra koden

1. Öppna filen i en webbläsare.

2. Lägg till ditt LUIS-redigerings-ID och ditt LUIS-program-ID.

3. Ändra **matrisen med yttranden** för att lägga till i programmet. De lagras i variabeln utteranceJSON. Ändra de här värdena enligt behoven för din egen domän och dina yttranden. 

    ```json
    // example batch utterances
    var utteranceJSON = [
        {
            "text": "go to Seattle",
            "intentName": "BookFlight",
            "entityLabels": [
                {
                    "entityName": "Location::LocationTo",
                    "startCharIndex": 6,
                    "endCharIndex": 12
                }
            ]
        }
    ,
        {
            "text": "book a flight",
            "intentName": "BookFlight",
            "entityLabels": []
        }
    ];
    ```

4. Välj knappen `Upload utterance`. LUIS-resultaten visas under knapparna.

5. Välj knappen `Train model` för att träna programmet med de här nya yttrandena.

6. Välj knappen `Train Status` för att visa träningsstatus. 

    ![Add-utterances.html](./media/luis-quickstart-javascript-add-utterance/add-utterance.png)

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med snabbstarten tar du bort alla filer som skapas i den här snabbstarten. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Integrera LUIS med en robot](luis-csharp-tutorial-build-bot-framework-sample.md)

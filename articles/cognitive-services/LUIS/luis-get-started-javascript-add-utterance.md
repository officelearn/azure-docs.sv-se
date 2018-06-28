---
title: Självstudie om hur du lägger till yttranden till en LUIS-app med hjälp av JavaScript | Microsoft Docs
description: I den här självstudien lär du dig att anropa en LUIS-app med hjälp av JavaScript.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: v-geberr
ms.openlocfilehash: b6d021dcfdddb5449aa989c6aa06d7faf326befb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265467"
---
# <a name="tutorial-add-utterances-to-app-using-javascript"></a>Självstudie: Lägga till yttranden till en app med JavaScript
I den här självstudien skriver du ett program för att lägga till ett yttrande till en avsikt med hjälp av redigerings-API:er i JavaScript.

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa Visual Studio-konsolprojekt 
> * Lägg till metod för att anropa LUIS API för att lägga till yttrande och träna appen
> * Lägg till JSON-fil med exempelyttranden för BookFlight-avsikt
> * Kör konsol och visa träningsstatus för yttranden

Mer information finns i den tekniska dokumentationen för API:erna för [lägga till exempelyttrande till avsikt](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [träna](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) och [träningsstatus](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto][LUIS] för att kunna redigera LUIS-programmet.

## <a name="prerequisites"></a>Nödvändiga komponenter
* Din [**LUIS-redigeringsnyckel**](luis-concept-keys.md#authoring-key). 
* Ditt befintliga **LUIS-program-ID** och **versions-ID**. 
* En ny fil med namnet `add-utterances.html` i projektet i VSCode.

> [!NOTE] 
> Den fullständiga `add-utterances.html`-filen finns tillgänglig från [**LUIS-Samples**-Github-lagringsplatsen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/javascript/add-utterance.html).


## <a name="write-the-code"></a>Skriva koden
Skapa `add-utterances.html` och lägg till följande kod:

   [!code-javascript[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/javascript/add-utterance.html "Java Dependencies")]

## <a name="view-in-browser"></a>Visa i webbläsare
1. Öppna filen i en webbläsare.

2. Lägg till ditt LUIS-redigerings-ID och ditt LUIS-program-ID och ändra versionen om den inte är `0.1`

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
När du är klar med självstudien tar du bort Visual Studio och konsolprogrammet om du inte längre behöver dem. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Integrera LUIS med en robot](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
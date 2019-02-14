---
title: 'Snabbstart: Skapa en app'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Skapa en LUIS-app där den fördefinierade domänen `HomeAutomation` används till att släcka lampor och stänga av apparater. Den fördefinierade domänen ger dig avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 347de9b575e3b47ee795026115fd2452c28b866b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877345"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Snabbstart: Använda fördefinierad hemautomatisering

I den här snabbstarten skapar du en LUIS-app som använder den fördefinierade domänen `HomeAutomation` för att släcka lampor och stänga av apparater. Den fördefinierade domänen ger dig avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet.

## <a name="prerequisites"></a>Nödvändiga komponenter

För den här artikeln behöver du ett kostnadsfritt LUIS-konto som skapas på LUIS-portalen på [https://www.luis.ai](https://www.luis.ai). 

## <a name="create-a-new-app"></a>Skapa en ny app
Du kan skapa och hantera dina appar på **Mina appar**. 

1. Logga in på LUIS-portalen.

2. Välj **Create new app** (Skapa ny app).

    [![Skärmbild av applista](media/luis-quickstart-new-app/app-list.png "Skärmbild av applista")](media/luis-quickstart-new-app/app-list.png)

3. Namnge appen ”Home Automation” (Hemautomatisering) i dialogrutan.

    [![Skärmbild av popup-fönstret Skapa ny app](media/luis-quickstart-new-app/create-new-app-dialog.png "Skärmbild av popup-fönstret Skapa ny app")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Välj programkultur. Välj engelska för appen Home Automation. Välj sedan **Done** (Klar). LUIS skapar appen Home Automation. 

    >[!NOTE]
    >Kulturen kan inte ändras när appen har skapats. 

## <a name="add-prebuilt-domain"></a>Lägga till fördefinierad domän

Välj **Prebuilt domains** (Fönsterruta domäner) i navigeringsfönstret till vänster. Sök sedan efter ”Home”. Välj **Add domain** (Lägg till domän).

[![Skärmbild av domänen för hemautomatisering som anges i en fördefinierad domänmeny](media/luis-quickstart-new-app/home-automation.png "Skärmbild av domänen för hemautomatisering som anges i en fördefinierad domänmeny")](media/luis-quickstart-new-app/home-automation.png)

När domänen har lagts till visar rutan för den fördefinierade domänen knappen **Remove domain** (Ta bort domän).

[![Skärmbild av domänen för hemautomatisering med knappen Ta bort](media/luis-quickstart-new-app/remove-domain.png "Skärmbild av domänen för hemautomatisering med knappen Ta bort")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Avsikter och entiteter

Välj **Intents** i navigeringsfönstret till vänster för att granska avsikter för domänen HomeAutomation. Varje avsikt har exempelyttranden.

> [!NOTE]
> **None** (Ingen) är en avsikt som tillhandahålls av alla LUIS-appar. Du kan använda den till att hantera yttranden som inte motsvarar funktioner som finns i din app. 

Välj avsikten **HomeAutomation.TurnOff** (stänga av). Du kan se att avsikten innehåller en lista med yttranden som är märkta med entiteter.

[![Skärmbild av avsikten HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnon.png "Skärmbild av avsikten HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-the-luis-app"></a>Träna LUIS-appen

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Testa din app
När du har tränat appen kan du testa den. Välj **Test** i det övre navigeringsfältet. Skriv ett testyttrande som ”Turn off the lights” (Släck lamporna) i fönstret Interactive Testing (Interaktiv testning) och tryck på RETUR. 

```
Turn off the lights
```

Kontrollera att avsikten med högst poäng motsvarar den avsikt du förväntade dig för varje testyttrande.

I den här exemplet identifieras ”Turn off the lights” (Släck lamporna) som avsikten med högst poäng, ”HomeAutomation.TurnOff”.

[![Skärmbild av testpanelen med ett yttrande markerat](media/luis-quickstart-new-app/test.png "Skärmbild av testpanelen med ett yttrande markerat")](media/luis-quickstart-new-app/test.png)


Välj **Test** igen för att dölja testfönstret. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Skicka fråga till slutpunkten med ett annat yttrande

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Gå till slutet av webbadressen, skriv `turn off the living room light` och tryck på Retur. I webbläsaren visas JSON-svaret för HTTP-slutpunkten.

    [![Skärmbild av en webbläsare med ett JSON-resultat som har identifierat avsikten TurnOff](media/luis-quickstart-new-app/turn-off-living-room.png "Skärmbild av en webbläsare med ett JSON-resultat som har identifierat avsikten TurnOff")](media/luis-quickstart-new-app/turn-off-living-room.png)
    
## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

Du kan anropa slutpunkten via kod:

> [!div class="nextstepaction"]
> [Anropa en LUIS-slutpunkt med kod](luis-get-started-cs-get-intent.md)

---
title: Skapa din första LUIS-app på 10 minuter
titleSuffix: Azure Cognitive Services
description: Skapa en LUIS-app där den fördefinierade domänen `HomeAutomation` används till att släcka lampor och stänga av apparater. Den fördefinierade domänen ger dig avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: d7c80cce3b046bb6c8e4412272c7d166bc038d9c
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282126"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Snabbstart: Använda en färdig hemautomatiseringsapp

I den här snabbstarten skapar du en LUIS-app som använder den fördefinierade domänen `HomeAutomation` för att släcka lampor och stänga av apparater. Den fördefinierade domänen ger dig avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet.

## <a name="prerequisites"></a>Nödvändiga komponenter

För den här artikeln behöver du ett kostnadsfritt LUIS-konto som skapas på LUIS-portalen på [https://www.luis.ai](https://www.luis.ai). 

## <a name="create-a-new-app"></a>Skapa en ny app
Du kan skapa och hantera dina appar på **Mina appar**. 

1. Logga in på LUIS-portalen.

2. Välj **Create new app** (Skapa ny app).

    [![](media/luis-quickstart-new-app/app-list.png "Skärmbild av applistan")](media/luis-quickstart-new-app/app-list.png)

3. Namnge appen ”Home Automation” (Hemautomatisering) i dialogrutan.

    [![](media/luis-quickstart-new-app/create-new-app-dialog.png "Skärmbild på popup-dialogrutan Create new app (Skapa ny app)")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Välj programkultur. Välj engelska för appen Home Automation. Välj sedan **Done** (Klar). LUIS skapar appen Home Automation. 

    >[!NOTE]
    >Kulturen kan inte ändras när appen har skapats. 

## <a name="add-prebuilt-domain"></a>Lägga till fördefinierad domän

Välj **Prebuilt domains** (Fönsterruta domäner) i navigeringsfönstret till vänster. Sök sedan efter ”Home”. Välj **Add domain** (Lägg till domän).

[![](media/luis-quickstart-new-app/home-automation.png "Skärmbild av domänen Home Automation i menyn för den fördefinierade domänen")](media/luis-quickstart-new-app/home-automation.png)

När domänen har lagts till visar rutan för den fördefinierade domänen knappen **Remove domain** (Ta bort domän).

[![](media/luis-quickstart-new-app/remove-domain.png "Skärmbild av domänen Home Automation med borttagningsknapp")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Avsikter och entiteter

Välj **Intents** i navigeringsfönstret till vänster för att granska avsikter för domänen HomeAutomation. Varje avsikt har exempelyttranden.

> [!NOTE]
> **None** (Ingen) är en avsikt som tillhandahålls av alla LUIS-appar. Du kan använda den till att hantera yttranden som inte motsvarar funktioner som finns i din app. 

Välj avsikten **HomeAutomation.TurnOff** (stänga av). Du kan se att avsikten innehåller en lista med yttranden som är märkta med entiteter.

[![](media/luis-quickstart-new-app/home-automation-turnon.png "Skärmbild av avsikten HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-the-luis-app"></a>Träna LUIS-appen

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Testa din app
När du har tränat appen kan du testa den. Välj **Test** i det övre navigeringsfältet. Skriv ett testyttrande som ”Turn off the lights” (Släck lamporna) i fönstret Interactive Testing (Interaktiv testning) och tryck på RETUR. 

```
Turn off the lights
```

Kontrollera att avsikten med högst poäng motsvarar den avsikt du förväntade dig för varje testyttrande.

I den här exemplet identifieras ”Turn off the lights” (Släck lamporna) som avsikten med högst poäng, ”HomeAutomation.TurnOff”.

[![](media/luis-quickstart-new-app/test.png "Skärmbild av testpanel med yttranden markerade")](media/luis-quickstart-new-app/test.png)


Välj **Test** igen för att dölja testfönstret. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL

[!INCLUDE[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Skicka fråga till slutpunkten med ett annat yttrande

1. [!INCLUDE[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Gå till slutet av webbadressen, skriv `turn off the living room light` och tryck på Retur. I webbläsaren visas JSON-svaret för HTTP-slutpunkten.

    [![](media/luis-quickstart-new-app/turn-off-living-room.png "Skärmbild av webbläsare med JSON-resultat som identifierar avsikten TurnOff")](media/luis-quickstart-new-app/turn-off-living-room.png)
    
## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

Du kan anropa slutpunkten via kod:

> [!div class="nextstepaction"]
> [Anropa en LUIS-slutpunkt med kod](luis-get-started-cs-get-intent.md)

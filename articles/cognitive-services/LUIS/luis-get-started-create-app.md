---
title: Skapa din första LUIS-app på 10 minuter – Cognitive Services LUIS | Microsoft Docs
description: I den här snabbstarten skapar du en LUIS-app som använder den fördefinierade domänen `HomeAutomation` för att släcka lampor och stänga av apparater. Den fördefinierade domänen ger dig avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/22/2018
ms.author: diberry
ms.openlocfilehash: 457f23936dec0cf85e9aebbf3e54bba37c2f3ca3
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "43771642"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Snabbstart: Använda en färdig hemautomatiseringsapp

I den här snabbstarten skapar du en LUIS-app som använder den fördefinierade domänen `HomeAutomation` för att släcka lampor och stänga av apparater. Den fördefinierade domänen ger dig avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet.

## <a name="prerequisites"></a>Nödvändiga komponenter

För den här artikeln behöver du ett kostnadsfritt LUIS-konto som skapas på LUIS-portalen på [http://www.luis.ai](http://www.luis.ai). 

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

Välj **Intents** i navigeringsfönstret till vänster för att granska avsikter för domänen HomeAutomation. 

[![](media/luis-quickstart-new-app/home-automation-intents.png "Skärmbild av lista över avsikter med avsikternas namn markerade")](media/luis-quickstart-new-app/home-automation-intents.png)

Varje avsikt har exempelyttranden.

> [!NOTE]
> **None** (Ingen) är en avsikt som tillhandahålls av alla LUIS-appar. Du kan använda den till att hantera yttranden som inte motsvarar funktioner som finns i din app. 

Välj avsikten **HomeAutomation.TurnOff** (stänga av). Du kan se att avsikten innehåller en lista med yttranden som är märkta med entiteter.

[![](media/luis-quickstart-new-app/home-automation-turnon.png "Skärmbild av avsikten HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-your-app"></a>Träna appen

Välj **Train** (Träna) i det övre navigeringsfältet.

[![](media/luis-quickstart-new-app/trained.png "Skärmbild av avsikten HomeAutomation.TurnOff med grönt meddelande om lyckad åtgärd")](media/luis-quickstart-new-app/trained.png)

## <a name="test-your-app"></a>Testa din app
När du har tränat appen kan du testa den. Välj **Test** i det övre navigeringsfältet. Skriv ett testyttrande som ”Turn off the lights” (Släck lamporna) i fönstret Interactive Testing (Interaktiv testning) och tryck på RETUR. 

```
Turn off the lights
```

Kontrollera att avsikten med högst poäng motsvarar den avsikt du förväntade dig för varje testyttrande.

I den här exemplet identifieras ”Turn off the lights” (Släck lamporna) som avsikten med högst poäng, ”HomeAutomation.TurnOff”.

[![](media/luis-quickstart-new-app/test.png "Skärmbild av testpanel med yttranden markerade")](media/luis-quickstart-new-app/test.png)


Välj **Test** igen för att dölja testfönstret. 

## <a name="publish-your-app"></a>Publicera appen
Välj **Publish** (Publicera) i det övre navigeringsfältet. 

[![](media/luis-quickstart-new-app/publish.png "Skärmbild av app med publiceringsknappen markerad")](media/luis-quickstart-new-app/publish.png)

Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera).

Det gröna meddelandefältet högst upp anger att appen har publicerats.

[![](media/luis-quickstart-new-app/published.png "Skärmbild av app med utförd publicering")](media/luis-quickstart-new-app/published.png)

När du har publicerat kan du använda slutpunkts-URL:en som visas på sidan **Publish app** (Publicera appen).

[![](media/luis-quickstart-new-app/endpoint.png "Skärmbild på sidan Publish (Publicera) med slutpunkts-URL markerad")](media/luis-quickstart-new-app/endpoint.png)

## <a name="use-your-app"></a>Använda appen
Du kan testa din publicerade slutpunkt i en webbläsare med hjälp av den genererade URL:en. Öppna URL:en i webbläsaren, ange URL-parametern ”&q” för att testa frågan. Lägg till exempel till `turn off the living room light` i slutet av URL:en och tryck sedan på RETUR. I webbläsaren visas JSON-svaret för HTTP-slutpunkten.


[![](media/luis-quickstart-new-app/turn-off-living-room.png "Skärmbild av webbläsare med JSON-resultat som identifierar avsikten TurnOff")](media/luis-quickstart-new-app/turn-off-living-room.png)

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. För att göra det väljer du ellipsknappen (***...***) till höger om appnamnet i applistan och väljer **Delete** (Ta bort). På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg

Du kan anropa slutpunkten via kod:

> [!div class="nextstepaction"]
> [Anropa en LUIS-slutpunkt med kod](luis-get-started-cs-get-intent.md)

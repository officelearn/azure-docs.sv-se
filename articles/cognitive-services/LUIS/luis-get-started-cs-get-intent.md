---
title: Självstudie om att anropa en Language Understanding-app (LUIS) med hjälp av C# | Microsoft Docs
description: I den här självstudien lär du dig att anropa en LUIS-app med hjälp av C#.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 0416d19d27810a2ab8eeb20e16b2f921fc7826ee
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263497"
---
# <a name="tutorial-call-a-luis-endpoint-using-c"></a>Självstudie: anropa en LUIS-slutpunkt med C#

Överför yttranden till en LUIS-slutpunkt och få avsikt och entiteter tillbaka.

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa LUIS-prenumeration och kopiera nyckelvärde för senare användning
> * Visa LUIS-slutpunktsresultat från webbläsaren till offentlig IoT-exempelapp
> * Skapa Visual Studio C#-konsolapp för att göra HTTPS-anrop till LUIS-slutpunkt

<!-- link to free account --> För den här artikeln behöver du ett kostnadsfritt [LUIS-konto][LUIS] för att kunna redigera LUIS-programmet.

## <a name="create-luis-subscription-key"></a>Skapa LUIS-prenumerationsnyckel
1. Först behöver du skapa ett [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) i Azure-portalen. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

2. Logga in på Azure Portal på https://portal.azure.com. 

3. Följ stegen på sidan om att [skapa prenumerationsnycklar med hjälp av Azure](./luis-how-to-azure-subscription.md) för att få en nyckel.

4. Gå tillbaka till [LUIS](luis-reference-regions.md)-webbplatsen. Logga in med ditt Azure-konto. 

    [![](media/luis-get-started-cs-get-intent/app-list.png "Skärmbild av applistan")](media/luis-get-started-cs-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>Förstå vad LUIS returnerar

För att förstå vad en LUIS-app returnerar kan du klistra in URL:en för en LUIS-exempelapp i ett webbläsarfönster. Exempelappen är en IoT-app som känner av om användaren vill aktivera eller inaktivera ljus.

1. Slutpunkten för exempelappen har det här formatet: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` Kopiera URL:en och ange din prenumerationsnyckel som värde i fältet `subscription-key`.
2. Klistra in URL:en i ett webbläsarfönster och tryck på Retur. Webbläsaren visar ett JSON-resultat som indikerar att LUIS identifierar avsikten `HomeAutomation.TurnOn` och entiteten `HomeAutomation.Room` med värdet `bedroom`.

    ![JSON-resultatet identifierar avsikten TurnOn](./media/luis-get-started-cs-get-intent/turn-on-bedroom.png)
3. Ändra värdet för parametern `q=` i URL: en till `turn off the living room light`, och tryck på Retur. Resultatet indikerar nu att LUIS identifierat avsikten `HomeAutomation.TurnOff` och entiteten `HomeAutomation.Room` med värdet `living room`. 

    ![JSON-resultatet identifierar avsikten TurnOff](./media/luis-get-started-cs-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-c"></a>Förbruka ett LUIS-resultat med hjälp av slutpunkts-API med C# 

Du kan använda C# för att komma åt samma resultat som du såg i webbläsarfönstret i föregående steg. 

1. Skapa ett nytt konsolprogram i Visual Studio. Kopiera den kod som följer och spara den till en *.cs-fil:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/endpoint-api-samples/csharp/Program.cs)]
1. Ersätt värdet för variabeln `subscriptionKey` med din LUIS-prenumerationsnyckel.

3. I Visual Studio-projektet lägger du till en referens till **System.Web**.

4. Kör konsolprogrammet. Det visar samma JSON som du såg tidigare i webbläsarfönstret.

![Konsolfönstret visar JSON-resultat från LUIS](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="clean-up-resources"></a>Rensa resurser
De två resurser som skapades i den här självstudien är LUIS-prenumerationsnyckeln och C#-projektet. Ta bort LUIS-prenumerationsnyckeln från Azure-portalen. Stäng Visual Studio-projektet och ta bort katalogen från filsystemet. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Lägg till yttranden](luis-get-started-cs-add-utterance.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
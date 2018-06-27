---
title: Använda tal C#-SDK med THOMAS - Azure | Microsoft Docs
titleSuffix: Azure
description: Använda tal C# SDK-exempel för att tala i mikrofon och få THOMAS avsikt och entiteter förutsägelser som returneras.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/26/2018
ms.author: v-geberr;
ms.openlocfilehash: b8a2c0dbadb0124b9250849a0260f5b34d38a5c3
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021671"
---
# <a name="integrate-speech-service"></a>Integrera service tal
Den [tal tjänsten](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) kan du använda en enskild begäran att ta emot ljud och returnera THOMAS förutsägelse JSON-objekt.

I den här artikeln, hämta och använda ett C#-projekt i Visual Studio för att tala en utterance i en mikrofon och ta emot information om THOMAS förutsägelse. Projektet använder talfunktionen [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) paketet, som redan ingår som en referens. 

För den här artikeln behöver du ett kostnadsfritt [THOMAS] [ LUIS] webbplats konto för att importera programmet.

## <a name="create-luis-endpoint-key"></a>Skapa THOMAS slutpunktsnyckel
I Azure-portalen [skapa](luis-how-to-azure-subscription.md#create-luis-endpoint-key) en **språk förstå** (THOMAS) nyckel. 

## <a name="import-human-resources-luis-app"></a>Importera personal THOMAS app
Intents och utterances för den här artikeln kommer från personalavdelningen THOMAS appen tillgänglig från den [THOMAS prover](https://github.com/Microsoft/LUIS-Samples) Github-lagringsplatsen. Hämta den [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) filen, spara den med filnamnstillägget *.json och [importera](create-new-app.md#import-new-app) till THOMAS. 

Det här programmet har avsikter, enheter och utterances relaterade till domänen personal. Exempel utterances omfattar:

```
Who is John Smith's manager?
Who does John Smith manage?
Where is Form 123456?
Do I have any paid time off?
```

## <a name="add-keyphrase-prebuilt-entity"></a>Lägg till KeyPhrase färdiga entitet
När du har importerat appen, Välj **entiteter**, sedan **hantera färdiga entiteter**. Lägg till den **KeyPhrase** entitet. Entiteten KeyPhrase extraherar viktiga ämne från utterance.

## <a name="train-and-publish-the-app"></a>Träna och publicera appen
1. I det övre, högra navigeringsfältet, Välj den **träna** knappen för att träna THOMAS appen.

2. Välj **publicera** att gå till sidan Publicera. 

3. Längst ned i den **publicera** , Lägg till THOMAS nyckeln som skapats i den [skapa THOMAS slutpunktsnyckel](#create-luis-endpoint-key) avsnitt.

4. Publicera THOMAS appen genom att välja den **publicera** till höger i publicera platsen. 

  På den **publicera** sidan, samla in app-ID, publicera region och prenumerations-ID för THOMAS nyckeln som skapats i den [skapa THOMAS slutpunktsnyckel](#create-luis-endpoint-key) avsnitt. Du behöver ändra koden för att använda dessa värden senare i den här artikeln. 

  Dessa värden ingår i slutpunkts-URL längst ned i den **publicera** för nyckeln som du skapade. Gör **inte** använda den kostnadsfria starter-nyckeln för den här övningen. 

  https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**? prenumeration nyckel =**LUISKEY**& q =

## <a name="audio-device"></a>Ljudenhet
Den här artikeln använder enheten på datorn. Det kan vara hörlurar med mikrofon eller en inbyggda ljudenhet. Kontrollera ljud inkommande nivåer för att se om du ska talar högre än vanligt om du vill att ditt tal som identifieras av enheten. 

## <a name="download-the-luis-sample-project"></a>Ladda ned LUIS Sample-projekt
 Klona eller hämta den [THOMAS prover](https://github.com/Microsoft/LUIS-Samples) databasen. Öppna den [tal avsiktshantering projektet](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) med Visual Studio och återställa NuGet-paketen. Lösningsfilen VS är.\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Tal SDK ingår redan som en referens. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Skärmbild av Visual Studio 2017 visning Microsoft.CognitiveServices.Speech NuGet-paketet")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Ändra C#-kod
Öppna den **LUIS_samples.cs** filen och ändra följande variabler:

|Variabelnamn|Syfte|
|--|--|
|luisSubscriptionKey|Motsvarar slutpunkts-URL prenumeration nyckel värde från publicera sida|
|luisRegion|Motsvarar slutpunkts-URL första underdomän|
|luisAppId|Motsvarar slutpunkts-URL-flöde efter **appar /**|

[![](./media/luis-tutorial-speech-to-intent/change-variables.png "Skärmbild av Visual Studio 2017 visa LUIS_samples.cs variabler")](./media/luis-tutorial-speech-to-intent/change-variables.png#lightbox)

Filen har redan de personal avsikter mappas.

[![](./media/luis-tutorial-speech-to-intent/intents.png "Skärmbild av Visual Studio 2017 visa LUIS_samples.cs avsikter")](./media/luis-tutorial-speech-to-intent/intents.png#lightbox)

Skapa och köra appen. 

![Skärmbild av kommandoraden som kör program](./media/luis-tutorial-speech-to-intent/cmdline-1.png)

## <a name="test-code-with-utterance"></a>Testa kod med utterance
Välj **8** och tala i mikrofonens ”som är chef för John Smith”.

```cmd
1. Speech recognition with microphone input.
2. Speech recognition in the specified language.
3. Speech recognition with file input.
4. Speech recognition using customized model.
5. Speech continuous recognition using events.
6. Translation with microphone input.
7. Translation with file input.
8. Speech recognition of LUIS intent.
0. Stop.
Your choice: 8
LUIS...
Say something...
ResultId:cc83cebc9d6040d5956880bcdc5f5a98 Status:Recognized IntentId:<GetEmployeeOrgChart> Recognized text:<Who is the manager of John Smith?> Recognized Json:{"DisplayText":"Who is the manager of John Smith?","Duration":25700000,"Offset":9200000,"RecognitionStatus":"Success"}. LanguageUnderstandingJson:{
  "query": "Who is the manager of John Smith?",
  "topScoringIntent": {
    "intent": "GetEmployeeOrgChart",
    "score": 0.617331
  },
  "entities": [
    {
      "entity": "manager of john smith",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 31
    }
  ]
}

Recognition done. Your Choice:

```

Rätt avsikten **GetEmployeeOrgChart**, hittades med 61%. KeyPhrase entitet returnerades. 

Tal SDK Returnerar hela THOMAS svaret. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS HumanResources appen när de inte längre behövs. Om du vill göra det, väljer du menyn med tre punkter (...) till höger om appnamnet i applistan väljer **ta bort**. I popup-fönstret **ta bort appen?** väljer **Ok**.

Kom ihåg att ta bort katalogen Samples THOMAS när du är klar med exempelkoden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Integrera THOMAS med en BOT](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
---
title: Använd tal C# SDK med LUIS
titleSuffix: Azure Cognitive Services
description: Med taltjänsten kan du använda en enskild begäran att ta emot ljud och returnera LUIS förutsägelse JSON-objekt. I den här artikeln får du hämta och använda ett C#-projekt i Visual Studio ska säga ett uttryck i en mikrofon och ta emot information om LUIS förutsägelse. Projektet använder tal NuGet-paketet, som redan ingår som en referens.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: fb17e2d8c0ef1df5a6d4965730d3ddd3764d58f5
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868759"
---
# <a name="integrate-speech-service"></a>Integrera Speech service
Den [taltjänst](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) kan du använda en enskild begäran att ta emot ljud och returnera LUIS förutsägelse JSON-objekt. I den här artikeln får du hämta och använda ett C#-projekt i Visual Studio ska säga ett uttryck i en mikrofon och ta emot information om LUIS förutsägelse. Projektet använder tal [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) paketet, som redan ingår som en referens. 

I den här artikeln behöver du en kostnadsfri [LUIS] [ LUIS] webbplats-konto för att kunna importera programmet.

## <a name="create-luis-endpoint-key"></a>Skapa LUIS-slutpunktsnyckel
I Azure-portalen [skapa](luis-how-to-azure-subscription.md#create-luis-endpoint-key) en **Språkförståelse** (LUIS)-nyckel. 

## <a name="import-human-resources-luis-app"></a>Importera personalfrågor LUIS app
Avsikter och yttranden för den här artikeln kommer från den personal LUIS-app som är tillgängliga från den [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) Github-lagringsplatsen. Ladda ned den [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/HumanResources.json) fil, spara den med den `.json` -tillägget och [importera](luis-how-to-start-new-app.md#import-new-app) den i LUIS. 

Den här appen har avsikter, entiteter och yttranden som rör personal-domänen. Exempel yttranden är:

|Exempel på yttranden|
|--|
|Vem är John Smith manager?|
|Vem som hanterar John Smith?|
|Var är formuläret 123456?|
|Måste jag helst betald?|


## <a name="add-keyphrase-prebuilt-entity"></a>Lägg till KeyPhrase fördefinierade entitet
När du har importerat appen, Välj **entiteter**, sedan **hantera förskapade entiteter**. Lägg till den **KeyPhrase** entitet. Entiteten KeyPhrase extraherar viktiga föremålet från uttryck.

## <a name="train-and-publish-the-app"></a>Träna och publicera appen
1. I det övre, högra navigeringsfältet, väljer du den **träna** knappen för att träna LUIS-app.

2. Välj **hantera** uppe till höger fält, Välj **nycklar och slutpunkter** i det vänstra navigeringsfönstret. 

3. På den **nycklar och slutpunkter** sidan bör du tilldela LUIS-nyckel som skapas i den [skapa LUIS slutpunktsnyckeln](#create-luis-endpoint-key) avsnittet.

  Samla in app-ID på den här sidan, publicera region och prenumerations-ID för den LUIS-nyckeln som skapats i den [skapa LUIS slutpunktsnyckeln](#create-luis-endpoint-key) avsnittet. Du behöver ändra koden för att använda de här värdena senare i den här artikeln. 
  
  Gör **inte** använder den kostnadsfria starter-nyckeln för den här övningen. Endast en **Språkförståelse** nyckeln som skapats i Azure-portalen kommer att fungera i den här övningen. 

  https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**? prenumerationsnyckel =**LUISKEY**& q =


4. Publicera LUIS-app genom att välja den **publicera** knappen i övre högra fältet. 

## <a name="audio-device"></a>Ljudenhet
Den här artikeln används enheten på datorn. Det kan vara ett headset med mikrofon eller en inbyggd ljudenhet. Kontrollera ljud inkommande nivåer för att se om du bör tala högre än vanligt om du vill att ditt tal som identifieras av enheten. 

## <a name="download-the-luis-sample-project"></a>Ladda ned LUIS Sample-projekt
 Klona eller ladda ned den [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) lagringsplats. Öppna den [tal till avsikt projekt](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) med Visual Studio och återställa NuGet-paketen. VS-lösningsfilen är.\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Tal-SDK ingår redan som en referens. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Skärmbild av Visual Studio 2017 med Microsoft.CognitiveServices.Speech NuGet-paketet")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Ändra C#-kod
Öppna den `Program.cs` filen och ändra följande variabler:

|Variabelnamn|Syfte|
|--|--|
|LUIS_assigned_endpoint_key|Motsvarar endpoint URL: er tilldelas prenumeration-key-värde från publiceringssidan|
|LUIS_endpoint_key_region|Motsvarar första underdomänen för slutpunkts-URL, till exempel `westus`|
|LUIS_app_ID|Motsvarar slutpunkts-URL-väg följa **appar /**|

Den `Program.cs` filen har redan personalfrågor avsikter mappad.

Skapa och köra appen. 

## <a name="test-code-with-utterance"></a>Testa kod med uttryck
Tala i mikrofonen ”som är godkända tandläkare i Redmond”?.

[!code-console[Command line response from spoken utterance](~/samples-luis/documentation-samples/tutorial-speech-intent-recognition/console-output.txt "Command line response from spoken utterance")]

Rätt avsikten **GetEmployeeBenefits**, hittades med 85%. Entiteten keyPhrase returnerades. 

Tal-SDK: N Returnerar hela LUIS-svaret. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort appen LUIS HumanResources när de inte längre behövs. Om du vill göra det, Välj appen och sedan i kontextuella verktygsfältet ovanför listan **ta bort**. På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

Kom ihåg att ta bort LUIS-Samples-katalogen när du är klar med exempelkoden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Integrera LUIS med en BOT](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
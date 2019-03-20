---
title: Använda Speech C# SDK
titleSuffix: Azure Cognitive Services
description: Med Speech-tjänsten kan du använda en enskild begäran för att ta emot ljud och returnera LUIS JSON-förutsägelseobjekt. I den här artikeln laddar du ned och använder ett C#-projekt i Visual Studio för att tala in ett yttrande i en mikrofon och ta emot LUIS-förutsägelseinformation.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 6f775ffaf53019cc50bc38c294b4d5f40c8eca90
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076759"
---
# <a name="integrate-speech-service-with-your-language-understanding-app"></a>Integrera Speech-tjänsten med din Language Understanding-app
Med [Speech-tjänsten](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) kan du använda en enskild begäran för att ta emot ljud och returnera LUIS JSON-förutsägelseobjekt. I den här artikeln laddar du ned och använder ett C#-projekt i Visual Studio för att tala in ett yttrande i en mikrofon och ta emot LUIS-förutsägelseinformation. Projektet använder Speech [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)-paketet, som redan ingår som referens. 

För den här artikeln behöver du ett kostnadsfritt [LUIS][LUIS]-webbplatskonto för att importera programmet.

## <a name="create-luis-endpoint-key"></a>Skapa LUIS-slutpunktsnyckel
I Azure-portalen [skapar](luis-how-to-azure-subscription.md) du en **Language Understanding**-nyckel (LUIS). 

## <a name="import-human-resources-luis-app"></a>Importera LUIS-appen Human Resources
Avsikterna och yttrandena för den här artikeln kommer från LUIS-appen Human Resources, som är tillgänglig från GitHub-lagringsplatsen [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding). Ladda ned filen [HumanResources.json](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources.json), spara den med `.json`-tillägget och [importera](luis-how-to-start-new-app.md#import-new-app) den i LUIS. 

Den här appen har avsikter, entiteter och yttranden som rör Human Resources-domänen. Här är några exempelyttranden:

|Exempel på yttranden|
|--|
|Who is John Smith's manager? (Vem är John Smiths chef?)|
|Who does John Smith manage? (Vem är John Smith chef för?)|
|Where is Form 123456? (Var är formuläret 123456?)|
|Do I have any paid time off? (Har jag någon betald semester?)|


## <a name="add-keyphrase-prebuilt-entity"></a>Lägga till fördefinierad KeyPhrase-entitet
När du har importerat appen väljer du **Entiteter** och sedan **Lägg till fördefinierad entitet**. Lägg till **KeyPhrase**-entiteten. KeyPhrase-entiteten extraherar nyckelämnen från yttrandet.

## <a name="train-and-publish-the-app"></a>Träna och publicera appen
1. I det övre högra navigeringsfältet väljer du knappen **Träna** för att träna LUIS-appen.

2. Välj **Hantera** i fältet uppe till höger och välj **Nycklar och slutpunkter** i det vänstra navigeringsfönstret. 

3. På sidan **Nycklar och slutpunkter** tilldelar du den LUIS-nyckel som skapades i avsnittet [Skapa LUIS-slutpunktsnyckel](#create-luis-endpoint-key).

   På den här sidan samlar du in app-ID, publiceringsregion och prenumerations-ID för den LUIS-nyckel som skapades i avsnittet [Skapa LUIS-slutpunktsnyckel](#create-luis-endpoint-key). Du behöver ändra koden för att använda de här värdena senare i den här artikeln. 
  
   Använd **inte** den kostnadsfria startnyckeln för den här övningen. Endast en **Language Understanding**-nyckel som skapats i Azure-portalen fungerar i den här övningen. 

   https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**?subscription-key=**LUISKEY**&q=


4. Publicera LUIS-appen genom att välja knappen **Publicera** i det övre högra fältet. 

## <a name="audio-device"></a>Ljudenhet
Den här artikeln använder ljudenheten på datorn. Det kan vara ett headset med mikrofon eller en inbyggd ljudenhet. Kontrollera nivåerna för inkommande ljud för att se om du bör tala högre än vanligt för att ditt tak ska identifieras av enheten. 

## <a name="download-the-luis-sample-project"></a>Ladda ned LUIS-exempelprojektet
 Klona eller ladda ned lagringsplatsen [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding). Öppna [Tal till avsikt-projektet](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-speech-intent-recognition) med Visual Studio och återställ NuGet-paketen. VS-lösningsfilen är documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Speech SDK ingår redan som referens. 

[![Skärmbild av Visual Studio 2017 med Microsoft.CognitiveServices.Speech NuGet-paketet](./media/luis-tutorial-speech-to-intent/nuget-package.png "Skärmbild av Visual Studio 2017 med Microsoft.CognitiveServices.Speech NuGet-paketet")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Ändra C#-koden
Öppna filen `Program.cs` och ändra följande variabler:

|Variabelnamn|Syfte|
|--|--|
|LUIS_assigned_endpoint_key|Motsvarar slutpunkt-URL:ens tilldelade prenumerationsnyckelvärde från sidan Publicera|
|LUIS_endpoint_key_region|Motsvarar slutpunkt-URL:ens första underdomän, till exempel `westus`|
|LUIS_app_ID|Motsvarar slutpunkt-URL:ens väg efter **apps/**|

Filen `Program.cs` har redan Human Resources-avsikterna mappade.

Skapa och kör appen. 

## <a name="test-code-with-utterance"></a>Testa kod med yttrande
Tala in följande i mikrofonen: ”Who are the approved dentists in Redmond?” (Vilka är godkända tandläkare i Redmond?).

[!code-console[Command line response from spoken utterance](~/samples-luis/documentation-samples/tutorial-speech-intent-recognition/console-output.txt "Command line response from spoken utterance")]

Den rätta avsikten, **GetEmployeeBenefits**, hittades med 85 % konfidensbedömning. KeyPhrase-entiteten returnerades. 

Speech SDK returnerar hela LUIS-svaret. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen Human Resources när den inte längre behövs. För att göra det markerar du appen, går till det kontextuella verktygsfältet ovanför listan och väljer **Ta bort**. På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

Kom ihåg att ta bort katalogen när du är klar med exempelkoden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Integrera LUIS med en robot](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website

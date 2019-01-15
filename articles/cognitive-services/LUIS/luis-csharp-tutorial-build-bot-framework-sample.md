---
title: Bot - C# - v3
titleSuffix: Language Understanding - Azure Cognitive Services
description: Använd C# och skapa en chattrobot som är integrerad med språkförståelse (LUIS). Den här chattrobot använder fördefinierade HomeAutomation domänen för att snabbt implementera en bot-lösning.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: c3e142c65cdfe00fb26694063f7733d62882cc0c
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267376"
---
# <a name="luis-bot-in-c-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>LUIS-bot i C# med Bot Framework 3.x och Azure Web app-robot

Använd C# och skapa en chattrobot som är integrerad med språkförståelse (LUIS). Den här chattrobot använder fördefinierade HomeAutomation domänen för att snabbt implementera en bot-lösning. Roboten har byggts med Bot Framework 3.x och Azure Web app-robot.

## <a name="prerequisite"></a>Krav

* [HomeAutomation LUIS-app](luis-get-started-create-app.md). Avsikter från den här LUIS programkartan till robotens dialogrutan hanterarna. 

## <a name="luis-homeautomation-intents"></a>LUIS HomeAutomation avsikter

| Avsikt | Exempel på yttrande | Bot-funktioner |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Tända lampan. | När LUIS avsikten `HomeAutomation.TurnOn` identifieras får roboten anropar den `OnIntent` dialogrutan hanterare. Den här dialogrutan är där du vill anropa en IoT-tjänst för att aktivera en enhet och meddela användaren som enheten har slagits på. |
| HomeAutomation.TurnOff | Inaktivera sovrum lamporna. | När LUIS avsikten `HomeAutomation.TurnOff` identifieras får roboten anropar den `OffIntent` dialogrutan hanterare. Den här dialogrutan är där du vill anropa en IoT-tjänst för att stänga av en enhet och be användaren att enheten har inaktiverats. |

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Skapa en Luis-bot med Bot Service

1. I den [Azure-portalen](https://portal.azure.com)väljer **Skapa ny resurs** på menyn längst upp till vänster.

    ![Skapa ny resurs i Azure-portalen](./media/luis-tutorial-cscharp-web-bot/bot-service-creation.png)

2. I sökrutan söker du efter **Web App-robot**. 

    ![Välj web app-robot som resurstyp](./media/luis-tutorial-cscharp-web-bot/bot-service-selection.png)

3. I fönstret Web App-robot klickar du på **skapa**.

4. I **Robottjänst**, ange nödvändig information och klicka på **skapa**. Detta skapar och distribuerar bot service och LUIS-app till Azure. Om du vill använda [tal promotor](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), granska [region krav](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) innan du skapar din robot. 
    * Ange **appnamn** till din robot namn. Namnet används som underdomänen när din robot distribueras till molnet (exempelvis mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Välj prenumerationen [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), App service-plan och [plats](https://azure.microsoft.com/regions/).
    * För **Bot mallen**väljer:
        * **SDK v3**
        * **C#**
        * **Språkförståelse**
    * Välj den **LUIS platsen**. Det här är redigeringen [region](luis-reference-regions.md) appen skapas i.
    * Markera kryssrutan bekräftelse för juridiskt meddelande. Villkoren i juridiskt meddelande är lägre än kryssrutan.

    ![Robottjänst](./media/luis-tutorial-cscharp-web-bot/bot-service-setting-callout-template.png)


5. Bekräfta att bot-tjänsten har distribuerats.
    * Klicka på aviseringar (klockikonen finns längs överkanten av Azure portal). Meddelandet ändras från **distributionen har påbörjats** till **distributionen lyckades**.
    * När ändras meddelandet och **distributionen lyckades**, klickar du på **gå till resurs** på som meddelanden.

> [!Note]
> Den här web bot skapa den skapade också en ny LUIS-app åt dig. Det har tränats och för dig. 

## <a name="try-the-default-bot"></a>Prova standard bot

Bekräfta att roboten har distribuerats genom att välja den **meddelanden** kryssrutan. Meddelanden ändras från **distribution pågår...**  till **distributionen lyckades**. Klicka på **gå till resurs** knappen för att öppna den bot-resurser.

När roboten har distribuerats klickar du på **Test i Web Chat** att öppna fönstret Web Chat. Skriv ”hello” i Web Chat.

  ![Testa roboten i Web Chat](./media/luis-tutorial-cscharp-web-bot/bot-service-web-chat.png)

Roboten svarar genom att säga ”du har nått hälsning. Du SA: hello ”.  Det här svaret bekräftar att roboten har tagit emot ditt meddelande och skickas till en standard LUIS-app som skapats. Det här standardvärdet LUIS-app har upptäckt en hälsning avsikt. I nästa steg ansluter du roboten till LUIS-app som du skapade tidigare i stället för standard LUIS-app.

## <a name="connect-your-luis-app-to-the-bot"></a>Anslut din LUIS-app till roboten

Öppna **programinställningar** och redigera den **LuisAppId** fält som innehåller program-ID för LUIS-appen. Om du har skapat din HomeAutomation LUIS-app i en annan region än västra USA kan du behöva ändra den **LuisAPIHostName** samt. Den **LuisAPIKey** är inställd på att din redigering nyckel. Du ändra det till din slutpunktsnyckeln när trafiken överskrider kvoten för kostnadsfria nivån. 

  ![Uppdatera LUIS-app-ID i Azure](./media/luis-tutorial-cscharp-web-bot/bot-service-app-settings.png)

> [!Note]
> Om du inte har LUIS-app-ID för den [Start Automation app](luis-get-started-create-app.md), logga in på den [LUIS](luis-reference-regions.md) webbplats som använder samma konto som du använder för att logga in på Azure. 
> 1. Klicka på **Mina appar**. 
> 2. Hitta LUIS-appen du skapade tidigare, som innehåller avsikter och entiteter från HomeAutomation-domän.
> 3. I den **inställningar** för LUIS-app, hitta och kopiera app-ID. Se till att det [tränade](luis-interactive-test.md) och [publicerade](luis-how-to-publish-app.md). 

    > [!WARNING]
    > If you delete your app ID or LUIS key, the bot will stop working.

## <a name="modify-the-bot-code"></a>Ändra bot-koden

1. Klicka på **skapa** och klicka sedan på **öppna online Kodredigerare**.

   ![Öppna online Kodredigerare](./media/luis-tutorial-cscharp-web-bot/bot-service-build.png)

2. Högerklicka på `build.cmd` och välj **kör från konsolen** att skapa programmet. Det finns flera byggsteg tjänsten slutförs automatiskt åt dig. Versionen har slutförts när den har slutförts med ”har slutförts”.

3. Öppna i kodredigeraren `/Dialogs/BasicLuisDialog.cs`. Det innehåller följande kod:

   [!code-csharp[Default BasicLuisDialog.cs](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/Default_BasicLuisDialog.cs "Default BasicLuisDialog.cs")]

## <a name="change-code-to-homeautomation-intents"></a>Ändra koden till HomeAutomation avsikter


1. Ta bort de tre avsikt attribut och metoder för **hälsning**, **Avbryt**, och **hjälpa**. Dessa avsikter används inte i den fördefinierade HomeAutomation-domänen. Se till att välja den **ingen** avsikt attribut och metoden. 

2. Lägga till beroenden överst i filen med de andra beroendena:

   [!code-csharp[Dependencies](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=4-5&dedent=8 "dependencies")]

3. Lägg till konstanter för att hantera strängar överst i den `BasicLuisDialog ` klass:

   [!code-csharp[Add Intent and Entity Constants](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=23-32&dedent=8 "Add Intent and Entity Constants")]

4. Lägg till kod för nya avsikter i `HomeAutomation.TurnOn` och `HomeAutomation.TurnOff` inuti den `BasicLuisDialog ` klass:

   [!code-csharp[Add Intents](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=61-71&dedent=8 "Add Intents")]

5. Lägg till kod för att hämta alla entiteter som upptäckts av LUIS inuti den `BasicLuisDialog ` klass:

   [!code-csharp[Collect entities](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=34-53&dedent=8 "Collect entities")]

6. Ändra **ShowLuisResult** -metod i den `BasicLuisDialog ` klassen för att avrunda poängen, samla in entiteterna och visa svarsmeddelandet i chattrobot:

   [!code-csharp[Display message in chatbot](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=73-83&dedent=8 "Display message in chatbot")]

## <a name="build-the-bot"></a>Skapa roboten
I kodredigeraren, högerklickar du på `build.cmd` och välj **kör från konsolen**.

![Skapa Web-robot ](./media/luis-tutorial-cscharp-web-bot/bot-service-build-run-from-console.png)

Kodvyn ersätts med ett terminalfönster som visar förlopp och resultat för versionen.

![Skapa Web-robot lyckades](./media/luis-tutorial-cscharp-web-bot/bot-service-build-success.png)

> [!TIP]
> Ett annat sätt att skapa roboten är att välja bot-namnet i det översta blå fältet och välja **öppna Kudu-konsolen**. Öppnas konsolen till **D:\home**. 
> 
> Ändra katalogen till **D:\home\site\wwwroot** genom att skriva: `cd site\wwwroot`
>
> Köra build-skriptet genom att skriva: `build.cmd`

## <a name="test-the-bot"></a>Testa roboten

I Azure-portalen klickar du på **testa i Web Chat** att testa roboten. Skriv meddelanden like ”tänd lamporna” och ”inaktivera min heater” anropa avsikter som du lade till den.

   ![Testa HomeAutomation bot i Web Chat](./media/luis-tutorial-cscharp-web-bot/bot-service-chat-results.png)

> [!TIP]
> Du kan kvarhålla LUIS-app utan några ändringar till din robot kod. Se [lägger du till exempel yttranden](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) och [träna och testa LUIS-appen](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test). 

## <a name="download-the-bot-to-debug"></a>Ladda ned roboten att felsöka
Om din robot inte fungerar kan ladda ned projektet till den lokala datorn och fortsätta [felsökning](https://docs.microsoft.com/bot-framework/bot-service-debug-bot#debug-a-c-bot). 

## <a name="learn-more-about-bot-framework"></a>Läs mer om Bot Framework
Läs mer om [Bot Framework](https://dev.botframework.com/) och [3.x](https://github.com/Microsoft/BotBuilder) och [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK: er.

## <a name="next-steps"></a>Nästa steg

Lägg till LUIS avsikter och Bot service dialogrutor för hantering av **hjälpa**, **Avbryt**, och **hälsning** avsikter. Kom ihåg att träna, publicera och [skapa](#build-the-bot) web app-robot. Både LUIS och roboten bör ha samma avsikter.

Se fler [exempel](https://github.com/Microsoft/AI) med konversationsanpassade robotar. 

> [!div class="nextstepaction"]
> [Lägg till avsikter](./luis-how-to-add-intents.md)
> [tal promotor](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)

<!-- tested on Win10 -->

---
title: Integrera THOMAS med en bot med Bot Builder SDK för C# i Azure | Microsoft Docs
description: Skapa en bot integrerad med en THOMAS-program med hjälp av ramverket Bot.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/06/2018
ms.author: v-geberr
ms.openlocfilehash: b3283880ebb116e5397c38d722a0790cff414f38
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111930"
---
# <a name="web-app-bot-using-the-luis-template-for-c"></a>Web App Bot med mallen THOMAS för C#

Skapa en chatbot med integrerad språk förståelse.

## <a name="prerequisite"></a>Krav

* [HomeAutomation THOMAS app](luis-get-started-create-app.md). Avsikter från den här THOMAS app kartan till den bot dialogrutan hanterarna. 

## <a name="luis-homeautomation-intents"></a>THOMAS HomeAutomation avsikter

| Avsikten | Exempel utterance | Bot funktioner |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Aktivera indikeringar. | När THOMAS avsikten `HomeAutomation.TurnOn` har identifierats i bot anropar den `OnIntent` dialogrutan hanterare. Den här dialogrutan är där du vill anropa en IoT-tjänst för att aktivera en enhet och meddela användaren som enheten har aktiverats. |
| HomeAutomation.TurnOff | Inaktivera sovrum indikeringar. | När THOMAS avsikten `HomeAutomation.TurnOff` har identifierats i bot anropar den `OffIntent` dialogrutan hanterare. Den här dialogrutan är där du vill anropa en IoT-tjänsten för att stänga av en enhet och meddela användaren om att enheten har inaktiverats. |

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Skapa en språk förstå bot med Bot Service

1. I den [Azure-portalen](https://portal.azure.com)väljer **Skapa ny resurs** på den översta vänstra menyn.

    ![Skapa ny resurs](./media/luis-tutorial-cscharp-web-bot/bot-service-creation.png)

2. I sökrutan söker du efter **Web App Bot**. 

    ![Skapa ny resurs](./media/luis-tutorial-cscharp-web-bot/bot-service-selection.png)

3. Klicka på i fönstret Web App Bot **skapa**.

4. I **Bot Service**, ange informationen som krävs och klicka på **skapa**. Detta skapar och distribuerar bot service och THOMAS app till Azure. Om du vill använda [tal promotor](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), granska [region krav](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) innan du skapar din bot. 
    * Ange **appnamn** till din bot namn. Namnet används som underdomänen när din bot distribueras till molnet (exempelvis mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Välj prenumerationen [resursgruppen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), apptjänstplan, och [plats](https://azure.microsoft.com/regions/).
    * Välj den **språk förstå (C#)** mall för den **Bot mallen** fältet.
    * Välj den **THOMAS platsen**. Detta är redigeringen [region] [ LUIS] appen skapas i.
    * Markera kryssrutan bekräftelse för juridiskt meddelande. Villkoren i juridiskt meddelande är lägre än kryssrutan.

    ![Robottjänst](./media/luis-tutorial-cscharp-web-bot/bot-service-setting-callout-template.png)


5. Bekräfta att tjänsten bot har distribuerats.
    * Klicka på meddelanden (klockikonen finns längs överkanten av Azure portal). Meddelandet ändras från **distributionen har startat** till **distributionen lyckades**.
    * När meddelandet ändras till **distributionen lyckades**, klickar du på **finns resurs** på detta meddelande.

> [!Note]
> Den här processen för web app bot skapas också en ny app THOMAS för dig. Det har tränats och för dig. 

## <a name="try-the-default-bot"></a>Försök standard bot

Bekräfta att bot har distribuerats genom att välja den **meddelanden** kryssrutan. Meddelanden ändras från **distribution pågår...**  till **distributionen lyckades**. Klicka på **finns resurs** knappen för att öppna den bot resurser.

När bot distribueras, klickar du på **Test i Web chatta** att öppna fönstret Web chatta. Skriv ”hello” i Web chatta.

  ![Testa bot i Web chatt](./media/luis-tutorial-cscharp-web-bot/bot-service-web-chat.png)

Bot svarar genom att säga ”du har nått hälsning. Du SA: hello ”.  Det här svaret bekräftar att bot har tagit emot meddelandet och skickas till en standard THOMAS app som skapats. Den här standardinställningen THOMAS app har upptäckt en hälsning avsikt. I nästa steg ansluts bot till THOMAS appen som du skapade tidigare i stället för default THOMAS app.

## <a name="connect-your-luis-app-to-the-bot"></a>Anslut appen THOMAS till bot

Öppna **programinställningar** och redigera den **LuisAppId** fältet ska innehålla program-ID för appen THOMAS. Om du har skapat HomeAutomation THOMAS appen i en annan region än västra USA kan du behöver ändra den **LuisAPIHostName** samt. Den **LuisAPIKey** är inställd på att din nyckel för redigering. Du ändra det till din slutpunktsnyckel när trafiken överskrider kvoten kostnadsfria nivån. 

  ![Uppdatera THOMAS app-ID i Azure](./media/luis-tutorial-cscharp-web-bot/bot-service-app-settings.png)

> [!Note]
> Om du inte har THOMAS app-ID för den [Start Automation app](luis-get-started-create-app.md), logga in på den [THOMAS](luis-reference-regions.md) webbplats som använder samma konto som du använder för att logga in på Azure. 
> 1. Klicka på **Mina appar**. 
> 2. Hitta THOMAS appen du skapade tidigare, som innehåller avsikter och entiteter från domänen HomeAutomation.
> 3. I den **inställningar** THOMAS appens sida, hitta och kopiera app-ID. Se till att det [tränad](interactive-test.md) och [publicerade](PublishApp.md). 

    > [!WARNING]
    > If you delete your app ID or LUIS key, the bot will stop working.

## <a name="modify-the-bot-code"></a>Ändra bot koden

1. Klicka på **skapa** och klicka sedan på **öppna online redigerare**.

   ![Öppna online redigerare](./media/luis-tutorial-cscharp-web-bot/bot-service-build.png)

2. Högerklicka på `build.cmd` och välj **köra från konsolen** att bygga appen. Det finns flera build steg tjänsten slutförs automatiskt åt dig. Versionen är klar när den är klar med ”har slutförts”.

3. Öppna i Redigeraren för koden `/Dialogs/BasicLuisDialog.cs`. Det innehåller följande kod:

   [!code-csharp[Default BasicLuisDialog.cs](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/Default_BasicLuisDialog.cs "Default BasicLuisDialog.cs")]

## <a name="change-code-to-homeautomation-intents"></a>Ändra koden till HomeAutomation avsikter


1. Ta bort de tre avsiktshantering attribut och metoder för **hälsning**, **Avbryt**, och **hjälp**. Dessa avsikter används inte i domänen HomeAutomation fördefinierade. Se till att hålla den **ingen** avsiktshantering attribut och metoden. 

2. Lägga till beroenden överst i filen med de andra beroenden:

   [!code-csharp[Dependencies](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=4-5&dedent=8 "dependencies")]

3. Lägg till konstanter för att hantera strängar överst i den `BasicLuisDialog ` klass:

   [!code-csharp[Add Intent and Entity Constants](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=23-32&dedent=8 "Add Intent and Entity Constants")]

4. Lägg till kod för de nya innehållet i `HomeAutomation.TurnOn` och `HomeAutomation.TurnOff` inuti den `BasicLuisDialog ` klass:

   [!code-csharp[Add Intents](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=61-71&dedent=8 "Add Intents")]

5. Lägg till kod för att hämta alla entiteter som hittades av THOMAS inuti den `BasicLuisDialog ` klass:

   [!code-csharp[Collect entities](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=34-53&dedent=8 "Collect entities")]

6. Ändra **ShowLuisResult** metod i den `BasicLuisDialog ` klassen om du vill avrunda poäng, samla in entiteterna och visa svarsmeddelandet i chatbot:

   [!code-csharp[Display message in chatbot](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=73-83&dedent=8 "Display message in chatbot")]

## <a name="build-the-bot"></a>Skapa bot
Högerklicka på i kodredigeraren `build.cmd` och välj **köra från konsolen**.

![Skapa Web bot ](./media/luis-tutorial-cscharp-web-bot/bot-service-build-run-from-console.png)

Kodvyn ersätts med ett terminalfönster visar förlopp och resultat för versionen.

![Skapa Web bot lyckades](./media/luis-tutorial-cscharp-web-bot/bot-service-build-success.png)

> [!TIP]
> En alternativ metod för att skapa bot är att välja bot namn i fältet blå överst och välj **öppna Kudu-konsolen**. Öppnar konsolen **D:\home**. 
> 
> Ändra katalogen till **D:\home\site\wwwroot** genom att skriva: `cd site\wwwroot`
>
> Kör skriptet build genom att skriva: `build.cmd`

## <a name="test-the-bot"></a>Testa bot

I Azure-portalen klickar du på **testa i Web chatta** att testa bot. Skriv meddelanden like ”aktivera indikeringar” och ”inaktivera min värmare” att anropa avsikter som du lade till den.

   ![Testa HomeAutomation bot i Web chatt](./media/luis-tutorial-cscharp-web-bot/bot-service-chat-results.png)

> [!TIP]
> Du kan träna om appen THOMAS utan några andra ändringar till din bot kod. Se [lägger du till exempel utterances](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) och [träna och testa appen THOMAS](https://docs.microsoft.com/azure/cognitive-services/LUIS/interactive-test). 

## <a name="download-the-bot-to-debug"></a>Hämta bot för felsökning
Om din bot inte fungerar hämta projektet till den lokala datorn och fortsätta [felsökning](https://docs.microsoft.com/bot-framework/bot-service-debug-bot#debug-an-azure-app-service-web-app-c-bot). 

## <a name="learn-more-about-bot-framework"></a>Mer information om Bot Framework
Lär dig mer om [Bot Framework](https://dev.botframework.com/) och [3.x](https://github.com/Microsoft/BotBuilder) och [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK: er.

## <a name="next-steps"></a>Nästa steg

Lägg till THOMAS avsikter och Bot dialogrutorna för hantering av **hjälp**, **Avbryt**, och **hälsning** avsikter. Kom ihåg att träna, publicera och [skapa](#build-the-bot) web app bot. Både THOMAS och bot bör ha samma avsikter.

> [!div class="nextstepaction"]
> [Lägg till avsikter](./luis-how-to-add-intents.md)
> [tal promotor](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


<!-- Links -->
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-cs-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/csharp
[Github-LUIS-Samples-cs-hotel-bot-readme]: https://github.com/Microsoft/LUIS-Samples/blob/master/bot-integration-samples/hotel-finder/csharp/README.md
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[AssignedEndpointDoc]: https://docs.microsoft.com/azure/cognitive-services/LUIS/manage-keys
[VisualStudio]: https://www.visualstudio.com/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
<!-- tested on Win10 -->

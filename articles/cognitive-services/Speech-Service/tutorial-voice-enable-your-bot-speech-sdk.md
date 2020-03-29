---
title: 'Självstudiekurs: Röster aktiverar din bot med hjälp av Speech SDK - Taltjänst'
titleSuffix: Azure Cognitive Services
description: I den här självstudien ska du skapa en Echo Bot med Microsoft Bot-Framework, distribuera den till Azure och registrera den med guiden Direktlinje för Bot.Inv. Sedan konfigurerar du en exempelklientapp för Windows som låter dig tala med din robot och höra den svara tillbaka till dig.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 96d2c2e5e3772575e681d2db079ab0122b7014e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348541"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Självstudiekurs: Röstaktivera din bot med tal-SDK

Du kan nu använda kraften i taltjänsten för att enkelt uttrycka en chattrobot.

I den här självstudien ska du skapa en Echo Bot med Microsoft Bot-Framework, distribuera den till Azure och registrera den med guiden Direktlinje för Bot.Inv. Sedan konfigurerar du en exempelklientapp för Windows som låter dig tala med din robot och höra den svara tillbaka till dig.

Den här självstudien är utformad för utvecklare som precis har börjat sin resa med Azure, Bot-Framework-robotar, Direct Line Speech eller Speech SDK och snabbt vill bygga ett fungerande system med begränsad kodning. Ingen erfarenhet eller förtrogenhet med dessa tjänster behövs.

I slutet av den här övningen har du konfigurerat ett system som fungerar på följande sätt:

1. Exempelklientprogrammet är konfigurerat för att ansluta till Direct Line Speech-kanalen och Echo Bot
1. Ljud spelas in från standardmikrofonen på knapptryck (eller spelas in kontinuerligt om anpassat nyckelord är aktiverat)
1. Alternativt sker anpassad sökordsidentifiering, gating ljud streaming till molnet
1. Med Tal-SDK ansluter appen till direktlinjetalskanalen och strömmar ljud
1. Alternativt sker sökordsverifiering med högre noggrannhet på tjänsten
1. Ljudet skickas till taligenkänningstjänsten och transkriberas till text
1. Den erkända texten skickas till Echo-Bot som en Bot Framework Activity 
1. Svarstexten omvandlas till ljud av TTS-tjänsten (Text-to-Speech) och strömmas tillbaka till klientprogrammet för uppspelning

![diagram-tagg](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "Flödet i talkanalen")

> [!NOTE]
> Stegen i den här självstudien kräver ingen betald tjänst. Som ny Azure-användare kan du använda krediter från din kostnadsfria Azure-utvärderingsprenumeration och den kostnadsfria nivån för taltjänsten för att slutföra den här självstudien.

Här är vad den här guiden täcker:
> [!div class="checklist"]
> * Skapa nya Azure-resurser
> * Skapa, testa och distribuera Echo Bot-exemplet till en Azure App-tjänst
> * Registrera din bot med direct line-talkanal
> * Skapa och kör Windows Voice Assistant-klienten för att interagera med din Echo Bot
> * Lägga till anpassad nyckelordsaktivering
> * Lär dig att ändra språket i det erkända och talade talet

## <a name="prerequisites"></a>Krav

Här är vad du behöver för att slutföra den här guiden:

- En Windows 10-dator med fungerande mikrofon och högtalare (eller hörlurar)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) eller senare
- [.NET Core SDK](https://dotnet.microsoft.com/download) version 2.1 eller senare
- Ett Azure-konto. [Registrera dig gratis](https://azure.microsoft.com/free/ai/).
- Ett [GitHub-konto](https://github.com/)
- [Git för Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Klientappen som du skapar i den här självstudien använder en handfull Azure-tjänster. Om du vill minska tur-och-retur-tiden för svar från din robot bör du se till att dessa tjänster finns i samma Azure-region. I det här avsnittet ska du skapa en resursgrupp i regionen **Västra USA.** Den här resursgruppen används när du skapar enskilda resurser för Bot-Framework, direct line speech-kanalen och taltjänsten.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">Skapa en resursgrupp<span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. Du uppmanas att ange viss information:
   * Ange **prenumeration på** kostnadsfri **utvärderingsversion** (du kan också använda en befintlig prenumeration).
   * Ange ett namn på **resursgruppen**. Vi rekommenderar **SpeechEchoBotTutorial-ResourceGroup**.
   * Välj **Västra USA**i listrutan **Region** .
1. Klicka på **Granska och skapa**. Du bör se en banner med texten **Validering passerade**.
1. Klicka på **Skapa**. Det kan ta några minuter att skapa resursgruppen.
1. Precis som med de resurser som du skapar senare i den här självstudien är det en bra idé att fästa den här resursgruppen på instrumentpanelen för enkel åtkomst. Om du vill fästa den här resursgruppen klickar du på pinikonen längst upp till höger på instrumentpanelen.

### <a name="choosing-an-azure-region"></a>Välja en Azure-region

Om du vill använda en annan region för den här självstudien kan dessa faktorer begränsa dina val:

* Se till att du använder en [Azure-region som stöds](regions.md#voice-assistants).
* Kanalen Direct Line Speech använder tjänsten text-till-tal, som har standardröster och neurala röster. Neurala röster är [begränsade till specifika Azure-regioner](regions.md#standard-and-neural-voices).
* Kostnadsfria provversionsnycklar kan vara begränsade till en viss region.

Mer information om regioner finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Skapa resurser

Nu när du har en resursgrupp i en region som stöds är nästa steg att skapa enskilda resurser för varje tjänst som du ska använda i den här självstudien.

### <a name="create-a-speech-service-resource"></a>Skapa en taltjänstresurs

Så här skapar du en talresurs:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Skapa en taltjänstresurs<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. Du uppmanas att ange viss information:
   * Ge din resurs ett **namn**. Vi rekommenderar **SpeechEchoBotTutorial-Tal**
   * För **prenumeration**kontrollerar du att **kostnadsfri utvärderingsversion** är markerat.
   * För **Plats**väljer du **Västra USA**.
   * Välj **F0**för **prisnivå.** Det här är den kostnadsfria nivån.
   * För **resursgrupp**väljer du **SpeechEchoBotTutorial-ResourceGroup**.
5. När du har angett all nödvändig information klickar du på **Skapa**. Det kan ta några minuter att skapa resursen.
6. Senare i den här självstudien behöver du prenumerationsnycklar för den här tjänsten. Du kan komma åt dessa nycklar när som helst från **resursens översikt** (Hantera nycklar) eller **nycklar**.

Kontrollera nu att resursgruppen (**SpeechEchoBotTutorial-ResourceGroup**) har en talresurs:

| Namn | Typ  | Location |
|------|-------|----------|
| SpeechechoBotTutorial-tal | Cognitive Services | USA, västra |

### <a name="create-an-azure-app-service-plan"></a>Skapa en Azure App Service-plan

Nästa steg är att skapa en appserviceplan. En App Service-plan definierar en uppsättning beräkningsresurser som en webbapp ska köra.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Skapa en Azure App Service-plan<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. Du uppmanas att ange viss information:
   * Ange **prenumeration på** kostnadsfri **utvärderingsversion** (du kan också använda en befintlig prenumeration).
   * För **resursgrupp**väljer du **SpeechEchoBotTutorial-ResourceGroup**.
   * Ge din resurs ett **namn**. Vi rekommenderar **SpeechEchoBotTutorial-AppServicePlan**
   * För **operativsystem**väljer du **Windows**.
   * För **Region**väljer du **Västra USA**.
   * För **prisnivå**kontrollerar du att **Standard S1** är markerat. Detta bör vara standardvärdet. Om den inte är det måste du ställa in **operativsystemet** på **Windows** enligt beskrivningen ovan.
5. Klicka på **Granska och skapa**. Du bör se en banner med texten **Validering passerade**.
6. Klicka på **Skapa**. Det kan ta några minuter att skapa resursgruppen.

Kontrollera nu att resursgruppen (**SpeechEchoBotTutorial-ResourceGroup**) har två resurser:

| Namn | Typ  | Location |
|------|-------|----------|
| SpeechechoBotTutorial-AppServicePlan | App Service-plan | USA, västra |
| SpeechechoBotTutorial-tal | Cognitive Services | USA, västra |

## <a name="build-an-echo-bot"></a>Bygg en Echo Bot

Nu när du har skapat några resurser, låt oss bygga en bot. Vi ska börja med Echo Bot-exemplet, som som namnet antyder, ekar av den text som du har angett som svar. Oroa dig inte, exempelkoden är klar för dig att använda utan några ändringar. Den är konfigurerad för att fungera med direct line-talkanalen, som vi ansluter när vi har distribuerat roboten till Azure.

> [!NOTE]
> Instruktionerna som följer, samt ytterligare information om Echo Bot finns i [exemplets README på GitHub](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="run-the-bot-sample-on-your-machine"></a>Kör bot-provet på din maskin

1. Klona exempeldatabasen:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Starta Visual Studio.
3. Välj **Öppna** > **Open** > **filprojekt/lösning**i verktygsfältet och öppna echo bot-projektlösningen:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. När projektet har lästs in trycker du på <kbd>F5</kbd> för att bygga och köra projektet.
5. En webbläsare bör starta och du ser en skärm som liknar detta.
    > [!div class="mx-imgBorder"]
    > [![echobot-running-on-localhost echobot-running-on-localhost echobot-running-on-localhost echobot](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "EchoBot körs på localhost")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Testa bot-provet med Bot Framework Emulator

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) är en skrivbordsapp som gör att botutvecklare kan testa och felsöka sina robotar lokalt eller på distans genom en tunnel. Emulatorn stöder maskinskriven text som ingång (inte röst). Boten kommer att svara med text. Följ dessa steg för att använda Bot Framework Emulator för att testa din Echo Bot körs lokalt, med textinmatning och textutdata. När vi har distribuerat roboten till Azure testar vi den med röstinmatning och röstutdata.

1. Installera [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) version 4.3.0 eller mer
2. Starta Bot Framework Emulator och öppna din bot:
   * **Fil** -> **Öppna Bot**.
3. Ange webbadressen för din robot. Ett exempel:

   ```
   http://localhost:3978/api/messages
   ```
   och tryck på "Anslut".
4. Boten bör omedelbart hälsa dig med "Hej och välkommen!" . Skriv in ett textmeddelande och bekräfta att du får ett svar från boten.
5. Så här kan ett utbyte av kommunikation med en Echo Bot-instans se ut: [ ![bot-framework-emulator](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Bot Ram emulator")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Distribuera din robot till en Azure App-tjänst

Nästa steg är att distribuera Echo Bot till Azure. Det finns några sätt att distribuera en bot, men i den här självstudien fokuserar vi på att publicera direkt från Visual Studio.

> [!NOTE]
> Du kan också distribuera en robot med hjälp av [Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) och [distributionsmallar](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates).

1. Öppna Echo-roboten som har konfigurerats för direct line-tal-kanal från Visual Studio:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. Högerklicka på **EchoBot-projektet** i **Solution Explorer**och välj **Publicera...**
1. Ett nytt fönster med namnet **Välj ett publiceringsmål** öppnas.
1. Välj **Apptjänst** i **Azure-tjänsternas** navigering, välj **Skapa ny**och klicka sedan på Skapa **profil**.
1. När fönstret **Skapa apptjänst** visas:
   * Klicka på **Lägg till ett konto**och logga in med dina Azure-kontouppgifter. Om du redan är inloggad väljer du det konto du vill använda i listrutan.
   * För **appnamnet**måste du ange ett globalt unikt namn för din Robot. Det här namnet används för att skapa en unik bot-URL. Ett standardvärde fylls i inklusive datum och tid (till exempel: "EchoBot20190805125647"). Du kan använda standardnamnet för den här självstudien.
   * För **Prenumeration**anger du den till **kostnadsfri utvärderingsversion**
   * För **resursgrupp**väljer du **SpeechEchoBotTutorial-ResourceGroup**
   * För **värdplan**väljer du **SpeechEchoBotTutorial-AppServicePlan**
   * För **Application Insights**, lämna som **ingen**
1. Klicka på **Skapa**
1. Du bör se ett meddelande om lyckade objekt i Visual Studio som ser ut så här:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. Din standardwebbläsare ska öppnas och visa en sida med texten: "Din bot är klar!".
1. Kontrollera nu resursgruppen **SpeechEchoBotTutorial-ResourceGroup** i Azure-portalen och bekräfta att det finns tre resurser:

| Namn | Typ  | Location |
|------|-------|----------|
| EchoBot20190805125647 | App Service | USA, västra |
| SpeechechoBotTutorial-AppServicePlan | App Service-plan | USA, västra |
| SpeechechoBotTutorial-tal | Cognitive Services | USA, västra |

## <a name="enable-web-sockets"></a>Aktivera webbuttag

Du måste göra en liten konfigurationsändring så att din robot kan kommunicera med direktlinjetalskanalen med hjälp av webbuttag. Så här aktiverar du webbuttag:

1. Navigera till [Azure-portalen](https://portal.azure.com)och hitta din App Service. Resursen ska namnges på samma sätt som **EchoBot20190805125647** (ditt unika appnamn).
2. Klicka på **Konfiguration**under **Inställningar**i navigeringen **för Azure-tjänster** .
3. Välj fliken **Allmänna inställningar.**
4. Leta reda på **växlingsknappen** för webbuttag och ställ in den **på På**.
5. Klicka på **Spara**.

> [!TIP]
> Du kan använda kontrollerna högst upp på sidan Azure App Service för att stoppa eller starta om tjänsten. Detta kan vara praktiskt vid felsökning.

## <a name="create-a-channel-registration"></a>Skapa en kanalregistrering

Nu när du har skapat en Azure App Service som värd för din bot, är nästa steg att skapa en **Bot Channels Registration**. Att skapa en kanalregistrering är en förutsättning för att registrera din bot med Bot-Framework-kanaler, inklusive Direct Line Speech-kanal.

> [!NOTE]
> Om du vill veta mer om hur robotar utnyttjar kanaler läser du [Anslut en bot till kanaler](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).


1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Skapa en registrering av Azure Bot-kanaler<span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. Du uppmanas att ange viss information:
   * För **Bot handtag**, ange **SpeechEchoBotTutorial-BotRegistration**.
   * För **prenumeration**väljer du **Kostnadsfri utvärderingsversion**.
   * För **resursgrupp**väljer du **SpeechEchoBotTutorial-ResourceGroup**.
   * För **Plats**väljer du **Västra USA**.
     * Välj **F0**för **prisnivå.**
     * För **slutpunkt för Meddelanden**anger du URL:en för webbappen med sökvägen `/api/messages` till. Till exempel: om ditt globalt unika appnamn var **EchoBot20190805125647** `https://EchoBot20190805125647.azurewebsites.net/api/messages/`skulle slutpunkten för meddelanden vara: .
     * För **programinsikter**kan du ställa in detta till **Av**. Mer information finns i [Bot analytics](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * Ignorera **Automatisk skapa app-ID och lösenord**.
5. Klicka på **Skapa**längst ned i bladet För registrering av **botkanaler.**

Kontrollera nu din resursgrupp **SpeechEchoBotTutorial-ResourceGroup** i Azure-portalen. Det bör nu visa fyra resurser:

| Namn | Typ  | Location |
|------|-------|----------|
| EchoBot20190805125647 | App Service | USA, västra |
| SpeechechoBotTutorial-AppServicePlan | App Service-plan | USA, västra |
| TalechoBotTutorial-BotRegistration | Bot kanaler registrering | Globala |
| SpeechechoBotTutorial-tal | Cognitive Services | USA, västra |

> [!IMPORTANT]
> Bot Channels Registration-resursen visar den globala regionen även om du har valt västra USA. Detta är normalt.

## <a name="register-the-direct-line-speech-channel"></a>Registrera direktlinjetalkanalen

Nu är det dags att registrera din bot med direct line-talkanalen. Den här kanalen är vad som används för att skapa en anslutning mellan din ekorobot och en klientapp som kompilerats med Speech SDK.

1. Leta reda på och öppna din **SpeechEchoBotTutorial-BotRegistration-resurs** i [Azure-portalen](https://portal.azure.com).
1. Välj **Kanaler**i navigeringen **för Azure-tjänster** .
   * Leta efter **fler kanaler,** leta reda på och klicka på **Direkt radtal**.
   * Granska texten på sidan **Konfigurera direktraden Tal**och expandera sedan den nedrullningsliga menyn "Kognitiv tjänstkonto".
   * Välj den talresurs som du skapade **SpeechEchoBotTutorial-Speech**tidigare (t.ex.
   * Klicka på **Spara**.

1. Klicka på **Inställningar**i navigeringen **för Bot-hantering** .
   * Markera rutan **Aktivera slutpunkt för direktuppspelning**. Detta behövs för att aktivera ett kommunikationsprotokoll som bygger på webbuttag mellan roboten och direktlinjetalkanalen.
   * Klicka på **Spara**.

> [!TIP]
> Om du vill veta mer läser du [Anslut en bot till direktlinjetal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0). Den här sidan innehåller ytterligare information och kända problem.

## <a name="build-the-windows-voice-assistant-client"></a>Skapa Windows Voice Assistant-klienten

I det här steget ska du skapa Windows Voice Assistant Client. Klienten är en WPF-app (Windows Presentation Foundation) i C# som använder [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) för att hantera kommunikationen med din robot med hjälp av direct line-talkanalen. Använd den för att interagera med och testa din bot innan du skriver en anpassad klientapp.

Windows Voice Assistant-klienten har ett enkelt användargränssnitt som gör att du kan konfigurera anslutningen till din robot, visa textkonversationen, visa Bot-Framework-aktiviteter i JSON-format och visa adaptiva kort. Det stöder också användningen av anpassade sökord. Du kommer att använda den här klienten för att tala med din bot och få ett röstsvar.

Innan vi går vidare, se till att mikrofonen och högtalarna är aktiverade och fungerar.

1. Navigera till GitHub-databasen för [Windows Voice Assistant Client](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md).
2. Följ instruktionerna för att klona databasen, bygga projektet, konfigurera klienten och starta klienten.
3. Klicka på **Anslut igen** och se till att meddelandet tryck **på mikrofonknappen eller skriv för att börja prata med din robot**.
4. Låt oss testa det. Klicka på mikrofonknappen och tala några ord på engelska. Den tolkade texten visas medan du talar. När du är klar med att tala, bot kommer att svara med sin egen röst, säger "eko" följt av de erkända orden.
5. Du kan också använda text för att kommunicera med roboten. Skriv bara in texten i det nedre fältet. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Felsöka fel i Windows Voice Assistant-klienten

Om du får ett felmeddelande i huvudappfönstret kan du använda den här tabellen för att identifiera och felsöka felet:

| Fel | Hur ska du göra? |
|-------|----------------------|
|FelautentiseringMissilure: WebSocket-uppgraderingen misslyckades med ett autentiseringsfel (401). Sök efter rätt prenumerationsnyckel (eller auktoriseringstoken) och regionnamn| På sidan Inställningar i appen kontrollerar du att du har angett nyckeln Talprenumeration och dess region korrekt.<br>Kontrollera att taltangenten och nyckelområdet har angetts korrekt. |
|Fel ConnectionFailure: Anslutningen stängdes av fjärrvärden. Felkod: 1011. Felinformation: Vi kunde inte ansluta till roboten innan vi skickade ett meddelande | Kontrollera att du har markerat rutan ["Aktivera slutpunkt för direktuppspelning"](#register-the-direct-line-speech-channel) och/eller [växlade **webbuttag** ](#enable-web-sockets) till På.<br>Kontrollera att din Azure App Service körs. Om så är det kan du prova att starta om App-tjänsten.|
|Fel ConnectionFailure: Anslutningen stängdes av fjärrvärden. Felkod: 1011. Felinformation: Svarsstatuskoden anger inte lyckad: 500 (InternalServerError)| Din bot angav en neural röst i fältet [Aktivitetsuppmaning,](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) men Azure-regionen som är associerad med din talprenumerationsnyckel stöder inte neurala röster. Se [Standard och neurala röster](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).|
|Fel ConnectionFailure: Anslutningen stängdes av fjärrvärden. Felkod: 1000. Felinformation: Överskriden maximal webb socketanslutning inaktiv varaktighet (> 300000 ms)| Detta är ett förväntat fel när en anslutning till kanalen lämnas öppen och inaktiv i mer än fem minuter. |

Om problemet inte tas upp i tabellen läser du [Röstassistenter: Vanliga frågor](faq-voice-assistants.md)och svar .

### <a name="view-bot-activities"></a>Visa bot-aktiviteter

Varje bot skickar **Activity** och tar emot aktivitetsmeddelanden. I **fönstret Aktivitetslogg** i Windows Voice Assistant Client visas tidsstämplade loggar med varje aktivitet som klienten har tagit emot från roboten. Du kan också se de aktiviteter som klienten skickade till roboten med hjälp av [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) metoden. När du väljer ett loggobjekt visas information om den associerade aktiviteten som JSON.

Här är ett exempel på en aktivitet som klienten har fått:

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Mer information om vad som returneras i JSON-utdata finns [i fälten i aktiviteten](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). I den här självstudien kan du fokusera på fälten [Text](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) och [Läs upp.](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Visa klientkällkod för anrop till Tal-SDK

Windows Voice Assistant-klienten använder NuGet-paketet [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), som innehåller Speech SDK. Ett bra ställe att börja granska exempelkoden är metoden InitSpeechConnector() i filen [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs), som skapar dessa två Speech SDK-objekt:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)- För konfigurationsinställningar (t.ex. talprenumerationsnyckel, nyckelregion)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)- Att hantera kanalanslutning och klientprenumerations för hantering av erkända tal- och botsvar.

## <a name="add-custom-keyword-activation"></a>Lägga till anpassad nyckelordsaktivering

Tal-SDK stöder anpassad nyckelordsaktivering. I likhet med "Hey Cortana" för Microsofts assistent kan du skriva en app som kontinuerligt lyssnar efter ett sökord som du väljer. Tänk på att ett nyckelord kan vara ett ord eller en fras med flera ord.

> [!NOTE]
> Nyckelordet *term* används ofta omväxlande med termen *aktiveringsord*, och du kan se båda används i Microsoft-dokumentation.

Nyckelordsidentifiering görs på klientappen. Om du använder ett nyckelord strömmas ljud bara till direktlinjetalskanalen om nyckelordet identifieras. Direct Line Speech-kanalen innehåller en komponent som kallas *nyckelordsverifiering (KWV),* som gör mer komplex bearbetning i molnet för att kontrollera att nyckelordet du har valt är i början av ljudströmmen. Om nyckelordsverifieringen lyckas kommunicerar kanalen med roboten.

Följ dessa steg för att skapa en sökordsmodell, konfigurera Windows Voice Assistant-klienten så att den här modellen används och slutligen testa den med din robot.

1. Följ dessa instruktioner för att [skapa ett anpassat nyckelord med hjälp av taltjänsten](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Packa upp modellfilen som du hämtade i föregående steg. Det bör namnges för ditt sökord. Du letar efter en `kws.table`fil som heter .
3. Leta reda på **menyn Inställningar** i Windows Voice Assistant (leta efter kugghjulsikonen längst upp till höger). Leta reda på **sökvägen till modellfilen** och ange `kws.table` filens fullständiga sökvägsnamn från steg 2.
4. Kontrollera att rutan är **aktiverad.** Du bör se det här meddelandet bredvid kryssrutan: "Kommer att lyssna efter nyckelordet vid nästa anslutning". Om du har angett fel fil eller en ogiltig sökväg bör du se ett felmeddelande.
5. Ange din **talprenumerationsnyckel,** **prenumerationsnyckelregionen**och klicka sedan på **OK** för att stänga **menyn Inställningar.**
6. Klicka på **Återanslut**. Du bör se ett meddelande som lyder: "Ny konversation startade - skriv, tryck på mikrofonknappen eller säg nyckelordet". Appen lyssnar nu kontinuerligt.
7. Tala om alla fraser som börjar med sökordet. Till exempel: "**{your keyword}**, what time is it?". Du behöver inte pausa när du har yttrat nyckelordet. När du är klar händer två saker:
   * Du kommer att se en transkription av vad du talade
   * Strax efter hör du botens svar
8. Fortsätt att experimentera med de tre indatatyper som din bot stöder:
   * Text skrivs i det nedre fältet
   * Trycka på mikrofonikonen och tala
   * Att säga en fras som börjar med ditt sökord

### <a name="view-the-source-code-that-enables-keyword"></a>Visa källkoden som aktiverar nyckelordet

I källkoden för Windows Voice Assistant Client kan du ta en titt på dessa filer för att granska koden som används för att aktivera nyckelordsidentifiering:

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs)innehåller ett anrop till metoden [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)Tal-SDK , som används för att instansiera modellen från en lokal fil på disken.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)innehåller ett anrop till [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)Tal SDK-metoden , som aktiverar kontinuerlig sökordsidentifiering.

## <a name="optional-change-the-language-and-bot-voice"></a>(Valfritt) Ändra språk och bot röst

Den bot som du har skapat kommer att lyssna efter och svara på engelska, med en standard amerikansk engelska text-till-tal röst. Du är dock inte begränsad till att använda engelska eller en standardröst. I det här avsnittet får du lära dig hur du ändrar det språk som din bot kommer att lyssna efter och svara på. Du får också lära dig att välja en annan röst för det språket.

### <a name="change-the-language"></a>Ändra språk

Du kan välja mellan vilket språk som helst som nämns i [tal-till-text-tabellen.](language-support.md#speech-to-text) I exemplet nedan kommer vi att ändra språket till tyska.

1. Öppna appen Windows Voice Assistant Client, klicka på inställningsknappen (ikonen längst upp till höger) och ange `de-de` i fältet Språk (det här är språket som nämns i [tal-till-text-tabellen).](language-support.md#speech-to-text) Detta anger att det talade språket ska `en-us`identifieras och åsidosätter standardvärdet . Detta instruerar också Direct Line Speech kanal att använda en standard tysk röst för Bot svar.
2. Stäng inställningssidan och klicka på knappen Återanslut för att upprätta en ny anslutning till din ekorobot.
3. Klicka på mikrofonknappen och säg en fras på tyska. Du kommer att se den tolkade texten och ekoroboten som svarar med den tyska standardrösten.

### <a name="change-the-default-bot-voice"></a>Ändra standardrorösten

Om du markerar text-till-tal-rösten och kontrollerar uttalet kan du göra om roboten anger svaret i form av ett [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md) i stället för enkel text. Ekot bot använder inte SSML, men vi kan enkelt ändra koden för att göra det. I exemplet nedan lägger vi till SSML till echo bot svar, så att den tyska rösten Stefan Apollo (en manlig röst) kommer att användas i stället för standard kvinnlig röst. Se lista över [standardröster](language-support.md#standard-voices) och [neurala röster](language-support.md#neural-voices) som stöds för ditt språk.

1. Låt oss börja `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`med att öppna .
2. Leta upp dessa två rader:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Ersätt dem med:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Bygg din lösning i Visual Studio och åtgärda eventuella byggfel.

Det andra argumentet i metoden "MessageFactory.Text" anger [fältet Aktivitets speak](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) i bot-svaret. Med ovanstående ändring har den ersatts från enkel text till SSML för att ange en icke-standard tysk röst.

### <a name="redeploy-your-bot"></a>Distribuera om din bot

Nu när du har gjort den nödvändiga ändringen av roboten är nästa steg att publicera om den till din Azure App Service och prova det:

1. Högerklicka på **EchoBot-projektet** i fönstret Solution Explorer och välj **Publicera**.
2. Din tidigare distributionskonfiguration har redan lästs in som standard. Klicka bara på **Publicera bredvid** **EchoBot20190805125647 - Webb distribuera**.
3. Meddelandet **Publicera efterföljande** visas i utdatafönstret i Visual Studio och en webbsida startas med meddelandet "Din robot är klar!".
4. Öppna appen Windows Voice Assistant Client, klicka på inställningsknappen (ikonen för `de-de` höger växelsida) och se till att du fortfarande har det i fältet Språk.
5. Följ instruktionerna i [Skapa Windows Voice Assistant-klienten](#build-the-windows-voice-assistant-client) för att återknyta kontakten med din nyligen distribuerade bot, tala på det nya språket och höra dig bot svara på det språket med den nya rösten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta använda ekoroboten som distribueras i den här självstudien kan du ta bort den och alla tillhörande Azure-resurser genom att helt enkelt ta bort Azure **Resource-gruppen SpeechEchoBotTutorial-ResourceGroup**.

1. Från [Azure-portalen](https://portal.azure.com)klickar du på **Resursgrupper** från **Azure-tjänstnavigeringen.**
2. Hitta resursgruppen med namnet: **SpeechEchoBotTutorial-ResourceGroup**. Klicka på de tre punkterna (...).
3. Välj **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa din egen klientapp med Speech SDK](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>Se även

* Distribuera till en [Azure-region nära dig](https://azure.microsoft.com/global-infrastructure/locations/) för att se förbättring av tidsförbättringen för bot-svarstid
* Distribuera till en [Azure-region som stöder högkvalitativa NeuralA TTS-röster](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* Priser som är associerade med direct line-talkanal:
  * [Priser för Bot-tjänsten](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Tjänst för taligenkänning](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Bygga och distribuera din egen röstaktiverade bot:
  * Bygg en [Bot-Framework bot](https://dev.botframework.com/). Registrera den med [Direct Line Speech kanal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) och anpassa din bot för [röst](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * Utforska befintliga [Bot-Framework-lösningar:](https://microsoft.github.io/botframework-solutions/index)Bygg en [virtuell assistent](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) och [utöka den till Direct Line Speech](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)

---
title: Använda Personanpassaren i chatt robot – Personanpassare
description: Anpassa en C# .NET chatt-robot med en anpassad-slinga för att ge rätt innehåll till en användare baserat på åtgärder (med funktioner) och kontext funktioner.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 07/17/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 7c4920eaa7a5619be37d38afd763e7be416d3124
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565729"
---
# <a name="tutorial-use-personalizer-in-net-chat-bot"></a>Självstudie: använda Personanpassaren i .NET Chat-robot

Använd en C# .NET chatt-robot med en användarvänlig slinga för att ge rätt innehåll till en användare. Den här Chat-roboten föreslår ett enskilt kaffe eller te för en användare. Användaren kan godkänna eller avvisa förslaget. Detta ger personanpassa information som hjälper dig att göra nästa förslag mer lämpligt.

**I den här guiden får du lära dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Ställa in Azure-resurser
> * Konfigurera och köra bot
> * Interagera med bot med bot Framework-emulator
> * Förstå var och hur bot-roboten använder Personanpassaren


## <a name="how-does-the-chat-bot-work"></a>Hur fungerar chatt-roboten?

En chatt-robot är vanligt vis en-och-tillbaka-konversation med en användare. Den här Chat-roboten använder Personanpassare för att välja den bästa åtgärden (kaffe eller te) för att erbjuda användaren. Personanpassare använder förstärknings inlärning för att göra detta val.

Chat-roboten måste hantera inaktive ras i konversationen. Chatt-roboten använder [bot Framework](https://github.com/microsoft/botframework-sdk) för att hantera bot-arkitekturen och-konversationen och använder kognitiva tjänster [language Understanding](../LUIS/index.yml) (Luis) för att förstå avsikten med det naturliga språket från användaren.

Chatt-roboten är en webbplats med en angiven väg tillgänglig för att besvara begär Anden `http://localhost:3978/api/messages` . Du kan använda bot-emulatorn för att visuellt interagera med den pågående chatten när du utvecklar en robot lokalt.

### <a name="user-interactions-with-the-bot"></a>Användar interaktion med bot

Det här är en enkel chatt-robot som gör att du kan ange text frågor.

|Användaren anger text|Robot svarar med text|Beskrivning av åtgärdens robot tar att fastställa svars text|
|--|--|--|
|Ingen text har angetts – bot startar konversationen.|`This is a simple chatbot example that illustrates how to use Personalizer. The bot learns what coffee or tea order is preferred by customers given some context information (such as weather, temperature, and day of the week) and information about the user.`<br>`To use the bot, just follow the prompts. To try out a new imaginary context, type “Reset” and a new one will be randomly generated.`<br>`Welcome to the coffee bot, please tell me if you want to see the menu or get a coffee or tea suggestion for today. Once I’ve given you a suggestion, you can reply with ‘like’ or ‘don’t like’. It’s Tuesday today and the weather is Snowy.`|Roboten startar konversationen med instruktions text och du kan se vad kontexten är: `Tuesday` , `Snowy` .|
|`Show menu`|`Here is our menu: Coffee: Cappuccino Espresso Latte Macchiato Mocha Tea: GreenTea Rooibos`|Bestäm avsikten med att fråga med LUIS och Visa meny val för kaffe-och te-objekt. Funktioner i åtgärderna är |
|`What do you suggest`|`How about Latte?`|Bestäm avsikten med att fråga med hjälp av LUIS, anropa **rang-API** och visa det bästa valet som en fråga `How about {response.RewardActionId}?` . Visar också JSON-anrop och-svar för illustrations syfte.|
|`I like it`|`That’s great! I’ll keep learning your preferences over time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Bestäm avsikten med en fråga med hjälp av LUIS, och anropa sedan **belönings-API: et** med belöning för `1` , visar JSON-anrop och-svar för illustration.|
|`I don't like it`|`Oh well, maybe I’ll guess better next time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Bestäm avsikten med en fråga med hjälp av LUIS, och anropa sedan **belönings-API: et** med belöning för `0` , visar JSON-anrop och-svar för illustration.|
|`Reset`|Returnerar instruktions text.|Bestäm avsikten med en fråga med hjälp av LUIS. sedan visas instruktions texten och kontexten återställs.|


### <a name="personalizer-in-this-bot"></a>Personanpassare i den här roboten

Den här Chat-roboten använder en Personanpassare för att välja den översta åtgärden (visst kaffe eller te) baserat på en lista med _åtgärder_ (viss typ av innehåll) och kontext funktioner.

Roboten skickar en lista med åtgärder, tillsammans med kontext funktioner, till den personliga slingan. Personanpassan returnerar den enda bästa åtgärden för din robot, som din robot visar.

I den här självstudien är **åtgärderna** typer av kaffe och te:

|Kaffe|Trietanolamin|
|--|--|
|Cappuccino<br>Espresso<br>Latte<br>Mocha|GreenTea<br>Rooibos|

**Rangordnings-API:** För att hjälpa personanpassa att lära sig mer om dina åtgärder, skickar bot följande med varje rang-API-begäran:

* Åtgärder _med funktioner_
* Kontext funktioner

En **funktion** i modellen är information om den åtgärd eller kontext som kan aggregeras (grupperas) över medlemmar i din användar bas för chatt-roboten. En funktion _är inte_ individuellt angiven (t. ex. ett användar-ID) eller mycket särskilt (till exempel en exakt tid på dagen).

Funktioner används för att justera åtgärder till det aktuella sammanhanget i modellen. Modellen är en representation av Personanpassars tidigare kunskap om åtgärder, kontext och deras funktioner som gör det möjligt för IT att fatta sammanfattande beslut.

Modellen, inklusive funktioner, uppdateras enligt ett schema baserat på inställningarna för **modell uppdaterings frekvensen** i Azure Portal.

Funktioner ska väljas med samma planering och design som du kan använda för scheman eller modeller i din tekniska arkitektur. Funktions värden kan ställas in med affärs logik eller system från tredje part.

> [!CAUTION]
> Funktioner i det här programmet är för demonstration och är kanske inte nödvändigt vis de bästa funktionerna i en webbapp för ditt användnings fall.

#### <a name="action-features"></a>Åtgärds funktioner

Varje åtgärd (innehålls objekt) har funktioner som hjälper dig att särskilja kaffe eller te-objektet.

Funktionerna konfigureras inte som en del av loop-konfigurationen i Azure Portal. De skickas istället som ett JSON-objekt med varje rang-API-anrop. Detta ger flexibilitet för åtgärderna och deras funktioner att växa, ändra och krympa över tid, vilket gör det möjligt för personligt företag att följa trender.

Funktioner för kaffe och te är:

* Ursprungs plats för kaffe bönor som Kenya och Brasilien
* Är kaffeet eller trietanolamin organiskt?
* Lätt eller mörkt rostade kaffe

Även om kaffeet har tre funktioner i föregående lista, har te bara en. Skicka bara funktioner till en Personanpassare som är begriplig för åtgärden. Överför inte ett tomt värde för en funktion om den inte gäller för åtgärden.

#### <a name="context-features"></a>Kontext funktioner

Med hjälp av Sammanhangs funktioner kan en användarvänligare förstå kontexten för miljön, till exempel visnings enhet, användare, plats och andra funktioner som är relevanta för ditt användnings fall.

Kontexten för den här Chat-roboten innehåller:

* Typ av väder (snö, regn, solig)
* Veckodag

Urvalet av funktioner är slumpmässigt i den här Chat-roboten. Använd verkliga data för dina kontext funktioner i en riktig robot.

### <a name="design-considerations-for-this-bot"></a>Design överväganden för den här roboten

Det finns några försiktighets åtgärder att notera om den här konversationen:
* **Bot-interaktion** : konversationen är mycket enkel eftersom den demonstrerar rankning och belöning i ett enkelt användnings fall. Den demonstrerar inte alla funktioner i bot Framework SDK eller emulatorn.
* **Personanpassare** : funktionerna är markerade slumpmässigt för att simulera användning. Gör inte några slumpmässiga funktioner i ett scenario för tillverknings Personanpassan.
* **Language Understanding (Luis)** : några exempel yttranden för Luis-modellen är endast avsedda för det här exemplet. Använd inte några få exempel yttranden i ditt produktions LUIS-program.


## <a name="install-required-software"></a>Installera nödvändig program vara
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Den nedladdnings bara exempel lagrings platsen innehåller instruktioner om du föredrar att använda .NET Core CLI.
- [Microsoft bot Framework-emulator](https://aka.ms/botframeworkemulator) är ett Skriv bords program som gör det möjligt för bot-utvecklare att testa och felsöka sina robotar på localhost eller köra fjärr anslutning via en tunnel.

## <a name="download-the-sample-code-of-the-chat-bot"></a>Hämta exempel koden för chatt-roboten

Chatt-roboten är tillgänglig i databaserna för personanpassa exempel. Klona eller [Hämta](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/archive/master.zip) lagrings platsen och öppna sedan exemplet i `/samples/ChatbotExample` katalogen med Visual Studio 2019.

Om du vill klona lagrings platsen använder du följande git-kommando i ett bash-gränssnitt (Terminal).

```bash
git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
```

## <a name="create-and-configure-personalizer-and-luis-resources"></a>Skapa och konfigurera personligare och LUIS-resurser

### <a name="create-azure-resources"></a>Skapa Azure-resurser

Om du vill använda den här chatt-roboten måste du skapa Azure-resurser för Personanpassare och Language Understanding (LUIS).

* [Skapa Luis-resurser](../luis/luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal). Välj **båda** i steget Skapa eftersom du behöver både författar-och förutsägelse resurser.
* [Skapa en personanpassa resurs](how-to-create-resource.md) och kopiera sedan nyckeln och slut punkten från Azure Portal. Du måste ange dessa värden i `appsettings.json` filen för .net-projektet.

### <a name="create-luis-app"></a>Skapa LUIS-app

Om du är nybörjare på LUIS måste du [Logga](https://www.luis.ai) in och omedelbart migrera ditt konto. Du behöver inte skapa nya resurser. i stället väljer du de resurser som du skapade i föregående avsnitt i den här självstudien.

1. Om du vill skapa ett nytt LUIS-program går du till [Luis-portalen](https://www.luis.ai)och väljer din prenumeration och din redigering av resurs.
1. Sedan, fortfarande på samma sida, väljer du **+ ny app för konversation** och **importerar sedan som JSON**.
1. I popup-dialogrutan väljer du **Välj fil** och väljer sedan `/samples/ChatbotExample/CognitiveModels/coffeebot.json` filen. Ange namnet `Personalizer Coffee bot` .
1. Välj knappen **träna** i det övre högra navigerings fönstret på Luis-portalen.
1. Välj knappen **publicera** för att publicera appen på **produktions platsen** för förutsägelse körning.
1. Välj **Hantera** och sedan **Inställningar**. Kopiera värdet för **app-ID: t**. Du måste ange det här värdet i `appsettings.json` filen för .net-projektet.
1. Välj **Azure-resurser** fortfarande i avsnittet **Hantera** . Detta visar de associerade resurserna i appen.
1. Välj **Lägg till förutsägelse resurs**. I popup-dialogrutan väljer du din prenumeration och den förutsägelse resurs som skapades i föregående avsnitt i den här självstudien. Välj sedan **klar**.
1. Kopiera värdena för **primär nyckel** och **slut punkts-URL**. Du måste ange dessa värden i `appsettings.json` filen för .net-projektet.

### <a name="configure-bot-with-appsettingsjson-file"></a>Konfigurera bot med appsettings.jspå fil

1. Öppna lösnings filen för chatt-roboten `ChatbotSamples.sln` med Visual Studio 2019.
1. Öppna `appsettings.json` i projektets rot Katalog.
1. Ange alla fem inställningar som har kopierats i föregående avsnitt i den här självstudien.

    ```json
    {
      "PersonalizerChatbot": {
        "LuisAppId": "",
        "LuisAPIKey": "",
        "LuisServiceEndpoint": "",
        "PersonalizerServiceEndpoint": "",
        "PersonalizerAPIKey": ""
      }
    }
    ```

### <a name="build-and-run-the-bot"></a>Skapa och kör bot

När du har konfigurerat `appsettings.json` är du redo att bygga och köra chatt-roboten. När du gör det öppnas en webbläsare på webbplatsen som körs `http://localhost:3978` .

:::image type="content" source="media/tutorial-chat-bot/chat-bot-web-site.png" alt-text="Skärm bild av webbläsare som visar chatt robots webbplats.":::

Håll webbplatsen igång eftersom självstudien förklarar vad roboten gör, så att du kan interagera med bot.


## <a name="set-up-the-bot-emulator"></a>Konfigurera bot-emulatorn

1. Öppna bot Framework-emulatorn och välj **Öppna bot**.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-startup.png" alt-text="Skärm bild av Start skärmen för bot-emulatorn.":::


1. Konfigurera roboten med följande **bot-URL** och välj sedan **Anslut** :

    `http://localhost:3978/api/messages`

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-open-bot-settings.png" alt-text="Skärm bild av robot-emulator öppna bot-inställningar.":::

    Emulatorn ansluter till Chat-roboten och visar instruktions texten, tillsammans med loggnings-och felsöknings information som kan vara till hjälp för lokal utveckling.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-bot-conversation-first-turn.png" alt-text="Skärm bild av robot-emulatorn i första gången konversationen.":::

## <a name="use-the-bot-in-the-bot-emulator"></a>Använd bot-roboten i bot-emulatorn

1. Be att se menyn genom att ange `I would like to see the menu` . Chat-roboten visar objekten.
1. Låt roboten föreslå ett objekt genom att skriva in `Please suggest a drink for me.` emulatorn som visar ranknings förfrågan och svaret i Chat-fönstret, så att du kan se hela JSON. Och roboten gör ett förslag, något som liknar `How about Latte?`
1. Svar som du vill att du godkänner det bästa valet av Personanpassare, `I like it.` visar emulatorn belönings förfrågan med belönings poängen 1 och svaret i Chat-fönstret, så att du kan se hela JSON. Och bot-roboten svarar med `That’s great! I’ll keep learning your preferences over time.` och `Would you like to get a new suggestion or reset the simulated context to a new day?`

    Om du svarar `no` på valet skickas belönings poängen 0 till personanpassaren.


## <a name="understand-the-net-code-using-personalizer"></a>Förstå .NET-koden med hjälp av personanpassa

.NET-lösningen är en vanlig bot-chatt i bot-ramverket. Koden som är relaterad till Personanpassare finns i följande mappar:
* `/samples/ChatbotExample/Bots`
    * `PersonalizerChatbot.cs` fil för interaktionen mellan bot och personanpassa
* `/samples/ChatbotExample/ReinforcementLearning` -hanterar åtgärder och funktioner för personanpassa modeller
* `/samples/ChatbotExample/Model` – filer för de personliga åtgärderna och funktionerna och för LUIS-avsikter

### <a name="personalizerchatbotcs---working-with-personalizer"></a>PersonalizerChatbot.cs – arbeta med Personanpassare

`PersonalizerChatbot`Klassen härleds från `Microsoft.Bot.Builder.ActivityHandler` . Den har tre egenskaper och metoder för att hantera konversations flödet.

> [!CAUTION]
> Kopiera inte kod från den här självstudien. Använd exempel koden i [lagrings platsen för personanpassa exempel](https://github.com/Azure-Samples/cognitive-services-personalizer-samples).

```csharp
public class PersonalizerChatbot : ActivityHandler
{

    private readonly LuisRecognizer _luisRecognizer;
    private readonly PersonalizerClient _personalizerClient;

    private readonly RLContextManager _rlFeaturesManager;

    public PersonalizerChatbot(LuisRecognizer luisRecognizer, RLContextManager rlContextManager, PersonalizerClient personalizerClient)
            {
                _luisRecognizer = luisRecognizer;
                _rlFeaturesManager = rlContextManager;
                _personalizerClient = personalizerClient;
            }
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Check LUIS model
            var recognizerResult = await _luisRecognizer.RecognizeAsync(turnContext, cancellationToken);
            var topIntent = recognizerResult?.GetTopScoringIntent();
            if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
            {
                Intents intent = (Intents)Enum.Parse(typeof(Intents), topIntent.Value.intent);
                switch (intent)
                {
                    case Intents.ShowMenu:
                        await turnContext.SendActivityAsync($"Here is our menu: \n Coffee: {CoffeesMethods.DisplayCoffees()}\n Tea: {TeaMethods.DisplayTeas()}", cancellationToken: cancellationToken);
                        break;
                    case Intents.ChooseRank:
                        // Here we generate the event ID for this Rank.
                        var response = await ChooseRankAsync(turnContext, _rlFeaturesManager.GenerateEventId(), cancellationToken);
                        _rlFeaturesManager.CurrentPreference = response.Ranking;
                        await turnContext.SendActivityAsync($"How about {response.RewardActionId}?", cancellationToken: cancellationToken);
                        break;
                    case Intents.RewardLike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 1, cancellationToken);
                            await turnContext.SendActivityAsync($"That's great! I'll keep learning your preferences over time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you like. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.RewardDislike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 0, cancellationToken);
                            await turnContext.SendActivityAsync($"Oh well, maybe I'll guess better next time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you dislike. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.Reset:
                        _rlFeaturesManager.GenerateRLFeatures();
                        await SendResetMessageAsync(turnContext, cancellationToken);
                        break;
                    default:
                        break;
                }
            }
            else
            {
                var msg = @"Could not match your message with any of the following LUIS intents:
                        'ShowMenu'
                        'ChooseRank'
                        'RewardLike'
                        'RewardDislike'.
                        Try typing 'Show me the menu','What do you suggest','I like it','I don't like it'.";
                await turnContext.SendActivityAsync(msg);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Generate a new weekday and weather condition
            // These will act as the context features when we call rank with Personalizer
            _rlFeaturesManager.GenerateRLFeatures();

            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
    }

    // code removed for brevity, full sample code available for download
    private async Task SendWelcomeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendResetMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendByebyeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
    private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
}
```

De metoder som föregås av `Send` Hantera konversation med robot-och Luis. Metoderna `ChooseRankAsync` och `RewardAsync` interagerar med personanpassare.

#### <a name="calling-rank-api-and-display-results"></a>Anropar rang-API och Visa resultat

Metoden `ChooseRankAsync` bygger de JSON-data som ska skickas till personanpassare rang-API genom att samla in åtgärder med funktioner och kontext funktionerna.

```csharp
private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
{
    IList<object> contextFeature = new List<object>
    {
        new { weather = _rlFeaturesManager.RLFeatures.Weather.ToString() },
        new { dayofweek = _rlFeaturesManager.RLFeatures.DayOfWeek.ToString() },
    };

    Random rand = new Random(DateTime.UtcNow.Millisecond);
    IList<RankableAction> actions = new List<RankableAction>();
    var coffees = Enum.GetValues(typeof(Coffees));
    var beansOrigin = Enum.GetValues(typeof(CoffeeBeansOrigin));
    var organic = Enum.GetValues(typeof(Organic));
    var roast = Enum.GetValues(typeof(CoffeeRoast));
    var teas = Enum.GetValues(typeof(Teas));

    foreach (var coffee in coffees)
    {
        actions.Add(new RankableAction
        {
            Id = coffee.ToString(),
            Features =
            new List<object>()
            {
                new { BeansOrigin = beansOrigin.GetValue(rand.Next(0, beansOrigin.Length)).ToString() },
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
                new { Roast = roast.GetValue(rand.Next(0, roast.Length)).ToString() },
            },
        });
    }

    foreach (var tea in teas)
    {
        actions.Add(new RankableAction
        {
            Id = tea.ToString(),
            Features =
            new List<object>()
            {
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
            },
        });
    }

    // Sending a rank request to Personalizer
    // Here we are asking Personalizer to decide which drink the user is most likely to want
    // based on the current context features (weather, day of the week generated in RLContextManager)
    // and the features of the drinks themselves
    var request = new RankRequest(actions, contextFeature, null, eventId);
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL TO RANK =====\n" +
        "This is what is getting sent to Rank:\n" +
        $"{JsonConvert.SerializeObject(request, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    var response = await _personalizerClient.RankAsync(request, cancellationToken);
    await turnContext.SendActivityAsync(
        $"===== DEBUG MESSAGE RETURN FROM RANK =====\n" +
        "This is what Rank returned:\n" +
        $"{JsonConvert.SerializeObject(response, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    return response;
}
```

#### <a name="calling-reward-api-and-display-results"></a>Anropar belönings-API och visnings resultat

Metoden `RewardAsync` bygger de JSON-data som ska skickas till personanpassars belönings-API genom att fastställa poäng. Poängen bestäms av LUIS-avsikten som identifieras i användar texten och skickas från- `OnTurnAsync` metoden.

```csharp
private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL REWARD =====\n" +
        "Calling Reward:\n" +
        $"eventId = {eventId}, reward = {reward}\n",
        cancellationToken: cancellationToken);

    // Sending a reward request to Personalizer
    // Here we are responding to the drink ranking Personalizer provided us
    // If the user liked the highest ranked drink, we give a high reward (1)
    // If they did not, we give a low reward (0)
    await _personalizerClient.RewardAsync(eventId, new RewardRequest(reward), cancellationToken);
}
```

## <a name="design-considerations-for-a-bot"></a>Design överväganden för en robot

Det här exemplet är tänkt att demonstrera en enkel komplett lösning av en egen användare i en bot. Ditt användnings fall kan vara mer komplext.

Om du vill använda Personanpassare i en produktions robot, planera för:
* Åtkomst till personligare i real tid _varje gång_ du behöver ett Rankat val. Ranknings-API: t kan inte grupperas eller cachelagras.  Belönings anropet kan fördröjas eller avlastas till en separat process och om du inte returnerar en belöning under den tids period som anges, anges ett standard belönings värde för händelsen.
* Användnings fall baserad beräkning av belöningen: det här exemplet visade två fördelar med noll och ett med inget intervall mellan och inget negativt värde för ett resultat. Ditt system har gjorts om du behöver mer detaljerad poäng.
* Bot-kanaler: det här exemplet använder en enda kanal, men om du tänker använda fler än en kanal, eller varianter av robotar på en enskild kanal, kan det vara nödvändigt att betrakta som en del av kontext funktionerna i personanpassa modell.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här självstudien kan du rensa följande resurser:

* Ta bort exempel projekt katalogen.
* Ta bort din Personanpassare och LUIS-resurs i Azure Portal.

## <a name="next-steps"></a>Nästa steg
* [Så här fungerar Personanpassning](how-personalizer-works.md)
* [Funktioner](concepts-features.md): Lär dig begrepp om funktioner som använder med åtgärder och kontext
* [Fördelar](concept-rewards.md): Lär dig att beräkna förmåner

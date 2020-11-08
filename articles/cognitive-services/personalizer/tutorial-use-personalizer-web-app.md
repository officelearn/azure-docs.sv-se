---
title: Använda Web App-personanpassar
description: Anpassa en C# .NET-webbapp med en personanpassa-slinga för att ge rätt innehåll till en användare baserat på åtgärder (med funktioner) och kontext funktioner.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c004887e3883ae711974b544510dff16a98d4ef9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363926"
---
# <a name="tutorial-add-personalizer-to-a-net-web-app"></a>Självstudie: Lägg till en Personanpassare till en .NET-webbapp

Anpassa en C# .NET-webbapp med en personanpassa-slinga för att ge rätt innehåll till en användare baserat på åtgärder (med funktioner) och kontext funktioner.

**I den här guiden får du lära dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Konfigurera en anpassnings nyckel och slut punkt
> * Samla in funktioner
> * Anropa rang-och belönings-API: er
> * Visa Top-åtgärd som anges som _rewardActionId_



## <a name="select-the-best-content-for-a-web-app"></a>Välj det bästa innehållet för en webbapp

En webbapp bör använda Personanpassare när det finns en lista med _åtgärder_ (viss typ av innehåll) på webb sidan som måste anpassas till ett enda översta objekt (rewardActionId) som ska visas. Exempel på åtgärds listor är nyhets artiklar, placerings platser för knappar och Word-alternativ för produkt namn.

Du skickar en lista med åtgärder, tillsammans med kontext funktioner, till den personliga slingan. Personanpassa väljer den enda bästa åtgärden. sedan visar din webbapp den åtgärden.

I den här självstudien är åtgärderna typer av livsmedel:

* pasta
* Ice-grädde
* apelsinjuicepriser
* salad
* popcorn
* kaffe
* soppor

För att hjälpa personanpassa att lära sig mer om dina åtgärder, skicka båda _åtgärderna med funktioner_ och _kontext funktioner_ med varje rang-API-begäran.

En **funktion** i modellen är information om den åtgärd eller kontext som kan aggregeras (grupperas) över medlemmar i din webbapp användar bas. En funktion _är inte_ individuellt angiven (t. ex. ett användar-ID) eller mycket särskilt (till exempel en exakt tid på dagen).

### <a name="actions-with-features"></a>Åtgärder med funktioner

Varje åtgärd (innehålls objekt) har funktioner som hjälper dig att särskilja objektet i livsmedlet.

Funktionerna konfigureras inte som en del av loop-konfigurationen i Azure Portal. De skickas istället som ett JSON-objekt med varje rang-API-anrop. Detta ger flexibilitet för åtgärderna och deras funktioner att växa, ändra och krympa över tid, vilket gör det möjligt för personligt företag att följa trender.

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
          },

          new RankableAction
          {
              Id = "ice cream",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
          },

          new RankableAction
          {
              Id = "juice",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "salad",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>Kontext funktioner

Med hjälp av kontext funktioner kan en bättre förståelse förstå åtgärdens kontext. Kontexten för det här exempel programmet innehåller:

* tid på dag – morgon, eftermiddag, kväll, natt
* användarens preferens för smak – salt, Söt, bitter, kä eller Savory
* webbläsarens kontext – användar agent, geografisk plats, referent

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>Hur använder webbappen personanpassar?

Webbappen använder sig av Personanpassare för att välja den bästa åtgärden i listan med alternativ för livsmedel. Detta görs genom att skicka följande information med varje rang-API-anrop:
* **åtgärder** med deras funktioner, till exempel `taste` och `spiceLevel`
* **kontext** funktioner som `time` dag, användarens `taste` preferens och webbläsarens användar agent information och kontext funktioner
* **åtgärder för att undanta** till exempel juice
* **eventId** , som skiljer sig för varje anrop till Range-API.

## <a name="personalizer-model-features-in-a-web-app"></a>Funktioner för personanpassa modeller i en webbapp

En personanpassare behöver funktioner för åtgärder (innehåll) och aktuell kontext (användare och miljö). Funktioner används för att justera åtgärder till det aktuella sammanhanget i modellen. Modellen är en representation av Personanpassars tidigare kunskap om åtgärder, kontext och deras funktioner som gör det möjligt för IT att fatta sammanfattande beslut.

Modellen, inklusive funktioner, uppdateras enligt ett schema baserat på inställningarna för **modell uppdaterings frekvensen** i Azure Portal.

> [!CAUTION]
> Funktioner i det här programmet är avsedda att illustrera funktioner och funktions värden, men inte nödvändigt vis de bästa funktionerna som ska användas i en webbapp.

### <a name="plan-for-features-and-their-values"></a>Planera för funktioner och deras värden

Funktioner ska väljas med samma planering och design som du kan använda för scheman eller modeller i din tekniska arkitektur. Funktions värden kan ställas in med affärs logik eller system från tredje part. Funktions värden får inte vara så högt exakta att de inte gäller för en grupp eller klass med funktioner.

### <a name="generalize-feature-values"></a>Generalisera funktions värden

#### <a name="generalize-into-categories"></a>Generalisera i kategorier

Den här appen använder sig av `time` en funktion men grupperar tid till kategorier som `morning` ,, `afternoon` `evening` och `night` . Det är ett exempel på hur du kan använda informationen i tid men inte på ett särskilt sätt, t `10:05:01 UTC+2` . ex..

#### <a name="generalize-into-parts"></a>Generalisera i delar

I den här appen används funktioner för HTTP-begäran från webbläsaren. Detta börjar med en mycket speciell sträng med alla data, till exempel:

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

**HttpRequestFeatures** klass bibliotek generaliserar den här strängen i ett **userAgentInfo** -objekt med enskilda värden. Alla värden som är för exakta anges till en tom sträng. När kontext funktionerna för begäran skickas har den följande JSON-format:

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>Använda exempel webb program

Den webbläsarbaserade webbappen (all kod har angetts) behöver följande program installerade för att köra appen.

Installera följande programvara:

* [.Net core 2,1](https://dotnet.microsoft.com/download/dotnet-core/2.1) – exempel Server delen använder .net Core
* [Node.js](https://nodejs.org/) -klientens/klient delen är beroende av det här programmet
* [Visual studio 2019](https://visualstudio.microsoft.com/vs/)eller [.net Core CLI](/dotnet/core/tools/) – Använd antingen utvecklings miljön i visual Studio 2019 eller .net Core CLI för att skapa och köra appen

### <a name="set-up-the-sample"></a>Konfigurera exemplet
1. Klona lagrings platsen för Azures anpassade Azure-exempel.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. Öppna lösningen genom att gå till _samples/HttpRequestFeatures_ `HttpRequestFeaturesExample.sln` .

    Om det behövs kan Visual Studio uppdatera .NET-paketet för Personanpassare.

### <a name="set-up-azure-personalizer-service"></a>Konfigurera Azure personanpassa service

1. [Skapa en personanpassa resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) i Azure Portal.

1. I Azure Portal hittar du `Endpoint` och antingen `Key1` eller `Key2` (fungerar antingen) på fliken **nycklar och slut punkter** . Det här är dina `PersonalizerServiceEndpoint` och dina `PersonalizerApiKey` .
1. Fyll i `PersonalizerServiceEndpoint` **appsettings.jspå**.
1. Konfigurera `PersonalizerApiKey` som en [program hemligheter](/aspnet/core/security/app-secrets) på något av följande sätt:

    * Om du använder .NET Core CLI kan du använda `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` kommandot.
    * Om du använder Visual Studio kan du högerklicka på projektet och välja meny alternativet **hantera användar hemligheter** för att konfigurera de personliga nycklarna. Genom att göra detta öppnar Visual Studio en `secrets.json` fil där du kan lägga till nycklarna enligt följande:

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>Kör exemplet

Skapa och kör HttpRequestFeaturesExample med någon av följande metoder:

* Visual Studio 2019: Tryck på **F5**
* .NET Core CLI: `dotnet build` sedan `dotnet run`

Via en webbläsare kan du skicka en rang-begäran och en belönings förfrågan och se deras svar, samt de funktioner för HTTP-begäranden som har extraherats från din miljö.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar ett exempel på funktionen HTTP-förfrågan i en webbläsare.](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>Demonstrera den personliga slingan

1. Välj knappen **Skapa ny rang förfrågan** för att skapa ett nytt JSON-objekt för rang-API-anropet. Detta skapar åtgärder (med funktioner) och kontext funktioner och visar värdena så att du kan se hur JSON ser ut.

    För ditt eget framtida program kan skapandet av åtgärder och funktioner inträffa på klienten, på servern, en blandning av två eller med anrop till andra tjänster.

1. Välj **Skicka rang-begäran** för att skicka JSON-objektet till servern. Servern anropar ett API för personanpassa rankning. Servern tar emot svaret och returnerar den främsta rangordnade åtgärden till klienten som ska visas.

1. Ange belöning svärdet och välj sedan knappen **Skicka belönings förfrågan** . Om du inte ändrar belöning svärdet skickar klient programmet alltid värdet för `1` till-personanpassa.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar avsnittet om belönings förfrågan.](./media/tutorial-web-app/reward-score-api-call.png)

    För ditt eget framtida program kan genereringen av belönings poängen inträffa efter att du samlat in information från användarens beteende på klienten, tillsammans med affärs logik på servern.

## <a name="understand-the-sample-web-app"></a>Förstå exempel webb programmet

Exempel-webbappen har en **C# .net** -server som hanterar samlingen med funktioner och skickar och tar emot http-anrop till din personanpassa-slutpunkt.

I exempel webb programmet används ett **blockerat klient program för klient program** för att samla in funktioner och bearbeta användar gränssnitts åtgärder som att klicka på knappar och skicka data till .net-servern.

I följande avsnitt förklaras de delar av servern och klienten som en utvecklare måste förstå för att använda Personanpassare.

## <a name="rank-api-client-application-sends-context-to-server"></a>Ranknings-API: klient programmet skickar en kontext till servern

Klient programmet samlar in användarens _användar agent_ för webbläsare.

> [!div class="mx-imgBorder"]
> ![Skapa och kör HTTPRequestFeaturesExample-projektet. Ett webbläsarfönster öppnas för att visa programmet för en enda sida.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>Ranknings-API: Personanpassare för Server program samtal

Det här är en typisk .NET-webbapp med ett klient program, men en stor del av pannans plåts kod tillhandahålls åt dig. All kod som inte är speciell för Personanpassan tas bort från följande kodfragment så att du kan fokusera på den anpassade koden.

### <a name="create-personalizer-client"></a>Skapa en personanpassa klient

I serverns **startup.cs** används den personligare slut punkten och nyckeln för att skapa en personanpassa klient. Klient programmet behöver inte kommunicera med en Personanpassare i den här appen, i stället förlitar sig på servern för att göra dessa SDK-anrop.

Webb serverns .NET-start kod är:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>Välj bästa åtgärd

I serverns **PersonalizerController.cs** sammanfattar API: et för **GenerateRank** -Server förberedelserna för att anropa rang-API: et

* Skapa `eventId` en ny för rang anropet
* Hämta listan över åtgärder
* Hämta en lista över funktioner från användaren och skapa kontext funktioner
* Du kan också ange eventuella undantagna åtgärder
* Anropa rang-API, returnera resultat till klienten

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

Den JSON som skickas till en Personanpassare som innehåller båda åtgärderna (med funktioner) och de aktuella kontext funktionerna ser ut så här:

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>Returnera personanpassa rewardActionId till klienten

Ranknings-API: et returnerar den valda bästa åtgärden **rewardActionId** till servern.

Visa åtgärden som returnerades i **rewardActionId**.

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>Klient visar åtgärden rewardActionId

I den här självstudien `rewardActionId` visas värdet.

I ditt eget framtida program kan det vara en del text, en knapp eller ett avsnitt på webb sidan som är markerat. Listan returneras för alla resultat efter analyser, inte en ordning på innehållet. Endast `rewardActionId` innehållet ska visas.

## <a name="reward-api-collect-information-for-reward"></a>Belönings-API: samla in information för belöning

[Belönings poängen](concept-rewards.md) bör planeras noggrant, precis som funktionerna planeras. Belönings poängen bör normalt vara ett värde mellan 0 och 1. Värdet _kan_ beräknas delvis i klient programmet, baserat på användar beteenden och delvis på servern, baserat på affärs logik och mål.

Om servern inte anropar belönings-API: n inom svars **tiden för belöningen** som kon figurer ats i Azure Portal för din personanpassa resurs, används **standard belöningen** (som också kon figurer ATS i Azure Portal) för den händelsen.

I det här exempel programmet kan du välja ett värde för att se hur belöningen påverkar valen.

## <a name="additional-ways-to-learn-from-this-sample"></a>Ytterligare sätt att lära sig från det här exemplet

Exemplet använder flera tidsbaserade händelser som kon figurer ATS i Azure Portal för din personanpassa resurs. Spela med dessa värden och gå sedan tillbaka till den här exempel-webbappen för att se hur ändringarna påverkar ranknings-och belönings anropen:

* Vänte tid för belöning
* Uppdaterings frekvens för modell

Ytterligare inställningar att spela med är:
* Standard belöning
* Utforsknings procent


## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här självstudien kan du rensa följande resurser:

* Ta bort exempel projekt katalogen.
* Ta bort din personanpassa resurs – Tänk på en personanpassar-resurs som dedikerad till åtgärder och Använd endast kontext återanvända resursen om du fortfarande använder de livsmedel som åtgärds ämnes domän.


## <a name="next-steps"></a>Nästa steg
* [Så här fungerar Personanpassning](how-personalizer-works.md)
* [Funktioner](concepts-features.md): Lär dig begrepp om funktioner som använder med åtgärder och kontext
* [Fördelar](concept-rewards.md): Lär dig att beräkna förmåner
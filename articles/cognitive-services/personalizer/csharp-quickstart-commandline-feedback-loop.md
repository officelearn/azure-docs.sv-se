---
title: 'Snabbstart: Skapa en feedback-slinga – Personanpassare'
titleSuffix: Azure Cognitive Services
description: Anpassa innehållet i den C# här snabb starten med tjänsten personanpassa.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 06/11/2019
ms.author: diberry
ms.openlocfilehash: 54aa23071fef09058a1702218d6b7fc920363518
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662801"
---
# <a name="quickstart-personalize-content-using-c"></a>Snabbstart: Anpassa innehåll medC# 

Visa personligt innehåll i den C# här snabb starten med tjänsten personanpassa.

Det här exemplet visar hur du använder det personliga klient biblioteket för C# för att utföra följande åtgärder: 

 * Rangordna en lista med åtgärder för anpassning.
 * Rapportera belöning för att allokera till den främsta rangordnade åtgärden baserat på val av användare för den angivna händelsen.

Att komma igång med Personanpassare omfattar följande steg:

1. Referera till SDK 
1. Skriva kod för att rangordna de åtgärder som du vill visa för användarna,
1. Skriva kod för att skicka fördelar för att träna slingan.

## <a name="prerequisites"></a>Förutsättningar

* Du behöver en [personanpassa tjänst](how-to-settings.md) för att hämta din prenumerations nyckel och URL för slut punkts tjänst. 
* [Visual Studio 2015 eller 2017](https://visualstudio.microsoft.com/downloads/).
* Paketet [Microsoft. Azure. CognitiveServices. personanpassa](https://go.microsoft.com/fwlink/?linkid=2092272) SDK NuGet. Installationsinstruktioner finns nedan.

## <a name="change-the-model-update-frequency"></a>Ändra modell uppdaterings frekvensen

Ändra uppdaterings frekvensen för **modellen** till 10 sekunder i den personliga resursen i Azure Portal. Detta kommer att träna tjänsten snabbt, så att du kan se hur de viktigaste åtgärderna ändras för varje iteration.

När en säkerhetsslinga först instansieras finns det ingen modell eftersom det inte har skett några belönings-API-anrop att träna från. Ranknings anrop returnerar lika många sannolikheter för varje objekt. Ditt program borde fortfarande alltid rangordna innehåll med hjälp av utdata från RewardActionId.

![Ändra modell uppdaterings frekvens](./media/settings/configure-model-update-frequency-settings.png)

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Skapa en ny konsol app och referera till personanpassa SDK 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. Skapa en ny Visual C#-konsolapp i Visual Studio.
1. Installera NuGet-paketet för personanpassa klient bibliotek. Välj **verktyg**på menyn, Välj **NuGet Package Manager**och sedan **Hantera NuGet-paket för lösningen**.
1. Markera **Inkludera för hands version**.
1. Välj fliken **Bläddra** och skriv `Microsoft.Azure.CognitiveServices.Personalizer`i rutan **Sök** .
1. Välj **Microsoft. Azure. CognitiveServices. personanpassare** när den visas.
1. Markera kryss rutan bredvid ditt projekt namn och välj **Installera**.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Lägg till koden och Lägg i din personligare och Azure-nycklar

1. Ersätt Program.cs med följande kod. 
1. Ersätt `serviceKey` värdet med din giltiga prenumerations nyckel för din personliga nyckel.
1. Ersätt `serviceEndpoint` med tjänstens slut punkt. Ett exempel är `https://westus2.api.cognitive.microsoft.com/`.
1. Kör programmet.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Lägg till kod som rangordnar de åtgärder som du vill ska visas för användarna

Följande C# kod är en komplett lista för att skicka användar information, _features och information om ditt innehåll, _åtgärder_till personanpassare med hjälp av SDK. Personanpassare returnerar den främsta rangordnade åtgärden för att visa din användare.  

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;

namespace PersonalizerExample
{
    class Program
    {
        // The key specific to your personalizer service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string ApiKey = "";

        // The endpoint specific to your personalizer service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string ServiceEndpoint = "";

        static void Main(string[] args)
        {
            int iteration = 1;
            bool runLoop = true;

            // Get the actions list to choose from personalizer with their features.
            IList<RankableAction> actions = GetActions();

            // Initialize Personalizer client.
            PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

            do
            {
                Console.WriteLine("\nIteration: " + iteration++);

                // Get context information from the user.
                string timeOfDayFeature = GetUsersTimeOfDay();
                string tasteFeature = GetUsersTastePreference();

                // Create current context from user specified data.
                IList<object> currentContext = new List<object>() {
                    new { time = timeOfDayFeature },
                    new { taste = tasteFeature }
                };

                // Exclude an action for personalizer ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
                var request = new RankRequest(actions, currentContext, excludeActions, eventId);
                RankResponse response = client.Rank(request);

                Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

                float reward = 0.0f;
                string answer = GetKey();

                if (answer == "Y")
                {
                    reward = 1;
                    Console.WriteLine("\nGreat! Enjoy your food.");
                }
                else if (answer == "N")
                {
                    reward = 0;
                    Console.WriteLine("\nYou didn't like the recommended food choice.");
                }
                else
                {
                    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
                }

                Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
                foreach (var rankedResponse in response.Ranking)
                {
                    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
                }

                // Send the reward for the action based on user response.
                client.Reward(response.EventId, new RewardRequest(reward));

                Console.WriteLine("\nPress q to break, any other key to continue:");
                runLoop = !(GetKey() == "Q");

            } while (runLoop);
        }

        /// <summary>
        /// Initializes the personalizer client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalizer client instance</returns>
        static PersonalizerClient InitializePersonalizerClient(string url)
        {
            PersonalizerClient client = new PersonalizerClient(
                new ApiKeyServiceClientCredentials(ApiKey)) {Endpoint = url};

            return client;
        }

        /// <summary>
        /// Get users time of the day context.
        /// </summary>
        /// <returns>Time of day feature selected by the user.</returns>
        static string GetUsersTimeOfDay()
        {
            string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

            Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
            if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
                timeIndex = 1;
            }

            return timeOfDayFeatures[timeIndex - 1];
        }

        /// <summary>
        /// Gets user food preference.
        /// </summary>
        /// <returns>Food taste feature selected by the user.</returns>
        static string GetUsersTastePreference()
        {
            string[] tasteFeatures = new string[] { "salty", "sweet" };

            Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
            if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
                tasteIndex = 1;
            }

            return tasteFeatures[tasteIndex - 1];
        }

        /// <summary>
        /// Creates personalizer actions feature list.
        /// </summary>
        /// <returns>List of actions for personalizer.</returns>
        static IList<RankableAction> GetActions()
        {
            IList<RankableAction> actions = new List<RankableAction>
            {
                new RankableAction
                {
                    Id = "pasta",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
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
                    new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
                }
            };

            return actions;
        }

        private static string GetKey()
        {
            return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
        }
    }
}
```

## <a name="run-the-program"></a>Köra programmet

Skapa och kör programmet. Snabb start programmet ber några frågor om att samla in användar inställningar, kallas funktioner, och ger sedan den främsta åtgärden.

![Snabb start programmet ber några frågor om att samla in användar inställningar, kallas funktioner, och ger sedan den främsta åtgärden.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med snabbstarten tar du bort alla filer som skapas i den här snabbstarten. 

## <a name="next-steps"></a>Nästa steg

[Så här fungerar Personanpassaren](how-personalizer-works.md)



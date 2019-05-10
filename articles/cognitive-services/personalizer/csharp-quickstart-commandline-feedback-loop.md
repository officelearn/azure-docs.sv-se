---
title: Feedback-loop - Personalizer
titleSuffix: Azure Cognitive Services
description: Anpassa innehållet i den här C# Snabbstart med Personalizer-tjänsten.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/08/2019
ms.author: edjez
ms.openlocfilehash: b0dc8fbbb80a4d03b2cb64d09ffe9a36883c5bf9
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521375"
---
# <a name="quickstart-personalize-content-using-c"></a>Snabbstart: Anpassa innehåll med hjälp avC# 

Visa anpassat innehåll i den här C# Snabbstart med Personalizer-tjänsten.

Det här exemplet visar hur du använder klientbiblioteket Personalizer för C# att utföra följande åtgärder: 

 * Rangordning en lista med åtgärder för anpassning.
 * Rapportera trafik att allokera överst rangordnas åtgärder baserat på användarens val för den angivna händelsen.

Komma igång med Personalizer omfattar följande steg:

1. Refererar till SDK: N 
1. Skriva kod för att rangordna de åtgärder som du vill visa till dina användare
1. Skriva kod för att skicka alla för att träna loopen.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Du behöver en [Personalizer service](how-to-settings.md) att få din prenumeration nyckel och slutpunkt-url. 
* [Visual Studio 2015 eller 2017](https://visualstudio.microsoft.com/downloads/).
* Den [Microsoft.Azure.CognitiveServices.Personalizer](https://go.microsoft.com/fwlink/?linkid=2092272) NuGet-paketet SDK. Installationsinstruktioner finns nedan.

## <a name="change-the-model-update-frequency"></a>Ändra uppdateringsfrekvensen för modellen

I Personalizer resursen i Azure-portalen, ändra den **modellen uppdateringsfrekvensen** till 10 sekunder. Detta kommer träna tjänsten snabbt, så att du kan se hur den översta åtgärden ändras för varje upprepning

![Ändra uppdateringsfrekvensen för modellen](./media/settings/configure-model-update-frequency-settings.png)

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Skapa en ny konsolapp och refererar till Personalizer-SDK 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. Skapa en ny Visual C#-konsolapp i Visual Studio.
1. Installera Personalizer klienten bibliotekets NuGet-paket. På menyn, Välj **verktyg**väljer **Nuget package Manager**, sedan **hantera NuGet-paket för lösningen**.
1. Välj den **Bläddra** fliken och i den **Search** skriver `Microsoft.Azure.CognitiveServices.Personalizer`.
1. Välj **Microsoft.Azure.CognitiveServices.Personalizer** när den visas.
1. Markera kryssrutan bredvid ditt projektnamn och välj **installera**.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Lägg till kod och placera i dina Personalizer och Azure-nycklar

1. Ersätt Program.cs med följande kod. 
1. Ersätt `serviceKey` värdet med prenumerationsnyckeln giltig Personalizer.
1. Ersätt `serviceEndpoint` med tjänstens slutpunkt. Ett exempel är `https://westus2.api.cognitive.microsoft.com/`.
1. Kör programmet.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Lägg till kod för att rangordna de åtgärder som du vill visa dina användare

Följande C# koden är en komplett lista att skicka information om användare, _features och information om ditt innehåll _åtgärder_, att Personalizer med hjälp av SDK. Personalizer returnerar upp rangordnas åtgärd för att visa dina användare.  

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

Skapa och kör programmet. Snabbstart ombeds några frågor att samla in användarinställningar, kallas funktioner, sedan ger den översta åtgärden.

![Snabbstart ombeds några frågor att samla in användarinställningar, kallas funktioner, sedan ger den översta åtgärden.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med snabbstarten tar du bort alla filer som skapas i den här snabbstarten. 

## <a name="next-steps"></a>Nästa steg

[Hur Personalizer fungerar](how-personalizer-works.md)



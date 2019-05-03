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
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: c566d1fd4b151efc0d28b7059504e60a1451c034
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027147"
---
# <a name="quickstart-personalize-content-using-c"></a>Snabbstart: Anpassa innehåll med hjälp avC# 

Visa anpassat innehåll i den här C# Snabbstart med Personalizer-tjänsten.

Det här exemplet visar hur du använder klientbiblioteket personanpassning för C# att utföra följande åtgärder: 

 * Rangordning en lista med åtgärder för anpassning.
 * Rapportera trafik att allokera överst rangordnas åtgärder baserat på användarens val för den angivna händelsen.

Komma igång med personanpassning omfattar följande steg:

1. Refererar till SDK: N 
1. Skriva kod för att rangordna de åtgärder som du vill visa till dina användare
1. Skriva kod för att skicka alla för att träna loopen.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Du behöver en prenumerationsnyckel och token som utfärdas tjänst-url.
* [Visual Studio 2015 eller 2017](https://visualstudio.microsoft.com/downloads/).
* Microsoft.Azure.CognitiveServices.Personalization SDK NuGet-paketet. Installationsinstruktioner finns nedan.

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Skapa en ny konsolapp och refererar till Personalizer-SDK 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. Skapa en ny Visual C#-konsolapp i Visual Studio.
1. Installera personanpassning klienten bibliotekets NuGet-paket. På menyn, Välj **verktyg**väljer **Nuget package Manager**, sedan **hantera NuGet-paket för lösningen**.
1. Välj den **Bläddra** fliken och i den **Search** skriver `Microsoft.Azure.CognitiveServices.Personalization`.
1. Välj **Microsoft.Azure.CognitiveServices.Personalization** när den visas.
1. Markera kryssrutan bredvid ditt projektnamn och välj **installera**.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Lägg till kod och placera i dina Personalizer och Azure-nycklar

1. Ersätt Program.cs med följande kod. 
1. Ersätt `serviceKey` värdet med prenumerationsnyckeln giltig Personalizer.
1. Ersätt `serviceEndpoint` med tjänstens slutpunkt. Ett exempel är `https://westus2.api.cognitive.microsoft.com/`.
1. Kör programmet.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Lägg till kod för att rangordna de åtgärder som du vill visa dina användare

Följande C# koden är en komplett lista att skicka information om användare, _features och information om ditt innehåll _åtgärder_, att Personalizer med hjälp av SDK. Personalizer returnerar upp rangordnas åtgärd för att visa dina användare.  

```csharp
using Microsoft.Azure.CognitiveServices.Personalization;
using Microsoft.Azure.CognitiveServices.Personalization.Models;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PersonalizationExample
{
    class Program
    {
        // The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string serviceKey = "";

        // The endpoint specific to your personalization service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string serviceEndpoint = "";

        static void Main(string[] args)
        {
            int iteration = 1;
            bool runLoop = true;

            Uri url = new Uri(serviceEndpoint);

            // Get the actions list to choose from personalization with their features.
            IList<RankableAction> actions = GetActions();

            // Initialize Personalization client.
            PersonalizationClient client = InitializePersonalizationClient(url);

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

                // Exclude an action for personalization ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
                var request = new RankRequest(actions, currentContext, excludeActions, eventId);
                RankResponse response = client.Rank(request);

                Console.WriteLine("\nPersonalization service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

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

                Console.WriteLine("\nPersonalization service ranked the actions with the probabilities as below:");
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
        /// Initializes the personalization client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalization client instance</returns>
        static PersonalizationClient InitializePersonalizationClient(Uri url)
        {
            PersonalizationClient client = new PersonalizationClient(url,
            new ApiKeyServiceClientCredentials(serviceKey),
            new DelegatingHandler[] { });

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
        /// Creates personalization actions feature list.
        /// </summary>
        /// <returns>List of actions for personalization.</returns>
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



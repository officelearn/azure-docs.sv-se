---
title: 'Snabbstart: SDK för entitetssökning i Bing, C#'
titleSuffix: Azure Cognitive Services
description: Konfigurera för SDK för entitetssökning-konsolprogrammet med C#.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 01/30/2018
ms.author: v-gedod
ms.openlocfilehash: 7cbb97fbf237e14d6822edfa174ca469f8988e2d
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52307366"
---
# <a name="quickstart-bing-entity-search-sdk-with-c"></a>Snabbstart: SDK för entitetssökning i Bing med C#

API för entitetsökning i Bing innehåller funktionerna i REST API:et för entitetssökning och parsning av resultatet.

[Källkoden till exemplen med SDK:t för entitetssökning i Bing med C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch) är tillgänglig på GitHub.

## <a name="application-dependencies"></a>Programberoenden
Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under **Sök**.  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Om du vill konfigurera ett konsolprogram med hjälp av SDK:n för entitetssökning i Bing, bläddrar du till alternativet `Manage NuGet Packages` från Solution Explorer i Visual Studio.  Lägg till paketet `Microsoft.Azure.CognitiveServices.Search.EntitySearch`.

När du installerar [NuGet-paketet för entitetssökning](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0) installeras även beroenden, inklusive följande sammansättningar:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>Klient för entitetssökning
Om du vill skapa en instans av `EntitySearchAPI`-klienten lägger du till användningsdirektiv:
```
using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;

```
Instansiera sedan klienten:
```
var client = new EntitySearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
Använd klienten för att söka med en frågetext:
```
var entityData = client.Entities.Search(query: "Satya Nadella");

```
Parsa resultaten från den föregående frågan:
```
if (entityData?.Entities?.Value?.Count > 0)
{
    // find the entity that represents the dominant one
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();

    if (mainEntity != null)
    {
        Console.WriteLine("Searched for \"Satya Nadella\" and found a dominant entity with this description:");
        Console.WriteLine(mainEntity.Description);
    }
    else
    {
        Console.WriteLine("Couldn't find main entity Satya Nadella!");
    }
}
else
{
    Console.WriteLine("Didn't see any data..");
}

```

## <a name="complete-console-application"></a>Slutföra konsolprogram
Följande konsolprogram letar upp en enda entitet på frågan ”Satya Nadella” och skriver ut en kort beskrivning.
```
using System;
using System.Linq;
using System.Text;
using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
using Newtonsoft.Json;

namespace EntitySrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new EntitySearchAPI(new ApiKeyServiceClientCredentials("19aa718a79d6444daaa415981d9f54ad"));

            DominantEntityLookup(client);
            
            // Include the following methods to use queries defined under the headings following this example.
            //HandlingDisambiguation(client);
            //RestaurantLookup(client);
            //MultipleRestaurantLookup(client);
            //Error(client);

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

        public static void DominantEntityLookup(EntitySearchAPI client)
        {
            try
            {
                var entityData = client.Entities.Search(query: "Satya Nadella");

                if (entityData?.Entities?.Value?.Count > 0)
                {
                    // find the entity that represents the dominant one
                    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();

                    if (mainEntity != null)
                    {
                        Console.WriteLine("Searched for \"Satya Nadella\" and found a dominant entity with this description:");
                        Console.WriteLine(mainEntity.Description);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find main entity Satya Nadella!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any data..");
                }
            }
            catch (ErrorResponseException ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
    }
}

```

## <a name="ambiguous-results"></a>Tvetydiga resultat
Följande kod löser tvetydigheter i resultat för den tvetydiga frågan ”William Gates”.
```
       public static void HandlingDisambiguation(EntitySearchAPI client)
        {
            try
            {
                var entityData = client.Entities.Search(query: "william gates");

                if (entityData?.Entities?.Value?.Count > 0)
                {
                    // find the entity that represents the dominant one
                    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
                    var disambigEntities = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DisambiguationItem).ToList();

                    if (mainEntity != null)
                    {
                        Console.WriteLine("Searched for \"William Gates\" and found a dominant entity with type hint \"{0}\" with this description:", mainEntity.EntityPresentationInfo.EntityTypeDisplayHint);
                        Console.WriteLine(mainEntity.Description);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find a reliable dominant entity for William Gates!");
                    }

                    if (disambigEntities?.Count > 0)
                    {
                        Console.WriteLine();
                        Console.WriteLine("This query is pretty ambiguous and can be referring to multiple things. Did you mean one of these: ");

                        var sb = new StringBuilder();

                        foreach (var disambig in disambigEntities)
                        {
                            sb.AppendFormat(", or {0} the {1}", disambig.Name, disambig.EntityPresentationInfo.EntityTypeDisplayHint);
                        }

                        Console.WriteLine(sb.ToString().Substring(5) + "?");
                    }
                    else
                    {
                        Console.WriteLine("We didn't find any disambiguation items for William Gates, so we must be certain what you're talking about!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any data..");
                }
            }
            catch (ErrorResponseException ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```

## <a name="entitydata-places"></a>EntityData-platser
Följande kod söker efter den enskilda butiken ”Microsoft Store” och skriver ut dess telefonnummer.
```
        public static void StoreLookup(EntitySearchAPI client)
        {
            try
            {
                var entityData = client.Entities.Search(query: "microsoft store");

                if (entityData?.Places?.Value?.Count > 0)
                {
                    // Some local entities will be places, others won't be. Depending on the data you want, try to cast to the appropriate schema.
                    // In this case, the item being returned is technically a store, but the Place schema has the data we want (telephone)
                    var store = entityData.Places.Value.FirstOrDefault() as Place;

                    if (store != null)
                    {
                        Console.WriteLine("Searched for \"Microsoft Store\" and found a store with this phone number:");
                        Console.WriteLine(store.Telephone);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find a place!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any data..");
                }
            }
            catch (ErrorResponseException ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```
## <a name="entityscenario-list"></a>EntityScenario-lista
Följande kod söker efter en lista över ”Seattle restaurants” (restauranger i Seattle) och skriver ut deras namn och telefonnummer.
```
       public static void MultipleRestaurantLookup(EntitySearchAPI client)
        {
            try
            {
                var restaurants = client.Entities.Search(query: "seattle restaurants");

                if (restaurants?.Places?.Value?.Count > 0)
                {
                    // get all the list items that relate to this query
                    var listItems = restaurants.Places.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.ListItem).ToList();

                    if (listItems?.Count > 0)
                    {
                        var sb = new StringBuilder();

                        foreach (var item in listItems)
                        {
                            var place = item as Place;

                            if (place == null)
                            {
                                Console.WriteLine("Unexpectedly found something that isn't a place named \"{0}\"", item.Name);
                                continue;
                            }

                            sb.AppendFormat(",{0} ({1}) ", place.Name, place.Telephone);
                        }

                        Console.WriteLine("Ok, we found these places: ");
                        Console.WriteLine(sb.ToString().Substring(1));
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find any relevant results for \"seattle restaurants\"");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any data..");
                }
            }
            catch (ErrorResponseException ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```
## <a name="error-results"></a>Felresultat
Följande kod utlöser en felaktig begäran och visar hur du läser felsvaret.
```
        public static void Error(EntitySearchAPI client)
        {
            try
            {
                var entityData = client.Entities.Search(query: "satya nadella", market: "no-ty");
            }
            catch (ErrorResponseException ex)
            {
                // The status code of the error should be a good indication of what occurred. However, if you'd like more details, you can dig into the response.
                // Please note that depending on the type of error, the response schema might be different, so you aren't guaranteed a specific error response schema.
                Console.WriteLine("Exception occurred, status code {0} with reason {1}.", ex.Response.StatusCode, ex.Response.ReasonPhrase);

                // if you'd like more descriptive information (if available), attempt to parse the content as an ErrorResponse
                var issue = JsonConvert.DeserializeObject<ErrorResponse>(ex.Response.Content);

                if (issue != null && issue.Errors?.Count > 0)
                {
                    if (issue.Errors[0].SubCode == ErrorSubCode.ParameterInvalidValue)
                    {
                        Console.WriteLine("Turns out the issue is parameter \"{0}\" has an invalid value \"{1}\". Detailed message is \"{2}\"", issue.Errors[0].Parameter, issue.Errors[0].Value, issue.Errors[0].Message);
                    }
                }
            }
        }

```
## <a name="next-steps"></a>Nästa steg

[Exempel med Cognitive Services SDK för .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)

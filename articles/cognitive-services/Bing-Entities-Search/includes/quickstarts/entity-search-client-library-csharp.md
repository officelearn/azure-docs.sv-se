---
title: Snabbstart för Bing Entitetssökning C#-klientbibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 39a6c21ad056980e8c7b146e36a6e185cb3ed95e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79136783"
---
Använd den här snabbstarten för att börja söka efter entiteter med klientbiblioteket Bing Entity Search för C#. Bing Entity Search har ett REST API som är kompatibelt med de flesta programmeringsspråk, men klientbiblioteket är ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch).


## <a name="prerequisites"></a>Krav

* Alla utgåvor av [Visual Studio 2017 eller senare](https://www.visualstudio.com/downloads/).
* [Json.NET](https://www.newtonsoft.com/json) framework, tillgänglig som ett NuGet-paket.
* Om du använder Linux/Mac OS kan det här programmet köras med [Mono](https://www.mono-project.com/).
* [NuGet-paket för SDK för Nyhetssökning i Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). Installering av det här paketet installerar även följande:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Om du vill lägga till klientbiblioteket Bing Entity Search i Visual Studio-projektet använder `Microsoft.Azure.CognitiveServices.Search.EntitySearch` du alternativet Hantera **NuGet-paket** från Solution **Explorer**och lägger till paketet.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Skapa och initiera ett program

1. skapa en ny C#-konsollösning i Visual Studio. Lägg sedan till följande i huvudkodfilen.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Skapa en klient och skicka en sökbegäran

1. Skapa en ny sökklient. Lägg till din prenumerationsnyckel genom att skapa en ny `ApiKeyServiceClientCredentials`.

    ```csharp
    var client = new EntitySearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. Använd klientens `Entities.Search()`-funktion för att söka efter din fråga:
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>Hämta och skriva ut en entitetsbeskrivning

1. Om API:et returnerade sökresultat hämtar du huvudentiteten från `entityData`.

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. Skriva ut beskrivningen av huvudentiteten 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ensidig webbapp](../../tutorial-bing-entities-search-single-page-app.md)

* [Vad är API:et för sökning av Bing-entitet?](../../overview.md )
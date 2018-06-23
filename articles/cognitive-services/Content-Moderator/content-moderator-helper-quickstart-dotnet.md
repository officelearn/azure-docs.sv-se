---
title: Azure innehåll kontrollant SDK för .NET hjälpmetod | Microsoft Docs
description: Hur du återställer en innehåll kontrollant-klient som använder Azure innehåll kontrollant SDK för .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/04/2018
ms.author: sajagtap
ms.openlocfilehash: 36f2124708731f78f34849d8210ed39ea8f59140
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351468"
---
# <a name="helper-code-to-return-a-content-moderator-client"></a>Helper-kod för att returnera en innehåll kontrollant-klient

Den här artikeln innehåller information och kodexempel som hjälper dig att komma igång med innehåll kontrollant SDK för .NET för att skapa en innehåll kontrollant klient för din prenumeration.

Biblioteket används av andra Snabbstart i det här avsnittet.

Den här artikeln förutsätter att du redan är bekant med Visual Studio och C#.

> [!IMPORTANT]
> Den här Klassbiblioteket innehåller koden avsedd endast i demonstrationssyfte.
> Om du anpassa den här koden för användning i produktion ska du använda en säker metod för att lagra och med din prenumeration innehåll kontrollant-nyckel.

## <a name="sign-up-for-content-moderator-services"></a>Registrera dig för innehåll kontrollant services

Innan du kan använda innehåll kontrollant tjänster via REST API eller SDK behöver du en prenumeration för.
Referera till den [Quickstart](quick-start.md) att lära dig hur du kan hämta nyckeln.

## <a name="create-your-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Skapa en ny **Class Library (.NET Framework)** projekt.

   I exempelkoden är namnet I projektet **ModeratorHelper**.

1. Lägg till en referens till den **System.Configuration** Framework-sammansättning.

### <a name="install-required-packages"></a>Installera de paket som krävs

Installera följande NuGet-paket:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="create-the-content-moderator-client"></a>Skapa innehåll kontrollant-klient

Ersätt innehållet i filen ModeratorHelper.cs med följande kod:

    using Microsoft.CognitiveServices.ContentModerator;

    namespace ModeratorHelper
    {
    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "myRegion";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "myKey";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.BaseUrl = AzureBaseURL;
            return client;
        }
    }
    }


> [!IMPORTANT]
> Uppdatering av **AzureRegion** och **CMSubscriptionKey** fält med värdena i din region identifierare och prenumeration nyckel.

Nu har du ett snabbt sätt att skapa en innehåll kontrollant klient för din prenumeration.

## <a name="next-steps"></a>Nästa steg

[Hämta Visual Studio-lösningen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för denna och andra innehåll kontrollant Snabbstart för .NET, och komma igång med din integrering.

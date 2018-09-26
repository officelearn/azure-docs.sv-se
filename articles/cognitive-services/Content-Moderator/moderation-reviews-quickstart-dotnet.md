---
title: Azure Content Moderator – skapa granskningar med hjälp av .NET | Microsoft Docs
description: Så här skapar du granskar med Azure Content Moderator-SDK för .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/10/2018
ms.author: sajagtap
ms.openlocfilehash: c5f301e7ed15100c39f0af77942147275b966ed9
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47180781"
---
# <a name="create-reviews-using-net"></a>Skapa granskningar med hjälp av .NET

Den här artikeln innehåller information och kodexempel som hjälper dig att komma igång med den [Content Moderator-SDK för .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) till:
 
- Skapa en uppsättning granskningar för mänskliga moderatorer
- Hämta status för befintliga granskningar för mänskliga moderatorer

I allmänhet går innehåll igenom vissa automatiserad moderering före schemalagda för mänsklig granskning. Den här artikeln beskriver bara hur du skapar granskningen för mänskliga moderering. Ett scenario med fullständig finns den [Facebook innehållsmoderering](facebook-post-moderation.md) och [katalogmoderering](ecommerce-retail-catalog-moderation.md) självstudier.

Den här artikeln förutsätter att du redan är bekant med Visual Studio och C#.

## <a name="sign-up-for-content-moderator"></a>Registrera dig för Content Moderator

Innan du kan använda Content Moderator-tjänster via REST-API: et eller SDK: N, måste en prenumerationsnyckel.
Referera till den [snabbstarten](quick-start.md) att lära dig hur du kan hämta nyckeln.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>Registrera dig för ett konto för granskning-verktyget om inte slutförts i föregående steg

Om du har fått din Content Moderator från Azure-portalen, även [registrera granska verktyget konto](https://contentmoderator.cognitive.microsoft.com/) och skapa en granskningsteam. Du behöver Id-teamet och granskningsverktyget att anropa granska API för att starta ett jobb och visa granskningarna i granskningsverktyget.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Se till att din API-nyckel kan anropa API: et för granskning för att skapa en granskning

När du har slutfört föregående steg, kan det sluta med två Content Moderator-nycklar om du utgick från Azure-portalen. 

Om du planerar att använda Azure-tillhandahållna API-nyckeln i SDK-exempel, följer du stegen som beskrivs i den [med hjälp av Azure-nyckel med granska API: et](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api) avsnittet så att dina program kan anropa API: et för granskning och skapa granskningar.

Om du använder den kostnadsfria utvärderingsversionen nyckeln som genererats av granskningsverktyget granska verktyget kontot redan vet om nyckeln och därför inga ytterligare åtgärder krävs.

## <a name="create-your-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Lägga till en ny **konsolapp (.NET Framework)** projekt i lösningen.

   Namnge projektet i exempelkoden **CreateReviews**.

1. Välj det här projektet som enda Startprojekt för lösningen.

### <a name="install-required-packages"></a>Installera de paket som krävs

Installera följande NuGet-paket:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Uppdatera programmet använder uttryck

Ändra programmet är med hjälp av uttryck.

    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="create-the-content-moderator-client"></a>Skapa Content Moderator-klienten

Lägg till följande kod för att skapa en Content Moderator-klient för din prenumeration.

> [!IMPORTANT]
> Uppdatera den **AzureRegion** och **CMSubscriptionKey** fält med värdena för din region identifierare och prenumeration nyckel.


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
        private static readonly string AzureRegion = "YOUR API REGION";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"https://{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR API KEY";

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

            client.Endpoint = AzureBaseURL;
            return client;
        }
    }

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Skapa en klass för att associera interna innehållsinformation med en åtkomstgransknings-ID

Lägg till följande klass till den **programmet** klass.
Använd den här klassen för att associera åtkomstgransknings-ID till ditt interna innehålls-ID för objektet.

    /// <summary>
    /// Associates the review ID (assigned by the service) to the internal
    /// content ID of the item.
    /// </summary>
    public class ReviewItem
    {
        /// <summary>
        /// The media type for the item to review.
        /// </summary>
        public string Type;

        /// <summary>
        /// The URL of the item to review.
        /// </summary>
        public string Url;

        /// <summary>
        /// The internal content ID for the item to review.
        /// </summary>
        public string ContentId;

        /// <summary>
        /// The ID that the service assigned to the review.
        /// </summary>
        public string ReviewId;
    }

### <a name="initialize-application-specific-settings"></a>Initiera programspecifika inställningar

> [!NOTE]
> Din nyckel för Content Moderator-tjänsten har en begäranden per sekund (RPS) hastighetsbegränsning, och om du överskrider gränsen SDK: N genereras ett undantag med en 429 felkod. 
>
> En nyckel för kostnadsfria nivån har en hastighetsbegränsning för en RPS.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Lägg till följande konstanter i den **programmet** klassen i Program.cs.
    
    /// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Image List API.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const double latencyDelay = 45;

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Lägg till följande konstanter och statiska fält till den **programmet** klassen i Program.cs.

Uppdatera dessa värden så att den innehåller information som är specifika för din prenumeration och dina team.

> [!NOTE]
> Du ställer in TeamName konstanten till det namn som du använde när du skapade din [Content Moderator-granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) prenumeration. Du kan hämta TeamName från den **autentiseringsuppgifter** i avsnittet den **inställningar** (kugghjulet)-menyn.
>
> Ditt Teamnamn är värdet för den **Id** i den **API** avsnittet.

    /// <summary>
    /// The name of the team to assign the review to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Content Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "YOUR REVIEW TEAM ID";

    /// <summary>
    /// The optional name of the subteam to assign the review to.
    /// </summary>
    private const string Subteam = null;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Reviews show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "YOUR API ENDPOINT";

    /// <summary>
    /// The media type for the item to review.
    /// </summary>
    /// <remarks>Valid values are "image", "text", and "video".</remarks>
    private const string MediaType = "image";

    /// <summary>
    /// The URLs of the images to create review jobs for.
    /// </summary>
    private static readonly string[] ImageUrls = new string[] {
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg"
        // add more if you want
    };

    /// <summary>
    /// The metadata key to initially add to each review item.
    /// </summary>
    private const string MetadataKey = "sc";

    /// <summary>
    /// The metadata value to initially add to each review item.
    /// </summary>
    private const string MetadataValue = "true";

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Lägg till följande statiska fält i den **programmet** klassen i Program.cs.

Du kan använda de här fälten för att spåra programmets tillstånd.

    /// <summary>
    /// A static reference to the text writer to use for logging.
    /// </summary>
    private static TextWriter writer;

    /// <summary>
    /// The cached review information, associating a local content ID
    /// to the created review ID for each item.
    /// </summary>
    private static List<ReviewItem> reviewItems =
        new List<ReviewItem>();

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>Skapa en metod för att skriva meddelanden till loggfilen

Lägg till följande metod i klassen **Program**. 

    /// <summary>
    /// Writes a message to the log file, and optionally to the console.
    /// </summary>
    /// <param name="message">The message.</param>
    /// <param name="echo">if set to <c>true</c>, write the message to the console.</param>
    private static void WriteLine(string message = null, bool echo = false)
    {
        writer.WriteLine(message ?? String.Empty);

        if (echo)
        {
            Console.WriteLine(message ?? String.Empty);
        }
    }

## <a name="create-a-method-to-create-a-set-of-reviews"></a>Skapa en metod för att skapa en uppsättning granskningar

Normalt kan du har några affärslogik för att identifiera inkommande bilder, text, eller video som behöver granskas. Men här bara använda en fast lista med avbildningar.

Lägg till följande metod i klassen **Program**.

    /// <summary>
    /// Create the reviews using the fixed list of images.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void CreateReviews(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Creating reviews for the following images:", true);

        // Create the structure to hold the request body information.
        List<CreateReviewBodyItem> requestInfo =
            new List<CreateReviewBodyItem>();

        // Create some standard metadata to add to each item.
        List<CreateReviewBodyItemMetadataItem> metadata =
            new List<CreateReviewBodyItemMetadataItem>(
            new CreateReviewBodyItemMetadataItem[] {
                new CreateReviewBodyItemMetadataItem(
                    MetadataKey, MetadataValue)
            });

        // Populate the request body information and the initial cached review information.
        for (int i = 0; i < ImageUrls.Length; i++)
        {
            // Cache the local information with which to create the review.
            var itemInfo = new ReviewItem()
            {
                Type = MediaType,
                ContentId = i.ToString(),
                Url = ImageUrls[i],
                ReviewId = null
            };

            WriteLine($" - {itemInfo.Url}; with id = {itemInfo.ContentId}.", true);

            // Add the item informaton to the request information.
            requestInfo.Add(new CreateReviewBodyItem(
                itemInfo.Type, itemInfo.Url, itemInfo.ContentId,
                CallbackEndpoint, metadata));

            // Cache the review creation information.
            reviewItems.Add(itemInfo);
        }

        var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync(
            "application/json", TeamName, requestInfo);

        // Update the local cache to associate the created review IDs with
        // the associated content.
        var reviewIds = reviewResponse.Result.Body;
        for (int i = 0; i < reviewIds.Count; i++)
        {
            Program.reviewItems[i].ReviewId = reviewIds[i];
        }

        WriteLine(JsonConvert.SerializeObject(
        reviewIds, Formatting.Indented));

        Thread.Sleep(throttleRate);
    }

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>Skapa en metod för att hämta status för befintliga granskningar

Lägg till följande metod i klassen **Program**. 

> [!Note]
> I praktiken, anger du Webbadressen för återanrop `CallbackEndpoint` till den URL som ska få resultatet av manuell granskning (via en HTTP POST-begäran).
> Du kan ändra den här metoden för att kontrollera statusen för väntande granskningar.

    /// <summary>
    /// Gets the review details from the server.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void GetReviewDetails(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Getting review details:", true);
        foreach (var item in reviewItems)
        {
            var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(
                TeamName, item.ReviewId);

            WriteLine(
                $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                $"{reviewDetail.Result.Body.Status}.", true);
            WriteLine(JsonConvert.SerializeObject(
                reviewDetail.Result.Body, Formatting.Indented));

            Thread.Sleep(throttleRate);
        }
    }

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Lägg till kod för att skapa en uppsättning granskningar och kontrollera status

Lägg till följande kod till den **Main** metod.

Den här koden simulerar många av de åtgärder som du utför i definiera och hantera listan över samt med hjälp av listan för att skärmbilder. Loggningsfunktionerna kan du se svarsobjekt som genereras av SDK-anrop till tjänsten Content Moderator.

    using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
    {
        writer = outputWriter;
        using (var client = Clients.NewClient())
        {
            CreateReviews(client);
            GetReviewDetails(client);

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propigate.");
            Thread.Sleep(latencyDelay * 1000);

            GetReviewDetails(client);
        }

        writer = null;
        outputWriter.Flush();
        outputWriter.Close();
    }

    Console.WriteLine();
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();

## <a name="run-the-program-and-review-the-output"></a>Kör programmet och granska resultatet

Du kan se följande exempel på utdata:

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.

Logga in på Content Moderator-granskningsverktyget om du vill se den väntande avbildningen granska med den **sc** etiketten inställd **SANT**. Du kan också se standard **en** och **r** taggar och eventuella anpassade taggar som du har definierat i granskningsverktyget. 

Använd den **nästa** knapp för att skicka.

![Bild granskning för mänskliga moderatorer](images/moderation-reviews-quickstart-dotnet.PNG)

Tryck på valfri tangent för att fortsätta.

    Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...

## <a name="check-out-the-following-output-in-the-log-file"></a>Kolla in följande resultat i loggfilen.

> [!NOTE]
> I utdatafilen, strängarna ”\{teamname}” och ”\{callbackUrl}” värden för den `TeamName` och `CallbackEndpoint` respektive fält.

Granska ID: N och image innehåll URL: er är olika varje gång du kör programmet, och när en granskning är slutfört kan den `reviewerResultTags` fältet visar hur granskaren taggade objektet.

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.
    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Pending",
        "reviewerResultTags": [],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="your-callback-url-if-provided-receives-this-response"></a>Motringnings-Url om som tar emot det här svaret

Du ser ett svar som i följande exempel:

    {
        "ReviewId": "201801i48a2937e679a41c7966e838c92f5e649",
        "ModifiedOn": "2018-01-06T05:04:40.5525865Z",
        "ModifiedBy": "yourusername",
        "CallBackType": "Review",
        "ContentId": "0",
        "ContentType": "Image",
        "Metadata": {
            "sc": "true"
            },
        "ReviewerResultTags": {
            "a": "False",
            "r": "False",
        }
    }


## <a name="next-steps"></a>Nästa steg

Hämta den [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) och [Visual Studio-lösning](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för denna och andra Content Moderator-Snabbstart för .NET, och kom igång med din integrering.

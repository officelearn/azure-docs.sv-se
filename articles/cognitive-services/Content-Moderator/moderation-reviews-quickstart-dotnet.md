---
title: Azure innehåll kontrollant - skapa granskningar med hjälp av .NET | Microsoft Docs
description: Så här skapar du granskar med Azure innehåll kontrollant SDK för .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/04/2018
ms.author: sajagtap
ms.openlocfilehash: 6a0ff48f4ea17f9c800f3e6c096df2492699f87a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351840"
---
# <a name="create-reviews-using-net"></a>Skapa granskningar med hjälp av .NET

Den här artikeln innehåller information och kodexempel som hjälper dig att komma igång med innehåll kontrollant SDK för .NET för att:
 
- Skapa en uppsättning omdömen för mänsklig moderatorer
- Hämta status för befintliga omdömen för mänsklig moderatorer

I allmänhet passerar innehåll vissa automatiserade avbrottsmoderering före schemalagda för mänsklig granskning. Den här artikeln beskriver bara hur du skapar granskningen för mänsklig måtta. Ett scenario med fullständig finns i [Facebook innehåll avbrottsmoderering](facebook-post-moderation.md) och [e-handel katalogen avbrottsmoderering](ecommerce-retail-catalog-moderation.md) självstudier.

Den här artikeln förutsätter att du redan är bekant med Visual Studio och C#.

## <a name="sign-up-for-content-moderator-services"></a>Registrera dig för innehåll kontrollant services

Innan du kan använda innehåll kontrollant tjänster via REST API eller SDK behöver du en prenumeration för.
Referera till den [Quickstart](quick-start.md) att lära dig hur du kan hämta nyckeln.

## <a name="create-your-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Lägg till en ny **konsolapp (.NET Framework)** projekt i lösningen.

   Namnge projektet i koden, **CreateReviews**.

1. Välj det här projektet som Startprojekt enda för lösningen.

1. Lägg till en referens till den **ModeratorHelper** projektet sammansättningen som du skapade i den [innehåll kontrollant klienten helper quickstart](content-moderator-helper-quickstart-dotnet.md).

### <a name="install-required-packages"></a>Installera de paket som krävs

Installera följande NuGet-paket:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Uppdatera programmet använder instruktioner

Ändra programmet använder instruktioner.

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Skapa en klass för att associera interna innehållsinformation med ett ID för granskning

Lägg till följande klassen för att den **programmet** klass.
Använd den här klassen för att associera granska-ID till ditt interna innehålls-ID för objektet.

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
> Nyckeln för tjänsten för ditt innehåll kontrollant har en begäranden per hastighetsbegränsning för andra (RPS) och om du överskrider gränsen SDK utlöser ett undantag med en 429 felkod. 
>
> En kostnadsfri nivå-nyckel har en gräns för överföringshastigheten en RPS.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Lägg till följande konstanter till den **programmet** klassen i Program.cs.
    
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
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Lägg till följande konstanter och statiska fält till den **programmet** klassen i Program.cs.

Uppdatera dessa värden så att den innehåller information som är specifika för din prenumeration och team.

> [!NOTE]
> Du ställer in TeamName konstanten till namn som du använde när du skapade din [innehåll kontrollant granska verktyget](https://contentmoderator.cognitive.microsoft.com/) prenumeration. Du kan hämta TeamName från den **autentiseringsuppgifter** i avsnittet den **inställningar** (kugghjulet)-menyn.
>
> Teamnamnet på din är värdet för den **Id** i den **API** avsnitt.

    /// <summary>
    /// The name of the team to assign the review to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Conent Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "{teamname}";

    /// <summary>
    /// The optional name of the subteam to assign the review to.
    /// </summary>
    private const string Subteam = null;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Revies show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "{callbackUrl}";

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

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Lägg till följande statiska fält till den **programmet** klassen i Program.cs.

Du kan använda dessa fält för att spåra programmets tillstånd.

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

Normalt sett du vissa affärslogiken för att identifiera inkommande bilder, text, eller video som måste granskas. Men här bara använda en fast lista med bilder.

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

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>Skapa en metod för att hämta status för befintliga omdömen

Lägg till följande metod i klassen **Program**. 

> [!Note]
> I praktiken ska ställas in URL: en motringning `CallbackEndpoint` till den URL som kan få resultaten av manuell granskning (via en HTTP POST-begäran).
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

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Lägg till kod för att skapa en uppsättning omdömen och kontrollera statusen för

Lägg till följande kod i den **Main** metod.

Den här koden simulerar många av de åtgärder som du utför i definiera och hantera listan samt med hjälp av listan till skärmen avbildningar. Loggningsfunktioner kan du se svar-objekt som genererats av SDK-anrop till tjänsten innehåll kontrollanten.

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

## <a name="run-the-program-and-review-the-output"></a>Kör programmet och granska utdata

Följande exempel på utdata visas:

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.

Logga in på innehåll kontrollanten granska verktyget om du vill se väntande bilden granska med den **sc** etikett inställd på **SANT**. Du också se standard **en** och **r** taggar och eventuella anpassade taggar som du har definierat i Granska-verktyget. 

Använd den **nästa** knapp för att skicka.

![Granska bild för mänsklig moderatorer](images/moderation-reviews-quickstart-dotnet.PNG)

Tryck på valfri tangent för att fortsätta.

    Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...

## <a name="check-out-the-following-output-in-the-log-file"></a>Gå igenom följande utdata i loggfilen.

> [!NOTE]
> I utdatafilen, strängarna ”\{teamname}” och ”\{callbackUrl}” återspeglar värdena för den `TeamName` och `CallbackEndpoint` respektive fält.

Granska ID: N och image innehåll URL skiljer sig åt varje gång du kör programmet och när en granskning är slutfört kan den `reviewerResultTags` fältet visar hur granskaren märkta objektet.

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

## <a name="your-callback-url-if-provided-receives-this-response"></a>Motringning URL: en tar emot svaret om

Ett svar som i följande exempel visas:

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

[Hämta Visual Studio-lösningen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för denna och andra innehåll kontrollant Snabbstart för .NET, och komma igång med din integrering.

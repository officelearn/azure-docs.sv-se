---
title: Azure innehåll kontrollant - skapa video betyg granskningar med hjälp av .NET | Microsoft Docs
description: Så här skapar du video betyg granskar med Azure innehåll kontrollant SDK för .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/19/2018
ms.author: sajagtap
ms.openlocfilehash: 3286da6e38f0fba02386d877a835fb694ed0fdec
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352449"
---
# <a name="create-video-transcript-reviews-using-net"></a>Skapa video betyg granskningar med hjälp av .NET

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med innehåll kontrollant SDK med C# för att:

- Skapa en video granskning för mänsklig moderatorer
- Lägg till en kontrollerad betyg i granskningen
- Publicera granskningen

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har [kontrollerad videon](video-moderation-api.md) och [skapade video granska](video-reviews-quickstart-dotnet.md) i verktyget granska för mänsklig beslutsfattande. Nu vill du lägga till kontrollerad video betyg i verktyget granska.

Den här artikeln förutsätter också att du redan är bekant med Visual Studio och C#.

### <a name="sign-up-for-content-moderator-services"></a>Registrera dig för innehåll kontrollant services

Innan du kan använda innehåll kontrollant tjänster via REST API eller SDK behöver du en prenumeration för.

I instrumentpanelen för innehåll kontrollant du hittar din prenumeration nyckel i **inställningar** > **autentiseringsuppgifter** > **API**  >   **Utvärderingsversion Ocp-Apim-prenumeration-nyckeln**. Mer information finns i [översikt](overview.md).

### <a name="prepare-your-video-for-review"></a>Förbereda din video för granskning

Lägg till betyg i en video granskning. Videon måste publiceras online. Du behöver den strömmande slutpunkten. Den strömmande slutpunkten kan granska verktyget video player spelas upp.

![Videodemonstration miniatyr](images/ams-video-demo-view.PNG)

- Kopiera den **URL** på den här [Azure Media Services demonstrera](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) sidan för manifestet URL.

## <a name="create-your-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Lägg till en ny **konsolapp (.NET Framework)** projekt i lösningen.

1. Namnge projektet **VideoTranscriptReviews**.

1. Välj det här projektet som Startprojekt enda för lösningen.

### <a name="install-required-packages"></a>Installera de paket som krävs

Installera följande NuGet-paketen för TermLists-projektet.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Uppdatera programmet använder instruktioner

Ändra programmet använder enligt följande instruktioner.

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;
    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;


### <a name="add-private-properties"></a>Lägga till egenskaper

Lägg till följande privata egenskaper till namnområdet VideoTranscriptReviews, klass Program.

Där anges, Ersätt exempelvärden för dessa egenskaper.


    namespace VideoReviews
    {
        class Program
        {
            // NOTE: Replace this example location with the location for your Content Moderator account.
            /// <summary>
            /// The region/location for your Content Moderator account, 
            /// for example, westus.
            /// </summary>
            private static readonly string AzureRegion = "YOUR CONTENT MODERATOR REGION";

            // NOTE: Replace this example key with a valid subscription key.
            /// <summary>
            /// Your Content Moderator subscription key.
            /// </summary>
            private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

            // NOTE: Replace this example team name with your Content Moderator team name.
            /// <summary>
            /// The name of the team to assign the job to.
            /// </summary>
            /// <remarks>This must be the team name you used to create your 
            /// Content Moderator account. You can retrieve your team name from
            /// the Conent Moderator web site. Your team name is the Id associated 
            /// with your subscription.</remarks>
            public static readonly string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

            /// <summary>
            /// The base URL fragment for Content Moderator calls.
            /// </summary>
            private static readonly string AzureBaseURL =
                $"{AzureRegion}.api.cognitive.microsoft.com";

            /// <summary>
            /// The minimum amount of time, in milliseconds, to wait between calls
            /// to the Content Moderator APIs.
            /// </summary>
            private const int throttleRate = 2000;


### <a name="create-content-moderator-client-object"></a>Skapa innehåll kontrollant klienten objekt

Lägg till följande metoddefinition i namnområdet VideoTranscriptReviews, klass Program.

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
        {
            BaseUrl = AzureBaseURL
        };
    }

## <a name="create-a-video-review"></a>Skapa en video granska

Skapa en video granska med **ContentModeratorClient.Reviews.CreateVideoReviews**. Mer information finns i [API-referens för](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** har följande obligatoriska parametrar:
1. En sträng som innehåller en MIME-typ ska vara ”application/json”. 
1. Ditt innehåll kontrollant Teamnamn.
1. En **IList<CreateVideoReviewsBodyItem>**  objekt. Varje **CreateVideoReviewsBodyItem** -objektet representerar en video granskning. Denna Snabbstart skapar en granskning i taget.

**CreateVideoReviewsBodyItem** har flera egenskaper. Åtminstone, kan du ange följande egenskaper:
- **Innehåll**. URL till videon som ska granskas.
- **ContentId**. Ett ID som ska tilldelas till video granskningen.
- **Status för**. Ange värdet till ”Opublicerat”. Om du inte anger den standard som ”väntande”, vilket innebär att videon granska publiceras och mänsklig granskas. När en video granskning har publicerats kan lägga du inte längre bildrutor, ett betyg eller ett betyg avbrottsmoderering resultat till den.

> [!NOTE]
> **CreateVideoReviews** returnerar en IList<string>. Var och en av de här strängarna innehåller ett ID för en video granskning. Dessa ID: N är ett GUID och inte är detsamma som värdet för den **ContentId** egenskapen. 

Lägg till följande metoddefinition i namnområdet VideoReviews, klass Program.

    /// <summary>
    /// Create a video review. For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="id">The ID to assign to the video review.</param>
    /// <param name="content">The URL of the video to review.</param>
    /// <returns>The ID of the video review.</returns>
    private static string CreateReview(ContentModeratorClient client, string id, string content)
    {
        Console.WriteLine("Creating a video review.");

        List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
            new CreateVideoReviewsBodyItem
            {
                Content = content,
                ContentId = id,
                /* Note: to create a published review, set the Status to "Pending".
                However, you cannot add video frames or a transcript to a published review. */
                Status = "Unpublished",
            }
        };

        var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

        Thread.Sleep(throttleRate);

        // We created only one review.
        return result[0];
    }

> [!NOTE]
> Nyckeln för tjänsten för ditt innehåll kontrollant har en begäranden per hastighetsbegränsning för andra (RPS). Om du överskrider gränsen som utlöser ett undantag med 429 felkoden SDK. 
>
> En kostnadsfri nivå-nyckel har en gräns för överföringshastigheten en RPS.

## <a name="add-transcript-to-video-review"></a>Lägg till betyg i video granskning

Du lägger till ett betyg i en video granskning med **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** har följande obligatoriska parametrar:
1. Ditt innehåll kontrollant team-ID.
1. Granska video-ID som returneras av **CreateVideoReviews**.
1. En **dataströmmen** objekt som innehåller betyg.

Betyg måste vara i formatet WebVTT. Mer information finns i [WebVTT: The Web Video spårar textformat](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Ett exempel betyg används i formatet VTT. I en verklig lösning kan du använda tjänsten Azure Media Indexer för att [generera ett betyg](https://docs.microsoft.com/azure/media-services/media-services-index-content) från en video.

Lägg till följande metoddefinition i namnområdet VideotranscriptReviews, klass Program.

    /// <summary>
    /// Add a transcript to the indicated video review.
    /// The transcript must be in the WebVTT format.
    /// For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="transcript">The video transcript.</param>
    static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
    {
        Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
        client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
        Thread.Sleep(throttleRate);
    }

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Lägga till ett betyg avbrottsmoderering resultat video granskning

Förutom att lägga till ett betyg video se du också lägga till resultatet av att kontrollera att betyg. Det gör du med **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Mer information finns i [API-referens för](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** har följande obligatoriska parametrar:
1. En sträng som innehåller en MIME-typ ska vara ”application/json”. 
1. Ditt innehåll kontrollant Teamnamn.
1. Granska video-ID som returneras av **CreateVideoReviews**.
1. En IList<TranscriptModerationBodyItem>. En **TranscriptModerationBodyItem** har följande egenskaper:
- **Villkoren**. En IList<TranscriptModerationBodyItemTermsItem>. En **TranscriptModerationBodyItemTermsItem** har följande egenskaper:
- **Indexet**. Det nollbaserade indexet har löpt ut.
- **Termen**. En sträng som innehåller termen.
- **Tidsstämpel**. En sträng som innehåller, i sekunder, tid i betyg som villkoren påträffas.

Betyg måste vara i formatet WebVTT. Mer information finns i [WebVTT: The Web Video spårar textformat](https://www.w3.org/TR/webvtt1/).

Lägg till följande metoddefinition i namnområdet VideoTranscriptReviews, klass Program. Den här metoden skickar ett betyg till den **ContentModeratorClient.TextModeration.ScreenText** metod. Översätter också resultatet i en IList<TranscriptModerationBodyItem>, och skickar till **AddVideoTranscriptModerationResult**.

    /// <summary>
    /// Add the results of moderating a video transcript to the indicated video review.
    /// For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="transcript">The video transcript.</param>
    static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
    {
        Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

        // Screen the transcript using the Text Moderation API. For more information, see:
        // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
        Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

        // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
        List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
        if (null != screen.Terms)
        {
            foreach (var term in screen.Terms)
            {
                if (term.Index.HasValue)
                {
                    terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
                }
            }
        }

        List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
        {
            new TranscriptModerationBodyItem()
            {
                Timestamp = "0",
                Terms = terms
            }
        };

        client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

        Thread.Sleep(throttleRate);
    }


## <a name="publish-video-review"></a>Publicera video granskning

Du publicerar en video granskning med **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** har följande obligatoriska parametrar:
1. Ditt innehåll kontrollant Teamnamn.
1. Granska video-ID som returneras av **CreateVideoReviews**.

Lägg till följande metoddefinition i namnområdet VideoReviews, klass Program.

    /// <summary>
    /// Publish the indicated video review. For more information, see the reference API:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    private static void PublishReview(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Publishing the review with ID {0}.", review_id);
        client.Reviews.PublishVideoReview(TeamName, review_id);
        Thread.Sleep(throttleRate);
    }

## <a name="putting-it-all-together"></a>Alltihop

Lägg till den **Main** metoddefinition till namnområdet VideoTranscriptReviews, klassen Program. Stäng programmet klassen och VideoTranscriptReviews namnområde.

> [!NOTE]
> Ett exempel betyg används i formatet VTT. I en verklig lösning kan du använda tjänsten Azure Media Indexer för att [generera ett betyg](https://docs.microsoft.com/azure/media-services/media-services-index-content) från en video. 

    static void Main(string[] args)
    {
        using (ContentModeratorClient client = NewClient())
        {
            // Create a review with the content pointing to a streaming endpoint (manifest)
            var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
            string review_id = CreateReview(client, "review1", streamingcontent);

            var transcript = @"WEBVTT

            01:01.000 --> 02:02.000
            First line with a crap word in a transcript.

            02:03.000 --> 02:25.000
            This is another line in the transcript.
            ";

            AddTranscript(client, review_id, transcript);

            AddTranscriptModerationResult(client, review_id, transcript);

            // Publish the review
            PublishReview(client, review_id);

            Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
            Console.WriteLine("Press any key to close the application.");
            Console.Read();
        }
    }

## <a name="run-the-program-and-review-the-output"></a>Kör programmet och granska utdata

När du kör programmet, se utdata på följande rader:

    Creating a video review.
    Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="navigate-to-your-video-transcript-review"></a>Navigera till din video betyg granskning

Gå till video betyg granska i ditt innehåll kontrollant granska verktyget på den **granska**>**Video**>**betyg** skärmen.

Du ser följande funktioner:
- Betyg som du har lagt till två linjer
- Termen svordomar hittats och markerats av tjänsten text avbrottsmoderering
- Markera ett skrivfel texten börjar videon från tidsstämpeln

![Granska video betyg för mänsklig moderatorer](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar [video granskar](video-reviews-quickstart-dotnet.md) i verktyget granska.

Kolla in detaljerad vägledning om hur du utvecklar en [slutföra video avbrottsmoderering lösning](video-transcript-moderation-review-tutorial-dotnet.md).

[Hämta Visual Studio-lösningen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för denna och andra innehåll kontrollant Snabbstart för .NET.

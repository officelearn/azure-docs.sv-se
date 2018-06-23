---
title: Azure innehåll kontrollant - skapa video granskningar med hjälp av .NET | Microsoft Docs
description: Så här skapar du video granskar med Azure innehåll kontrollant SDK för .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/18/2018
ms.author: sajagtap
ms.openlocfilehash: cb487314b8695f3676fdb22a9d7e3ec5ca3ed9f2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351876"
---
# <a name="create-video-reviews-using-net"></a>Skapa video granskningar med hjälp av .NET

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med innehåll kontrollant SDK med C# för att:

- Skapa en video granskning för mänsklig moderatorer
- Lägg till ramar i ett omdöme
- Hämta ramarna för granskning 
- Hämta status och information för granska
- Publicera granskningen

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har [kontrollerad videon (se quickstart)](video-moderation-api.md) och ha svarsdata. Du behöver den för att skapa ram-baserade omdömen för mänsklig kontrollanter.

Den här artikeln förutsätter också att du redan är bekant med Visual Studio och C#.

### <a name="sign-up-for-content-moderator-services"></a>Registrera dig för innehåll kontrollant services

Innan du kan använda innehåll kontrollant tjänster via REST API eller SDK behöver du en prenumeration för.

I instrumentpanelen för innehåll kontrollant du din prenumeration nyckel i **inställningar** > **autentiseringsuppgifter** > **API**  >  **Utvärderingsversion Ocp-Apim-prenumeration-nyckeln**. Mer information finns i [översikt](overview.md).

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Förbereda din video och bildrutorna för granskning

Video- och exempel bildrutor att granska måste publiceras online eftersom du måste ha sina URL: er.

> [!NOTE]
> Manuellt sparade skärmdumpar från videon används med slumpmässiga vuxen dyr resultat som illustrerar hur granska API. I en verklig situation kan du använda den [video avbrottsmoderering utdata](video-moderation-api.md#run-the-program-and-review-the-output) kan skapa avbildningar och tilldela resultat. 

För video behöver du en strömmande slutpunkten så att granska verktyget spelas upp videon i vyn player.

![Videodemonstration miniatyr](images/ams-video-demo-view.PNG)

- Kopiera den **URL** på den här [Azure Media Services demonstrera](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) sidan för manifestet URL.

Använd följande bilder för bildrutor (avbildningar):

![Video miniatyrbilden 1](images/ams-video-frame-thumbnails-1.PNG) | ![Video miniatyrbilden 2](images/ams-video-frame-thumbnails-2.PNG) | ![Video miniatyrbilden 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Ram 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Ram 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Ramen 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Lägg till en ny **konsolapp (.NET Framework)** projekt i lösningen.

1. Namnge projektet **VideoReviews**.

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

Lägg till följande privata egenskaper till namnområdet VideoReviews, klass Program.

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

Lägg till följande metoddefinition i namnområdet VideoReviews, klass Program.

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
> Nyckeln för tjänsten för ditt innehåll kontrollant har en begäranden per hastighetsbegränsning för andra (RPS) och om du överskrider gränsen SDK utlöser ett undantag med en 429 felkod. 
>
> En kostnadsfri nivå-nyckel har en gräns för överföringshastigheten en RPS.

## <a name="add-video-frames-to-the-video-review"></a>Lägg till bildrutor video granskning

Du lägger till bildrutor video Se med **ContentModeratorClient.Reviews.AddVideoFrameUrl** (om din bildrutor finns online) eller **ContentModeratorClient.Reviews.AddVideoFrameStream** () Om din bildrutor finns lokalt). Denna Snabbstart förutsätter din bildrutor finns online och därför använder **AddVideoFrameUrl**. Mer information finns i [API-referens för](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** har följande obligatoriska parametrar:
1. En sträng som innehåller en MIME-typ ska vara ”application/json”.
1. Ditt innehåll kontrollant Teamnamn.
1. Granska video-ID som returneras av **CreateVideoReviews**.
1. En **IList<VideoFrameBodyItem>**  objekt. Varje **VideoFrameBodyItem** -objektet representerar en bildruta.

**VideoFrameBodyItem** har följande egenskaper:
- **Tidsstämpel**. En sträng som innehåller, i sekunder, tid i videon som video ram togs.
- **FrameImage**. URL till video ramen.
- **Metadata**. En IList<VideoFrameBodyItemMetadataItem>. **VideoFrameBodyItemMetadataItem** är bara ett nyckel/värde-par. Giltiga nycklarna är:
- **reviewRecommended**. TRUE om en mänsklig granskning av video RAM rekommenderas.
- **adultScore**. Ett värde mellan 0 och 1 som bedömer allvarlighetsgraden innehåll för vuxna i video ram.
- **en**. TRUE om videon innehåller innehåll för vuxna.
- **racyScore**. Ett värde mellan 0 och 1 som bedömer allvarlighetsgraden dyr innehåll i video ram.
- **r**. TRUE om videon bildruta innehåller dyr innehåll.
- **ReviewerResultTags**. En IList<VideoFrameBodyItemReviewerResultTagsItem>. **VideoFrameBodyItemReviewerResultTagsItem** är bara ett nyckel/värde-par. Ett program kan använda dessa taggar för att organisera bildrutor.

> [!NOTE]
> Denna Snabbstart genererar slumpmässiga värden för den **adultScore** och **racyScore** egenskaper. I ett produktionsprogram skulle du få värdena från den [video avbrottsmoderering service](video-moderation-api.md), distribuerad som en tjänst för Azure Media.

Lägg till följande metoddefinitioner i namnområdet VideoReviews, klass Program.

    <summary>
    /// Create a video frame to add to a video review after the video review is created.
    /// </summary>
    /// <param name="url">The URL of the video frame image.</param>
    /// <returns>The video frame.</returns>
    private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
    {
        // We generate random "adult" and "racy" scores for the video frame.
        Random rand = new Random();

        var frame = new VideoFrameBodyItem
        {
            // The timestamp is measured in milliseconds. Convert from seconds.
            Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
            FrameImage = url,

            Metadata = new List<VideoFrameBodyItemMetadataItem>
            {
                new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
                new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
                new VideoFrameBodyItemMetadataItem("a", "false"),
                new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
                new VideoFrameBodyItemMetadataItem("r", "false")
            },

            ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
            {
                new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
            }
        };

        return frame;
    }

    /// <summary>
    /// Add a video frame to the indicated video review. For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="url">The URL of the video frame image.</param>
    static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
    {
        Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

        var frames = new List<VideoFrameBodyItem>()
        {
            CreateFrameToAddToReview(url, timestamp_seconds)
        };
            
        client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

        Thread.Sleep(throttleRate);
    

## <a name="get-video-frames-for-video-review"></a>Hämta bildrutor för video granskning

Du kan hämta bildrutorna för en video granskning med **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** har följande obligatoriska parametrar:
1. Ditt innehåll kontrollant Teamnamn.
1. Granska video-ID som returneras av **CreateVideoReviews**.
1. Det nollbaserade indexet för den första video bildrutan få.
1. Antal bildrutor att hämta.

Lägg till följande metoddefinition i namnområdet VideoReviews, klass Program.

    /// <summary>
    /// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    static void GetFrames(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

        Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0, Int32.MaxValue);
        Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

        Thread.Sleep(throttleRate);
    }

## <a name="get-video-review-information"></a>Hämta video granska information

Du får en video granska med **ContentModeratorClient.Reviews.GetReview**. **GetReview** har följande obligatoriska parametrar:
1. Ditt innehåll kontrollant Teamnamn.
1. Granska video-ID som returneras av **CreateVideoReviews**.

Lägg till följande metoddefinition i namnområdet VideoReviews, klass Program.

    /// <summary>
    /// Get the information for the indicated video review. For more information, see the reference API:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    private static void GetReview(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

        var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
        Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

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

Lägg till den **Main** metoddefinition till namnområdet VideoReviews, klassen Program. Stäng programmet klassen och VideoReviews namnområde.

    static void Main(string[] args)
    {
        using (ContentModeratorClient client = NewClient())
        {
            // Create a review with the content pointing to a streaming endpoint (manifest)
            var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
            string review_id = CreateReview(client, "review1", streamingcontent);

            var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
            var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
            var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

            // Add the frames from 17, 64, and 144 seconds.
            AddFrame(client, review_id, frame1_url, "17");
            AddFrame(client, review_id, frame2_url, "64");
            AddFrame(client, review_id, frame3_url, "144");

            // Get frames information and show
            GetFrames(client, review_id);
            GetReview(client, review_id);

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
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    {
        "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
        "VideoFrames": [
        {
            "Timestamp": "17000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.808312381528463"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.846378884206702"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
        ]
        },
        {
            "Timestamp": "64000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.576078300166912"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.244768953064815"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
        ]
        },
        {
            "Timestamp": "144000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.664480847150311"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.933817870418456"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
            ]
        }
        ]
    }
    
    Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    {
        "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
        "SubTeam": "public",
        "Status": "UnPublished",
        "ReviewerResultTags": [],
        "CreatedBy": "testreview6",
        "Metadata": [
        {
            "Key": "FrameCount",
            "Value": "3"
        }
        ],
        "Type": "Video",
        "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        "ContentId": "review1",
        "CallbackEndpoint": null
    }

    Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="check-out-your-video-review"></a>Kolla in din video granskning

Slutligen visas video granska din innehåll kontrollant granska verktyget konto på den **granska**>**Video** skärmen.

![Video granskningen för mänsklig moderatorer](images/ams-video-review.PNG)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du lägger till [betyg avbrottsmoderering](video-transcript-moderation-review-tutorial-dotnet.md) till video granskningen. 

Kolla in detaljerad vägledning om hur du utvecklar en [slutföra video avbrottsmoderering lösning](video-transcript-moderation-review-tutorial-dotnet.md).

[Hämta Visual Studio-lösningen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för denna och andra innehåll kontrollant Snabbstart för .NET.

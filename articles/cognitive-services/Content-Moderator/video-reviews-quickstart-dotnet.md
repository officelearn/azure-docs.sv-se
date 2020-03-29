---
title: Skapa videorecensioner med .NET - Content Moderator
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med content moderator SDK med C# för att skapa videorecensioner.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 7130ed43183d64b00f8f5ef1697b9a3b456ad396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72931681"
---
# <a name="create-video-reviews-using-net"></a>Skapa videorecensioner med .NET

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med [content moderator SDK med C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) för att:

- Skapa en videorecension för mänskliga moderatorer
- Lägga till ramar i en recension
- Hämta ramarna för granskningen
- Få status och information om granskningen
- Publicera recensionen

## <a name="prerequisites"></a>Krav

- Logga in eller skapa ett konto på webbplatsen för [granskning](https://contentmoderator.cognitive.microsoft.com/) av innehållsmoderator.
- Den här artikeln förutsätter att du har [modererat videon (se snabbstart)](video-moderation-api.md) och har svarsdata. Du behöver det för att skapa ram-baserade recensioner för mänskliga moderatorer.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Se till att din API-nyckel kan anropa gransknings-API:et för att skapa en granskning

När du har slutfört föregående steg kan det hända att du har två Content Moderator-nycklar om du startade från Azure-portalen.

Om du planerar att använda den Azure-tillhandahållna API-nyckeln i ditt SDK-exempel följer du de steg som beskrivs i avsnittet om att [använda Azure-nyckel med API:et för granskning](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) så att ditt program kan anropa API:et för granskning och skapa granskningar.

Om du använder den nyckeln för den kostnadsfria utvärderingsversionen som genererades av granskningsverktyget känner ditt granskningsverktygskonto redan till nyckeln, och därför krävs inga ytterligare steg.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Förbered videon och videobildrutorna för granskning

Video- och exempelvideobildrutor som ska granskas måste publiceras online eftersom du behöver deras webbadresser.

> [!NOTE]
> Programmet använder manuellt sparade skärmdumpar från videon med slumpmässiga vuxna / racy poäng för att illustrera användningen av översynen API. I en verklig situation använder du [videomoderationsutdata](video-moderation-api.md#run-the-program-and-review-the-output) för att skapa bilder och tilldela poäng. 

För videon behöver du en slutpunkt för direktuppspelning så att granskningsverktyget spelar upp videon i spelarvyn.

![Miniatyrbild av videodemo](images/ams-video-demo-view.PNG)

- Kopiera **URL:en** på den här [demosidan](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) för Azure Media Services för manifest-URL:en.

För videobildrutor (bilder) använder du följande bilder:

![Miniatyrbild av videoram 1](images/ams-video-frame-thumbnails-1.PNG) | ![Miniatyrbild av videoram 2](images/ams-video-frame-thumbnails-2.PNG) | ![Miniatyrbild av videoram 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Ram 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Ram 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Ram 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Lägg till ett nytt projekt för en **konsolapp (.NET Framework)** i lösningen.

1. Namnge projektet **VideoReviews**.

1. Välj det här projektet som det enda startprojektet för lösningen.

### <a name="install-required-packages"></a>Installera de paket som krävs

Installera följande NuGet-paket för TermLists-projektet.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Uppdatera programmets using-instruktioner

Ändra programmets med hjälp av satser enligt följande.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Lägga till privata egenskaper

Lägg till följande privata egenskaper i namnområdet **VideoReviews**, klass **Program**. Uppdatera `AzureEndpoint` fälten och `CMSubscriptionKey` med värdena för slutpunkts-URL:en och prenumerationsnyckeln. Du hittar dessa på fliken **Snabbstart** för din resurs i Azure-portalen.


```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
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
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Klientobjekt för innehållsmodererator

Lägg till följande metoddefinition i namnområde **VideoReviews**, klass **Program**.

```csharp
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
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>Skapa en videogranskning

Skapa en videorecension med **ContentModeratorClient.Reviews.CreateVideoReviews**. Mer information finns i [API-referensen](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** har följande parametrar:
1. En sträng som innehåller en MIME-typ, som ska vara "application/json". 
1. Ditt innehållsmoderatorsteamnamn.
1. Ett **\<IList CreateVideoReviewsBodyItem>** objekt. Varje **CreateVideoReviewsBodyItem-objekt** representerar en videogranskning. Den här snabbstarten skapar en granskning i taget.

**CreateVideoReviewsBodyItem** har flera egenskaper. Du kan åtminstone ange följande egenskaper:
- **Innehåll**. Webbadressen till videon som ska granskas.
- **ContentId**. Ett ID som ska tilldelas videogranskningen.
- **Status**. Ange värdet till "Opublicerat". Om du inte ställer in den, är det standard "Väntande", vilket innebär att videogranskningen publiceras och väntar på mänsklig granskning. När en videogranskning har publicerats kan du inte längre lägga till videoramar, en utskrift eller ett resultat för utskriftsmoderering i den.

> [!NOTE]
> **CreateVideoReviews returnerar** \<en IList-sträng>. Var och en av dessa strängar innehåller ett ID för en videogranskning. Dessa ID:n är GUID:er och är **ContentId** inte samma som värdet för contentid-egenskapen. 

Lägg till följande metoddefinition i namnområde VideoReviews, klass Program.

```csharp
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
```

> [!NOTE]
> Content Moderator-tjänstnyckeln har en gräns för antal begäranden per sekund (RPS). Om du överskrider gränsen genererar SDK:t ett undantag med en 429-felkod.
>
> En nyckel på den kostnadsfria nivån har en gräns på en RPS.

## <a name="add-video-frames-to-the-video-review"></a>Lägga till videobildrutor i videogranskningen

Du lägger till videoramar i en videogranskning med **ContentModeratorClient.Reviews.AddVideoFrameUrl** (om dina videoramar finns online) eller **ContentModeratorClient.Reviews.AddVideoFrameStream** (om dina videoramar finns lokalt). Den här snabbstarten förutsätter att dina videoramar finns online och använder därför **AddVideoFrameUrl**. Mer information finns i [API-referensen](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** har följande parametrar:
1. En sträng som innehåller en MIME-typ, som ska vara "application/json".
1. Ditt innehållsmoderatorsteamnamn.
1. Videogransknings-ID som returneras av **CreateVideoReviews**.
1. Ett **IList\<VideoFrameBodyItem->** objekt. Varje **VideoFrameBodyItem-objekt** representerar en videobildruta.

**VideoFrameBodyItem** har följande egenskaper:
- **Tidsstämpel**. En sträng som i sekunder innehåller tiden i videon som videoramen togs från.
- **FrameImage**. Url:en för videoramen.
- **Metadata**. En IList\<VideoFrameBodyItemMetadataItem>. **VideoFrameBodyItemMetadataItem** är helt enkelt ett nyckel/värde-par. Giltiga nycklar inkluderar:
- **reviewRecom ändras**. Sant om en mänsklig granskning av videoramen rekommenderas.
- **vuxenScore**. Ett värde från 0 till 1 som betygsätter allvarlighetsgraden för barnförbjudet innehåll i videoramen.
- **a**. Sant om videon innehåller barnförbjudet innehåll.
- **racyScore**. Ett värde från 0 till 1 som betygsätter allvarlighetsgraden för racy-innehåll i videoramen.
- **r**. Sant om videoramen innehåller racy innehåll.
- **GranskareResultTags**. En IList\<VideoFrameBodyItemReviewerResultTagsItem>. **VideoFrameBodyItemReviewerResultTagsItem** är helt enkelt en nyckel / värde par. Ett program kan använda dessa taggar för att ordna videoramar.

> [!NOTE]
> Den här snabbstarten genererar slumpmässiga värden för **egenskaperna adultScore** och **racyScore.** I ett produktionsprogram hämtar du dessa värden från [videomoderationstjänsten](video-moderation-api.md)som distribueras som en Azure Media Service.

Lägg till följande metoddefinitioner i namnområde VideoReviews, klass Program.

```csharp
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
```

```csharp
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
```

## <a name="get-video-frames-for-video-review"></a>Skaffa videoramar för videogranskning

Du kan hämta videobildrutorna för en videogranskning med **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** har följande parametrar:
1. Ditt innehållsmoderatorsteamnamn.
1. Videogransknings-ID som returneras av **CreateVideoReviews**.
1. Det nollbaserade indexet för den första videoramen att få.
1. Antalet videobildrutor att hämta.

Lägg till följande metoddefinition i namnområde VideoReviews, klass Program.

```csharp
/// <summary>
/// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
static void GetFrames(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

    Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="get-video-review-information"></a>Få information om videogranskning

Du får information för en video recension med **ContentModeratorClient.Reviews.GetReview**. **GetReview** har följande parametrar:
1. Ditt innehållsmoderatorsteamnamn.
1. Videogransknings-ID som returneras av **CreateVideoReviews**.

Lägg till följande metoddefinition i namnområde VideoReviews, klass Program.

```csharp
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
```

## <a name="publish-video-review"></a>Publicera videogranskning

Du publicerar en videorecension med **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** har följande parametrar:
1. Ditt innehållsmoderatorsteamnamn.
1. Videogransknings-ID som returneras av **CreateVideoReviews**.

Lägg till följande metoddefinition i namnområde VideoReviews, klass Program.

```csharp
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
```

## <a name="putting-it-all-together"></a>Färdigställa allt

Lägg **Main** till huvudmetoddefinitionen i namnområde VideoReviews, klassProgram. Stäng slutligen klassen Program och namnområdet VideoReviews.

```csharp
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
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>Kör programmet och granska resultatet
När du kör programmet visas en utdata på följande rader:

```json
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
```

## <a name="check-out-your-video-review"></a>Kolla in din videorecension

Slutligen visas videogranskningen i granskningsverktyget för innehållsmoderator på skärmen **Granska**>**video.**

![Videorecension för mänskliga moderatorer](images/ams-video-review.PNG)

## <a name="next-steps"></a>Nästa steg

Hämta [snabbstarten innehållsmoderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) och [Visual Studio-lösningen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för snabbstarter för .NET.

Läs om hur du lägger till [avskriftsmodering](video-transcript-moderation-review-tutorial-dotnet.md) i videogranskningen. 

Kolla in den detaljerade handledning om hur man utvecklar en [komplett video moderering lösning](video-transcript-moderation-review-tutorial-dotnet.md).

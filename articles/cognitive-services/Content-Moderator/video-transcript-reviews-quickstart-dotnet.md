---
title: Skapa recensioner av videoavskrifter med .NET - Content Moderator
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar videoavskriftgranskningar med hjälp av Azure Cognitive Services Content Moderator SDK för .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: b2d763454b86570b57a16fb9ae2107a2a2bcd23d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73744387"
---
# <a name="create-video-transcript-reviews-using-net"></a>Skapa recensioner för videoavskrifter med .NET

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med [content moderator SDK med C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) för att:

- Skapa en videorecension för mänskliga moderatorer
- Lägga till en modererad utskrift i granskningen
- Publicera recensionen

## <a name="prerequisites"></a>Krav

- Logga in eller skapa ett konto på webbplatsen för [granskning](https://contentmoderator.cognitive.microsoft.com/) av innehållsmoderator om du inte redan har gjort det.
- Den här artikeln förutsätter att du har [modererat videon](video-moderation-api.md) och [skapat videogranskningen](video-reviews-quickstart-dotnet.md) i granskningsverktyget för mänskligt beslutsfattande. Nu vill du lägga till modererade videoavskrifter i granskningsverktyget.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Se till att din API-nyckel kan anropa gransknings-API:et (Jobbskapande)

När du har slutfört föregående steg kan det hända att du har två Content Moderator-nycklar om du startade från Azure-portalen.

Om du planerar att använda den Azure-tillhandahållna API-nyckeln i ditt SDK-exempel följer du de steg som beskrivs i avsnittet om att [använda Azure-nyckel med API:et för granskning](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) så att ditt program kan anropa API:et för granskning och skapa granskningar.

Om du använder den nyckeln för den kostnadsfria utvärderingsversionen som genererades av granskningsverktyget känner ditt granskningsverktygskonto redan till nyckeln, och därför krävs inga ytterligare steg.

## <a name="prepare-your-video-for-review"></a>Förbered videon för granskning

Lägg till avskriften i en videogranskning. Videon måste publiceras online. Du behöver dess slutpunkt för direktuppspelning. Den strömmande slutpunkten gör att granskningsverktyget videospelare för att spela upp videon.

![Miniatyrbild av videodemo](images/ams-video-demo-view.PNG)

- Kopiera **URL:en** på den här [demosidan](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) för Azure Media Services för manifest-URL:en.

## <a name="create-your-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Lägg till ett nytt projekt för en **konsolapp (.NET Framework)** i lösningen.

1. Namnge projektet **VideoTranscriptReviews**.

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

Lägg till följande privata egenskaper i namnområdet **VideoTranscriptReviews**, klass **Program**. Uppdatera `AzureEndpoint` fälten och `CMSubscriptionKey` med värdena för slutpunkts-URL:en och prenumerationsnyckeln. Du hittar dessa på fliken **Snabbstart** för din resurs i Azure-portalen.

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

Lägg till följande metoddefinition i namnområdet VideoTranscriptReviews, klass Program.

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
> Din tjänstnyckel för Content Moderator har en hastighetsgräns för begäranden per sekund (RPS). Om du överskrider gränsen utlöser SDK:n ett undantag med felkoden 429.
>
> En nyckel på den kostnadsfria nivån har en gräns på en RPS.

## <a name="add-transcript-to-video-review"></a>Lägga till avskrift i videogranskning

Du lägger till en utskrift i en videogranskning med **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** har följande parametrar som krävs:
1. Ditt team-ID för innehållsmoderator.
1. Videogransknings-ID som returneras av **CreateVideoReviews**.
1. Ett **Stream-objekt** som innehåller avskriften.

Avskriften måste vara i WebVTT-format. Mer information finns i [WebVTT: Web Video Text Tracks Format](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Programmet använder ett exempel avskrift i VTT-format. I en verklig lösning använder du Azure Media Indexer-tjänsten för att [generera en avskrift](https://docs.microsoft.com/azure/media-services/media-services-index-content) från en video.

Lägg till följande metoddefinition i namnområdet VideotranscriptReviews, klass Program.

```csharp
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
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Lägga till ett resultat för avskriftsmodering i videogranskning

Förutom att lägga till en utskrift i en videogranskning lägger du också till resultatet av att du modererar utskriften. Du gör det med **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Mer information finns i [API-referensen](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** har följande obligatoriska parametrar:
1. En sträng som innehåller en MIME-typ, som ska vara "application/json". 
1. Ditt innehållsmoderatorsteamnamn.
1. Videogransknings-ID som returneras av **CreateVideoReviews**.
1. En IList\<TranskriptAtionBodyItem>. En **TranscriptModerationBodyItem** har följande egenskaper:
1. **Villkor**. En IList\<TranscriptModerationBodyItemTermsItem>. En **TranscriptModerationBodyItemTermsItem** har följande egenskaper:
1. **Index**. Termens nollbaserade index.
1. **Term**. En sträng som innehåller termen.
1. **Tidsstämpel**. En sträng som i sekunder innehåller tiden i avskriften då termerna hittas.

Avskriften måste vara i WebVTT-format. Mer information finns i [WebVTT: Web Video Text Tracks Format](https://www.w3.org/TR/webvtt1/).

Lägg till följande metoddefinition i namnområdet VideoTranscriptReviews, klass Program. Den här metoden skickar en avskrift till metoden **ContentModeratorClient.TextModeration.ScreenText.** Det översätter också resultatet till\<en IList TranscriptModerationBodyItem>, och skickar till **AddVideoTranscriptModerationResult**.

```csharp
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

Lägg **Main** till huvudmetoddefinitionen i namnområdet VideoTranscriptReviews, klass Program. Stäng slutligen klassen Program och videotranscriptReviews namnområde.

> [!NOTE]
> Programmet använder ett exempel avskrift i VTT-format. I en verklig lösning använder du Azure Media Indexer-tjänsten för att [generera en avskrift](https://docs.microsoft.com/azure/media-services/media-services-index-content) från en video.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

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

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Navigera till din videoavskrift recension

Gå till videoavskriftsgranskningen i granskningsverktyget för innehållsmoderatorer på skärmen **Granska**>**videoavskrift.** **Video**>

Följande funktioner visas:
- De två rader utskrift du lagt till
- Svordomar termen hittades och markeras av texten moderering tjänst
- Om du väljer en transkriptionstext startas videon från den tidsstämpeln

![Video avskrift recension för mänskliga moderatorer](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Nästa steg

Hämta [snabbstarten innehållsmoderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) och [Visual Studio-lösningen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för snabbstarter för .NET.

Läs om hur du skapar [videorecensioner](video-reviews-quickstart-dotnet.md) i granskningsverktyget.

Kolla in den detaljerade handledning om hur man utvecklar en [komplett video moderering lösning](video-transcript-moderation-review-tutorial-dotnet.md).

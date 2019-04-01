---
title: Skapa videoavskriften granskningar med hjälp av .NET - Content Moderator
titlesuffix: Azure Cognitive Services
description: Skapa videoavskriften granskningar med Content Moderator-SDK för .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/19/2019
ms.author: sajagtap
ms.openlocfilehash: 56cd608d337d817b849a0902569e9aeddeca80ab
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758581"
---
# <a name="create-video-transcript-reviews-using-net"></a>Skapa videoavskriften granskningar med hjälp av .NET

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med den [Content Moderator-SDK med C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) till:

- Skapa en video granskning för mänskliga moderatorer
- Lägg till en kontrollerad avskrift till granskningen
- Publicera granskningen

## <a name="prerequisites"></a>Förutsättningar

- Logga in eller skapa ett konto på Content Moderator [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) om du inte gjort det redan.
- Den här artikeln förutsätter att du har [modereras videon](video-moderation-api.md) och [skapade video granskningen](video-reviews-quickstart-dotnet.md) i granskningsverktyg för mänskliga beslutsfattande. Du nu vill lägga till kontrollerad video betyg i granskningsverktyget.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Se till att din API-nyckel kan anropa API för granskning (jobbskapande)

När du har slutfört föregående steg kan det hända att du har två Content Moderator-nycklar om du startade från Azure-portalen.

Om du planerar att använda den Azure-tillhandahållna API-nyckeln i ditt SDK-exempel följer du de steg som beskrivs i avsnittet om att [använda Azure-nyckel med API:et för granskning](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) så att ditt program kan anropa API:et för granskning och skapa granskningar.

Om du använder den nyckeln för den kostnadsfria utvärderingsversionen som genererades av granskningsverktyget känner ditt granskningsverktygskonto redan till nyckeln, och därför krävs inga ytterligare steg.

## <a name="prepare-your-video-for-review"></a>Förbereda din video för granskning

Lägg till avskriften i en video granskning. Videon måste publiceras online. Behöver du dess slutpunkt för direktuppspelning. Slutpunkten för direktuppspelning kan granska verktyget videospelaren spela upp videon.

![Videodemonstration miniatyr](images/ams-video-demo-view.PNG)

- Kopiera den **URL** på den här [Azure Media Services demonstrera](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) för manifest-URL: en.

## <a name="create-your-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Lägg till ett nytt projekt för en **konsolapp (.NET Framework)** i lösningen.

1. Ge projektet namnet **VideoTranscriptReviews**.

1. Välj det här projektet som det enda startprojektet för lösningen.

### <a name="install-required-packages"></a>Installera de paket som krävs

Installera följande NuGet-paket för TermLists-projektet.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Uppdatera programmets using-instruktioner

Ändra programmet använder enligt följande instruktioner.


```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Lägga till privata egenskaper

Lägg till följande privata egenskaper till namnområdet VideoTranscriptReviews, klassen Program.

Där det anges, Ersätt exempelvärden för dessa egenskaper.

```csharp
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
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

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
```

### <a name="create-content-moderator-client-object"></a>Skapa klientobjekt för Content Moderator

Lägg till följande metoddefinitionen i namnområdet VideoTranscriptReviews, klassen Program.

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
        Endpoint = AzureBaseURL
    };
}
```

## <a name="create-a-video-review"></a>Skapa en granskning av video

Skapa en video granskning med **ContentModeratorClient.Reviews.CreateVideoReviews**. Mer information finns i [API-referensen](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** har följande obligatoriska parametrar:
1. En sträng som innehåller en mimetyp som ska vara ”application/json”. 
1. Din Content Moderator-Teamnamn.
1. En **IList<CreateVideoReviewsBodyItem>**  objekt. Varje **CreateVideoReviewsBodyItem** -objektet representerar en video granskning. Den här snabbstarten skapar en granskning i taget.

**CreateVideoReviewsBodyItem** har flera egenskaper. Som ett minimum kan du ange följande egenskaper:
- **Innehåll**. URL till videoklippet som ska granskas.
- **ContentId**. Ett ID som ska tilldelas till video granskningen.
- **Status för**. Ange värdet till ”Opublicerat”. Om du inte anger den standard som ”väntande”, vilket innebär att video granskningen har publicerats och väntar på mänsklig granskning. När en granskning av videon har publicerats kan du inte längre lägga till bildrutor, en avskrift eller en avskrift moderering resultatet till den.

> [!NOTE]
> **CreateVideoReviews** returnerar en IList<string>. Var och en av de här strängarna innehåller ett ID för en video granskning. Dessa ID: N är ett GUID och inte är samma som värde för den **ContentId** egenskapen.

Lägg till följande metoddefinitionen i namnområdet VideoReviews, klassen Program.

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

## <a name="add-transcript-to-video-review"></a>Lägg till avskrift till video granska

Du lägger till en avskrift i en video granskning med **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** har följande obligatoriska parametrar:
1. Content Moderator-team-ID.
1. Granska video-ID som returneras av **CreateVideoReviews**.
1. En **Stream** objekt som innehåller avskriften.

Avskriften måste vara i formatet WebVTT. Mer information finns i [WebVTT: Web Video texten spårar Format](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> En exempel-avskrift används i VTT-format. I en verklig-lösning kan du använda Azure Media Indexer-tjänst som [Generera en avskrift](https://docs.microsoft.com/azure/media-services/media-services-index-content) från en video.

Lägg till följande metoddefinitionen i namnområdet VideotranscriptReviews, klassen Program.

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

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Lägg till en avskrift moderering resultatet till video granska

Förutom att lägga till en avskrift till en video granska du också lägga till resultatet av att kontrollera den avskriften. Det gör du med **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Mer information finns i [API-referensen](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** har följande obligatoriska parametrar:
1. En sträng som innehåller en mimetyp som ska vara ”application/json”. 
1. Din Content Moderator-Teamnamn.
1. Granska video-ID som returneras av **CreateVideoReviews**.
1. En IList<TranscriptModerationBodyItem>. En **TranscriptModerationBodyItem** har följande egenskaper:
1. **Allmänna**. En IList<TranscriptModerationBodyItemTermsItem>. En **TranscriptModerationBodyItemTermsItem** har följande egenskaper:
1. **Index**. Det nollbaserade indexet har löpt ut.
1. **Termen**. En sträng som innehåller termen.
1. **Tidsstämpel**. En sträng som innehåller, i sekunder, tiden i avskriften som villkoren påträffas.

Avskriften måste vara i formatet WebVTT. Mer information finns i [WebVTT: Web Video texten spårar Format](https://www.w3.org/TR/webvtt1/).

Lägg till följande metoddefinitionen i namnområdet VideoTranscriptReviews, klassen Program. Den här metoden skickar en avskrift för att den **ContentModeratorClient.TextModeration.ScreenText** metod. Översätter också resultatet i en IList<TranscriptModerationBodyItem>, och skickar till **AddVideoTranscriptModerationResult**.

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

## <a name="publish-video-review"></a>Publicera video granskning

Du publicerar en video granskning med **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** har följande obligatoriska parametrar:
1. Din Content Moderator-Teamnamn.
1. Granska video-ID som returneras av **CreateVideoReviews**.

Lägg till följande metoddefinitionen i namnområdet VideoReviews, klassen Program.

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

Lägg till den **Main** metoddefinitionen namnområde VideoTranscriptReviews, klassen Program. Stäng i programklassen och VideoTranscriptReviews-namnområdet.

> [!NOTE]
> En exempel-avskrift används i VTT-format. I en verklig-lösning kan du använda Azure Media Indexer-tjänst som [Generera en avskrift](https://docs.microsoft.com/azure/media-services/media-services-index-content) från en video.

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
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>Kör programmet och granska resultatet

När du kör programmet kan se du utdata som på följande rader:

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Gå till din videoavskriften granskning

Gå till videoavskriften granskningen i Content Moderator-granskningsverktyget på den **granska**>**Video**>**avskrift** skärmen.

Du ser följande funktioner:
- De två raderna i avskriften som du har lagt till
- Termen svordomar hittats och markerats av tjänsten för moderering av text
- Markera en avskrift text startar videon från den tidsstämpeln

![Videoavskriften granskning för mänskliga moderatorer](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Nästa steg

Hämta den [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) och [Visual Studio-lösning](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för denna och andra Content Moderator-Snabbstart för .NET.

Lär dig hur du skapar [videon går igenom](video-reviews-quickstart-dotnet.md) i granskningsverktyget.

Kolla in detaljerad genomgång om hur du utvecklar en [slutföra videomoderering lösning](video-transcript-moderation-review-tutorial-dotnet.md).

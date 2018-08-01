---
title: Ladda upp och indexera dina videor med Azure Video Indexer | Microsoft Docs
description: 'Det här avsnittet visar hur du använder API: er för att ladda upp och indexera dina videor med Azure Video Indexer'
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: c4a755d0c13516ce3cb0177cea2ea17e4a3abcbb
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390977"
---
# <a name="upload-and-index-your-videos"></a>Ladda upp och indexera dina videor  

Den här artikeln visar hur du använder den [ladda upp video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API för att ladda upp och indexera dina videor med Azure Video Indexer. Artikeln diskuteras även några av parametrarna att du kan ange för API: et ändra processen och utdata för API: et.

> [!Note]
> När du skapar en Video Indexer-konto, kan du välja ett kostnadsfritt konto (där du får ett visst antal kostnadsfria indexering minuter) eller ett betalt alternativ (där du inte begränsas av kvoten). <br/>Video Indexer ger upp till 600 minuter för kostnadsfria indexering för webbplatsen användare och upp till 2 400 minuters kostnadsfria indexering för API-användare med kostnadsfri utvärderingsversion. <br/>Med betald alternativet kan du skapa en Video Indexer-konto som är [är ansluten till din Azure-prenumeration och ett Azure Media Services-konto](connect-to-azure.md). Du betalar för minuter som indexeras som Media-konto som är relaterade kostnader. 

## <a name="configurations-and-params"></a>Konfigurationer och parametrar

Det här avsnittet beskrivs några av de valfria parametrarna och när du vill ställa in dem.

### <a name="externalid"></a>externalID 

Den här parametern kan du ange ett ID som ska associeras med en video. ID: T kan tillämpas på externa systemintegrering för ”Video innehållshantering” (VCM). Videor som finns i Video Indexer-portalen kan sökas med den angivna externt ID.

### <a name="indexingpreset"></a>indexingPreset

Använd den här parametern om raw eller externa inspelningar innehåller bakgrundsljud. Den här parametern används för att konfigurera indexeringsprocessen. Du kan ange följande värden:

- `Default` – Indexera och extrahera insikter med hjälp av både ljud och video
- `AudioOnly` – Indexera och extrahera insikter med hjälp av ljud endast (ignorera video)
- `DefaultWithNoiseReduction` – Indexera och lyfta ut kunskaper från både ljud och video, samtidigt som du använder bruset minskning algoritmer på ljudström

Priset beror på det valda alternativet för indexering.  

### <a name="callbackurl"></a>callbackUrl

En POST-URL för att meddela när indexering har slutförts. Video Indexer lägger till två fråga strängparametrar till den: id och tillstånd. Om webbadressen för återanrop är till exempel ”https://test.com/notifyme?projectName=MyProject', meddelandet ska skickas med ytterligare parametrar i'https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed'.

Du kan också lägga till fler parametrar i URL: en innan du publicerar anropet till Video Indexer och dessa parametrar kommer att inkluderas i motringningen. Senare i koden kan du parsa frågesträngen och få tillbaka alla angivna parametrar i frågesträngen (data som du ursprungligen hade läggas till URL: en plus informationen som Video Indexer som tillhandahålls.) 

### <a name="streamingpereset"></a>streamingPereset

När din video har laddats upp, kodar Video Indexer kan du kan också videon. Fortsätter sedan att indexering och analysera videon. När Video Indexer är klar analysera, kommer du få ett meddelande med video-ID.  

När du använder den [ladda upp video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) eller [indexera Video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API, en av följande valfria parametrar är `streamingPreset`. Om du ställer in `streamingPereset` till `Default`, `SingleBitrate`, eller `AdaptiveBitrate`, kodning processen utlöses. När indexering och kodningsjobb är klar har videon publicerats så att du kan också strömma videon. Slutpunkt för direktuppspelning som du vill att strömma videon måste finnas i den **kör** tillstånd.

För att kunna köra indexering och kodningsjobb den [Azure Media Services-konto som är anslutna till ditt konto för Video Indexer](connect-to-azure.md), kräver reserverade enheter. Mer information finns i [skala bearbetning av Media](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Eftersom dessa är beräkningsintensiva Beräkningsjobb rekommenderas starkt S3 enhetstyp. Antalet mediereserverade enheter definierar det maximala antalet jobb som kan köras parallellt. Baslinje-rekommendationen är 10 mediereserverade S3-enheter. 

Om du bara vill indexerar videon men inte koda den `streamingPereset`till `NoStreaming`.

## <a name="code-sample"></a>Kodexempel

I följande C#-kodavsnitt visar hur du använder alla Video Indexer API: er tillsammans.

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =newbyte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(newByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video id from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Debug.WriteLine("");
        Debug.WriteLine("State:");
        Debug.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Debug.WriteLine("");
            Debug.WriteLine("Full JSON:");
            Debug.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```



## <a name="next-steps"></a>Nästa steg

[Granska Azure Video Indexer-utdata som genereras av v2 API](video-indexer-output-json-v2.md)

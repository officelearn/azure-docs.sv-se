---
title: Ladda upp och indexera videor med Video Indexer
titlesuffix: Azure Media Services
description: Det här avsnittet visar hur du använder API:er för att ladda upp och indexera videor med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 12/10/2018
ms.author: juliako
ms.openlocfilehash: f29adb500401c9f5d6e177a0740ce54719c36a34
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53253212"
---
# <a name="upload-and-index-your-videos"></a>Ladda upp och indexera dina videor  

När du laddar upp videor med Video Indexer API, har du två alternativ för uppladdning: 

* ladda upp videon från en URL (rekommenderas),
* skicka videofilen som en bytematris i begärandetexten,
* Använd befintliga Azure Media Services-tillgångar genom att tillhandahålla [tillgångs-id](https://docs.microsoft.com/azure/media-services/latest/assets-concept) (stöds i endast betalda konton).

Artikeln visar hur du använder [Ladda upp video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)-API:t för att ladda upp och indexera videor baserat på en URL. Kodexemplet i artikeln innehåller den kommenterade koden som visar hur du laddar upp bytematrisen. <br/>Artikeln beskriver också några av de parametrar du kan ange i API:t för att ändra API:ts process och utdata.

När din video har laddats upp, kodar Video Indexer kan du kan också videon (beskrivs i artikeln). När du skapar ett Video Indexer-konto kan du välja ett kostnadsfritt utvärderingskonto (där du får ett visst antal kostnadsfria indexeringsminuter) eller ett betalalternativ (där du inte begränsas av kvoten). Med den kostnadsfria utvärderingen ger Video Indexer upp till 600 minuter kostnadsfri indexering för webbplatsanvändare och upp till 2 400 minuter kostnadsfri indexering för API-användare. Med betalalternativet skapar du ett Video Indexer-konto som är [anslutet till din Azure-prenumeration och ett Azure Media Services-konto](connect-to-azure.md). Du betalar för minuter som indexeras samt kostnader relaterade till mediekontot. 

## <a name="uploading-considerations"></a>Att tänka på gällande uppladdning
    
- När du laddar upp videon baserat på URL:en (rekommenderas) måste slutpunkten skyddas med TLS 1.2 (eller senare)
- Överföringsstorlek med URL: er är begränsad till 10GB
- Överföringsstorlek med alternativet byte-matrisen är begränsat till 2GB 
- Alternativet byte-matris på grund av timeout efter 30 min
- URL:en som anges i parametern `videoURL` måste kodas

> [!Tip]
> Det rekommenderas att du använder .NET Framework version 4.6.2 eller senare eftersom äldre .NET Framework-versioner inte använder TLS 1.2 som standard.
>
> Om du måste använda äldre .NET Framework lägger du till en rad i koden innan du gör REST API-anropet:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="configurations-and-params"></a>Konfigurationer och parametrar

I det här avsnittet beskrivs några av de valfria parametrarna och när du kan ange dem.

### <a name="externalid"></a>externalID 

Med den här parametern kan du ange ett ID som ska associeras med videon. ID:t kan tillämpas på extern VCM-systemintegrering (Video Content Management). Det går att söka efter de videor som finns på Video Indexer-portalen med det angivna externa ID:t.

### <a name="callbackurl"></a>callbackUrl

En URL som används för att meddela kunder (med en POST-begäran) om följande händelser:

- Indexering av tillståndsändring: 
    - Egenskaper:    
    
        |Namn|Beskrivning|
        |---|---|
        |id|Video-ID:n|
        |state|Videotillståndet|  
    - Exempel: https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- Person som identifierades i videon:
    - Egenskaper
    
        |Namn|Beskrivning|
        |---|---|
        |id| Video-ID:n|
        |faceId|Ansikts-ID som visas i videoindexet|
        |knownPersonId|Person-ID som är unikt inom en ansikts-modell|
        |personName|Namnet på personen|
        
     - Exempel: https://test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

#### <a name="notes"></a>Anteckningar

- Video Indexer returnerar alla befintliga parametrar som anges i den ursprungliga webbadressen.
- Den tillhandahållna webbadressen måste vara kodad.

### <a name="indexingpreset"></a>indexingPreset

Använd den här parametern om RAW-inspelningar eller externa inspelningar innehåller bakgrundsljud. Den här parametern används för att konfigurera indexeringsprocessen. Du kan ange följande värden:

- `Default` – Indexera och extrahera insikter med hjälp av både ljud och video
- `AudioOnly` – Indexera och extrahera insikter med hjälp av endast ljud (video ignoreras)
- `DefaultWithNoiseReduction` – Indexera och extrahera insikter från både ljud och video, samtidigt som algoritmer för brusreducering tillämpas på ljudströmmen

Priset beror på det valda indexeringsalternativet.  

### <a name="priority"></a>prioritet

Videor indexeras av Video Indexer enligt deras prioritet. Använd parametern **prioritet** för att ange prioritet för indexet. Följande värden är giltiga: **Låg**, **Normal** (standard), och **hög**.

**Prioritet**-parametern stöds endast för betalda konton.

### <a name="streamingpreset"></a>streamingPreset

När videon har laddats upp kan Video Indexer koda videon. Sedan fortsätter den med indexering och analys av videon. När Video Indexer är klar med analysen får du ett meddelande med video-ID:t.  

När du använder API:t [Ladda upp video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) eller [Indexera om video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) är en av de valfria parametrarna `streamingPreset`. Om du ställer in `streamingPreset` på `Default`, `SingleBitrate` eller `AdaptiveBitrate` utlöses kodningsprocessen. När indexerings- och kodningsjobben är klara publiceras videon så att du även kan strömma videon. Slutpunkten för direktuppspelning som du vill strömma videon från måste ha tillståndet **Körs**.

För att kunna köra indexerings- och kodningsjobben kräver [Azure Media Services-kontot som är anslutet till ditt Video Indexer-konto](connect-to-azure.md) reserverade enheter. Mer information finns i [Skala mediebearbetning](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Eftersom det är beräkningsintensiva jobb rekommenderas enhetstypen S3 starkt. Antalet RU:er definierar det maximala antalet jobb som kan köras parallellt. Baslinjerekommendationen är 10 S3-RU:er. 

Om du bara vill indexera videon men inte koda den ställer du in `streamingPreset` på `NoStreaming`.

### <a name="videourl"></a>videoUrl

En URL till video-/ljudfilen som ska indexeras. URL:en måste peka på en mediefil (HTML-sidor stöds inte). Filen kan skyddas av en åtkomsttoken som tillhandahålls som en del av URI:n och slutpunkten som hanterar filen måste skyddas med TLS 1.2 eller senare. URL:en måste vara kodad. 

Om `videoUrl` inte anges förväntar sig Video Indexer att du skickar filen som multipart/form-brödtextinnehåll.

## <a name="code-sample"></a>Kodexempel

Följande C#-kodavsnitt visar hur du använder alla Video Indexer-API:er tillsammans.

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
## <a name="common-errors"></a>Vanliga fel

De statuskoder som visas i följande tabell kan returneras av uppladdingsåtgärden.

|Statuskod|ErrorType (i svarstexten)|Beskrivning|
|---|---|---|
|400|VIDEO_ALREADY_IN_PROGRESS|Samma video håller redan på att bearbetas i det angivna kontot.|
|400|VIDEO_ALREADY_FAILED|Samma video misslyckades med att bearbetas i det angivna kontot för mindre än 2 timmar sedan. API-klienter ska vänta minst 2 timmar innan en video laddas upp på nytt.|

## <a name="next-steps"></a>Nästa steg

[Granska Azure Video Indexer-utdata som genereras av v2-API](video-indexer-output-json-v2.md)

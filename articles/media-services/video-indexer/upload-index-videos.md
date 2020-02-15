---
title: Ladda upp och indexera videor med Video Indexer
titleSuffix: Azure Media Services
description: Det här avsnittet visar hur du använder API:er för att ladda upp och indexera videor med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/13/2020
ms.author: juliako
ms.openlocfilehash: b0a4f390a3a897d14adc2944195b0c51148de495
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209281"
---
# <a name="upload-and-index-your-videos"></a>Ladda upp och indexera dina videor  

När du laddar upp videor med Video Indexer API har du följande uppladdnings alternativ: 

* ladda upp videon från en URL (rekommenderas),
* skicka videofilen som en bytematris i begärandetexten,
* Använd befintlig Azure Media Services till gång genom att ange [till gångs-ID: t](https://docs.microsoft.com/azure/media-services/latest/assets-concept) (stöds endast i betalda konton).

När din video har laddats upp kan Video Indexer (valfritt) koda videon (beskrivs i artikeln). När du skapar ett Video Indexer-konto kan du välja ett kostnadsfritt utvärderingskonto (där du får ett visst antal kostnadsfria indexeringsminuter) eller ett betalalternativ (där du inte begränsas av kvoten). Med den kostnadsfria utvärderingen ger Video Indexer upp till 600 minuter kostnadsfri indexering för webbplatsanvändare och upp till 2 400 minuter kostnadsfri indexering för API-användare. Med alternativet betald skapar du ett Video Indexer-konto som är [anslutet till din Azure-prenumeration och ett Azure Media Services-konto](connect-to-azure.md). Du betalar för minuter som indexeras samt kostnader relaterade till mediekontot. 

Artikeln visar hur du överför och indexerar dina videor med följande alternativ:

* [Video Indexer webbplats](#website) 
* [Video Indexer-API: er](#apis)

## <a name="uploading-considerations-and-limitations"></a>Att överföra överväganden och begränsningar
 
- Ett namn på videon får inte vara större än 80 tecken.
- När du laddar upp videon baserat på URL (önskad) måste slut punkten skyddas med TLS 1,2 (eller högre).
- Uppladdnings storleken med URL-alternativet är begränsad till 30 GB.
- Längden på fråge-URL: en är begränsad till 6144 tecken där Frågesträngens URL-längd är begränsad till 4096 tecken.
- Uppladdnings storleken med alternativet byte mat ris är begränsad till 2 GB.
- Tids gränsen nåddes för byte mat ris-alternativet efter 30 min.
- Den URL som anges i `videoURL` param måste vara kodad.
- Indexering av Media Services till gångar har samma begränsning som indexering från URL: en.
- Video Indexer har en maximal tids gräns på 4 timmar för en enskild fil.
- URL: en måste vara tillgänglig (till exempel en offentlig URL). 

    Om det är en privat URL måste åtkomsttoken anges i begäran.
- URL: en måste peka på en giltig mediafil och inte till en webb sida, till exempel en länk till sidan `www.youtube.com`.
- I ett betalt konto kan du överföra upp till 50 filmer per minut och i ett utvärderings konto upp till 5 filmer per minut.

> [!Tip]
> Det rekommenderas att du använder .NET Framework version 4.6.2 eller senare eftersom äldre .NET Framework-versioner inte använder TLS 1.2 som standard.
>
> Om du måste använda äldre .NET Framework lägger du till en rad i koden innan du gör REST API-anropet:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="supported-file-formats-for-video-indexer"></a>Fil format som stöds för Video Indexer

I artikeln om [inmatade behållare/fil format](../latest/media-encoder-standard-formats.md#input-containerfile-formats) finns en lista över fil format som du kan använda med video Indexer.

## <a name="a-idwebsiteupload-and-index-a-video-using-the-video-indexer-website"></a><a id="website"/>Ladda upp och indexera en video med hjälp av Video Indexer webbplats

> [!NOTE]
> Ett namn på videon får inte vara större än 80 tecken.

1. Logga in på [Video Indexer](https://www.videoindexer.ai/)-webbplatsen.
2. Ladda upp en video genom att trycka på knappen eller länken **Ladda upp**.

    ![Ladda upp](./media/video-indexer-get-started/video-indexer-upload.png)

    När videon har laddats upp påbörjar Video Indexer indexering och analys av videon.

    ![Uppladdad](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    När Video Indexer är klar med analysen får du ett meddelande med en länk till videon och en kort beskrivning av vad som hittades i videon. Till exempel: personer, ämnen och OCR.

## <a name="a-idapisupload-and-index-with-api"></a><a id="apis"/>Ladda upp och indexera med API

Använd [Ladda upp video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) -API för att ladda upp och indexera videor baserat på en URL. Kod exemplet som följer innehåller den kommenterade koden som visar hur du överför byte-matrisen. 

### <a name="configurations-and-params"></a>Konfigurationer och parametrar

I det här avsnittet beskrivs några av de valfria parametrarna och när du kan ange dem.

#### <a name="externalid"></a>externalID 

Med den här parametern kan du ange ett ID som ska associeras med videon. ID:t kan tillämpas på extern VCM-systemintegrering (Video Content Management). Det går att söka efter de videor som finns på Video Indexer-portalen med det angivna externa ID:t.

#### <a name="callbackurl"></a>callbackUrl

En URL som används för att meddela kunder (med en POST-begäran) om följande händelser:

- Indexering av tillståndsändring: 
    - Egenskaper:    
    
        |Namn|Beskrivning|
        |---|---|
        |id|Video-ID|
        |state|Videotillståndet|  
    - Exempel: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- Person som identifierades i videon:
  - Egenskaper
    
      |Namn|Beskrivning|
      |---|---|
      |id| Video-ID|
      |faceId|Ansikts-ID som visas i videoindexet|
      |knownPersonId|Person-ID som är unikt inom en ansikts-modell|
      |personName|Namnet på personen|
        
    - Exempel: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

##### <a name="notes"></a>Anteckningar

- Video Indexer returnerar alla befintliga parametrar som anges i den ursprungliga webbadressen.
- Den tillhandahållna webbadressen måste vara kodad.

#### <a name="indexingpreset"></a>indexingPreset

Använd den här parametern om RAW-inspelningar eller externa inspelningar innehåller bakgrundsljud. Den här parametern används för att konfigurera indexeringsprocessen. Du kan ange följande värden:

- `AudioOnly` – Indexera och extrahera insikter med hjälp av endast ljud (video ignoreras)
- `VideoOnly`-index och extrahera insikter med endast video (ignorerar ljud)
- `Default` – Indexera och extrahera insikter med hjälp av både ljud och video
- `DefaultWithNoiseReduction` – Indexera och extrahera insikter från både ljud och video, samtidigt som algoritmer för brusreducering tillämpas på ljudströmmen

Priset beror på det valda indexeringsalternativet.  

#### <a name="priority"></a>prioritet

Videor indexeras av Video Indexer enligt deras prioritet. Använd parametern **prioritet** för att ange prioritet för indexet. Följande värden är giltiga: **Låg**, **Normal** (standard), och **Hög**.

**Prioritet**-parametern stöds endast för betalda konton.

#### <a name="streamingpreset"></a>streamingPreset

När videon har laddats upp kan Video Indexer koda videon. Sedan fortsätter den med indexering och analys av videon. När Video Indexer är klar med analysen får du ett meddelande med video-ID:t.  

När du använder API:t [Ladda upp video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) eller [Indexera om video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) är en av de valfria parametrarna `streamingPreset`. Om du ställer in `streamingPreset` på `Default`, `SingleBitrate` eller `AdaptiveBitrate` utlöses kodningsprocessen. När indexerings- och kodningsjobben är klara publiceras videon så att du även kan strömma videon. Slutpunkten för direktuppspelning som du vill strömma videon från måste ha tillståndet **Körs**.

För att kunna köra indexerings- och kodningsjobben kräver [Azure Media Services-kontot som är anslutet till ditt Video Indexer-konto](connect-to-azure.md) reserverade enheter. Mer information finns i [Skala mediebearbetning](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Eftersom det är beräkningsintensiva jobb rekommenderas enhetstypen S3 starkt. Antalet RU:er definierar det maximala antalet jobb som kan köras parallellt. Baslinjerekommendationen är 10 S3-RU:er. 

Om du bara vill indexera videon men inte koda den ställer du in `streamingPreset` på `NoStreaming`.

#### <a name="videourl"></a>videoUrl

En URL till video-/ljudfilen som ska indexeras. URL:en måste peka på en mediefil (HTML-sidor stöds inte). Filen kan skyddas av en åtkomsttoken som tillhandahålls som en del av URI:n och slutpunkten som hanterar filen måste skyddas med TLS 1.2 eller senare. URL:en måste vara kodad. 

Om `videoUrl` inte anges förväntar sig Video Indexer att du skickar filen som multipart/form-brödtextinnehåll.

### <a name="code-sample"></a>Kodexempel

Följande C#-kodavsnitt visar hur du använder alla Video Indexer-API:er tillsammans.

#### <a name="instructions-for-running-this-code-sample"></a>Instruktioner för att köra det här kod exemplet

När du har kopierat koden till din utvecklings plattform måste du ange två parametrar: API Management autentiseringsnyckel och video-URL.

* API-nyckel – API-nyckel är din prenumerations nyckel för din personliga API-hantering, som gör att du kan få en åtkomsttoken för att utföra åtgärder på ditt Video Indexers konto. 

    För att hämta din API-nyckel går du igenom det här flödet:

    * Navigera till https://api-portal.videoindexer.ai/
    * Inloggning
    * Gå till **produkter** -> **auktorisering** -> **Authorization-prenumeration**
    * Kopiera **primär nyckeln**
* Video-URL – en URL för video-/ljud filen som ska indexeras. URL:en måste peka på en mediefil (HTML-sidor stöds inte). Filen kan skyddas av en åtkomsttoken som tillhandahålls som en del av URI:n och slutpunkten som hanterar filen måste skyddas med TLS 1.2 eller senare. URL:en måste vara kodad.

Resultatet av att köra kod exemplet innehåller en URL för insikts-widget och en URL för en uppspelnings-widget som gör att du kan granska insikter och video som laddats upp. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

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
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

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

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

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

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
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
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

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
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

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

### <a name="common-errors"></a>Vanliga fel

De statuskoder som visas i följande tabell kan returneras av uppladdingsåtgärden.

|Statuskod|ErrorType (i svarstexten)|Beskrivning|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|Samma video håller redan på att bearbetas i det angivna kontot.|
|400|VIDEO_ALREADY_FAILED|Samma video misslyckades med att bearbetas i det angivna kontot för mindre än 2 timmar sedan. API-klienter ska vänta minst 2 timmar innan en video laddas upp på nytt.|
|429||Utvärderings konton tillåts 5 uppladdningar per minut. Betalade konton tillåts 50 uppladdningar per minut.|

## <a name="next-steps"></a>Nästa steg

[Granska Azure Video Indexer-utdata som skapats av API](video-indexer-output-json-v2.md)

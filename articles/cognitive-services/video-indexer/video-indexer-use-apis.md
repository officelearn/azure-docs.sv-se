---
title: Använda Azure API för Videoindexering | Microsoft Docs
description: Den här artikeln visar hur du kommer igång med Video Indexer API.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 82416c7c653438fcd8b8f4a4ead7591bad0ac022
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391521"
---
# <a name="use-azure-video-indexer-api"></a>Använda Azure API för Videoindexering

Video Indexer konsoliderar olika ljud- och artificiell intelligens (AI)-tekniker som erbjuds av Microsoft i en integrerad tjänst, vilket förenklar utvecklingen. API: erna är utformade för att aktivera som utvecklare kan fokusera på förbrukar Media AI tekniker utan att oroa dig över skalning, global räckvidd, tillgänglighet och tillförlitlighet för molnplattform. Du kan använda API: et för att överföra dina filer, hämta detaljerad information i video och få URL: er i widgetar insikt och spelare om du vill bädda in dem i ditt program och andra uppgifter.

När du skapar en Video Indexer-konto, kan du välja ett kostnadsfritt konto (där du får ett visst antal kostnadsfria indexering minuter) eller ett betalt alternativ (där du inte begränsas av kvoten). Video Indexer ger upp till 600 minuter för kostnadsfria indexering för webbplatsen användare och upp till 2 400 minuters kostnadsfria indexering för API-användare med kostnadsfri utvärderingsversion. Med betald alternativet kan du skapa en Video Indexer-konto som är [är ansluten till din Azure-prenumeration och ett Azure Media Services-konto](connect-to-azure.md). Du betalar för minuter som indexeras som Media-konto som är relaterade kostnader. 

Den här artikeln visar hur utvecklare kan dra nytta av den [Video Indexer API](https://api-portal.videoindexer.ai/). En mer detaljerad översikt över tjänsten Video Indexer finns det [översikt](video-indexer-overview.md) artikeln.

## <a name="subscribe-to-the-api"></a>Prenumerera på API: et

1. Logga in.

    Om du vill börja utveckla med Video Indexer, måste du första logga In på den [Video Indexer](https://api-portal.videoindexer.ai/) portal. 
    
    ![Registrera dig](./media/video-indexer-use-apis/video-indexer-api01.png)

    > [!Important]
    > * Du måste använda samma provider som du använde när du registrerat dig för Video Indexer.
    > * Personliga Google och Microsoft (outlook/live) konton kan bara användas för utvärderingskonton. Konton som är ansluten till Azure kräver Azure AD.
    > * Det kan vara endast en aktiv konto per e-postmeddelande. Om en användare som försöker logga in med user@gmail.com för LinkedIn och efter det med user@gmail.com för Google en senare version visar en felsida, om användaren redan finns.


2. Prenumerera på.

    Välj den [produkter](https://api-portal.videoindexer.ai/products) fliken. Välj auktorisering och prenumerera på. 
    
    ![Registrera dig](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Nya användare prenumererar automatiskt auktorisering.
    
    När du prenumererar kommer du att kunna se din prenumeration och dina primära och sekundära nycklarna. Nycklarna som ska skyddas. Nycklarna bör endast användas av serverkoden. De får inte vara tillgängliga på klientsidan (.js, .html osv.).

    ![Registrera dig](./media/video-indexer-use-apis/video-indexer-api03.png)

## <a name="obtain-access-token-using-the-authorization-api"></a>Hämta åtkomsttoken med hjälp av API: et för auktorisering

När du prenumererar på API: et för auktorisering, kommer du att kunna hämta åtkomsttoken. Dessa åtkomsttoken används för att autentisera mot API: et för åtgärder. 

Varje anrop till API för åtgärder ska associeras med en åtkomsttoken som matchar auktorisering omfånget för anropet.

- Användarnivå - administratörsnivå användartoken kan du utföra åtgärder på den **användaren** nivå. Till exempel få associerade konton.
- Kontonivå – konto på åtkomsttoken kan du utföra åtgärder på den **konto** nivå eller **video** nivå. Till exempel ladda upp video, lista alla videor, hämta videoinsikter osv.
- Videonivå – video på åtkomsttoken kan du utföra åtgärder på en specifik **video**. Till exempel få videoinsikter, ladda ned bildtexter, hämta widgetar, osv. 

Du kan styra om dessa token är readonly eller att de tillåter redigering genom att ange **allowEdit = SANT/FALSKT**.

De flesta scenarier med server-till-server kommer du förmodligen använda samma **konto** token eftersom den omfattar både **konto** operations och **video** åtgärder. Men om du planerar att göra klientsidan anrop till Video Indexer (till exempel från javascript), som du vill använda en **video** åtkomsttoken, att hindra klienter från att få åtkomst till hela kontot. Som också är därför att om inbäddning VideoIndexer klientkod-klienten (t.ex, använder **hämta insikter Widget** eller **hämta Player Widget**) måste du ange en **video**åtkomst-token.

Om du vill göra det enklare, kan du använda den **auktorisering** API > **GetAccounts** att hämta säkerhetstoken för dina konton utan att hämta en användare först. Du kan också begära att få konton med giltig token, så att du kan hoppa över en ytterligare anropet för att hämta en token för konto.

Åtkomsttoken upphör att gälla efter 1 timme. Kontrollera att ditt åtkomst-token är giltig innan du använder Operations-API. Om upphör att gälla, anropa API för auktorisering igen för att få en ny åtkomsttoken.
 
Du är redo att börja integrera med API: et. Hitta [detaljerad beskrivning av varje Video Indexer REST API](http://api-portal.videoindexer.ai/).

## <a name="location"></a>Plats

Åtgärden för alla API: er kräver en plats-parameter som anger regionen till vilket anropet ska dirigeras och kontot har skapats.

De värden som beskrivs i tabellen nedan gäller. Den **Param värde** värdet som du skickar när du använder API: et.

|**Namn**|**Param värde**|**Beskrivning**|
|---|---|---|
|Utvärdering|utvärderingsversion|Används för utvärderingskonton.|
|Västra USA|västra USA 2|Används för Azure regionen västra USA 2.|
|Norra Europa |europanorra|Används för regionen Azure Norra Europa.|
|Östasien|asienöstra|Används för Östasien för Azure-region.|

## <a name="account-id"></a>Konto-ID 

Konto-ID-parametern krävs i alla operativa API-anrop. Konto-ID är ett GUID som kan hämtas på något av följande sätt:

* Använda Video Indexer-portal för att hämta konto-ID:

    1. Logga in på [videoindexer](https://www.videoindexer.ai/).
    2. Bläddra till den **inställningar** sidan.
    3. Kopiera konto-ID.

        ![Konto-ID](./media/video-indexer-use-apis/account-id.png)

* Använd API för att programmässigt få konto-ID.

    Använd den [hämta konton](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) API.
    
    > [!TIP]
    > Du kan generera åtkomsttoken för konton genom att definiera `generateAccessTokens=true`.
    
* Hämta konto-ID från URL: en för en videospelarsidan i ditt konto.

    När du tittar på en video, ID: T visas efter den `accounts` avsnittet och innan den `videos` avsnittet.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Rekommendationer

Det här avsnittet innehåller några rekommendationer när du använder Video Indexer API.

- Om du planerar att ladda upp en video, rekommenderas att placera filen på en plats för offentligt nätverk (till exempel OneDrive). Hämta en länk till videon och ange URL som överför fil param. 

    URL: en till Video Indexer måste peka på en media (ljud eller video)-fil. Vissa av länkarna som genererats av OneDrive är för en HTML-sida som innehåller filen. En enkel verifiering för URL: en är att klistra in den i en webbläsare – om filen börjar ladda ned, är det troligt en bra URL. Om webbläsaren är rendering vissa visualisering, är det sannolikt inte en länk till en fil men en HTML-sida.
    
- När du anropar API: et som hämtar videoinsikter för angivna videon, kan du få en detaljerad JSON-utdata som svarsinnehållet. [Se information om den returnerade JSON-filen i det här avsnittet](video-indexer-output-json.md).

## <a name="code-sample"></a>Kodexempel

I följande C#-kodavsnitt visar hur du använder alla Video Indexer API: er tillsammans.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token            
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

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
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="next-steps"></a>Nästa steg

[Granska information om utdata JSON](video-indexer-output-json.md).

## <a name="see-also"></a>Se också

[Video Indexer-översikt](video-indexer-overview.md)

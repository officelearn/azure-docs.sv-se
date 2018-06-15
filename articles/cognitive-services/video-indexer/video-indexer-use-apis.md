---
title: Använd Azure Video indexeraren API | Microsoft Docs
description: 'Den här artikeln visar hur du kommer igång med API: T för videon indexeraren.'
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 06/04/2018
ms.author: juliako
ms.openlocfilehash: d378934a0c085910475c366f4bdb538f09efc12b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35356478"
---
# <a name="use-azure-video-indexer-api"></a>Använd Azure Video indexeraren API

Video indexeraren konsoliderar olika ljud och video artificiell intelligence (AI)-tekniker som erbjuds av Microsoft i en integrerad tjänst gör det enklare utveckling. API: erna är utformade för att aktivera utvecklare kan fokusera på förbrukar Media AI tekniker utan att oroa skala, globala nå, tillgänglighet och tillförlitlighet för molnplattform. Du kan använda API: et för att överföra filer, få detaljerad video insikter, hämta URL: er för insikt och player widgetar för att bädda in dem i ditt program och andra aktiviteter.

> [!Note]
> Den kostnadsfria utvärderingsversionen har en daglig uppladdningsgräns på 100 filer. Du kan kontrollera felmeddelande för ytterligare information. Observera att du kan ändra den dagliga gränsen.

Den här artikeln visar hur utvecklare kan dra nytta av den [Video indexeraren API](https://api-portal.videoindexer.ai/). En detaljerad översikt över tjänsten Video indexeraren finns i [översikt](video-indexer-overview.md) artikel.

## <a name="subscribe-to-the-api"></a>Prenumerera på API: et

1. Logga in.

    Om du vill börja utveckla med Video indexerare, måste du först logga In på den [Video indexeraren](https://api-portal.videoindexer.ai/) portal. 
    
    ![Registrera dig](./media/video-indexer-use-apis/video-indexer-api01.png)

    > [!Important]
    > 1. Du måste använda samma provider som du använde när du registrerade dig för Video indexeraren.
    > 2. Innan Azure AD-användare från en domän kan logga in domänadministratör AAD måste aktivera den domänregistrering [här](https://api-portal.videoindexer.ai/aadadminconsent).
    > 3. Personliga Google och Microsoft (outlook/live) konton kan bara användas för utvärderingskonton. Konton som är ansluten till Azure kräver AAD.

2. Prenumerera på.

    Välj den [produkter](https://api-portal.videoindexer.ai/products) fliken. Välj auktorisering och prenumerera. 
    
    ![Registrera dig](./media/video-indexer-use-apis/video-indexer-api02.png)
    
    När du prenumererar kan kommer du att kunna se din prenumeration och din primära och sekundära nycklarna. Nycklarna som ska skyddas. Nycklarna bör endast användas av serverkod. De får inte vara tillgängliga på klientsidan (.js, .html osv.).

    ![Registrera dig](./media/video-indexer-use-apis/video-indexer-api03.png)

## <a name="obtain-access-token-using-the-authorization-api"></a>Hämta åtkomsttoken med hjälp av API för auktorisering

När du prenumererar på auktorisering API kommer du att kunna erhålla åtkomsttoken. Dessa åtkomsttoken används för att autentisera mot Operations-API. 

Varje anrop till API: et Operations ska kopplas till en åtkomst-token matchar auktorisering omfattning anropet.

- Användarnivå - användare åtkomst-token kan du utföra åtgärder på den **användaren** nivå. Till exempel få associerade konton.
- Kontonivå – konto åtkomst-token kan du utföra åtgärder på den **konto** nivå eller **video** nivå. Till exempel överföra video, visa alla videoklipp, hämta video insikter osv.
- Video nivå – video åtkomst-token kan du utföra åtgärder på en specifik **video**. Till exempel få video insikter, Hämta beskrivningar, hämta widgetar, osv. 

Du kan styra om dessa token är readonly eller att de tillåter redigering genom att ange **allowEdit = SANT/FALSKT**.

De flesta scenarier med server-till-server, kommer du förmodligen använda samma **konto** token eftersom den omfattar både **konto** operations och **video** åtgärder. Men om du planerar att göra på klientsidan anrop till Video indexeraren (t.ex. från javascript) som du vill använda en **video** åtkomsttoken att hindra klienter från att få åtkomst till hela kontot. Det är därför att om bädda in VideoIndexer klientkod i din klient (till exempel med hjälp av **få insikter Widget** eller **hämta Player Widget**) måste du ange en **video**åtkomst-token.

Om du vill göra det lättare kan du använda den **auktorisering** API > **GetAccounts** att hämta token dina konton utan att hämta en användare först. Du kan också begära att få konton med ogiltig token så att du kan hoppa över en ytterligare anropa för att få en token för kontot.

Åtkomst-token upphör efter 1 timme. Kontrollera åtkomst-token är giltig innan du använder Operations-API. Om upphör att gälla, anropa API för auktorisering igen för att få en ny åtkomsttoken.
 
Du är redo att börja integrera med API: et. Hitta [detaljerad beskrivning av varje Video indexeraren REST API](http://api-portal.videoindexer.ai/).

## <a name="location"></a>Plats

Åtgärden för alla API: er kräver en plats-parameter som anger regionen där anropet ska vidarebefordras och i kontot har skapats.

De värden som beskrivs i följande tabell gäller. Den **Param värde** värdet du skickar när du använder API: et.

|**Namn**|**Param värde**|**Beskrivning**|
|---|---|---|
|Utvärdering|utvärderingsversion|Används för utvärderingskonton.|
|Västra USA|westus2|Används för Azure West US 2-region.|
|Norra Europa |europanorra|Används för Azure Norra Europa region.|
|Östasien|asienöstra|Används för Östasien för Azure-region.|

## <a name="account-id"></a>Konto-ID 

Konto-ID-parametern krävs i alla operativa API-anrop. Konto-ID är ett GUID som kan erhållas på något av följande sätt:

* Använda Video indexeraren portal för att hämta konto-ID:

    1. Logga in på [videoindexer](https://www.videoindexer.ai/).
    2. Bläddra till den **inställningar** sidan.
    3. Kopiera konto-ID.

        ![Konto-ID](./media/video-indexer-use-apis/account-id.png)

* Använd API för att genom programmering få konto-ID.

    Använd den [hämta konton](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) API.
    
    > [!TIP]
    > Du kan generera åtkomsttoken för konton genom att definiera `generateAccessTokens=true`.
    
* Hämta konto-ID från URL-Adressen till en player sida i ditt konto.

    När du vill se en video, ID: T visas efter den `accounts` avsnitt och innan den `videos` avsnitt.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Rekommendationer

Det här avsnittet innehåller några rekommendationer när du använder Video indexeraren API.

- Om du planerar att ladda upp en video, rekommenderas att placera filen i en offentlig nätverksplats (till exempel OneDrive). Hämta en länk till videon och ange en Webbadress som överför filen param. 

    URL: en som tillhandahållits till indexerare Video måste peka på en mediefil för (ljud och video). Vissa av länkarna som genererats av OneDrive är för en HTML-sida som innehåller filen. En enkel verifiering för URL: en skulle kunna klistra in den i en webbläsare – om filen börjar hämta, är det troligt en bra URL. Om webbläsaren rendera vissa visualisering, är det förmodligen inte en länk till en fil men en HTML-sida.
    
- När du anropar API som hämtar video insikter för den angivna videon, kan du få en detaljerad JSON-utdata som svar-innehåll. [Se information om returnerade JSON i det här avsnittet](video-indexer-output-json.md).

## <a name="code-sample"></a>Kodexempel

Följande C# kodfragment demonstrerar användningen av alla Video indexeraren API: erna tillsammans.

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

[Granska information om resultatet JSON](video-indexer-output-json.md).

## <a name="see-also"></a>Se också

[Indexeraren videoöversikt](video-indexer-overview.md)

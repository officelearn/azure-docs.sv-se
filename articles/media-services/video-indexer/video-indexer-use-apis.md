---
title: Använda Video Indexer-API:et
titleSuffix: Azure Media Services
description: I den här artikeln beskrivs hur du kommer igång med Azure Media Services Video Indexer API.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/13/2020
ms.author: juliako
ms.openlocfilehash: 82bdb177cf4d9c400d1b13ba7178658089950557
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314340"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Självstudie: Använda Video Indexer-API:t

Video Indexer konsoliderar olika ljud- och videotekniker för artificiell intelligens (AI) som erbjuds av Microsoft till en integrerad tjänst, vilket gör utvecklingen enklare. API:erna är utformade för att göra det möjligt för utvecklare att fokusera på att konsumera Media AI-teknik utan att oroa dig för skala, global räckvidd, tillgänglighet och tillförlitlighet för molnplattformar. Du kan använda API:et för att ladda upp dina filer, få detaljerade videoinsikter, få webbadresser med inbäddningsbara insikter och widgets för spelare med mera.

När du skapar ett videoindexerkonto kan du välja ett kostnadsfritt utvärderingskonto (där du får ett visst antal kostnadsfria indexeringsminuter) eller ett betalt alternativ (där du inte begränsas av kvoten). Med en gratis testperiod ger Video Indexer upp till 600 minuters gratis indexering till webbplatsanvändare och upp till 2400 minuters gratis indexering till API-användare. Med ett betalt alternativ skapar du ett videoindexerkonto som är [kopplat till din Azure-prenumeration och ett Azure Media Services-konto](connect-to-azure.md). Du betalar för minuter som indexeras samt kostnader relaterade till Azure Media Services-kontot.

Den här artikeln visar hur utvecklarna kan dra nytta av [Video Indexer-API:t](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Prenumerera på API:t

1. Logga in på [Video Indexer-utvecklarportalen](https://api-portal.videoindexer.ai/).
    
    ![Logga in på Utvecklarportalen för Video Indexer](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Du måste använda samma provider som du använde när du registrerade dig för Video Indexer.
   > * Personliga Google- och Microsoft-konton (Outlook/Live) kan endast användas för utvärderingskonton. Konton som är anslutna till Azure kräver Azure AD.
   > * Det kan bara finnas ett aktivt konto per e-post. Om en användare försöker user@gmail.com logga in med user@gmail.com för LinkedIn och senare med för Google visas en felsida på den senare och säger att användaren redan finns.

2. Prenumerera.

    Välj fliken [Produkter.](https://api-portal.videoindexer.ai/products) Välj sedan Auktorisering och prenumerera.
    
    ![Fliken Produkter i utvecklarportalen för videoindexerare](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Nya användare prenumererar automatiskt på Auktorisering.
    
    När du prenumererar kan du se din prenumeration och dina primära och sekundära nycklar. Nycklarna ska vara skyddade. Nycklarna ska bara användas av din serverkod. De bör inte vara tillgängliga på klientsidan (.js, .html och så vidare).

    ![Prenumeration och nycklar i Utvecklarportalen för Video Indexer](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Video Indexer-användare kan använda en enstaka prenumerationsnyckel för att ansluta till flera Video Indexer-konton. Sedan kan du länka dessa Video Indexer-konton till olika Media Services-konton.

## <a name="obtain-access-token-using-the-authorization-api"></a>Hämta åtkomsttoken med hjälp av auktoriserings-API:t

När du prenumererar på API:et för auktorisering kan du hämta åtkomsttoken. Dessa åtkomsttoken används för att autentisera mot åtgärds-API:t.

Varje anrop till åtgärds-API:t ska associeras med en åtkomsttoken, som matchar auktoriseringsområdet för anropet.

- Användarnivå: Med åtkomsttoken på användarnivå kan du utföra åtgärder på **användarnivå.** Till exempel hämta associerade konton.
- Kontonivå: Med åtkomsttoken på kontonivå kan du utföra åtgärder på **kontonivå** eller **videonivå.** Ladda till exempel upp video, lista alla videor, få videoinsikter och så vidare.
- Videonivå: Med åtkomsttoken på videonivå kan du utföra åtgärder på en viss **video**. Till exempel, få video insikter, ladda ner bildtexter, få widgets och så vidare.

Du kan styra om dessa token är skrivskyddade eller om de tillåter redigering genom att ange **allowEdit=true/false**.

För de flesta server-till-server-scenarier använder du förmodligen samma **kontotoken** eftersom den täcker både **kontoåtgärder** och **videoåtgärder.** Men om du planerar att ringa appeller på klientsidan till Video Indexer (till **video** exempel från JavaScript) vill du använda en videoåtkomsttoken för att förhindra att klienter får åtkomst till hela kontot. Det är också anledningen till att när du bäddar in videoindexeringsklientkod i klienten (till exempel med **widgeten Hämta statistik** eller **Widgeten Hämta spelare)** måste du ange en videoåtkomsttoken. **video**

Du kan göra det lättare genom att använda **Auktoriserings**-API:t > **GetAccounts** för att hämta dina konton utan att erhålla en användartoken först. Du kan också begära att få kontona med giltiga token, så att du kan hoppa över ett ytterligare anrop för att få en kontotoken.

Åtkomsttoken upphör att gälla efter 1 timme. Kontrollera att din åtkomsttoken är giltig innan du använder åtgärds-API:t. Om den upphör att gälla anropar du API:et för auktorisering igen för att få en ny åtkomsttoken.

Du är redo att börja integrera med API: et. Se [den detaljerade beskrivningen av varje Video Indexer-REST API](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Konto-ID

Parametern Konto-ID krävs i alla åtgärds-API-anrop. Konto-ID är ett GUID som kan erhållas på något av följande sätt:

* Använd **Video Indexer-webbplatsen** och hämta konto-ID:t:

    1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
    2. Gå till sidan **Inställningar**.
    3. Kopiera konto-ID:t.

        ![Inställningar och konto-ID för videoindexerare](./media/video-indexer-use-apis/account-id.png)

* Använd **Video Indexer-utvecklarportalen** och hämta konto-ID:t programmatiskt.

    Använd [API:et för hämta konto.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?)

    > [!TIP]
    > Du kan generera åtkomsttoken för kontona genom att definiera `generateAccessTokens=true`.

* Hämta konto-ID:t från URL:en till en spelarsida på ditt konto.

    När du tittar på en video visas ID:t efter avsnittet `accounts` och före avsnittet `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Rekommendationer

Det här avsnittet innehåller några rekommendationer när du använder Video Indexer-API:t.

- Om du planerar att ladda upp en video rekommenderar vi att filen placeras på en plats i det offentliga nätverket (till exempel OneDrive). Hämta länken till videon och ange URL:en som parameter för uppladdningsfil.

    URL:en som skickas till Video Indexer måste peka på en mediefil (ljud eller video). Vissa av länkarna som genereras av OneDrive är till en HTML-sida som innehåller filen. En enkel verifiering av webbadressen är att klistra in den i en webbläsare, om filen börjar ladda ner, är det sannolikt en bra webbadress. Om webbläsaren återger viss visualisering är det sannolikt inte en länk till en fil utan till en HTML-sida.

- När du anropar API:t som hämtar videoinsikter för den angivna videon får du detaljerade JSON-utdata som svarsinnehåll. [Information om returnerad JSON finns i det här avsnittet](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Kodexempel

Följande C#-kodavsnitt visar hur du använder alla Video Indexer-API:er tillsammans.

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
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

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

## <a name="see-also"></a>Se även

- [Översikt över Video Indexer](video-indexer-overview.md)
- [Regioner](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Nästa steg

- [Undersök detaljer om produktionen JSON](video-indexer-output-json-v2.md)
- Kolla in [exempelkoden](https://github.com/Azure-Samples/media-services-video-indexer) som visar viktig aspekt av att ladda upp och indexera en video. Genom att följa koden ger du dig en god uppfattning om hur du använder vårt API för grundläggande funktioner. Se till att läsa inline kommentarer och märker våra bästa praxis råd.


---
title: Använda Video Indexer-API – Azure
titlesuffix: Azure Media Services
description: Den här artikeln visar hur du kommer igång med att använda Video Indexer-API:t.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: dc3e654e741fd1a326c83e51c72284f41ad74798
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55988059"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Självstudier: Använda Video Indexer-API:et

> [!Note]
> Video Indexer-API:t v1 blev inaktuellt den 1 augusti 2018. Nu bör du använda Video Indexer-API:t v2. <br/>Om du vill utveckla med Video Indexer-API:er v2 läser du instruktionerna [här](https://api-portal.videoindexer.ai/). 

Video Indexer konsoliderar olika AI-tekniker (artificiell intelligens) för ljud och video som erbjuds av Microsoft i en integrerad tjänst, vilket förenklar utvecklingen. API:erna är utformade så att utvecklare kan fokusera på att använda AI-medietekniker utan att behöva fundera på skalning, global räckvidd, tillgänglighet och tillförlitlighet för molnplattformen. Du kan använda API:t för att ladda upp filer, få detaljerade videoinsikter, få URL:er till insikts- och spelarwidgetar så att du kan bädda in dem i ett program, och andra uppgifter.

När du skapar ett Video Indexer-konto kan du välja ett kostnadsfritt utvärderingskonto (där du får ett visst antal kostnadsfria indexeringsminuter) eller ett betalalternativ (där du inte begränsas av kvoten). Med den kostnadsfria utvärderingen ger Video Indexer upp till 600 minuter kostnadsfri indexering för webbplatsanvändare och upp till 2 400 minuter kostnadsfri indexering för API-användare. Med betalalternativet skapar du ett Video Indexer-konto som är [anslutet till din Azure-prenumeration och ett Azure Media Services-konto](connect-to-azure.md). Du betalar för minuter som indexeras samt kostnader relaterade till Azure Media Services-kontot. 

Den här artikeln visar hur utvecklarna kan dra nytta av [Video Indexer-API:t](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Prenumerera på API:t

1. Logga in på [Video Indexer-utvecklarportalen](https://api-portal.videoindexer.ai/).
    
    ![Logga in](./media/video-indexer-use-apis/video-indexer-api01.png)

    > [!Important]
    > * Du måste använda samma provider som du använde när du registrerade dig för Video Indexer.
    > * Personliga Google- och Microsoft-konton (Outlook/Live) kan bara användas för utvärderingskonton. Konton som är anslutna till Azure kräver Azure AD.
    > * Det kan bara finnas ett aktivt konto per e-postadress. Om en användare försöker logga in med user@gmail.com för LinkedIn och därefter med user@gmail.com för Google visas en felsida i det senare fallet om att användaren redan finns.

2. Prenumerera.

    Välj fliken [Produkter](https://api-portal.videoindexer.ai/products). Välj sedan Auktorisering och Prenumerera. 
    
    ![Registrera dig](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Nya användare prenumererar automatiskt på Auktorisering.
    
    När du prenumererar kan du se din prenumeration och dina primära och sekundära nycklar. Nycklarna ska vara skyddade. Nycklarna ska bara användas av din serverkod. De ska inte vara tillgängliga på klientsidan (.js, .html osv.).

    ![Registrera dig](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Video Indexer-användare kan använda en enstaka prenumerationsnyckel för att ansluta till flera Video Indexer-konton. Sedan kan du länka dessa Video Indexer-konton till olika Media Services-konton.

## <a name="obtain-access-token-using-the-authorization-api"></a>Hämta åtkomsttoken med hjälp av auktoriserings-API:t

När du prenumererar på auktoriserings-API:t kan du hämta åtkomsttoken. Dessa åtkomsttoken används för att autentisera mot åtgärds-API:t. 

Varje anrop till åtgärds-API:t ska associeras med en åtkomsttoken, som matchar auktoriseringsområdet för anropet.

- Användarnivå – med åtkomsttoken på användarnivå kan du utföra åtgärder på **användar**nivån. Till exempel hämta associerade konton.
- Kontonivå – med åtkomsttoken på kontonivå kan du utföra åtgärder på **konto**nivån eller **video**nivån. Till exempel ladda upp video, visa alla videor, få videoinsikter osv.
- Videonivå – med åtkomsttoken på videonivå kan du utföra åtgärder på en specifik **video**. Till exempel få videoinsikter, ladda ned textning, få widgetar osv. 

Du kan styra om dessa token är skrivskyddade eller om de kan redigeras genom att ange **allowEdit=true/false**.

För de flesta server-till-server-scenarier använder du förmodligen samma **konto**token eftersom den omfattar både **konto**åtgärder och **video**åtgärder. Men om du planerar att utföra anrop på klientsidan till Video Indexer (till exempel från javascript) bör du använda en **video**åtkomsttoken, för att förhindra att klienter får åtkomst till hela kontot. Det är också anledningen till att du måste ange en **video**åtkomsttoken när du bäddar in VideoIndexer-klientkod i klienten (till exempel med **insiktswidgeten** eller **spelarwidgeten**).

Du kan göra det lättare genom att använda **Auktoriserings**-API:t > **GetAccounts** för att hämta dina konton utan att erhålla en användartoken först. Du kan också begära att få kontona med giltiga token, så att du kan hoppa över ett ytterligare anrop för att få en kontotoken.

Åtkomsttoken upphör att gälla efter 1 timme. Kontrollera att din åtkomsttoken är giltig innan du använder åtgärds-API:t. Om den upphör att gälla anropar du auktoriserings-API:t igen för att få en ny åtkomsttoken.
 
Du kan börja integrera med API:t. Se [den detaljerade beskrivningen av varje Video Indexer-REST API](http://api-portal.videoindexer.ai/).

## <a name="location"></a>Plats

Alla åtgärds-API:er kräver en platsparameter, som anger vilken region anropet ska dirigeras till och vilket konto det skapades på.

De värden som beskrivs i tabellen nedan gäller. **Parametervärde** är det värde du skickar när du använder API:t.

|**Namn**|**Parametervärde**|**Beskrivning**|
|---|---|---|
|Utvärdering|spårning|Används för utvärderingskonton.|
|Västra USA|westus2|Används för Azure-regionen Västra USA 2.|
|Norra Europa |northeurope|Används för Azure-regionen Norra Europa.|
|Östasien|eastasia|Används för Azure-regionen Östasien.|

## <a name="account-id"></a>Konto-ID 

Parametern Konto-ID krävs i alla åtgärds-API-anrop. Konto-ID är ett GUID som kan erhållas på något av följande sätt:

* Använd **Video Indexer-webbplatsen** och hämta konto-ID:t:

    1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
    2. Gå till sidan **Inställningar**.
    3. Kopiera konto-ID:t.

        ![Konto-ID](./media/video-indexer-use-apis/account-id.png)

* Använd **Video Indexer-utvecklarportalen** och hämta konto-ID:t programmatiskt.

    Använd API:t [Hämta konton](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?).
    
    > [!TIP]
    > Du kan generera åtkomsttoken för kontona genom att definiera `generateAccessTokens=true`.
    
* Hämta konto-ID:t från URL:en till en spelarsida på ditt konto.

    När du tittar på en video visas ID:t efter avsnittet `accounts` och före avsnittet `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Rekommendationer

Det här avsnittet innehåller några rekommendationer när du använder Video Indexer-API:t.

- Om du planerar att ladda upp en video rekommenderas du att placera filen på en offentlig nätverksplats (till exempel OneDrive). Hämta länken till videon och ange URL:en som parameter för uppladdningsfil. 

    URL:en som skickas till Video Indexer måste peka på en mediefil (ljud eller video). Vissa av länkarna som genereras av OneDrive är till en HTML-sida som innehåller filen. En enkel kontroll av URL:en är att klistra in den i en webbläsare – om filen börjar laddas ned är det sannolikt en giltig URL. Om webbläsaren renderar någon visualisering är det sannolikt inte en länk till en fil, utan till en HTML-sida.
    
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

[Granska information i JSON-utdata](video-indexer-output-json-v2.md).

[Översikt över Video Indexer](video-indexer-overview.md)

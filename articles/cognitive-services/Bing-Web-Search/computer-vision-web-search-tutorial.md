---
title: Visual Sök mobilappen självstudiekursen
description: Öppna källa C#-program som implementerar visual sökning med hjälp av ramverket kognitiva Services datorn Vision API, Bing webb-API för sökning och Xamarin.Forms plattformar.
services: bing-web-search, computer-vision
author: Aristoddle
manager: bking
ms.service: cognitive-services
ms.devlang: csharp
ms.topic: article
ms.date: 06/22/2017
ms.author: t-jolan
ms.openlocfilehash: 54dabaeaad2e49ba7cc138636054bc3dfa4b8379
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352515"
---
# <a name="visual-search-mobile-app-tutorial"></a>Visual Sök mobilappen självstudiekursen

## <a name="introduction"></a>Introduktion  
Den här självstudiekursen utforskar den [datorn Vision API](https://azure.microsoft.com/services/cognitive-services/computer-vision/) och [Bing Web Sök API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) slutpunkter och visar hur de kan användas för att skapa ett visual basic sökprogram med [Xamarin.Forms](https://developer.xamarin.com/guides/xamarin-forms/).  Generellt sett ingår den här kursen i följande avsnitt: 

> [!div class="checklist"]
> * Konfigurera Visual Studio för att utveckla Xamarin.Forms-program
> * Med hjälp av den [Xamarin Media Plugin](https://github.com/jamesmontemagno/MediaPlugin) att fånga och importera bilddata
> * Dela upp text från en avbildning med hjälp av API: er för datorn Vision
> * Search-begäranden skickas till Bing webb-API för sökning
> * Parsning av JSON-svar från både API: er med [Json.NET](https://github.com/JamesNK/Newtonsoft.Json) (med LINQ och modellen objekt deserialisering)
> * Skapa ett plattformsoberoende Xamarin.Forms användargränssnitt för visual sökning 

## <a name="prerequisites"></a>Förutsättningar

Det här exemplet har utvecklats med hjälp av Xamarin.Forms med [Visual Studio 2017](https://www.visualstudio.com/downloads/). Den Community Edition av Visual Studio 2017 kan användas. Mer information om hur du använder Xamarin med Visual Studio finns i [Xamarin dokumentationen](https://developer.xamarin.com/guides/cross-platform/getting_started/).

Det här exemplet används följande NuGet-paket:

> [!div class="checklist"]
> * [Xamarin Media plugin-program](https://github.com/jamesmontemagno/MediaPlugin)
> * [Json.NET](https://github.com/JamesNK/Newtonsoft.Json)

Programmet använder följande kognitiva Services API: erna:

> [!div class="checklist"]
> * [Bing webbsökning API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 
> * [API för visuellt innehåll](https://azure.microsoft.com/services/cognitive-services/computer-vision/)

30-dagars utvärderingsversion nycklar för API: erna finns på [försök kognitiva Services](https://azure.microsoft.com/try/cognitive-services/). Mer information om hur du skaffar nycklar för kommersiell användning finns [priser](https://azure.microsoft.com/pricing/calculator/).

## <a name="setup-for-development"></a>Installationsprogrammet för utveckling  

### <a name="installing-xamarin-on-windows"></a>Installera Xamarin i Windows
Öppna Visual Studio installationsprogrammet med en utgåva av Visual Studio 2017 installerat, Välj menyn hamburger som är associerade med installationen av Visual Studio och välj **ändra**.  

![Installationsprogrammet för Visual Studio](./media/computer-vision-web-search-tutorial/visual-studio-installer.png) 

Rulla ned till Mobile & spel och aktivera **Mobile utveckling med .NET**, om den inte redan är aktiverad.

![Visual Studio installer med Xamarin.Forms valt](./media/computer-vision-web-search-tutorial/xamarin-forms-is-enabled.png)

Klicka slutligen på **ändra** i nederkant högra hörnet av fönstret och vänta tills Xamarin för att installera.

### <a name="installing-xamarin-on-macos"></a>Installera Xamarin på macOS
Xamarin levereras före med Visual Studio för Mac. Vara bör kräver ingen installation.

## <a name="building-and-running-the-app"></a>Skapa och köra appen

En fil för Visual Studio-lösning *(.sln)* Visual sökningen programmet kan laddas ned från [Visual appen Sök med kognitiva Services](https://azure.microsoft.com/resources/samples/cognitive-services-xamarin-forms-computer-vision-search/). Du kan hämta ZIP-arkivet med hjälp av en webbläsare, klona den till din arbetsstation från GitHub eller hämta den med hjälp av Visual Studio.

När du börjar arbeta med exemplet, öppna `VisualSearchApp.sln` i Visual Studio.  Vid initiering av de nödvändiga komponenterna kan ta en stund.

Programmet kräver två tredjeparts-bibliotek: **Json.NET** och **Xamarin Media Plugin**. Du kan installera dessa bibliotek höger i Visual Studio med NuGet Package Manager. Välj **Verktyg > NuGet Package Manager > Hantera NuGet-paket för lösningen**, eller högerklicka på lösningen i Solution Explorer och välja **hantera NuGet-paket** på snabbmenyn.

I fönstret NuGet söka efter och installera **Xamarin Media plugin** (Xam.Plugin.Media) version 2.6.2 och **Json.NET** (Newtonsoft.Json) 10.0.3. Se till att välja alla projekt när du installerar.

Om du vill bygga program för alla tillgängliga plattformar, trycker du på **Ctrl + Skift + B**, eller klicka på **skapa** på menyn i menyfliksområdet och väljer **skapa lösning**.  För att kompilera och testa kod för iOS från ett Windows-system, se [handboken](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/) för hjälp.

Du måste välja en mål-konfiguration, plattform och projektet innan du kör programmet.  Xamarin.Forms program kompilera till maskinkod för Windows-, Android och iOS. Vägledningen innehåller skärmdumpar av Windows-versionen av exemplet, men alla versioner är funktionellt likvärdiga. Distributionsinställningar som används i den här handboken visas här.  

![Visual Studio som konfigurerats för att kompilera för en Android-telefon](./media/computer-vision-web-search-tutorial/configuration-selection.png)

När du skapar har slutförts och du har valt en målplattform, klickar du på den **starta** knapp i verktygsfältet eller tryck på **F5**. Visual Studio distribuerar din lösning till målplattformen och startar den.

Lägg till nycklar sida visas. (Den här sidan har definierats i `AddKeysPage.xaml`.)  

![Bild av guiden Lägg till nycklar för sidan](./media/computer-vision-web-search-tutorial/add-keys-page.png)  

Här, klistra in i din dator Vision och Bing webb-API för sökning. Datorn Vision API måste du väljer du den server som är värd för slutpunkten.

> [!TIP]
> Om du vill hoppa över den här sidan, anger du informationen i lämplig plats i `App.xaml.cs`. 

Programmet verifierar dina nycklar genom att utföra en Testfråga, sedan tar du OCR Välj sidan (definieras i `OcrSelectPage.xaml`).
   
![Bild av sidan OCR väljer](./media/computer-vision-web-search-tutorial/ocr-select-page.png)  

Du kan välja om den text som du vill identifiera skrivs ut eller handskriven längst upp i den här skärmen.

> [!TIP]
> Håll det objektet som du försöker identifiera text som nivån som möjligt och kontrollera att det är jämnt upplysta med ingen reflektion. Vi har hittat att handskriven OCR ibland fungerar bättre för skrivstilsteckensnitt som eller annan ”avancerad” text.

Klicka sedan på **ta foto** eller **importera foto** så att antingen ta ett foto med enhetens kamera eller välj ett foto som lagras på enheten.

När ett foto har vidtagits eller valt, skickas bilden Vision-API: et för datorn. Sidan ord hittades (definieras i `OcrResultsPage.xaml`) visar ord som identifieras i avbildningen.

![Bild av sidan OCR resultat](./media/computer-vision-web-search-tutorial/ocr-results-page.png)  

> [!NOTE]
> Bilden som vi använde dessa resultat är i källdatabasen som `SamplePhotos\TestImage.jpg`.

När du klickar på ett objekt i ord hitta sidan Web resultatsidan (`WebResultsPage.xaml`) visas kan du upp Bing resultat av sökningen.

![Bild av resultatsidan](./media/computer-vision-web-search-tutorial/web-results-page.png)  
Välj slutligen ett element på sidan Webbresultat för att öppna länken i ett inbäddat webbvy. (Eller gå tillbaka om du vill välja ett annat slutresultat.)

![Bild av sidan Visa](./media/computer-vision-web-search-tutorial/web-view-page.png)  

Du kan interagera med sidan som du skulle ha gjort i valfri webbläsare, eller gå tillbaka till sidan Webbresultat. 

## <a name="review-and-learn"></a>Granska och lära dig

Nu när du har gjort Visual Sök-app för en rotationsrutan vi utforska exakt hur vi använder de två Microsoft kognitiva Services API: er.  Om du använder det här exemplet som en startpunkt för ditt eget program eller helt enkelt som en anvisningar för de API: er, är det användbart att gå igenom program skärmen-av-skärmen för att se exakt hur det fungerar.

### <a name="add-keys-page"></a>Lägg till nycklar på sidan
Lägg till nycklar sidan UI har definierats i `AddKeysPage.xaml`, och dess primära logik som definieras i `AddKeysPage.xaml.cs`. Eftersom nycklarna verifieras genom att göra en testbegäran är tiden mogna att fastställa hur du använder kognitiva Services-slutpunkter i C#.  

Den grundläggande strukturen interaktionen är: 

1. Skapa en instans av *HttpResponseMessage* och *HttpClient* objekt från *System.Net.Http*
2. Koppla önskade huvuden (definieras i API-referens för varje slutpunkt) till den *HttpClient* objekt
3. Skicka en GET eller POST-begäran med dina data, om du lägger till nödvändiga parametrar till slutpunkten URI
4. Kontrollera att svaret har lyckats
5. Ange svaret för ytterligare bearbetning

Den funktion som kontrollerar giltigheten för Bing Search API-nyckeln är `CheckBingSearchKey()`, visas här.

```csharp
async Task CheckBingSearchKey(object sender = null, EventArgs e = null)
{
    HttpResponseMessage response;
    HttpClient SearchApiClient = new HttpClient();

    SearchApiClient.DefaultRequestHeaders.Add(AppConstants.OcpApimSubscriptionKey, BingSearchKeyEntry.Text);

    try
    {
        response = await SearchApiClient.GetAsync(AppConstants.BingWebSearchApiUrl + "q=test");

        if (response.StatusCode != System.Net.HttpStatusCode.Unauthorized)
        {
            BingSearchKeyEntry.BackgroundColor = Color.Green;
            AppConstants.BingWebSearchApiKey = BingSearchKeyEntry.Text;
            bingSearchKeyWorks = true;
        }
        else
        {
            BingSearchKeyEntry.BackgroundColor = Color.Red;
            bingSearchKeyWorks = false;
        }
    }
    catch( Exception exception )
    {
        BingSearchKeyEntry.BackgroundColor = Color.Red;
        Console.WriteLine($"ERROR: {exception.Message}");
    }
}
```

En liknande funktion, `CheckComputerVisionKey()`, används för att verifiera den datorn Vision nyckeln.

### <a name="ocr-select-page"></a>Välj OCR sida

Sidan Välj OCR (`OcrSelectPage.xaml`) har två viktiga roller. Först avgör vilken typ av OCR utförs på mål-foto. Andra, det låter användaren fånga eller öppna den bild som de vill bearbeta.

Den andra aktiviteten är traditionellt besvärlig i ett program för flera plattformar, eftersom varje plattform som kräver annan kod. Xamarin Media plugin-programmet hanterar det med bara några få rader med kod.

Följande funktion innehåller ett exempel på hur Xamarin Media plugin-programmet för foto avbildning.  I den finns det:

1. Kontrollera att en kamera är tillgänglig på den aktuella enheten
2. Skapa en instans av en `StoreCameraMediaOptions` objekt för att ange var du vill spara den fångade avbildningen
3. Hämta en avbildning och få en `MediaFile` objekt som innehåller bilddata
4. Packa upp den `MediaFile` i en bytematris
5. Returnera Bytematrisen för vidare bearbetning

Här är `TakePhoto()`, den funktion som använder Xamarin Media plugin-programmet för foto avbildning.  

```csharp
async Task<byte[]> TakePhoto()
{
    MediaFile photoMediaFile = null;
    byte[] photoByteArray = null;

    if (CrossMedia.Current.IsCameraAvailable)
    {
        var mediaOptions = new StoreCameraMediaOptions
        {
            PhotoSize = PhotoSize.Medium,
            AllowCropping = true,
            SaveToAlbum = true,
            Name = $"{DateTime.UtcNow}.jpg"
        };
        photoMediaFile = await CrossMedia.Current.TakePhotoAsync(mediaOptions);
        photoByteArray = MediaFileToByteArray(photoMediaFile);
    }
    else
    {
        await DisplayAlert("Error", "No camera found", "OK");
        Console.WriteLine($"ERROR: No camera found");
    }
    return photoByteArray;
}
```
Ett foto importera verktyget fungerar på liknande sätt. Båda delarna av funktioner finns i `OcrSelectPage.xaml.cs`. 
 
> [!NOTE]
> Handskriven OCR slutpunkten kan endast hantera foton som är mindre än 4 MB. För att undvika problem med filens storlek kan vi minska bilden till 50% av den ursprungliga storleken genom att ange alternativet `PhotoSize = PhotoSize.Medium` på den `StoreCameraMediaOptions` objekt.  Om enheten tar ovanligt hög kvalitet foton och du får felmeddelanden, kan du försöka `PhotoSize = PhotoSize.Small` i stället.

Här är verktygsfunktionen som används för att konvertera en *MediaFile* i en byte-matris.

```csharp
byte[] MediaFileToByteArray(MediaFile photoMediaFile)
{
    using (var memStream = new MemoryStream())
    {
        photoMediaFile.GetStream().CopyTo(memStream);
        return memStream.ToArray();
    }
}
```

### <a name="ocr-results-page"></a>OCR resultatsidan

OCR resultatsidan visar text som hämtats från en avbildning med hjälp av den **Json.NET** [SelectToken metoden](http://www.newtonsoft.com/json/help/html/SelectToken.htm).  De två slutpunkterna OCR fungerar på olika sätt så att det är användbart att diskutera båda.  

Eftersom datorn Vision API finns bara på några serverplatser, måste dess slutpunkt genereras dynamiskt vid körning. Funktionen `SetOcrLocation` (en del av statiskhet *AppConstants* klass hittades i `AppConstants.cs`) anger platsen för URI-slutpunkten. Den motsvarar användarens val på sidan Lägg till nycklar eller använder värdet `App.xaml.cs`. 

```csharp
public static void SetOcrLocation(string location)
{
    ComputerVisionApiOcrUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/ocr?language=en&detectOrientation=true";
    ComputerVisionApiHandwritingUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/recognizeText?handwriting=true";
}
```

Låt oss ta en närmare titt på de här API-begäranden. Datorn Vision OCR API är kan tolka text från en obestämd språk, men vi säga att söka efter engelska texten för att förbättra resultatet. Vi kan också identifiera textorientering för oss API: et. Med hjälp av en fast orientering kan förbättra våra tolkning resultat, men orientering identifiering kan vara användbart i ett mobilt program.

Du kan lära dig mer om parametrarna som är tillgängliga med den här slutpunkten från den [utskrifts OCR API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fc).  

API: et för hand OCR är fortfarande under förhandsgranskning och för närvarande endast fungerar med text på engelska.  Därför är den enda parametern för närvarande en flagga som indikerar om att tolka handskriven text alls. Handskriven OCR API kan parsa både datorn utskriften och handskriven text, men `handwriting=false` ger bättre resultat på tryckt text. 

Eftersom det här programmet är på engelska, vi kan ha används handskriven OCR för det här exemplet och ange den `handwriting` flaggan enligt vad användaren talar om för oss att känna igen.  Vi använde båda slutpunkter som illustration.  

Du kan lära dig mer om parametrarna som är tillgängliga med den här slutpunkten från den [handskriven OCR API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200).

Nu ska vi titta på de funktioner som anropar API: erna.

`FetchPrintedWordList()` använder utskrifts OCR slutpunkten för att parsa utskrivna texten från bilder. HTTP-begäran följer en struktur liknande anropet som används på sidan Lägg till nycklar för att verifiera nyckeln, men vi behöver skicka ett foto. Ett foto är för stor för att skicka in en frågesträng, så vi måste använda en HTTP POST-begäran i stället för en GET-begäran. Vi behöver koda våra foto (som finns i minnet som en bytematris) till en `ByteArrayContent` objektet och lägga till ett sidhuvud till detta objekt som definierar typ av data som vi skickar. 

Du kan läsa om vilka typer av godkända innehåll i den [datorn Vision API-referens för](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200).  

```csharp
async Task<ObservableCollection<string>> FetchPrintedWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiOcrUrl, content);
        }

        string ResponseString = await response.Content.ReadAsStringAsync();
        JObject json = JObject.Parse(ResponseString);

        // Here, we pull down each "line" of text and then join it to 
        // make a string representing the entirety of each line.  
        // In the Handwritten endpoint, you are able to extract the 
        // "line" without any further processing.  If you would like 
        // to simply get a list of all extracted words,* you can do 
        // this with:
        // json.SelectTokens("$.regions[*].lines[*].words[*].text) 
        IEnumerable<JToken> lines = json.SelectTokens("$.regions[*].lines[*]");
        if (lines != null)
        {
            foreach (JToken line in lines)
            {
                IEnumerable<JToken> words = line.SelectTokens("$.words[*].text");
                if (words != null)
                {
                    wordList.Add(string.Join(" ", words.Select(x => x.ToString())));
                }
            }
        }
    }
    return wordList;
}
```
> [!TIP]
> Observera hur vi använder den **Json.NET** [SelectToken metoden](http://www.newtonsoft.com/json/help/html/SelectToken.htm) att extrahera text från svaret-objektet. `SelectToken` är idealiskt när vi behöver bara en del av JSON-svar som vi kan sedan överföra till nästa funktionen. I andra delar av kod vi deserialisera JSON-svar till modellobjekt som definierats i `ModelObjects.cs`.

Den främsta skillnaden mellan utskrifts OCR och handskriven OCR begäran är att tjänsten Print OCR returnerar den tolkade texten som en del av HTTP-svaret medan tjänsten handskriven OCR kräver en ytterligare begäran att hämta information om. Den första hand OCR-begäranden returnerar ett HTTP-202 status, som endast signalerar att bearbetningen har börjat. Det här svaret innehåller en slutpunkt som klienten måste kontrollera om du vill ha avslutat svar när den är tillgänglig. Se `FetchHandwrittenWordList()` vill se hur det fungerar.

```csharp
async Task<ObservableCollection<string>> FetchHandwrittenWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        // Make the POST request to the handwriting recognition URL
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiHandwritingUrl, content);
        }

        // Fetch results
        IEnumerable<string> operationLocationValues;
        string statusUri = string.Empty;
        if (response.Headers.TryGetValues("Operation-Location", out operationLocationValues))
        {
            statusUri = operationLocationValues.FirstOrDefault();

            // Ping status URL, wait for processing to finish 
            JObject obj = await FetchResultFromStatusUri(statusUri);
            IEnumerable<JToken> strings = obj.SelectTokens("$.recognitionResult.lines[*].text");
            foreach (string s in strings)
            {
                wordList.Add((string)s);
            }
        }
    }
    return wordList;
}
```

`FetchResultFromStatusUri()` är den andra delen av handskriftsigenkänning OCR-processen. Pingar den URI som hämtats från det första svaret metadata antingen tills en resultat eller funktionen timeout.  Det är viktigt att den här funktionen anropas asynkront på en egen tråd. Den här metoden skulle annars låser sig användargränssnittet tills bearbetningen har slutförts.

```csharp
// Takes in the url to check for handwritten text parsing results, and pings it per second until processing is finished
// Returns the JObject holding data for a successful parse
async Task<JObject> FetchResultFromStatusUri(string statusUri)
{
    JObject obj = null;
    int timeoutcounter = 0;
    HttpResponseMessage response = await visionApiClient.GetAsync(statusUri);
    string responseString = await response.Content.ReadAsStringAsync();
    obj = JObject.Parse(responseString);
    while ((!((string)obj.SelectToken("status")).Equals("Succeeded")) && (timeoutcounter++ < 60))
    {
        await Task.Delay(1000);
        response = await visionApiClient.GetAsync(statusUri);
        responseString = await response.Content.ReadAsStringAsync();
        obj = JObject.Parse(responseString);
    } 
    return obj;
}
```

### <a name="web-results-page"></a>Resultat för webbsida
När användaren väljer en sökterm som visas på sidan OCR-resultat, flytta vi till sidan Webbresultat.  Här vi skapar en [Bing Web Sök API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) begäran, skicka till tjänstens slutpunkt och deserialisera JSON-svar med den **Json.NET** [DeserializeObject](http://www.newtonsoft.com/json/help/html/DeserializeObject.htm) metod.  

```csharp
async Task<WebResultsList> GetQueryResults()
{
    // URL-encode the query term
    var queryString = System.Net.WebUtility.UrlEncode(queryTerm);

    // Here we encode the URL that will be used for the GET request to 
    // find query results.  Its arguments are as follows:
    // 
    // - [count=20] This sets the number of webpage objects returned at 
    //   "$.webpages" in the JSON response.  Currently, the API asks for 
    //   20 webpages in the response
    //
    // - [mkt=en-US] This sets the market where the results come from.
    //   Currently, the API looks for english results based in the 
    //   United States.
    //
    // - [q=queryString] This sets the string queried using the Search 
    //   API.   
    //
    // - [responseFilter=Webpages] This filters the response to only 
    //   include Webpage results.  This tag can take a comma seperated 
    //   list of response types that you are looking for.  If left 
    //   blank, all responses (webPages, News, Videos, etc) are 
    //   returned.
    //
    // - [setLang=en] This sets the languge for user interface strings. 
    //   To learn more about UI strings, check the Web Search API 
    //   reference.
    //
    // - [API Reference] https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference
    string uri = AppConstants.BingWebSearchApiUrl + $"count=20&mkt=en-US&q={queryString}&responseFilter=Webpages&setLang=en";

    // Make the HTTP Request
    WebResultsList webResults = null;
    HttpResponseMessage httpResponseMessage = await searchApiClient.GetAsync(uri);
    var responseContentString = await httpResponseMessage.Content.ReadAsStringAsync();
    JObject json = JObject.Parse(responseContentString);
    JToken resultBlock = json.SelectToken("$.webPages");
    if (resultBlock != null)
    {
        webResults = JsonConvert.DeserializeObject<WebResultsList>(resultBlock.ToString());
    }
    return webResults;
}
```

Bing webb-API för sökning fungerar bäst när du anger så mycket information som du kan om vad användaren kanske vill. Särskilt bör du alltid använda den `mkt` och `setLang` parametrar (här anges för amerikansk engelska) att identifiera dina användares plats och önskat språk.

> [!NOTE]
> Vi hållas Bing webbsökning frågan enkla för att kontrollera källkoden var det enkelt att förstå.  I ett riktigt program bör du lägga till följande huvuden HTTP-begäran för bättre resultat. 
> * Användaragent  
> * X-MSEdge-ClientID  
> * X-Sök-ClientIP  
> * X sökplats  
>
> Du kan lära dig mer om dessa värden i huvudet i den [Bing Web Sök API-referens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers)

## <a name="next-steps"></a>Nästa steg
Microsoft kognitiva Services innehåller många ytterligare tjänster som du lätt kan integrera i det här programmet.  Du kan till exempel:

* Lägg till [Bing enheten Sök](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) att utöka dina webb-sökresultat
* Växla i [Bing anpassad sökning](https://azure.microsoft.com/services/cognitive-services/bing-custom-search/) i stället för Bing webbsökning
* Använd den [Bing Image-sökning](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) bild insikter möjlighet att Lär dig mer om avbildningen och liknande bilder på webben
* Använda [Bing stavningskontroll](https://azure.microsoft.com/services/cognitive-services/spell-check/) att ytterligare förbättra kvaliteten på tolkade texten
* Integrera den [Microsoft Translator](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) att visa extraherade texten på olika språk
* Blanda och matcha services från den [kognitiva Services-portalen](https://azure.microsoft.com/services/cognitive-services/); sky har gränsen!

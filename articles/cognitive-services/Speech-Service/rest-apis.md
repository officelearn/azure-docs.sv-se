---
title: 'Speech Services REST API: er – Speech Services'
titleSuffix: Azure Cognitive Services
description: 'Lär dig hur du använder tal till text och text till tal REST API: erna. I den här artikeln får du lära dig om auktorisering, alternativ frågan, hur du strukturerar en begäran och får ett svar.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: c1693c5e04c33da3f21847aada0adfe1b66c4b52
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878093"
---
# <a name="speech-service-rest-apis"></a>Speech Service REST API: er

Som ett alternativ till den [tal SDK](speech-sdk.md), Speech Service kan du konvertera tal till text och text till tal med en uppsättning REST API: er. Varje tillgänglig slutpunkt är associerad med en region. Ditt program kräver en prenumerationsnyckel för den slutpunkt som du tänker använda.

Förstå innan du använder REST-API: er:
* Tal till text-begäranden med hjälp av REST-API kan bara innehålla 10 sekunder för ljud.
* Tal till text REST API: et returnerar endast slutliga resultaten. Ofullständiga resultat tillhandahålls inte.
* Text till tal REST-API kräver ingen auktoriseringsrubrik. Det innebär att du måste utföra en token exchange för att komma åt tjänsten. Mer information finns i [Autentisering](#authentication).

## <a name="authentication"></a>Autentisering

Varje begäran antingen tal till text eller text till tal REST-API: et kräver ingen auktoriseringsrubrik. Den här tabellen visar vilka rubriker stöds för varje tjänst:

| Stöds auktorisering rubriker | Tal-till-text | Text till tal |
|------------------------|----------------|----------------|
| OCP-Apim-Subscription-Key | Ja | Nej |
| Auktorisering: Ägar | Ja | Ja |

När du använder den `Ocp-Apim-Subscription-Key` rubrik, måste du bara ange din prenumerationsnyckel. Exempel:

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

När du använder den `Authorization: Bearer` rubrik, är du behöver göra en begäran om att den `issueToken` slutpunkt. I den här begäran, kan du byta din prenumerationsnyckel för en åtkomsttoken som är giltig i 10 minuter. I kommande avsnitt lär du dig att hämta en token, använda en token och uppdatera en token.

### <a name="how-to-get-an-access-token"></a>Så här hämtar du en åtkomsttoken

Om du vill få en åtkomsttoken, måste du begära att de `issueToken` slutpunkten med den `Ocp-Apim-Subscription-Key` och din prenumerationsnyckel.

Dessa regioner och slutpunkter stöds:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Använd de här exemplen för att skapa din begäran om åtkomsttoken.

#### <a name="http-sample"></a>HTTP-exempel

Det här exemplet är en enkel HTTP-begäran att hämta en token. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumerationsnyckel för Speech Service. Om prenumerationen inte är i regionen USA, västra, ersätter den `Host` huvud med värdnamnet för din region.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Brödtexten i svaret innehåller åtkomsttoken i Java Web Token (JWT)-format.

#### <a name="powershell-sample"></a>PowerShell-exempel

Det här exemplet är ett enkelt PowerShell.skript för att få en åtkomsttoken. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumerationsnyckel för Speech Service. Se till att använda rätt slutpunkt för den region som matchar din prenumeration. Det här exemplet är inställd till USA, västra.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>cURL-exempel

cURL är ett kommandoradsverktyg som är tillgängliga i Linux (och i Windows-undersystem för Linux). Den här cURL-kommando visar hur du kan få en åtkomsttoken. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumerationsnyckel för Speech Service. Se till att använda rätt slutpunkt för den region som matchar din prenumeration. Det här exemplet är inställd till USA, västra.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C#-exempel

Detta C# klassen visar hur du kan få en åtkomsttoken. Skicka din prenumerationsnyckel för Speech Service när du skapa en instans av klassen. Om prenumerationen inte är i regionen USA, västra, ändra värdet för `FetchTokenUri` så att den matchar regionen för din prenumeration.

```cs
/*
    * This class demonstrates how to get a valid access token.
    */
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="how-to-use-an-access-token"></a>Hur du använder en åtkomsttoken

Åtkomst-token ska skickas till tjänsten som den `Authorization: Bearer <TOKEN>` rubrik. Varje åtkomsttoken är giltig i 10 minuter. Du kan hämta en ny token när som helst, men för att minimera nätverkstrafiken och svarstid, rekommenderas att använda samma token i nio minuter.

Här är ett exempel HTTP-begäran till text till tal REST-API:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="how-to-renew-an-access-token-using-c"></a>Hur du förnyar ett åtkomst-token med hjälp avC#

Detta C# koden är en lättillgänglig ersättning till den klass som presenteras tidigare. Den `Authentication` klass hämtar automatiskt en ny åtkomsttoken varje nio minuter med hjälp av en timer. Den här metoden garanterar att en giltig token alltid är tillgänglig medan programmet körs.

> [!NOTE]
> Du kan lagra en tidsstämpel för när den sista token hämtades istället för att använda en timer. Sedan kan du begära en ny endast om den är nära upphör att gälla. Den här metoden undviker begär nya token onödigt och kan passa bättre för program som begär tal sporadiska.

Som tidigare se till att den `FetchTokenUri` värdet matchar din region för prenumerationen. Skicka din prenumerationsnyckel när du skapa en instans av klassen.

```cs
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;
    private Timer accessTokenRenewer;

    //Access token expires every 10 minutes. Renew it every 9 minutes.
    private const int RefreshTokenDuration = 9;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

        // renew the token on set duration.
        accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                        this,
                                        TimeSpan.FromMinutes(RefreshTokenDuration),
                                        TimeSpan.FromMilliseconds(-1));
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private void RenewAccessToken()
    {
        this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
        Console.WriteLine("Renewed token.");
    }

    private void OnTokenExpiredCallback(object stateInfo)
    {
        try
        {
            RenewAccessToken();
        }
        catch (Exception ex)
        {
            Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
        }
        finally
        {
            try
            {
                accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
            }
        }
    }

    private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

## <a name="speech-to-text-api"></a>Tal till text-API

Tal till text REST API har endast stöd för korta yttranden. Begäranden kan innehålla upp till 10 sekunder ljud med en total varaktighet för 14 sekunder. REST API: et returnerar bara de slutliga resultaten, inte partiell eller mellanliggande resultat.

Om du skickar längre ljud är ett krav för ditt program kan du använda den [tal SDK](speech-sdk.md) eller [batch avskrift](batch-transcription.md).

### <a name="regions-and-endpoints"></a>Regioner och slutpunkter

Dessa regioner har stöd för tal till text-avskrift med hjälp av REST-API. Kontrollera att du väljer den slutpunkt som matchar din region för prenumerationen.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="query-parameters"></a>Frågeparametrar

Dessa parametrar kan ingå i frågesträngen för REST-begäran.

| Parameter | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| `language` | Identifierar talat språk som tolkas. Se [språk som stöds](language-support.md#speech-to-text). | Krävs |
| `format` | Anger resultatformatet. Godkända värden är `simple` och `detailed`. Enkel resultatet inkluderar `RecognitionStatus`, `DisplayText`, `Offset`, och `Duration`. Detaljerad respons omfattar flera resultat med tillförsikt värden och fyra olika representationer. Standardinställningen är `simple`. | Valfri |
| `profanity` | Anger hur du hanterar svordomar i igenkänningsresultat. Godkända värden är `masked`, som ersätter svordomar med asterisker `removed`, vilket tar bort alla svordomar från resultatet, eller `raw`, som innehåller svordomar i resultatet. Standardinställningen är `masked`. | Valfri |

### <a name="request-headers"></a>Begärandehuvud

Den här tabellen innehåller obligatoriska och valfria rubriker för tal till text-begäranden.

|Huvud| Beskrivning | Obligatoriskt / valfritt |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Prenumerationsnyckeln Speech Service. | Antingen den här rubriken eller `Authorization` krävs. |
| `Authorization` | En autentiseringstoken föregås av ordet `Bearer`. Mer information finns i [Autentisering](#authentication). | Antingen den här rubriken eller `Ocp-Apim-Subscription-Key` krävs. |
| `Content-type` | Beskriver format och codec-enheten för den angivna ljuddata. Godkända värden är `audio/wav; codecs=audio/pcm; samplerate=16000` och `audio/ogg; codecs=opus`. | Krävs |
| `Transfer-Encoding` | Anger att segmenterat ljuddata skickas, i stället för en enskild fil. Använd bara den här rubriken om storlekar ljuddata. | Valfri |
| `Expect` | Om du använder med chunked skicka `Expect: 100-continue`. Med Taltjänsten medvetna om den första begäran och väntar på dig ytterligare data.| Krävs om du skickar segmenterade ljuddata. |
| `Accept` | Om det måste vara `application/json`. Med Taltjänsten ger resultat i JSON. Vissa webbramverk för begäran innehåller ett inkompatibelt standardvärdet om du inte anger något, så det är bra att alltid `Accept`. | Valfritt men rekommenderas. |

### <a name="audio-formats"></a>Ljudformat

Ljud skickas i brödtexten i HTTP `POST` begäran. Det måste vara i något av formaten i den här tabellen:

| Format | Codec | Bithastighet | Samplingshastighet |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bitars | 16 kHz, mono |
| OGG | OPUS | 16-bitars | 16 kHz, mono |

>[!NOTE]
>Formaten ovan stöds via REST-API och WebSocket i Speech-tjänsten. Den [tal SDK](speech-sdk.md) för närvarande endast stöd för WAV formatera med PCM-codec.

### <a name="sample-request"></a>Exempelbegäran

Det här är en vanliga HTTP-begäran. Exemplet nedan innehåller värdnamn och nödvändiga rubriker. Det är viktigt att notera att tjänsten förväntar sig även ljuddata som inte ingår i det här exemplet. Som nämnts tidigare, rekommenderas storlekar, men krävs inte.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status-codes"></a>HTTP-statuskoder

HTTP-statuskod för varje svar anger lyckad eller vanliga fel.

| HTTP-statuskod | Beskrivning | Möjlig orsak |
|------------------|-------------|-----------------|
| 100 | Fortsätt | Den första begäran har godkänts. Gå vidare med att skicka resten av data. (Används med segmentvis överföring). |
| 200 | Ok | Begäran lyckades. svarstexten är ett JSON-objekt. |
| 400 | Felaktig förfrågan | Språkkod inte eller är inte ett språk som stöds. Ogiltig ljudfil. |
| 401 | Behörighet saknas | Prenumerationsnyckel eller auktorisering token är ogiltig i regionen eller ogiltig slutpunkt. |
| 403 | Förbjudna | Prenumerationsnyckel eller auktorisering saknas token. |

### <a name="chunked-transfer"></a>Segmentvis överföring

Segmentvis överföring (`Transfer-Encoding: chunked`) kan hjälpa dig att minska svarstiden för igenkänning av eftersom den tillåter Speech-tjänsten ska börja bearbeta ljudfilen medan den överförs. REST API: et tillhandahåller inte partiell eller mellanliggande resultat. Det här alternativet är avsedd endast för att förbättra svarstiden.

Detta kodexempel visar hur du skickar ljud i segment. Endast det första segmentet ska innehålla ljud filens huvud. `request` ett objekt i HTTPWebRequest är ansluten till rätt REST-slutpunkten. `audioFile` är sökvägen till en ljudfil på disken.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

### <a name="response-parameters"></a>Svarsparametrar

Resultaten anges som JSON. Den `simple` format innehåller fälten på översta nivån.

| Parameter | Beskrivning  |
|-----------|--------------|
|`RecognitionStatus`|Status, till exempel `Success` för lyckad erkännande. Se nästa tabell.|
|`DisplayText`|Den tolkade texten efter versaler, skiljetecken, inverterade text normalisering (konvertera tal till kortare former, till exempel 200 för ”tvåhundra” eller ”Dr. Smith ”för” läkare smith ”), och svordomar Maskning. Visa endast om åtgärden lyckades.|
|`Offset`|Tid (i 100 nanosekunder enheter) som okänt tal som börjar gälla i ljudströmmen.|
|`Duration`|Tiden (i 100 nanosekunder enheter) för den identifierade tal i ljudströmmen.|

Den `RecognitionStatus` fältet får innehålla dessa värden:

| Status | Beskrivning |
|--------|-------------|
| `Success` | Erkännande lyckades och `DisplayText` fältet finns. |
| `NoMatch` | Tal påträffades i ljudströmmen, men inga ord från målspråket som kunde matchas. Oftast igenkänning av språket är ett annat språk än den användaren som talar. |
| `InitialSilenceTimeout` | Början av ljudströmmen innehöll endast tystnad och tjänsten tidsgränsen för tal. |
| `BabbleTimeout` | Början av ljudströmmen innehöll endast bruset och tjänsten tidsgränsen för tal. |
| `Error` | Igenkänning av tjänsten påträffade ett internt fel och kunde inte fortsätta. Försök igen om det är möjligt. |

> [!NOTE]
> Om ljudet består endast av svordomar, och `profanity` Frågeparametern anges till `remove`, tjänsten inte returnerar ett tal resultat.

Den `detailed` format innehåller samma data som den `simple` format tillsammans med `NBest`, en lista över alternativ tolkningar av detta igenkänning av tal. De här resultaten returneras rangordnas från det mest sannolikt minst sannolikt den första posten är samma som den huvudsakliga igenkänningsresultatet.  När du använder den `detailed` format, `DisplayText` tillhandahålls som `Display` för varje resultat i den `NBest` lista.

Varje objekt i den `NBest` listan innehåller:

| Parameter | Beskrivning |
|-----------|-------------|
| `Confidence` | Förtroendepoäng för registerposten från 0,0 (inget förtroende) 1.0 (fullt förtroende) |
| `Lexical` | Lexikal form av den tolkade texten: de ord känns igen. |
| `ITN` | (”Canonical”) inverterade-text-normaliserat form av den tolkade texten med phone tal, tal, förkortningar (”läkare smith” till ”dr smith”) och andra omformningen. |
| `MaskedITN` | Formuläret ITN med svordomar maskning tillämpas, om så krävs. |
| `Display` | Visningsformulär för den tolkade texten med skiljetecken och gemener/versaler har lagts till. Den här parametern är samma som `DisplayText` anges när formatet är inställt på `simple`. |

### <a name="sample-responses"></a>Exempel-svar

Detta är ett typiskt svar för `simple` erkännande.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Detta är ett typiskt svar för `detailed` erkännande.

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="text-to-speech-api"></a>Text till tal-API

Text till tal REST API har stöd för neurala och standard text till tal röster, som stöder ett visst språk och dialekt som identifieras av nationella inställningar.

* En fullständig lista över röster Se [språkstöd](language-support.md#text-to-speech).
* Information om regional tillgänglighet finns i [regioner](regions.md#text-to-speech).

> [!IMPORTANT]
> Kostnader för olika för standard, anpassade och neural röster. Mer information finns i [priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="request-headers"></a>Begärandehuvud

Den här tabellen innehåller obligatoriska och valfria rubriker för tal till text-begäranden.

| Huvud | Beskrivning | Obligatoriskt / valfritt |
|--------|-------------|---------------------|
| `Authorization` | En autentiseringstoken föregås av ordet `Bearer`. Mer information finns i [autentisering](#authentication). | Krävs |
| `Content-Type` | Anger innehållstypen för den angivna texten. Godkänt värde: `application/ssml+xml`. | Krävs |
| `X-Microsoft-OutputFormat` | Anger formatet för ljuduppspelning. En fullständig lista över godkända värden, se [ljud utdata](#audio-outputs). | Krävs |
| `User-Agent` | Programnamnet. Det måste vara färre än 255 tecken. | Krävs |

### <a name="audio-outputs"></a>Ljud utdata

Detta är en lista över de format som ljud som skickas i varje begäran som den `X-Microsoft-OutputFormat` rubrik. Var och en innehåller en bithastigheten och kodningstyp. Speech-tjänsten stöder 24-KHz, 16 KHz och 8 KHz ljud matar ut.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Om din valda röst- och utdataformat har olika bithastigheter, samplas ljudet efter behov. Dock stöder inte 24khz röster `audio-16khz-16kbps-mono-siren` och `riff-16khz-16kbps-mono-siren` utdataformat.

### <a name="request-body"></a>Begärandetext

Brödtexten i var och en `POST` begäran skickas som [tal syntes Markup Language (SSML)](speech-synthesis-markup.md). SSML kan du välja rösten och språket för syntetiskt tal returneras av text till tal-tjänsten. En fullständig lista över stöds röster Se [språkstöd](language-support.md#text-to-speech).

> [!NOTE]
> Om du använder en anpassad röst kan brödtexten i en begäran skickas som oformaterad text (ASCII- eller UTF-8).

### <a name="sample-request"></a>Exempelbegäran

Den här HTTP-begäran använder SSML för att ange röst- och språk. Brödtexten får inte överskrida 1 000 tecken.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>HTTP-statuskoder

HTTP-statuskod för varje svar anger lyckad eller vanliga fel.

| HTTP-statuskod | Beskrivning | Möjlig orsak |
|------------------|-------------|-----------------|
| 200 | Ok | Begäran lyckades. svarstexten är en ljudfil. |
| 400 | Felaktig begäran | En obligatorisk parameter är tom, null eller saknas. Eller värdet som skickas till antingen en obligatorisk eller valfri parameter är ogiltig. Ett vanligt problem är en rubrik som är för lång. |
| 401 | Behörighet saknas | Begäran har inte behörighet. Kontrollera att din prenumerationsnyckel eller token är giltig och i rätt region. |
| 413 | Begäran om entiteten är för stor | SSML-indata är längre än 1024 tecken. |
| 429 | För många begäranden | Du har överskridit kvoten eller antalet begäranden som tillåts för din prenumeration. |
| 502 | Felaktig gateway | Problem med nätverket eller servern. Kan också vara ogiltiga sidhuvuden. |

Om HTTP-status är `200 OK`, brödtexten i svaret innehåller en ljudfil i det begärda formatet. Den här filen kan spelas upp när den har överförts, sparas i en buffert eller sparas som en fil.

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)

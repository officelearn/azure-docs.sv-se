---
title: 'Speech Service REST API: er'
description: 'Referens för REST API: er för Taltjänst.'
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: erhopf
ms.openlocfilehash: 7f3daf71f4d94371af5f7d98c4e03761d7217a2a
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025845"
---
# <a name="speech-service-rest-apis"></a>Speech Service REST API: er

REST-API: er för tjänsten Azure Cognitive Services tal liknar API: er som tillhandahålls av den [taligenkänning för Bing](https://docs.microsoft.com/azure/cognitive-services/Speech). Slutpunkterna skiljer sig från de slutpunkter som används av tjänsten för Bing-tal. Regionala slutpunkter är tillgängliga och du måste använda en prenumerationsnyckel som motsvarar den slutpunkt som du använder.

## <a name="speech-to-text"></a>Tal till text

Slutpunkter för tal till Text REST API visas i följande tabell. Använd det som matchar din region för prenumerationen.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

> [!NOTE]
> Om du har anpassat den akustiska modellen eller språkmodell eller uttal, använder du din anpassade slutpunkt.

Detta API stöder endast kort yttranden. Begäranden kan innehålla upp till 10 sekunder ljud och de senaste 14 sekunder övergripande maximalt. REST API: et returnerar endast slutresultat inte partiell eller mellanliggande resultat. Speech-tjänsten har också en [batch avskrift](batch-transcription.md) API som kan transkribera längre ljud.


### <a name="query-parameters"></a>Frågeparametrar

Följande parametrar kan ingå i frågesträngen för REST-begäran.

|Parameternamn|Krävs/valfritt|Betydelse|
|-|-|-|
|`language`|Krävs|Identifierare för språket som ska identifieras. Se [språk som stöds](language-support.md#speech-to-text).|
|`format`|Valfri<br>standard: `simple`|Resultatformatet, `simple` eller `detailed`. Enkel resultatet inkluderar `RecognitionStatus`, `DisplayText`, `Offset`, och varaktighet. Detaljerade resultat omfattar flera kandidater med tillförsikt värden och fyra olika representationer.|
|`profanity`|Valfri<br>standard: `masked`|Hur du hanterar svordomar i igenkänningsresultat. Kan vara `masked` (ersätter olämpligt språk med en asterisk), `removed` (tar bort alla svordomar) eller `raw` (inklusive svordomar).

### <a name="request-headers"></a>Begärandehuvud

Följande fält skickas i HTTP-frågehuvudet.

|Huvud|Betydelse|
|------|-------|
|`Ocp-Apim-Subscription-Key`|Speech service prenumerationsnyckeln. Antingen den här rubriken eller `Authorization` måste anges.|
|`Authorization`|En autentiseringstoken föregås av ordet `Bearer`. Antingen den här rubriken eller `Ocp-Apim-Subscription-Key` måste anges. Se [autentisering](#authentication).|
|`Content-type`|Beskriver format och codec för ljuddata. För närvarande kan det här värdet måste vara `audio/wav; codec=audio/pcm; samplerate=16000`.|
|`Transfer-Encoding`|Valfri. Om du måste vara `chunked` att tillåta ljuddata skickas i flera små segment i stället för en enskild fil.|
|`Expect`|Om du använder med chunked skicka `Expect: 100-continue`. Med taltjänsten medvetna om den första begäran och väntar på dig ytterligare data.|
|`Accept`|Valfri. Om anges måste innehålla `application/json`, som tal-tjänst ger resultat i JSON-format. (Vissa webbramverk för begäran innehåller ett inkompatibelt standardvärdet om du inte anger något, så det är bra att alltid `Accept`.)|

### <a name="audio-format"></a>Ljudformatet

Ljudet skickas i brödtexten i HTTP `POST` begäran. Det bör vara 16-bitars WAV format med PCM en kanal (mono) på 16 KHz av följande format/kodningen.

* WAV format med PCM-codec
* OGG format med OPUS codec

>[!NOTE]
>Formaten ovan stöds via REST-API och WebSocket i Speech-tjänsten. Den [tal SDK](/index.yml) för närvarande endast stöd för WAV formatera med PCM-codec.

### <a name="chunked-transfer"></a>Segmentvis överföring

Segmentvis överföring (`Transfer-Encoding: chunked`) kan hjälpa dig att minska svarstiden för igenkänning av eftersom den tillåter Speech-tjänsten ska börja bearbeta ljudfilen medan den överförs. REST API: et tillhandahåller inte partiell eller mellanliggande resultat. Det här alternativet är avsedd endast för att förbättra svarstiden.

Följande kod visar hur du skickar ljud i segment. Endast det första segmentet ska innehålla ljud filens huvud. `request` ett objekt i HTTPWebRequest är ansluten till rätt REST-slutpunkten. `audioFile` är sökvägen till en ljudfil på disken.

```csharp
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

### <a name="example-request"></a>Exempelbegäran

Följande är en typisk begäran.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status"></a>HTTP-status

HTTP-status för svaret anger lyckad eller vanliga felvillkor.

HTTP-kod|Betydelse|Möjlig orsak
-|-|-|
100|Fortsätt|Den första begäran har godkänts. Gå vidare med att skicka resten av data. (Används med segmentvis överföring).
200|Ok|Begäran lyckades. svarstexten är ett JSON-objekt.
400|Felaktig förfrågan|Språkkod inte eller är inte ett språk som stöds. Ogiltig ljudfil.
401|Behörighet saknas|Prenumerationsnyckel eller auktorisering token är ogiltig i regionen eller ogiltig slutpunkt.
403|Förbjudna|Prenumerationsnyckel eller auktorisering saknas token.

### <a name="json-response"></a>JSON-svar

Resultaten returneras i JSON-format. Den `simple` format innehåller endast följande översta fält.

|Fältnamn|Innehåll|
|-|-|
|`RecognitionStatus`|Status, till exempel `Success` för lyckad erkännande. Se nästa tabell.|
|`DisplayText`|Den tolkade texten efter versaler, skiljetecken, inverterade text normalisering (konvertera tal till kortare former, till exempel 200 för ”tvåhundra” eller ”Dr. Smith ”för” läkare smith ”), och svordomar Maskning. Visa endast om åtgärden lyckades.|
|`Offset`|Tid (i 100 nanosekunder enheter) som okänt tal som börjar gälla i ljudströmmen.|
|`Duration`|Tiden (i 100 nanosekunder enheter) för den identifierade tal i ljudströmmen.|

Den `RecognitionStatus` fältet kan innehålla följande värden.

|Statusvärde|Beskrivning
|-|-|
| `Success` | Erkännande lyckades och fältet text finns. |
| `NoMatch` | Tal påträffades i ljudströmmen, men inga ord från målspråket som kunde matchas. Oftast igenkänning av språket är ett annat språk än den användaren som talar. |
| `InitialSilenceTimeout` | Början av ljudströmmen innehöll endast tystnad och tjänsten tidsgränsen för tal. |
| `BabbleTimeout` | Början av ljudströmmen innehöll endast bruset och tjänsten tidsgränsen för tal. |
| `Error` | Igenkänning av tjänsten påträffade ett internt fel och kunde inte fortsätta. Försök igen om det är möjligt. |

> [!NOTE]
> Om ljudet består endast av svordomar, och `profanity` Frågeparametern anges till `remove`, tjänsten inte returnerar ett tal resultat.


Den `detailed` format innehåller samma fält som den `simple` format tillsammans med en `NBest` fält. Den `NBest` fält är en lista över alternativ tolkningar av samma tal, rangordnas från mest sannolikt minst sannolikt. Den första posten är samma som den huvudsakliga igenkänningsresultatet. Varje post innehåller följande fält:

|Fältnamn|Innehåll|
|-|-|
|`Confidence`|Förtroendepoäng för registerposten från 0,0 (inget förtroende) 1.0 (fullt förtroende)
|`Lexical`|Lexikal form av den tolkade texten: de ord känns igen.
|`ITN`|(”Canonical”) inverterade-text-normaliserat form av den tolkade texten med phone tal, tal, förkortningar (”läkare smith” till ”dr smith”) och andra omformningen.
|`MaskedITN`| Formuläret ITN med svordomar maskning tillämpas, om så krävs.
|`Display`| Visningsformulär för den tolkade texten med skiljetecken och gemener/versaler har lagts till. Samma som `DisplayText` i översta resultat.

### <a name="sample-responses"></a>Exempel-svar

Följande är ett typiskt svar för `simple` erkännande.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Följande är ett typiskt svar för `detailed` erkännande.

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

## <a name="text-to-speech"></a>Text till tal

Följande är REST-slutpunkter för tjänsten tal Text till tal-API. Använd den slutpunkt som matchar din region för prenumerationen.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

Speech-tjänsten stöder 24-KHz ljuduppspelning förutom 16 Khz utdata som stöds av Bing-taligenkänning. Fyra 24-KHz utdataformat är tillgängliga för användning i den `X-Microsoft-OutputFormat` HTTP-huvud, som är två 24-KHz röster `Jessa24kRUS` och `Guy24kRUS`.

Nationell inställning | Språk   | Kön | Tjänsten Namnmappningen
-------|------------|--------|------------
sv-SE  | Svenska | Kvinna | ”Microsoft Server tal Text till tal-röst (en-US, Jessa24kRUS)”
sv-SE  | Svenska | Man   | ”Microsoft Server tal Text till tal-röst (en-US, Guy24kRUS)”

En fullständig lista över tillgängliga röster är tillgängliga i [språk som stöds](language-support.md#text-to-speech).

### <a name="request-headers"></a>Begärandehuvud

Följande fält skickas i HTTP-frågehuvudet.

|Huvud|Betydelse|
|------|-------|
|`Authorization`|En autentiseringstoken föregås av ordet `Bearer`. Krävs. Se [autentisering](#authentication).|
|`Content-Type`|Inkommande innehållstyp: `application/ssml+xml`.|
|`X-Microsoft-OutputFormat`|Ljudformatet utdata. Se nästa tabell.|
|`User-Agent`|Programnamnet. Krävs. måste innehålla färre än 255 tecken.|

Tillgängliga ljudet utdataformat (`X-Microsoft-OutputFormat`) en bithastighet och kodning.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `raw-8khz-8bit-mono-mulaw`
`riff-8khz-8bit-mono-mulaw`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

> [!NOTE]
> Om din valda röst- och utdataformat har olika bithastigheter, samplas ljudet efter behov. Dock stöder inte 24khz röster `audio-16khz-16kbps-mono-siren` och `riff-16khz-16kbps-mono-siren` utdataformat.

### <a name="request-body"></a>Begärandetext

Texten som ska konverteras till tal skickas som en del av en HTTP `POST` begär i oformaterad text (ASCII- eller UTF-8) eller [tal syntes Markup Language](speech-synthesis-markup.md) (SSML)-format (UTF-8). Oformaterad text begäranden använda tjänstens standard röst- och språk. Skicka SSML att använda en annan röst.

### <a name="sample-request"></a>Exempelbegäran

Följande HTTP-begäran använder en SSML brödtext för att välja röst. Innehållet måste vara längre än 1 000 tecken.

```xml
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

### <a name="http-response"></a>HTTP-svar

HTTP-status för svaret anger lyckad eller vanliga felvillkor.

HTTP-kod|Betydelse|Möjlig orsak
-|-|-|
200|Ok|Begäran lyckades. svarstexten är en ljudfil.
400 |Felaktig begäran |En obligatorisk parameter är tom, null eller saknas. Eller värdet som skickas till antingen en obligatorisk eller valfri parameter är ogiltig. Ett vanligt problem är en rubrik som är för lång.
401|Behörighet saknas |Begäran har inte behörighet. Kontrollera att din prenumerationsnyckel eller token är giltig och i rätt region.
413|Begäran om entiteten är för stor|SSML-indata är längre än 1024 tecken.
429|För många begäranden|Du har överskridit kvoten eller antalet begäranden som tillåts för din prenumeration.
502|Felaktig gateway | Problem med nätverket eller servern. Kan också vara ogiltiga sidhuvuden.

Om HTTP-status är `200 OK`, brödtexten i svaret innehåller en ljudfil i det begärda formatet. Den här filen kan spelas upp när den överförs eller sparas till en buffert eller fil för senare uppspelning eller annan användning.

## <a name="authentication"></a>Autentisering

Skicka en begäran till tal-tjänstens REST-API krävs en prenumerationsnyckel eller en åtkomst-token. I allmänhet är det enklast att skicka prenumerationsnyckeln direkt. Med taltjänsten hämtar sedan åtkomsttoken för dig. För att minimera svarstiden, kanske du vill använda en åtkomst-token i stället.

Du kan hämta en token genom att presentera din prenumerationsnyckel till en regional taltjänst `issueToken` slutpunkt, som visas i följande tabell. Använd den slutpunkt som matchar din region för prenumerationen.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Varje åtkomsttoken är giltig i 10 minuter. Du kan hämta en ny token när som helst. Om du vill kan du hämta en token precis innan varje tal REST API-begäran. För att minimera nätverkstrafiken och svarstid, bör du använda samma token i nio minuter.

I följande avsnitt visas hur du får en token och hur du använder den i en begäran.

### <a name="get-a-token-http"></a>Hämta en token: HTTP

I följande exempel är en HTTP-begäran för exemplet för att hämta en token. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumerationsnyckel för tal-tjänst. Om prenumerationen inte är i regionen USA, västra, ersätter den `Host` huvud med värdnamnet för din region.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Brödtexten i svaret på den här begäran är den åtkomst-token i Java Web Token (JWT)-format.

### <a name="get-a-token-powershell"></a>Hämta en token: PowerShell

Följande Windows PowerShell-skript visar hur du kan hämta en åtkomsttoken. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumerationsnyckel för tal-tjänst. Om prenumerationen inte är i regionen USA, västra, ändras namnet på den angivna URI: N.

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

### <a name="get-a-token-curl"></a>Hämta en token: cURL

cURL är ett kommandoradsverktyg som är tillgängliga i Linux (och i Windows-undersystem för Linux). Följande cURL-kommando visar hur du kan hämta en åtkomsttoken. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumerationsnyckel för tal-tjänst. Om prenumerationen inte är i regionen USA, västra, ändras namnet på den angivna URI: N.

> [!NOTE]
> Kommandot visas på flera rader för läsbarhet, men ange den på en enda rad i en shell-prompten.

```
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
 -H "Content-type: application/x-www-form-urlencoded"
 -H "Content-Length: 0"
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="get-a-token-c"></a>Hämta en token: C#

Följande C#-klass illustrerar hur du skaffar en åtkomst-token. Skicka din prenumerationsnyckel för tal-tjänst när du skapa en instans av klassen. Om prenumerationen inte är i regionen USA, västra, ändra värdnamnet för `FetchTokenUri` på rätt sätt.

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

### <a name="use-a-token"></a>Använda en token

Om du vill använda en token i en REST API-begäran, kan du ange den i den `Authorization` rubrik som följer efter ordet `Bearer`. Här är ett exempel Text till tal-REST-begäran som innehåller en token. Ersätta din faktiska token för `YOUR_ACCESS_TOKEN`. Använd rätt värdnamnet i den `Host` rubrik.

```xml
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

### <a name="renew-authorization"></a>Förnya auktorisationen

Autentiseringstoken upphör att gälla efter 10 minuter. Förnya din behörighet genom att hämta en ny token innan den upphör. Exempelvis kan du skaffa en ny token efter nio minuter.

Följande C#-kod är en lättillgänglig ersättning till den klass som presenteras tidigare. Den `Authentication` klass automatiskt hämtar en ny åtkomsttoken varje nio minuter med hjälp av en timer. Den här metoden garanterar att en giltig token alltid är tillgänglig medan programmet körs.

> [!NOTE]
> Du kan lagra en tidsstämpel för när den sista token hämtades istället för att använda en timer. Sedan kan du begära en ny endast om den är nära upphör att gälla. Den här metoden undviker begär nya token onödigt och kan passa bättre för program som begär tal sporadiska.

Som tidigare se till att den `FetchTokenUri` värdet matchar din region för prenumerationen. Skicka din prenumerationsnyckel när du skapa en instans av klassen.

```cs
/*
    * This class demonstrates how to maintain a valid access token.
    */
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

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)

---
title: 'Taltjänst REST API: er'
description: 'Referens för REST API: er för tal-tjänst.'
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: 64dce26303c0e700da54d371af5cb275b1613d70
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122111"
---
# <a name="speech-service-rest-apis"></a>Taltjänst REST API: er

REST-API: er av enhetliga Speech-tjänsten som liknar de API: er som tillhandahålls av den [Taligenkänning](https://docs.microsoft.com/azure/cognitive-services/Speech) (kallades tidigare Bing Speech Service). Slutpunkterna skiljer sig från de slutpunkter som används av föregående Speech-tjänsten.

## <a name="speech-to-text"></a>Tal till text

I tal till Text-API, de slutpunkter som används som skiljer sig från föregående taltjänsten tal-API: T. De nya slutpunkterna visas i tabellen nedan. Använd det som matchar din region för prenumerationen.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

Tal till Text API är annars liknar den [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) för tidigare API för taligenkänning.

Tal till Text REST API stöder endast kort yttranden. Begäranden kan innehålla upp till 10 sekunder ljud och de senaste 14 sekunder övergripande maximalt. REST API: et returnerar endast slutresultat inte partiell eller mellanliggande resultat.

> [!NOTE]
> Om du har anpassat den akustiska modellen eller språkmodell eller uttal, använder du din anpassade slutpunkt.

## <a name="text-to-speech"></a>Text till tal

Den nya Text till tal-API har stöd för 24-KHz ljuduppspelning. Den `X-Microsoft-OutputFormat` huvudet innehåller nu följande värden.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

Med taltjänsten innehåller nu två 24-KHz röster:

Nationella inställningar | Språk   | Kön | Tjänsten Namnmappningen
-------|------------|--------|------------
sv-SE  | Svenska | Kvinna | ”Microsoft Server tal Text till tal-röst (en-US, Jessa24kRUS)” 
sv-SE  | Svenska | Man   | ”Microsoft Server tal Text till tal-röst (en-US, Guy24kRUS)”

Följande är REST-slutpunkter för den enhetliga service Text till tal API. Använd den slutpunkt som matchar din region för prenumerationen.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

Hålla dessa skillnader i åtanke när du refererar till den [REST API-dokumentation](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput) för tidigare API för taligenkänning.

## <a name="authentication"></a>Autentisering

Skicka en begäran till tal-tjänstens REST-API kräver en åtkomst-token. Du hämtar en token genom att tillhandahålla din prenumerationsnyckel till en regional taltjänst `issueToken` slutpunkt kan visas i tabellen nedan. Använd den slutpunkt som matchar din region för prenumerationen.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Varje åtkomsttoken är giltig i 10 minuter. Du kan hämta en ny token när som helst – om du vill, inklusive, precis innan varje tal REST API-begäran. För att minimera nätverkstrafiken och svarstid, men bör du använda samma token i nio minuter.

I följande avsnitt visas hur du får en token och hur du använder den i en begäran.

### <a name="getting-a-token-http"></a>Få en token: HTTP

Nedan visas ett exempel HTTP-begäran för att hämta en token. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumerationsnyckel för tal-tjänst. Om prenumerationen inte är i regionen USA, västra, ersätter den `Host` huvud med värdnamnet för din region.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Brödtexten i svaret på den här begäran är den åtkomst-token i Java Web Token (JWT)-format.

### <a name="getting-a-token-powershell"></a>Få en token: PowerShell

Windows PowerShell-skriptet nedan visar hur du kan hämta en åtkomsttoken. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumerationsnyckel för tal-tjänst. Om prenumerationen inte är i regionen USA, västra, ändras värdnamnet för den angivna URI: N.

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

### <a name="getting-a-token-curl"></a>Få en token: cURL

cURL är ett kommandoradsverktyg som är tillgängliga i Linux (och i Windows-undersystem för Linux). CURL-kommandot nedan visar hur du kan hämta en åtkomsttoken. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumerationsnyckel för tal-tjänst. Om prenumerationen inte är i regionen USA, västra, ändras värdnamnet för den angivna URI: N.

> [!NOTE]
> Kommandot visas på flera rader för läsbarhet, men bör anges på en enda rad i en shell-prompten.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>Få en token: C#

C# klassen nedan visar hur du kan hämta en åtkomsttoken. Skicka din prenumerationsnyckel för tal-tjänst när instanser skapades av klassen. Om prenumerationen inte är i regionen USA, västra, ändrar du värdnamnet för `FetchTokenUri` på rätt sätt.

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

### <a name="using-a-token"></a>Med hjälp av en token

Om du vill använda en token i en REST API-begäran, kan du ange den i den `Authorization` rubrik som följer efter ordet `Bearer`. Här, till exempel är ett exempel Text till tal-REST-begäran som innehåller en token. Ersätta din faktiska token för `YOUR_ACCESS_TOKEN` och använder rätt värdnamnet i den `Host` rubrik.

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

### <a name="renewing-authorization"></a>Förnya auktorisering

Autentiseringstoken upphör att gälla efter 10 minuter. Förnya din behörighet genom att hämta en ny token innan den upphör att gälla, till exempel efter nio minuter. 

Följande C#-kod är en lättillgänglig ersättning till den klass som presenteras tidigare. Den `Authentication` klass automatiskt hämtar en ny åtkomsttoken varje nio minuter med hjälp av en timer. Den här metoden garanterar att en giltig token alltid är tillgänglig medan programmet körs.

> [!NOTE]
> Du kan lagra en tidsstämpel i när den aktuella token har hämtats och sedan begära en ny endast om den aktuella token är att löpa ut istället för att använda en timer. Den här metoden undviker begär nya token onödigt och kanske passar bättre för program som begär tal sporadiska.

Som tidigare se till att den `FetchTokenUri` värdet matchar din region för prenumerationen. Skicka din prenumerationsnyckel när instanser skapades av klassen.

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


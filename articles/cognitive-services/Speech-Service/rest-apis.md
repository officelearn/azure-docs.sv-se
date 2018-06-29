---
title: 'Tal service REST API: er | Microsoft Docs'
description: 'Referens för REST API: er för tjänsten tal.'
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: 53560fd4f8240c4446898f58992a9319e5177435
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085379"
---
# <a name="speech-service-rest-apis"></a>Tal service REST API: er

REST-API: er för tjänsten enhetlig tal liknar de API: er som tillhandahålls av den [Speech API](https://docs.microsoft.com/azure/cognitive-services/Speech) (kallades tidigare Bing tal-tjänsten). Slutpunkterna skiljer sig från de slutpunkter som används av tjänsten tidigare tal.

## <a name="speech-to-text"></a>Tal till text

I tal Text-API: et, de slutpunkter som används som skiljer sig från tidigare tal tjänsten Speech Recognition API. Nya slutpunkter visas i tabellen nedan. Använd ett som matchar din prenumeration region.

[!include[](includes/endpoints-speech-to-text.md)]

Tal Text-API: et är annars liknar den [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) tidigare tal-API: t.

Tal Text REST-API: et stöder endast korta utterances. Begäranden kan innehålla upp till 10 sekunder ljud och senaste högst 14 övergripande sekunder. REST API returnerar endast slutresultatet, inte helt eller delvis mellanliggande resultat.

> [!NOTE]
> Om du har anpassat ljud modell eller språkmodell eller uttal du istället använda anpassade slutpunkten.

## <a name="text-to-speech"></a>Text till tal

Ny Text till tal API stöder 24-KHz ljuduppspelning. Den `X-Microsoft-OutputFormat` huvudet innehåller nu följande värden.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

Tjänsten tal innehåller nu två 24-KHz röster:

Nationella inställningar | Språk   | Kön | Tjänstemappning
-------|------------|--------|------------
sv-SE  | Svenska | Kvinna | ”Microsoft Server Speech Text till tal-röst (en-US, Jessa24kRUS)” 
sv-SE  | Svenska | Man   | ”Microsoft Server Speech Text till tal-röst (en-US, Guy24kRUS)”

Följande är REST-slutpunkter för den enhetliga service Text till tal API. Använd den slutpunkt som matchar din prenumeration region.

[!include[](includes/endpoints-text-to-speech.md)]

Att dessa skillnader i åtanke när du refererar till den [REST API-dokumentation](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput) tidigare tal-API: t.

## <a name="authentication"></a>Autentisering

En begäran skickades till tjänsten tal REST API kräver en åtkomst-token. Du hämtar en token med din prenumeration nyckel till en tjänst för regional tal `issueToken` slutpunkt, visas i tabellen nedan. Använd den slutpunkt som matchar din prenumeration region.

[!include[](includes/endpoints-token-service.md)]

Varje åtkomst-token är giltig i 10 minuter. Du kan hämta en ny token när som helst, inklusive, om du vill precis innan varje tal REST API-begäran. För att minimera nätverkstrafiken och svarstid, men bör du använda samma token för nio minuter.

Följande avsnitt visar hur du får en token och hur den används i en begäran.

### <a name="getting-a-token-http"></a>Hämta en token: HTTP

Nedan visas ett exempel http-begäran för att få en token. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumeration tjänstnyckeln tal. Om din prenumeration inte är i USA, västra region, ersätter den `Host` huvud med värdnamn för din region.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Brödtexten i svaret på begäran är den åtkomst-token i Java-Webbtoken (JWT)-format.

### <a name="getting-a-token-powershell"></a>Hämta en token: PowerShell

Windows PowerShell-skriptet nedan illustrerar hur du skaffar en åtkomst-token. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumeration tjänstnyckeln tal. Om din prenumeration inte är i USA, västra region, ändras värdnamnet för den angivna URI: N.

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

### <a name="getting-a-token-curl"></a>Hämta en token: cURL

cURL är ett kommandoradsverktyg som är tillgängliga i Linux (och i Windows-undersystem för Linux). Kommandot cURL nedan illustrerar hur du skaffar en åtkomst-token. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumeration tjänstnyckeln tal. Om din prenumeration inte är i USA, västra region, ändras värdnamnet för den angivna URI: N.

> [!NOTE]
> Kommandot visas på flera rader för att läsa, men det bör anges på en enda rad i en shell-kommandotolk.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>Hämta en token: C#

C# klassen nedan illustrerar hur du skaffar en åtkomst-token. Överföra prenumerationen för tal tjänstnyckeln när en instans skapades av klassen. Om din prenumeration inte är i USA, västra region, ändra värdnamnet för `FetchTokenUri` på lämpligt sätt.

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

### <a name="using-a-token"></a>Använder en token

Om du vill använda en token i en REST API-begäran, ger den i den `Authorization` huvud som följer efter ordet `Bearer`. Här, till exempel är ett exempel Text till tal REST-begäran som innehåller en token. Ersätta en aktuell token för `YOUR_ACCESS_TOKEN` och använda rätt värdnamnet i den `Host` rubrik.

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

Autentiseringstoken går ut efter 10 minuter. Förnya ditt tillstånd genom att hämta en ny token innan den upphör, till exempel efter nio minuter. 

Följande C#-kod är en gör att installera ersättning för klass presenteras tidigare. Den `Authentication` klassen hämtar automatiskt en ny åtkomsttoken varje nio minuter med hjälp av en timer. Den här metoden garanterar att en giltig token alltid är tillgängliga när programmet körs.

> [!NOTE]
> Du kan lagra en tidsstämpel när den aktuella token hämtades sedan begära en ny endast om den aktuella token är väg att löpa ut istället för att använda en timer. Det här sättet undviker begär ny token i onödan och kanske passar bättre för program som begär tal ovanligt.

Precis som tidigare, se till att den `FetchTokenUri` värdet matchar din prenumeration region. Ange din prenumeration nyckel när en instans skapades av klassen.

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

- [Hämta din utvärderingsprenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
- [Anpassa ljud modeller](how-to-customize-acoustic-models.md)
- [Anpassa språk modeller](how-to-customize-language-model.md)


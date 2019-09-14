---
title: Autentisera till Taligenkänning i Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Begär autentisering för att använda API för Bing-taligenkänning
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d1e708ff29293b87935d0d191ba44ad4a11917a0
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965661"
---
# <a name="authenticate-to-the-speech-api"></a>Autentisera till Speech API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Taligenkänning i Bing stöder autentisering med:

- En prenumerationsnyckel.
- En autentiseringstoken.

## <a name="use-a-subscription-key"></a>Använd en prenumerations nyckel

Om du vill använda tal tjänsten måste du först prenumerera på Speech API som är en del av Cognitive Services (tidigare Project-Oxford). Du kan få kostnads fria utvärderings prenumerations nycklar från sidan [Cognitive Services prenumeration](https://azure.microsoft.com/try/cognitive-services/) . När du har valt Speech API väljer du **Hämta API-nyckel** för att hämta nyckeln. Den returnerar en primär och sekundär nyckel. Båda nycklarna är knutna till samma kvot, så du kan använda båda nycklarna.

Registrera dig för ett [Azure-konto](https://azure.microsoft.com/free/)för långsiktig användning eller en ökad kvot.

Om du vill använda tal REST API måste du skicka prenumerations nyckeln i `Ocp-Apim-Subscription-Key` fältet i begär ande huvudet.

Name| Format| Beskrivning
----|-------|------------
OCP-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

Följande är ett exempel på ett begär ande huvud:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

> [!IMPORTANT]
> Om du använder [klient bibliotek](../GetStarted/GetStartedClientLibraries.md) i programmet kontrollerar du att du kan hämta autentiseringstoken med din prenumerations nyckel, enligt beskrivningen i följande avsnitt. Klient biblioteken använder prenumerations nyckeln för att hämta en autentiseringstoken och använder sedan token för autentisering.

## <a name="use-an-authorization-token"></a>Använd en autentiseringstoken

Alternativt kan du använda en autentiseringstoken för autentisering som bevis på autentisering/auktorisering. Om du vill hämta denna token måste du först skaffa en prenumerations nyckel från Speech API, enligt beskrivningen i [föregående avsnitt](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Hämta en autentiseringstoken

När du har en giltig prenumerations nyckel skickar du en POST-begäran till token-tjänsten för Cognitive Services. I svaret får du en autentiseringstoken som en JSON Web Token (JWT).

> [!NOTE]
> Token har ett förfallo datum på 10 minuter. Information om hur du förnyar token finns i följande avsnitt.

Token service-URI: n finns här:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Följande kod exempel visar hur du hämtar en åtkomsttoken. Ersätt `YOUR_SUBSCRIPTION_KEY` med din egen prenumerations nyckel:

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[klammerparentes](#tab/curl)

Exemplet använder sväng i Linux med bash. Om den inte är tillgänglig på din plattform kan du behöva installera en sväng. Exemplet fungerar även på Cygwin i Windows, git bash, zsh och andra gränssnitt.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
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
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

---

Följande är en exempel POST-begäran:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Om du inte kan hämta en autentiseringstoken från token-tjänsten kontrollerar du om prenumerations nyckeln fortfarande är giltig. Om du använder en kostnads fri utvärderings nyckel går du till sidan [Cognitive Services prenumeration](https://azure.microsoft.com/try/cognitive-services/) , klickar på Logga in för att logga in med det konto som du använde för att tillämpa den kostnads fria utvärderings nyckeln och kontrollerar om prenumerations nyckeln har upphört att gälla eller överskrider kvoten.

### <a name="use-an-authorization-token-in-a-request"></a>Använda en autentiseringstoken i en begäran

Varje gång du anropar Speech API måste du skicka autentiseringstoken i `Authorization` huvudet. `Authorization` Rubriken måste innehålla en JWT-åtkomsttoken.

I följande exempel visas hur du använder en autentiseringstoken när du anropar tal REST API.

> [!NOTE]
> Ersätt `YOUR_AUDIO_FILE` med sökvägen till din inspelade ljudfil. Ersätt `YOUR_ACCESS_TOKEN` med autentiseringstoken som du fick i föregående steg [Hämta en](#get-an-authorization-token)autentiseringstoken.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[klammerparentes](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Authorization"] = "Bearer " + token;

// Send an audio file by 1024 byte chunks
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

---

### <a name="renew-an-authorization-token"></a>Förnya en autentiseringstoken

Autentiseringstoken upphör att gälla efter en viss tids period (för närvarande 10 minuter). Du måste förnya autentiseringstoken innan den upphör att gälla.

Följande kod är ett exempel på en implementering C# i hur du förnyar autentiseringstoken:

```cs
    /*
     * This class demonstrates how to get a valid O-auth token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
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
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

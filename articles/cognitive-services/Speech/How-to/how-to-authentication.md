---
title: Autentisera till tjänsten Microsoft Speech | Microsoft Docs
description: Begär autentisering som ska användas av Microsoft tal-API
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: e36168cf3ff938af44f1028c2d26fd475d60b148
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352209"
---
# <a name="authenticate-to-the-speech-api"></a>Autentisera till tal-API

Tal tjänsten stöder autentisering med hjälp av:

- En prenumeration nyckel.
- En autentiseringstoken.

## <a name="use-a-subscription-key"></a>Använda en prenumeration för

Om du vill använda tal tjänsten måste du först prenumerera tal-API som är en del av kognitiva tjänster (tidigare projekt Oxford). Du kan hämta en kostnadsfri utvärderingsprenumeration nycklar från den [kognitiva abonnemang](https://azure.microsoft.com/try/cognitive-services/) sidan. När du har valt tal-API, Välj **hämta API-nyckel** att hämta nyckeln. Returnerar en primär och en sekundär nyckel. Båda nycklarna är knutna till samma kvoten, så du kan använda antingen nyckel.

För långsiktig användning eller en större kvot registrera dig för en [Azure-konto](https://azure.microsoft.com/free/).

Om du vill använda tal REST API du behöver för att skicka nyckeln prenumerationen i den `Ocp-Apim-Subscription-Key` i huvudet i begäran.

Namn| Format| Beskrivning
----|-------|------------
OCP-Apim-prenumeration-nyckel | ASCII | YOUR_SUBSCRIPTION_KEY

Följande är ett exempel på ett huvud:

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
> Om du använder [klientbibliotek](../GetStarted/GetStartedClientLibraries.md) i ditt program, kontrollera att du kan få autentiseringstoken med din prenumeration nyckel som beskrivs i följande avsnitt. Klientbiblioteken Använd prenumeration för att få en autentiseringstoken och sedan använda token för autentisering.

## <a name="use-an-authorization-token"></a>Använd en autentiseringstoken

Du kan också använda en Autentiseringstoken för autentisering som bevis på autentisering/auktorisering. För att få detta token, måste du först skaffa en prenumeration nyckel från tal-API som beskrivs i den [föregående avsnitt](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Hämta en autentiseringstoken

När du har en giltig prenumeration nyckel kan skicka en POST-begäran till tokentjänsten kognitiva tjänster. I svaret visas autentiseringstoken som en JSON-Webbtoken (JWT).

> [!NOTE]
> Token har en giltighetstid 10 minuter. Om du vill förnya token finns i följande avsnitt.

Tokentjänsten URI finns här:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Följande kodexempel visar hur du hämtar en åtkomst-token. Ersätt `YOUR_SUBSCRIPTION_KEY` med din egen prenumeration nyckel:

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

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

# <a name="curltabcurl"></a>[cURL](#tab/curl)

I exemplet används curl på Linux med bash. Om den inte är tillgänglig på din plattform, kan du behöva installera curl. Exemplet fungerar även på Cygwin på Windows, Git Bash, zsh och andra gränssnitt.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

Följande är ett exempel på POST-begäran:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Om du inte får tillstånd token från token-tjänsten bör du kontrollera om din prenumeration nyckel är fortfarande giltig. Om du använder en kostnadsfri utvärderingsversion nyckel går du till den [kognitiva abonnemang](https://azure.microsoft.com/try/cognitive-services/) klickar du på ”Logga in” för att logga in med det konto som du använde för att tillämpa den kostnadsfria utvärderingsversion nyckeln och kontrollera om nyckeln prenumeration har upphört att gälla eller överskrider den kvoten.

### <a name="use-an-authorization-token-in-a-request"></a>Använd en autentiseringstoken i en begäran

Varje gång du anropar tal-API, måste du skicka autentiseringstoken i den `Authorization` rubrik. Den `Authorization` huvudet måste innehålla en JWT-åtkomsttoken.

I följande exempel visas hur du använder en autentiseringstoken när du anropar tal REST API.

> [!NOTE]
> Ersätt `YOUR_AUDIO_FILE` med sökvägen till filen inspelade ljud. Ersätt `YOUR_ACCESS_TOKEN` med autentiseringstoken som du fick i förra steget [få en autentiseringstoken](#get-an-authorization-token).

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

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

# <a name="curltabcurl"></a>[cURL](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

Autentiseringstoken upphör att gälla efter en viss tidsperiod (för närvarande 10 minuter). Du måste förnya autentiseringstoken innan den upphör.

Följande kod är ett exempel på implementering i C# för hur du förnyar autentiseringstoken:

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

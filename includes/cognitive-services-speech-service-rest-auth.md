---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 9cad860b8808dd2682995768c282d8376ab5d9be
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58671790"
---
## <a name="authentication"></a>Authentication

Varje begäran kräver ingen auktoriseringsrubrik. Den här tabellen visar vilka rubriker stöds för varje tjänst:

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

[!INCLUDE [](./cognitive-services-speech-service-endpoints-token-service.md)]

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

Brödtexten i svaret innehåller åtkomsttoken i JSON Web Token (JWT)-format.

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

#### <a name="python-sample"></a>Python-exempel

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'

def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Hur du använder en åtkomsttoken

Åtkomst-token ska skickas till tjänsten som den `Authorization: Bearer <TOKEN>` rubrik. Varje åtkomsttoken är giltig i 10 minuter. Du kan hämta en ny token när som helst, men för att minimera nätverkstrafiken och svarstid, rekommenderas att använda samma token i nio minuter.

Här är ett exempel HTTP-begäran till text till tal REST-API:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```

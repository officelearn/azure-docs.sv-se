---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: dc5e251fee00ee22edb2261c1abd8404714834ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78668763"
---
## <a name="authentication"></a>Autentisering

Varje begäran kräver ett auktoriseringshuvud. Den här tabellen illustrerar vilka rubriker som stöds för varje tjänst:

| Auktoriseringshuvuden som stöds | Tal till text | Text till tal |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Ja | Inga |
| Tillstånd: Bärare | Ja | Ja |

När du `Ocp-Apim-Subscription-Key` använder huvudet behöver du bara ange din prenumerationsnyckel. Ett exempel:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

När du `Authorization: Bearer` använder sidhuvudet måste du göra `issueToken` en begäran till slutpunkten. I den här begäran byter du ut prenumerationsnyckeln mot en åtkomsttoken som är giltig i 10 minuter. I de närmaste avsnitten får du lära dig hur du får en token och använder en token.

### <a name="how-to-get-an-access-token"></a>Så här skaffar du en åtkomsttoken

För att få en åtkomsttoken måste du `issueToken` göra en `Ocp-Apim-Subscription-Key` begäran till slutpunkten med hjälp av prenumerationsnyckeln och prenumerationsnyckeln.

Slutpunkten `issueToken` har följande format:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Ersätt `<REGION_IDENTIFIER>` med identifieraren som matchar regionen för din prenumeration från den här tabellen:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Använd dessa exempel för att skapa din begäran om åtkomsttoken.

#### <a name="http-sample"></a>HTTP-exempel

Det här exemplet är en enkel HTTP-begäran för att få en token. Ersätt `YOUR_SUBSCRIPTION_KEY` med prenumerationsnyckeln för Taltjänsten. Om din prenumeration inte finns i regionen `Host` Västra USA ersätter du huvudet med regionens värdnamn.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Brödtexten i svaret innehåller åtkomsttoken i JSON Web Token -format (JWT).

#### <a name="powershell-sample"></a>PowerShell-exempel

Det här exemplet är ett enkelt PowerShell-skript för att få en åtkomsttoken. Ersätt `YOUR_SUBSCRIPTION_KEY` med prenumerationsnyckeln för Taltjänsten. Se till att använda rätt slutpunkt för den region som matchar din prenumeration. Det här exemplet är för närvarande inställt på västra USA.

```powershell
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

#### <a name="curl-sample"></a>cURL-prov

cURL är ett kommandoradsverktyg som finns tillgängligt i Linux (och i Windows-delsystemet för Linux). Det här cURL-kommandot illustrerar hur du får en åtkomsttoken. Ersätt `YOUR_SUBSCRIPTION_KEY` med prenumerationsnyckeln för Taltjänsten. Se till att använda rätt slutpunkt för den region som matchar din prenumeration. Det här exemplet är för närvarande inställt på västra USA.

```console
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C#-exempel

Den här C#-klassen illustrerar hur du får en åtkomsttoken. Skicka din taltjänstprenumerationsnyckel när du instansierar klassen. Om din prenumeration inte finns i regionen västra `FetchTokenUri` USA ändrar du värdet för att matcha regionen för din prenumeration.

```csharp
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

### <a name="how-to-use-an-access-token"></a>Så här använder du en åtkomsttoken

Åtkomsttoken ska skickas till tjänsten `Authorization: Bearer <TOKEN>` som huvud. Varje åtkomsttoken är giltig i 10 minuter. Du kan få en ny token när som helst, men för att minimera nätverkstrafik och svarstid, rekommenderar vi att du använder samma token i nio minuter.

Här är ett exempel på HTTP-begäran till REST-API:et för text-till-tal:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```

---
title: Generera säkerhetstoken för att komma åt IoT Plug and Play Preview-databas | Microsoft-dokument
description: Generera en signaturtoken för delad åtkomst som ska användas när du öppnar en IoT Plug and Play Preview-modelldatabas programmässigt.
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159208"
---
# <a name="generate-sas-token"></a>Generera SAS-token

Den här programguiden visar hur du programmässigt genererar en SAS-token (Shared Access Signature) som ska användas med IoT Plug and Play Preview-modelldatabasens API:er.

## <a name="python"></a>Python

Följande kodavsnitt visar hur du genererar en SAS-token med Python:

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

Följande kodavsnitt visar hur du genererar en SAS-token med C:\#

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>Använda SAS-token

När du har genererat en SAS-token kan du använda den för att göra en HTTP POST-begäran. Ett exempel:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Om du ger en klient en SAS-token har klienten inte resursens primära nyckel och kan inte återföra hash för att hämta den. En SAS-token ger dig kontroll över vad klienten kan komma åt och hur länge. När du ändrar primärnyckeln i principen ogiltigförklaras alla SAS-token som skapas från den.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om att generera säkerhetstoken som ska användas för att komma åt modellförhandsgranskningsmodelldatabaserna för IoT Plug and Play, är ett förslag till nästa steg att lära dig mer i [utvecklarguiden för IoT Plug and Play Preview.](concepts-developer-guide.md)

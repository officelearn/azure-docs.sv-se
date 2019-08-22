---
title: Skapa säkerhetstoken för att få åtkomst till IoT Plug and Play Preview-lagringsplats | Microsoft Docs
description: Generera en token för signatur för delad åtkomst som ska användas när du ansluter till en IoT-Plug and Play för hands versions lagrings plats.
author: YasinMSFT
ms.author: yahajiza
ms.date: 08/06/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e5d6e7087a7e3d5f4a001e16c5cfa19a6df6a68e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880545"
---
# <a name="generate-sas-token"></a>Generera SAS-token

Den här instruktions guiden visar hur du program mässigt genererar en SAS-token (signatur för delad åtkomst) som ska användas med IoT Plug and Play för hands versions lagrings-API: er.

## <a name="python"></a>Python

Följande fragment visar hur du skapar en SAS-token med python:

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

Följande fragment visar hur du skapar en SAS-token med C\#:

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

## <a name="use-the-sas-token"></a>Använd SAS-token

När du har genererat en SAS-token kan du använda den för att göra en HTTP POST-begäran. Exempel:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Om du ger en klient en SAS-token har klienten inte resursens primär nyckel och kan inte återställa hashen för att hämta den. En SAS-token ger dig kontroll över vad klienten har åtkomst till och hur länge. När du ändrar primär nyckeln i principen blir alla SAS-token som skapats från den ogiltiga.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om att skapa säkerhetstoken som ska användas för att få åtkomst till modell IoT Plug and Play för hands versions databaser, är ett förslag till nästa steg att lära dig mer i [IoT-plug and Play för hands versions guide](concepts-developer-guide.md).

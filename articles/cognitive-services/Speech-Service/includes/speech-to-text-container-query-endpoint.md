---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: debd1eda050e75ea2b9baed45e6e5af0f5a95c46
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944507"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Tal till text eller Custom Speech till text

Behållaren tillhandahåller WebSocket-baserade frågor för slut punkts-API: er som nås via [tal-SDK: n](../index.yml). Som standard använder tal-SDK online Speech Services. Om du vill använda behållaren måste du ändra initierings metoden.

> [!TIP]
> När du använder tal-SDK med behållare behöver du inte ange resurs [prenumerations nyckeln för Azure tal eller en token för autentisering](../rest-speech-to-text.md#authentication).

Se exemplen nedan.

# <a name="c"></a>[C#](#tab/csharp)

Ändra från att använda det här Azure-moln initierings anropet:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

till det här anropet med hjälp av behållar [värden](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet):

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# <a name="python"></a>[Python](#tab/python)

Ändra från att använda det här Azure-moln initierings anropet:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

---

---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1552dda28291112a2412bdf956bc49a0b541d7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95996456"
---
Behållaren tillhandahåller WebSocket-baserade frågor för slut punkts-API: er som nås via [tal-SDK: n](../index.yml). Som standard använder tal-SDK online Speech Services. Om du vill använda behållaren måste du ändra initierings metoden.

> [!TIP]
> När du använder tal-SDK med behållare behöver du inte ange resurs [prenumerations nyckeln för Azure tal eller en token för autentisering](../rest-speech-to-text.md#authentication).

Se exemplen nedan.

# <a name="c"></a>[C#](#tab/csharp)

Ändra från att använda det här Azure-moln initierings anropet:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Använda det här anropet med behållar [värden](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?preserve-view=true&view=azure-dotnet):

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

Använda det här anropet med behållarens [slut punkt](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python):

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
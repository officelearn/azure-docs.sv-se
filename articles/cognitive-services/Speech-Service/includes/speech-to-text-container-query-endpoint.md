---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 54ccaebd84c6af308ddcfa956add7f84b6e55832
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89321061"
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

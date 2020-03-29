---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/21/2020
ms.author: dapine
ms.openlocfilehash: 9b4317064196c4ea3d761fd1a0bd43a764054fe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77563280"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Tal-till-text eller Anpassad tal-till-text

Behållaren tillhandahåller websocket-baserade frågeslutpunkts-API:er som nås via [Tal-SDK](../index.yml). Som standard använder Tal-SDK taltjänster online. Om du vill använda behållaren måste du ändra initieringsmetoden.

> [!TIP]
> När du använder Tal-SDK med behållare behöver du inte ange prenumerationsnyckeln för Azure [Speech-resursen eller en autentiseringsbäraretoken](../rest-speech-to-text.md#authentication).

Se exemplen nedan.

# <a name="c"></a>[C#](#tab/csharp)

Ändra från att använda det här initieringsanropet i Azure-cloud:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

till det här anropet med behållarens [värd:](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="python"></a>[Python](#tab/python)

Ändra från att använda det här initieringsanropet i Azure-cloud:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

till det här anropet med behållarens [värd:](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***

---
title: Skicka fråga till text behållarens slut punkt
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2d96385f2d2d34d161739c55228220cf28871048
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "74132616"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Tal till text eller Custom Speech till text

Behållaren tillhandahåller WebSocket-baserade frågor för slut punkts-API: er som nås via [tal-SDK: n](../index.md). Som standard använder tal-SDK online Speech Services. Om du vill använda behållaren måste du ändra initierings metoden.

> [!TIP]
> När du använder tal-SDK med behållare behöver du inte ange resurs [prenumerations nyckeln för Azure tal eller en token för autentisering](../rest-speech-to-text.md#authentication).

Se exemplen nedan.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ändra från att använda det här Azure-moln initierings anropet:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

till det här anropet med hjälp av behållar [värden](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet):

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

Ändra från att använda det här Azure-moln initierings anropet:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

till det här anropet med hjälp av behållar [värden](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python):

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***

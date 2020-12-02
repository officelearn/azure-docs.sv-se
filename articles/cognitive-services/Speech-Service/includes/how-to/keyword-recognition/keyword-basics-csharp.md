---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: d71a7f6451cda5e2e50b5410140ac88361bf1735
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509368"
---
Börja med att läsa in din nyckelords modell fil med hjälp av den `FromFile()` statiska funktionen, som returnerar en `KeywordRecognitionModel` . Använd sökvägen till `.table` filen som du laddade ned från tal Studio. Dessutom skapar du en `AudioConfig` med hjälp av standard mikrofonen och instansierar sedan en ny `KeywordRecognizer` med hjälp av ljud konfigurationen.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Sedan utförs körning av nyckelords igenkänning med ett anrop till `RecognizeOnceAsync()` genom att skicka modell objekt. Detta startar en session för att känna igen som varar tills nyckelordet identifieras. Därför använder du vanligt vis det här design mönstret i program med flera trådar, eller i användnings fall där du kan vänta ett Väcknings ord i oändlighet.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> Exemplet som visas här använder lokal nyckelords igenkänning eftersom det inte kräver ett `SpeechConfig` objekt för autentiserings kontext och inte kontaktar Server delen. Du kan dock köra både nyckelords igenkänning och verifiering för att [använda en kontinuerlig backend-anslutning](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword).
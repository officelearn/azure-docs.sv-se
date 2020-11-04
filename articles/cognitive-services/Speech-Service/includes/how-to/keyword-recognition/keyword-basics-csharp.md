---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: 040ffea69f76255dcb1bfc6787cad45a95baa904
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305886"
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
> Exemplet som visas här använder lokal nyckelords igenkänning eftersom det inte kräver ett `SpeechConfig` objekt för autentiserings kontext och inte kontaktar Server delen. Du kan dock köra både nyckelords igenkänning och verifiering för att [använda en kontinuerlig backend-anslutning](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword).
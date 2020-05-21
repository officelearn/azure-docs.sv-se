---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 3740158f7134d7f0317809a7e1e89a97cdfffd94
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673007"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md)
> * Kontrol lera att du har åtkomst till en mikrofon för ljud inspelning

## <a name="setup-your-environment"></a>Konfigurera din miljö

Uppdatera filen go. mod med den senaste SDK-versionen genom att lägga till den här raden
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.12.0-alpha1
)
```

## <a name="start-with-some-boilerplate-code"></a>Börja med viss exempel kod
1. Ersätt innehållet i käll filen (t. ex. `sr-quickstart.go` ) med följande, som innehåller:

- "Main"-paket definition
- Importera nödvändiga moduler från tal-SDK
- variabler för att lagra prenumerations informationen som ska ersättas senare i den här snabb starten
- en enkel implementering med mikrofonen för ljud inspelning
- händelse hanterare för olika händelser som inträffar under tal igenkänning

```sh
package recognizer

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

Ersätt `YOUR_SUBSCRIPTION_KEY` värdena och `YOUR_SUBSCRIPTIONKEY_REGION` med faktiska värden från tal resursen.

- Navigera till Azure Portal och öppna tal resursen
- Under **nycklarna** till vänster finns det två tillgängliga prenumerations nycklar
    - Använd antingen ett som `YOUR_SUBSCRIPTION_KEY` värde för ersättning
- Under **översikten** till vänster noterar du regionen och mappar den till regions-ID: n
- Använd regions identifieraren som `YOUR_SUBSCRIPTIONKEY_REGION` värde ersättning, till exempel: `"westus"` för **västra USA**

## <a name="code-explanation"></a>Kod förklaring
Tal prenumerations nyckeln och regionen krävs för att skapa ett tal konfigurations objekt. Konfigurationsobjektet krävs för att instansiera ett tal igenkännings objekt.

Tolks instansen visar flera olika sätt att känna igen tal. I det här exemplet känns tal kontinuerligt igen. Den här funktionen gör att röst tjänsten vet att du skickar många fraser för igenkänning och när programmet slutar att känna igen tal. När resultatet har ställts in, kommer koden att skriva dem till-konsolen.

## <a name="build-and-run"></a>Skapa och kör
Nu har du konfigurerat för att skapa projektet och testa tal igenkänning med hjälp av tal tjänsten.
1. Bygg ditt projekt, t. ex. **"go Build"**
2. Kör modulen och tala en fras eller mening i enhetens mikrofon. Talet skickas till tal tjänsten och överförs till text som visas i utdata.


> [!NOTE]
> Tal-SDK: n kommer att känna igen med en-US för språket, se [Ange käll språk för tal till text](../../../../how-to-specify-source-language.md) om du vill ha information om hur du väljer käll språk.


## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]

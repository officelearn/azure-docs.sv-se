---
title: 'Kom igång med API: t Microsoft Speech igenkänning i Java på Android | Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: Använd Microsoft Speech API för att utveckla Android-program som konverterar talade ljud till text.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 77fee2ecee9cfabe3fad9c1df2c41c7803c3367e
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966834"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Snabbstart: Använd Taligenkänning i Bing igenkännings-API i Java på Android

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Med API: et för Taligenkänning i Bing igenkänning kan du utveckla Android-program som använder den molnbaserade Taligenkänning i Bing-tjänsten för att konvertera talade ljud till text. API: et stöder strömning i real tid, så att ditt program samtidigt kan ta emot partiella igenkännings resultat asynkront samtidigt som det skickar ljud till tjänsten.

I den här artikeln används ett exempel program för att demonstrera hur du använder klientbibliotek för Speech för Android för att utveckla tal-till-text-program i Java för Android-enheter.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Exemplet utvecklas av [Android Studio](https://developer.android.com/sdk/index.html) för Windows i Java.

### <a name="get-the-client-library-and-sample-application"></a>Hämta klient biblioteket och exempel programmet

Klientbibliotek för Speech och exempel för Android finns i [tal klient-SDK för Android](https://github.com/microsoft/cognitive-speech-stt-android). Du hittar det Bygg bara exemplet under katalogen samples/SpeechRecoExample. Du kan hitta de två bibliotek som du behöver använda i dina egna appar i SpeechSDK/libs under mappen armeabi och mappen x86. Storleken på libandroid_platform. so-filen är 22 MB, men den minskas till 4 MB vid distributions tillfället.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på Speech API och få en prenumerations nyckel för kostnads fri utvärdering

Speech API är en del av Cognitive Services (tidigare Project-Oxford). Du kan få kostnads fria utvärderings prenumerations nycklar från sidan [Cognitive Services prenumeration](https://azure.microsoft.com/try/cognitive-services/) . När du har valt Speech API väljer du **Hämta API-nyckel** för att hämta nyckeln. Den returnerar en primär och sekundär nyckel. Båda nycklarna är knutna till samma kvot, så du kan använda båda nycklarna.

Om du vill använda *igenkänning med avsikten*måste du också registrera dig för [language Understanding intelligent service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Hämta en prenumerations nyckel. Innan du kan använda tal klient bibliotek måste du ha en [prenumerations nyckel](https://azure.microsoft.com/try/cognitive-services/).
>
>* Använd din prenumerations nyckel. Med det tillhandahållna Android-exempelprogrammet uppdaterar du filen samples/SpeechRecoExample/res/Values/Strings. xml med dina prenumerations nycklar. Mer information finns i [skapa och köra exempel](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Använd klientbibliotek för Speech

Följ [instruktionerna](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library)för att använda klient biblioteket i programmet.

Du hittar klient biblioteks referensen för Android i mappen dokument i [tal klientens SDK för Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Skapa och köra exempel

Information om hur du skapar och kör exempel finns på den här [README-sidan](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Exempel på förklaring

### <a name="create-recognition-clients"></a>Skapa igenkännings klienter

Koden i följande exempel visar hur du skapar igenkännings klient klasser baserat på användar scenarier:

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

Klient biblioteket tillhandahåller förimplementerade igenkännings klient klasser för vanliga scenarier i tal igenkänning:

* `DataRecognitionClient`: Tal igenkänning med PCM-data (till exempel från en fil eller ljud källa). Data delas upp i buffertar och varje buffert skickas till tal tjänsten. Ingen ändring görs i buffertarna, så användaren kan använda sin egen tystnads identifiering om du vill. Om data tillhandahålls från WAV-filer kan du skicka data från filen till höger till tal-tjänsten. Om du har rå data, till exempel ljud som kommer via Bluetooth, skickar du först en format rubrik till tal-tjänsten följt av datan.
* `MicrophoneRecognitionClient`: Tal igenkänning med ljud som kommer från mikrofonen. Kontrol lera att mikrofonen är påslagen och att data från mikrofonen skickas till tal igenkännings tjänsten. En inbyggd "tystnads detektor" tillämpas på mikrofon data innan den skickas till igenkännings tjänsten.
* `DataRecognitionClientWithIntent` och `MicrophoneRecognitionClientWithIntent`: Dessa klienter returnerar, förutom igenkännings text, strukturerad information om syftet med talare, som kan användas för att köra ytterligare åtgärder av dina program. Om du vill använda "avsikt" måste du först träna en modell med hjälp av [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Igenkännings språk

När du använder `SpeechRecognitionServiceFactory` för att skapa klienten måste du välja ett språk. En fullständig lista över språk som stöds av tal service finns i [språk som stöds](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Du måste också ange `SpeechRecognitionMode` när du skapar klienten med: `SpeechRecognitionServiceFactory`

* `ShortPhrase`: En uttryck upp till 15 sekunder lång. När data skickas till tjänsten får klienten flera delar av resultaten och ett slut resultat med flera n-bästa alternativ.
* `LongDictation`: En uttryck upp till två minuter lång. När data skickas till tjänsten tar klienten emot flera del resultat och flera slut resultat baserat på var tjänsten identifierar mening pausar.

### <a name="attach-event-handlers"></a>Koppla händelse hanterare

Du kan koppla olika händelse hanterare till klienten som du skapade:

* **Partiella resultat händelser**: Den här händelsen anropas varje gång tal tjänsten förutsäger vad du kan säga, till och med innan du har slutfört tal ( `MicrophoneRecognitionClient`om du använder) eller Slutför sändningen av `DataRecognitionClient`data (om du använder).
* **Fel händelser**: Anropas när tjänsten identifierar ett fel.
* **Avsikts händelser**: Anropas på "WithIntent"-klienter ( `ShortPhrase` endast i läge) efter det slutliga igenkännings resultatet parsas till en strukturerad JSON-avsikt.
* **Resultat händelser**:
  * I `ShortPhrase` läget anropas den här händelsen och returnerar n-bästa resultat när du har slutfört inläsningen.
  * I `LongDictation` läget anropas händelse hanteraren flera gånger, baserat på var tjänsten identifierar mening pausar.
  * **För var och en av de n bästa alternativen**returneras ett konfidens värde och några olika former av den tolkade texten. Mer information finns i [utdataformat](../Concepts.md#output-format).

## <a name="related-topics"></a>Relaterade ämnen

* [Klient biblioteks referens för Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Kom igång med Microsoft Speech API i C# för Windows i .net](GetStartedCSharpDesktop.md)
* [Kom igång med Microsoft Speech API i mål-C på iOS](Get-Started-ObjectiveC-iOS.md)
* [Kom igång med Microsoft Speech API i Java Script](GetStartedJSWebsockets.md)
* [Kom igång med Microsoft Speech API via REST](GetStartedREST.md)

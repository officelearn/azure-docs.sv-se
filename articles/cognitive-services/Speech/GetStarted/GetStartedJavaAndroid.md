---
title: Kom igång med Microsoft Speech Recognition API i Java på Android | Microsoft Docs
description: Använd Microsoft tal-API för att utveckla Android-program som tal konverteras till text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/29/2017
ms.author: zhouwang
ms.openlocfilehash: a10f7be1c36fb431016a9867f606e26be858069e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352248"
---
# <a name="get-started-with-speech-recognition-in-java-on-android"></a>Komma igång med taligenkänning i Java på Android

Med tal Recognition API, kan du utveckla Android-program som använder tal molntjänst för att konvertera tal till text. API: et stöder direktuppspelning av realtid, så att programmet kan samtidigt och asynkront får partiella resultat samtidigt som det skickar ljud till tjänsten.

Den här artikeln använder ett exempelprogram för att demonstrera hur du använder klientbiblioteket tal för Android för att utveckla till text till tal-program i Java för Android-enheter.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Exemplet utvecklas av [Android Studio](http://developer.android.com/sdk/index.html) för Windows i Java.

### <a name="get-the-client-library-and-sample-application"></a>För att installera klienten bibliotek och exempelapp program

Dikterings-klientbiblioteket och exempel för Android är tillgängliga i den [tal klient-SDK för Android](https://github.com/microsoft/cognitive-speech-stt-android). Du kan hitta buildable provet under katalogen prover/SpeechRecoExample. Det finns två bibliotek måste du använda i dina egna appar i SpeechSDK/libs under armeabi och x86 mapp. Storleken på filen libandroid_platform.so är 22 MB, men den har minskats till 4 MB vid tidpunkten för distribution.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på tal-API och få en kostnadsfri utvärderingsprenumeration nyckel

Dikterings-API: N är en del av kognitiva tjänster (tidigare projekt Oxford). Du kan hämta en kostnadsfri utvärderingsprenumeration nycklar från den [kognitiva abonnemang](https://azure.microsoft.com/try/cognitive-services/) sidan. När du har valt tal-API, Välj **hämta API-nyckel** att hämta nyckeln. Returnerar en primär och en sekundär nyckel. Båda nycklarna är knutna till samma kvoten, så du kan använda antingen nyckel.

Om du vill använda *recognition med avsikt*, måste du också registrera dig för den [språk förstå Intelligent Service (THOMAS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Hämta en nyckel för prenumerationen. Innan du kan använda tal klientbibliotek, måste du ha en [prenumeration nyckeln](https://azure.microsoft.com/try/cognitive-services/).
>
>* Använd din prenumeration nyckel. Uppdatera filen samples/SpeechRecoExample/res/values/strings.xml med din prenumeration nycklar med angivna Android exempelprogrammet. Mer information finns i [skapa och kör exempel](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Använd klientbiblioteket tal

Så här använder du klientbiblioteket i ditt program i [instruktioner](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Du kan hitta klienten biblioteksreferens för Android i dokumentmappen i den [tal klient-SDK för Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Skapa och kör exempel

Information om hur du skapar och kör exempel finns [README page](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Exempel förklaras

### <a name="create-recognition-clients"></a>Skapa recognition klienter

Koden i följande exempel visar hur du skapar recognition klientklasser baserat på användarscenarier:

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

Klientbiblioteket innehåller redan implementerade recognition klientklasser för vanliga scenarier för taligenkänning:

* `DataRecognitionClient`: Taligenkänning med PCM data (till exempel från en fil eller ljud källa). Data har delats upp i buffertar och varje buffert skickas till tjänsten för tal. Ingen ändring görs till buffertar, så att användaren kan använda sina egna identifiering av tystnad, om så önskas. Om det finns data från WAV-filer kan du skicka data från höger filen till tal-tjänsten. Om du har rådata, till exempel ljud kommer över Bluetooth, du först skicka ett formulärhuvud till tal-tjänsten följt av data.
* `MicrophoneRecognitionClient`: Taligenkänning med ljud som kommer från mikrofonen. Kontrollera att mikrofonen är aktiverad och data från mikrofonen skickas till tjänsten recognition tal. En inbyggd ”tystnad detektor” tillämpas mikrofon data innan den skickas till tjänsten igenkänning.
* `DataRecognitionClientWithIntent` och `MicrophoneRecognitionClientWithIntent`: dessa klienter returnera, förutom recognition text strukturerad information om avsikten av talare som kan användas för att ge ytterligare åtgärder för programmen. Om du vill använda ”avsikt” måste du först tränar en modell med hjälp av [THOMAS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Språk

När du använder `SpeechRecognitionServiceFactory` för att skapa klienten, måste du välja ett språk. Fullständig lista över språk som stöds av tal Service, se [språk som stöds](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Du måste också ange `SpeechRecognitionMode` när du skapar klienten med `SpeechRecognitionServiceFactory`:

* `ShortPhrase`: Ett utterance upp till 15 sekunder långt. Klienten får du flera ofullständiga resultat och en slutresultatet med flera n bästa val som data skickas till tjänsten.
* `LongDictation`: Ett utterance upp till två minuter lång. Klienten får du flera ofullständiga resultat och flera slutliga resultat baserat på där tjänsten identifierar meningen pausar som data skickas till tjänsten.

### <a name="attach-event-handlers"></a>Koppla händelsehanterare

Du kan koppla olika händelsehanterare till klienten du skapade:

* **Partiell resultat händelser**: den här händelsen anropas varje gång tal Service förutsäger vad du kan säger, innan du har talat klart (om du använder `MicrophoneRecognitionClient`) eller har skickat data (om du använder `DataRecognitionClient`).
* **Felhändelser**: anropas när tjänsten upptäcker ett fel.
* **Avsiktshantering händelser**: anropas på ”WithIntent” klienter (endast i `ShortPhrase` läge) efter sista recognition resultatet parsas till en strukturerad JSON-avsikt.
* **Leda händelser**:
  * I `ShortPhrase` -läge för den här händelsen kallas och returnerar n bäst resultat när du har talat klart.
  * I `LongDictation` läge händelsehanteraren anropas flera gånger, baserat på där tjänsten identifierar meningen pausar.
  * **För varje n bästa val**, returneras ett förtroende-värde och ett par olika typer av tolkad text. Mer information finns i [utdataformat](../Concepts.md#output-format).

## <a name="related-topics"></a>Relaterade ämnen

* [Klienten biblioteksreferens för Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Komma igång med Microsoft tal-API i C# för Windows i .NET](GetStartedCSharpDesktop.md)
* [Kom igång med Microsoft tal-API i Objective-C på iOS](Get-Started-ObjectiveC-iOS.md)
* [Kom igång med Microsoft tal-API i JavaScript](GetStartedJSWebsockets.md)
* [Kom igång med Microsoft Speech API via REST](GetStartedREST.md)

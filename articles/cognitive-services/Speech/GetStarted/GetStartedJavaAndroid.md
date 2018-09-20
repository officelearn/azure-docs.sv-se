---
title: Kom igång med API för taligenkänning igenkänning av Microsoft i Java på Android | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Använd Microsoft Speech API för att utveckla Android-program som omvandla talat ljud till text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: d62a1fd3a497ed87deb24fc053ec3315a7aba26b
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367040"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Snabbstart: Använda Bing-taligenkänning API i Java på Android

Du kan utveckla Android-program som använder molnbaserade Bing Speech Service för att omvandla talat ljud till text med Taligenkänning för Bing. API: et stöder strömning i realtid, så att ditt program kan samtidigt och asynkront får delvisa igenkänningsresultat samtidigt som den skickar ljud till tjänsten.

Den här artikeln använder ett exempelprogram för att demonstrera hur du använder talklientbiblioteket för Android för att utveckla program eller tal till text i Java för Android-enheter.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Exemplet har utvecklats av [Android Studio](http://developer.android.com/sdk/index.html) för Windows i Java.

### <a name="get-the-client-library-and-sample-application"></a>Hämta program-bibliotek och exempelapp

Talklientbiblioteket och exempel för Android är tillgängliga i den [tal-klientens SDK för Android](https://github.com/microsoft/cognitive-speech-stt-android). Du kan hitta buildable exemplet under katalogen samples/SpeechRecoExample. Du kan hitta de två bibliotek som du behöver använda i dina egna appar i SpeechSDK/libs under armeabi och x86 mapp. Storleken på filen libandroid_platform.so är 22 MB, men det minskar till 4 MB vid tidpunkten för distribution.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på API för taligenkänning och få en kostnadsfri utvärderingsprenumeration-nyckel

API för taligenkänning är en del av Cognitive Services (tidigare projekt Oxford). Du kan få kostnadsfri utvärderingsprenumeration nycklar från den [Cognitive Services-prenumeration](https://azure.microsoft.com/try/cognitive-services/) sidan. När du har valt API för taligenkänning, Välj **hämta API-nyckel** att hämta nyckeln. Returnerar den primära och sekundära nyckeln. Båda nycklarna är knutna till samma kvot, så du kan använda någon av nycklarna.

Om du vill använda *erkännande med avsikt*, måste du också registrera dig för den [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Få en prenumerationsnyckel. Innan du kan använda tal-klientbiblioteken, måste du ha en [prenumerationsnyckel](https://azure.microsoft.com/try/cognitive-services/).
>
>* Använd din prenumerationsnyckel. Uppdatera filen samples/SpeechRecoExample/res/values/strings.xml med dina prenumerationsnycklar med angivna Android exempelprogrammet. Mer information finns i [skapa och kör exempel](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Använda tal-klientbiblioteket

Så här använder du klientbiblioteket i ditt program i [instruktioner](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Du kan hitta klienten biblioteksreferens för Android i docs-mappen på den [tal-klientens SDK för Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Skapa och kör exempel

Läs hur du skapar och kör exempel i det här [README page](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Exempel beskrivs

### <a name="create-recognition-clients"></a>Skapa igenkänning av klienter

Koden i följande exempel visar hur du skapar erkännande klientklasser baserat på användarscenarier:

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

Klientbiblioteket tillhandahåller redan implementerat erkännande klientklasser för vanliga scenarier för taligenkänning:

* `DataRecognitionClient`: Taligenkänning med PCM-data (till exempel från en källa för filen eller ljud). Data har delats upp i buffertar och varje buffert skickas till tal-tjänsten. Inga ändringar görs till buffertar, så att användaren kan använda sina egna identifiering av tystnad, om så önskas. Om data anges från WAV-filer, kan du skicka data från filen rätten att Speech Service. Om du har rådata, till exempel ljud kommer via Bluetooth, du först skicka ett sidhuvud i formatet till Speech Service följt av data.
* `MicrophoneRecognitionClient`: Taligenkänning med ljud från mikrofonen. Kontrollera att mikrofonen är på och data från mikrofonen skickas till tjänsten för taligenkänning. En inbyggd ”tystnad detektor” tillämpas på mikrofonen data innan den skickas till tjänsten för taligenkänning.
* `DataRecognitionClientWithIntent` och `MicrophoneRecognitionClientWithIntent`: dessa klienter returnera, förutom för text, strukturerad information om syftet för talare, som kan användas för att driva ytterligare åtgärder för programmen. Om du vill använda ”avsikt”, måste du först tränar en modell med hjälp av [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Språk

När du använder `SpeechRecognitionServiceFactory` för att skapa klienten, måste du välja ett språk. En fullständig lista över språk som stöds av Speech-tjänsten, se [språk som stöds](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Du måste också ange `SpeechRecognitionMode` när du skapar klienten med `SpeechRecognitionServiceFactory`:

* `ShortPhrase`: Ett uttryck upp till 15 sekunder långa. Som data skickas till tjänsten, tar klienten emot flera delresultat och ett slutresultat med flera n bästa val.
* `LongDictation`: Ett uttryck upp till två minuter lång. När data skickas till tjänsten, tar klienten emot flera delresultat och flera slutresultat baserat på där tjänsten identifierar paus i meningen.

### <a name="attach-event-handlers"></a>Bifoga händelsehanterare

Du kan koppla olika händelsehanterare till klienten du skapade:

* **Partiell resultat händelser**: den här händelsen anropas varje gång Speech Service förutsäger vad du kan säger, även innan du slutför talar (om du använder `MicrophoneRecognitionClient`) eller har skickat data (om du använder `DataRecognitionClient`).
* **Felhändelser**: kallas när tjänsten upptäcker ett fel.
* **Avsiktshantering händelser**: namnet på ”WithIntent” klienter (endast i `ShortPhrase` läge) när den sista igenkänningsresultatet parsas till en strukturerade JSON-avsikt.
* **Resultera händelser**:
  * I `ShortPhrase` läge, den här händelsen kallas och returnerar n bäst resultat när du har slutfört talar.
  * I `LongDictation` läge, händelsehanteraren anropas flera gånger, baserat på där tjänsten identifierar paus i meningen.
  * **För var och en av de n bästa val**, returneras ett förtroende-värde och ett par olika former av den tolkade texten. Mer information finns i [utdataformat](../Concepts.md#output-format).

## <a name="related-topics"></a>Relaterade ämnen

* [-Klientbiblioteksreferens för Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Kom igång med Microsoft-Taligenkänning i C# för Windows i .NET](GetStartedCSharpDesktop.md)
* [Kom igång med Microsoft-Taligenkänning i Objective-C på iOS](Get-Started-ObjectiveC-iOS.md)
* [Kom igång med Microsoft Speech API i JavaScript](GetStartedJSWebsockets.md)
* [Kom igång med Microsoft Speech API via REST](GetStartedREST.md)

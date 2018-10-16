---
title: Kom igång med igenkänning av API för Bing-taligenkänning i Objective-C på iOS | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Använd Bing-Taligenkänning för igenkänning av för att utveckla iOS-program som omvandla talat ljud till text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 7c4a5029208854528afdfdbfcdc63434a2a94e24
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338716"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Snabbstart: Använda Bing-Taligenkänning för taligenkänning i Objective-C på iOS

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Du kan utveckla iOS-program som använder molnbaserade Speech-tjänsten för att omvandla talat ljud till text med tal-API för taligenkänning. API: et stöder strömning i realtid, så att ditt program kan samtidigt och asynkront får delvisa igenkänningsresultat samtidigt som den skickar ljud till tjänsten.

Den här artikeln använder ett exempelprogram för att demonstrera grunderna för hur du kommer igång med API för taligenkänning för att utveckla ett iOS-program. En fullständig API-referens, finns det [tal SDK-klientbiblioteksreferens](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Kontrollera att Mac XCode IDE installeras.

### <a name="get-the-client-library-and-examples"></a>Hämta bibliotek och exempel

Talklientbiblioteket och exempel för iOS är tillgängliga på den [tal-klient SDK för iOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på det tal-API: T och få en kostnadsfri utvärderingsprenumeration-nyckel

API för taligenkänning är en del av Cognitive Services (tidigare projekt Oxford). Du kan få kostnadsfri utvärderingsprenumeration nycklar från den [Cognitive Services-prenumeration](https://azure.microsoft.com/try/cognitive-services/) sidan. När du har valt API för taligenkänning, Välj **hämta API-nyckel** att hämta nyckeln. Returnerar den primära och sekundära nyckeln. Båda nycklarna är knutna till samma kvot, så du kan använda någon av nycklarna.

Om du vill använda *erkännande med avsikt*, måste du också registrera dig för den [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Få en prenumerationsnyckel. Innan du kan använda tal-klientbiblioteken, måste du ha en [prenumerationsnyckel](https://azure.microsoft.com/try/cognitive-services/).
>
> * Använd din prenumerationsnyckel. Du måste uppdatera filen Samples/SpeechRecognitionServerExample/settings.plist med din prenumerationsnyckel med det angivna iOS-exempelprogrammet. Mer information finns i [skapa och kör exempel](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Använda tal-klientbiblioteket

Om du vill lägga till klientbiblioteket i XCode-projekt, följer du dessa [instruktioner](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Klienten biblioteksreferens för iOS finns i det här [webbsidan](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Skapa och kör exempel

Information om hur du bygger och kör exempel finns i den här [README page](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Exempel beskrivs

### <a name="create-recognition-clients"></a>Skapa igenkänning av klienter

Följande kod i det här exemplet visar hur du skapar erkännande klientklasser baserat på användarscenarier:

```
{
    NSString* language = @"en-us";

    NSString* path = [[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"];
    NSDictionary* settings = [[NSDictionary alloc] initWithContentsOfFile:path];

    NSString* primaryOrSecondaryKey = [settings objectForKey:(@"primaryKey")];
    NSString* luisAppID = [settings objectForKey:(@"luisAppID")];
    NSString* luisSubscriptionID = [settings objectForKey:(@"luisSubscriptionID")];

    if (isMicrophoneReco) {
        if (!isIntent) {
            micClient = [SpeechRecognitionServiceFactory createMicrophoneClient:(recoMode)
                                                                   withLanguage:(language)
                                                                        withKey:(primaryOrSecondaryKey)
                                                                   withProtocol:(self)];
        }
        else {
            MicrophoneRecognitionClientWithIntent* micIntentClient;
            micIntentClient = [SpeechRecognitionServiceFactory createMicrophoneClientWithIntent:(language)
                                                                                        withKey:(primaryOrSecondaryKey)
                                                                                  withLUISAppID:(luisAppID)
                                                                                 withLUISSecret:(luisSubscriptionID)
                                                                                   withProtocol:(self)];
            micClient = micIntentClient;
        }
    }
    else {
        if (!isIntent) {
            dataClient = [SpeechRecognitionServiceFactory createDataClient:(recoMode)
                                                              withLanguage:(language)
                                                                   withKey:(primaryOrSecondaryKey)
                                                              withProtocol:(self)];
        }
        else {
            DataRecognitionClientWithIntent* dataIntentClient;
            dataIntentClient = [SpeechRecognitionServiceFactory createDataClientWithIntent:(language)
                                                                                   withKey:(primaryOrSecondaryKey)
                                                                             withLUISAppID:(luisAppID)
                                                                            withLUISSecret:(luisSubscriptionID)
                                                                              withProtocol:(self)];
            dataClient = dataIntentClient;
        }
    }
}

```

Klientbiblioteket tillhandahåller redan implementerat erkännande klientklasser för vanliga scenarier för taligenkänning:

* `DataRecognitionClient`: Taligenkänning med PCM-data (till exempel från en källa för filen eller ljud). Data har delats upp i buffertar och varje buffert skickas till tal-tjänsten. Inga ändringar görs till buffertar, så att användare kan använda sina egna identifiering av tystnad, om så önskas. Om informationen som tillhandahålls av WAV-filer, kan du skicka data från fil-direkt till servern. Om du har rådata, till exempel ljud kommer via Bluetooth, först skickar du ett sidhuvud i formatet till servern följt av data.
* `MicrophoneRecognitionClient`: Taligenkänning med ljud från mikrofonen. Kontrollera att mikrofonen är på och att data från mikrofonen skickas till tjänsten för taligenkänning. En inbyggd ”tystnad detektor” tillämpas på mikrofonen data innan den skickas till tjänsten för taligenkänning.
* `DataRecognitionClientWithIntent` och `MicrophoneRecognitionClientWithIntent`: förutom igenkänning av text, returnera dessa klienter strukturerad information om avsikten talaren, som dina program kan använda för att driva ytterligare åtgärder. Om du vill använda ”avsikt”, måste du först tränar en modell med hjälp av [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Språk

När du använder `SpeechRecognitionServiceFactory` för att skapa klienten, måste du välja ett språk. En fullständig lista över språk som stöds av Speech-tjänsten, se [språk som stöds](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Du måste också ange `SpeechRecognitionMode` när du skapar klienten med `SpeechRecognitionServiceFactory`:

* `SpeechRecognitionMode_ShortPhrase`: Ett uttryck upp till 15 sekunder långa. Som data skickas till tjänsten, tar klienten emot flera delresultat och ett slutresultat med flera n bästa val.
* `SpeechRecognitionMode_LongDictation`: Ett uttryck upp till två minuter lång. När data skickas till tjänsten, tar klienten emot flera delresultat och flera slutresultat baserat på var servern identifierar paus i meningen.

### <a name="attach-event-handlers"></a>Bifoga händelsehanterare

Du kan koppla olika händelsehanterare till klienten du skapade:

* **Partiell resultat händelser**: den här händelsen anropas varje gång den Speech Service förutsäger vad du kan säger, även innan du slutför talar (om du använder `MicrophoneRecognitionClient`) eller har skickat data (om du använder `DataRecognitionClient`).
* **Felhändelser**: kallas när tjänsten upptäcker ett fel.
* **Avsiktshantering händelser**: namnet på ”WithIntent” klienter (endast i ShortPhrase läge) när slutgiltiga resultatet parsas till en strukturerade JSON-avsikt.
* **Resultera händelser**:
  * I `SpeechRecognitionMode_ShortPhrase` läge, den här händelsen kallas och returnerar n bäst resultat när du har slutfört talar.
  * I `SpeechRecognitionMode_LongDictation` läge, händelsehanteraren anropas flera gånger, baserat på där tjänsten identifierar paus i meningen.
  * **För var och en av de n bästa val**, returneras ett förtroende-värde och ett par olika former av den tolkade texten. Mer information finns i [utdataformat](../Concepts.md#output-format).

## <a name="related-topics"></a>Relaterade ämnen

* [Biblioteksreferens för klienten för iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Kom igång med Microsoft-taligenkänning och/eller avsikten i Java på Android](GetStartedJavaAndroid.md)
* [Kom igång med Microsoft Speech API i JavaScript](GetStartedJSWebsockets.md)
* [Kom igång med Microsoft Speech API via REST](GetStartedREST.md)

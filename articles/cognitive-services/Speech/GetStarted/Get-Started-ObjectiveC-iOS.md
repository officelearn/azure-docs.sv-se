---
title: Kom igång med Microsoft Speech Recognition API i Objective-C på iOS | Microsoft Docs
description: Använd Microsoft Speech Recognition API för att utveckla iOS-program som tal konverteras till text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/29/2017
ms.author: zhouwang
ms.openlocfilehash: bbb8d3975cdab537135b97ca9bbf6e845aa3fa0e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352239"
---
# <a name="get-started-with-the-speech-recognition-api-in-objective-c-on-ios"></a>Kom igång med tal Recognition API i Objective-C på iOS

Med tal Recognition API, kan du utveckla iOS-program som använder tal molntjänst för att konvertera tal till text. API: et stöder direktuppspelning av realtid, så att programmet kan samtidigt och asynkront får partiella resultat samtidigt som det skickar ljud till tjänsten.

Den här artikeln använder ett exempelprogram för att demonstrera grunderna för hur du kommer igång med Speech Recognition API du utvecklar ett iOS-program. En fullständig API-referens, finns det [tal SDK klienten biblioteksreferens](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Kontrollera att Mac XCode IDE är installerad.

### <a name="get-the-client-library-and-examples"></a>Installera klienten bibliotek och exempel

Dikterings-klientbiblioteket och exempel för iOS är tillgängliga på den [tal klient-SDK för iOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på Speech Recognition API och få en kostnadsfri utvärderingsprenumeration nyckel

Dikterings-API: N är en del av kognitiva tjänster (tidigare projekt Oxford). Du kan hämta en kostnadsfri utvärderingsprenumeration nycklar från den [kognitiva abonnemang](https://azure.microsoft.com/try/cognitive-services/) sidan. När du har valt tal-API, Välj **hämta API-nyckel** att hämta nyckeln. Returnerar en primär och en sekundär nyckel. Båda nycklarna är knutna till samma kvoten, så du kan använda antingen nyckel.

Om du vill använda *recognition med avsikt*, måste du också registrera dig för den [språk förstå Intelligent Service (THOMAS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Hämta en nyckel för prenumerationen. Innan du kan använda tal klientbibliotek, måste du ha en [prenumeration nyckeln](https://azure.microsoft.com/try/cognitive-services/).
>
> * Använd din prenumeration nyckel. Med det angivna iOS exempelprogrammet som du behöver uppdatera filen Samples/SpeechRecognitionServerExample/settings.plist med din prenumeration nyckel. Mer information finns i [skapa och kör exempel](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Använd klientbiblioteket tal

Så lägger du till klientbiblioteket i XCode-projekt dessa [instruktioner](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Klienten biblioteksreferens för iOS finns det [webbsidan](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Skapa och kör exempel

Information om hur du bygga och kör exempel finns [README page](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Exempel förklaras

### <a name="create-recognition-clients"></a>Skapa recognition klienter

Följande kod i exemplet visar hur du skapar recognition klientklasser baserat på användarscenarier:

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

Klientbiblioteket innehåller redan implementerade recognition klientklasser för vanliga scenarier för taligenkänning:

* `DataRecognitionClient`: Taligenkänning med PCM data (till exempel från en fil eller ljud källa). Data har delats upp i buffertar och varje buffert skickas till tjänsten för tal. Ingen ändring görs till buffertar, så att användarna kan använda sina egna identifiering av tystnad, om så önskas. Om det finns data från WAV-filer kan du skicka data från höger fil till servern. Om du har rådata, till exempel ljud kommer över Bluetooth, du först skicka ett formulärhuvud till servern följt av data.
* `MicrophoneRecognitionClient`: Taligenkänning med ljud som kommer från mikrofonen. Kontrollera att mikrofonen är påslagen och att data från mikrofonen skickas till tjänsten speech recognition. En inbyggd ”tystnad detektor” tillämpas mikrofon data innan den skickas till tjänsten igenkänning.
* `DataRecognitionClientWithIntent` och `MicrophoneRecognitionClientWithIntent`: förutom recognition text, returnera dessa klienter strukturerade information om avsikten av talare som dina program kan använda för att ge ytterligare åtgärder. Om du vill använda ”avsikt” måste du först tränar en modell med hjälp av [THOMAS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Språk

När du använder `SpeechRecognitionServiceFactory` för att skapa klienten, måste du välja ett språk. Fullständig lista över språk som stöds av tal Service, se [språk som stöds](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Du måste också ange `SpeechRecognitionMode` när du skapar klienten med `SpeechRecognitionServiceFactory`:

* `SpeechRecognitionMode_ShortPhrase`: Ett utterance upp till 15 sekunder långt. Klienten får du flera ofullständiga resultat och en slutresultatet med flera n bästa val som data skickas till tjänsten.
* `SpeechRecognitionMode_LongDictation`: Ett utterance upp till två minuter lång. Klienten får du flera ofullständiga resultat och flera slutliga resultat baserat på där servern identifierar meningen pausar som data skickas till tjänsten.

### <a name="attach-event-handlers"></a>Koppla händelsehanterare

Du kan koppla olika händelsehanterare till klienten du skapade:

* **Partiell resultat händelser**: den här händelsen anropas varje gång tjänsten tal förutsäger vad du kan säger, innan du har talat klart (om du använder `MicrophoneRecognitionClient`) eller har skickat data (om du använder `DataRecognitionClient`).
* **Felhändelser**: anropas när tjänsten upptäcker ett fel.
* **Avsiktshantering händelser**: namnet på ”WithIntent” klienter (endast i ShortPhrase läge) när slutgiltiga resultatet är parsas till en strukturerad JSON-avsikt.
* **Leda händelser**:
  * I `SpeechRecognitionMode_ShortPhrase` -läge för den här händelsen kallas och returnerar n bäst resultat när du har talat klart.
  * I `SpeechRecognitionMode_LongDictation` läge händelsehanteraren anropas flera gånger, baserat på där tjänsten identifierar meningen pausar.
  * **För varje n bästa val**, returneras ett förtroende-värde och ett par olika typer av tolkad text. Mer information finns i [utdataformat](../Concepts.md#output-format).

## <a name="related-topics"></a>Relaterade ämnen

* [Klienten biblioteksreferens för iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Kom igång med Microsoft taligenkänning och/eller avsikten i Java på Android](GetStartedJavaAndroid.md)
* [Kom igång med Microsoft tal-API i JavaScript](GetStartedJSWebsockets.md)
* [Kom igång med Microsoft Speech API via REST](GetStartedREST.md)

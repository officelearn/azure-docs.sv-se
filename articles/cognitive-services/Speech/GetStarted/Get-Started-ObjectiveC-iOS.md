---
title: 'Kom igång med API: et för Taligenkänning i Bing igenkänning i mål-C på iOS | Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: Använd Taligenkänning i Bing igenkännings-API för att utveckla iOS-program som konverterar talade ljud till text.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e42784e6d2751f7e76aec8caf1d6e1f9f09a9fd1
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965939"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Snabbstart: Använd Taligenkänning i Bing igenkännings-API i mål-C på iOS

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Med tal igenkännings-API: et kan du utveckla iOS-program som använder molnbaserad tal tjänst för att konvertera talade ljud till text. API: et stöder strömning i real tid, så att ditt program samtidigt kan ta emot partiella igenkännings resultat asynkront samtidigt som det skickar ljud till tjänsten.

I den här artikeln används ett exempel program för att demonstrera grunderna för hur du kommer igång med API för tal igenkänning för att utveckla ett iOS-program. En fullständig API-referens finns i [referens för tal SDK-klientens bibliotek](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Kontrol lera att Mac XCode IDE är installerat.

### <a name="get-the-client-library-and-examples"></a>Hämta klient biblioteket och exemplen

Klientbibliotek för Speech och exempel för iOS finns på [tal klient-SDK för iOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på API för tal igenkänning och få en prenumerations nyckel för kostnads fri utvärdering

Speech API är en del av Cognitive Services (tidigare Project-Oxford). Du kan få kostnads fria utvärderings prenumerations nycklar från sidan [Cognitive Services prenumeration](https://azure.microsoft.com/try/cognitive-services/) . När du har valt Speech API väljer du **Hämta API-nyckel** för att hämta nyckeln. Den returnerar en primär och sekundär nyckel. Båda nycklarna är knutna till samma kvot, så du kan använda båda nycklarna.

Om du vill använda *igenkänning med avsikten*måste du också registrera dig för [language Understanding intelligent service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Hämta en prenumerations nyckel. Innan du kan använda tal klient bibliotek måste du ha en [prenumerations nyckel](https://azure.microsoft.com/try/cognitive-services/).
>
> * Använd din prenumerations nyckel. Med det tillhandahållna iOS-exempelprogrammet måste du uppdatera filen samples/SpeechRecognitionServerExample/Settings. plist med din prenumerations nyckel. Mer information finns i [skapa och köra exempel](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Använd klientbibliotek för Speech

Följ dessa [instruktioner](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library)om du vill lägga till klient biblioteket i ett Xcode-projekt.

För att hitta klient biblioteks referensen för iOS, se den här [webb sidan](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Skapa och köra exempel

Information om hur du skapar och kör exempel finns på den här [README-sidan](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Exempel på förklaring

### <a name="create-recognition-clients"></a>Skapa igenkännings klienter

Följande kod i exemplet visar hur du skapar igenkännings klient klasser baserat på användar scenarier:

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

Klient biblioteket tillhandahåller förimplementerade igenkännings klient klasser för vanliga scenarier i tal igenkänning:

* `DataRecognitionClient`: Tal igenkänning med PCM-data (till exempel från en fil eller ljud källa). Data delas upp i buffertar och varje buffert skickas till tal tjänsten. Ingen ändring görs i buffertarna, så användare kan använda sin egen tystnads identifiering om du vill. Om data tillhandahålls från WAV-filer kan du skicka data från filen direkt till servern. Om du har rå data, till exempel ljud som kommer via Bluetooth, skickar du först en format rubrik till servern följt av datan.
* `MicrophoneRecognitionClient`: Tal igenkänning med ljud som kommer från mikrofonen. Kontrol lera att mikrofonen är påslagen och att data från mikrofonen skickas till tal igenkännings tjänsten. En inbyggd "tystnads detektor" tillämpas på mikrofon data innan den skickas till igenkännings tjänsten.
* `DataRecognitionClientWithIntent` och `MicrophoneRecognitionClientWithIntent`: Förutom igenkännings text returnerar dessa klienter strukturerad information om syftet med talare, som dina program kan använda för att köra ytterligare åtgärder. Om du vill använda "avsikt" måste du först träna en modell med hjälp av [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Igenkännings språk

När du använder `SpeechRecognitionServiceFactory` för att skapa klienten måste du välja ett språk. En fullständig lista över språk som stöds av tal service finns i [språk som stöds](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Du måste också ange `SpeechRecognitionMode` när du skapar klienten med: `SpeechRecognitionServiceFactory`

* `SpeechRecognitionMode_ShortPhrase`: En uttryck upp till 15 sekunder lång. När data skickas till tjänsten får klienten flera delar av resultaten och ett slut resultat med flera n-bästa alternativ.
* `SpeechRecognitionMode_LongDictation`: En uttryck upp till två minuter lång. När data skickas till tjänsten får klienten flera partiella resultat och flera slut resultat, baserat på var servern identifierar mening pausar.

### <a name="attach-event-handlers"></a>Koppla händelse hanterare

Du kan koppla olika händelse hanterare till klienten som du skapade:

* **Partiella resultat händelser**: Den här händelsen anropas varje gång som tal tjänsten förutsäger vad du kan säga, till och med innan du avslutar tal (om du `MicrophoneRecognitionClient`använder) eller Slutför sändning av data (om `DataRecognitionClient`du använder).
* **Fel händelser**: Anropas när tjänsten identifierar ett fel.
* **Avsikts händelser**: Anropas på "WithIntent"-klienter (endast i ShortPhrase-läge) efter det slutliga igenkännings resultatet parsas till en strukturerad JSON-avsikt.
* **Resultat händelser**:
  * I `SpeechRecognitionMode_ShortPhrase` läget anropas den här händelsen och returnerar n-bästa resultat när du har slutfört inläsningen.
  * I `SpeechRecognitionMode_LongDictation` läget anropas händelse hanteraren flera gånger, baserat på var tjänsten identifierar mening pausar.
  * **För var och en av de n bästa alternativen**returneras ett konfidens värde och några olika former av den tolkade texten. Mer information finns i [utdataformat](../Concepts.md#output-format).

## <a name="related-topics"></a>Relaterade ämnen

* [Klient biblioteks referens för iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Kom igång med Microsoft tal igenkänning och/eller avsikt i Java på Android](GetStartedJavaAndroid.md)
* [Kom igång med Microsoft Speech API i Java Script](GetStartedJSWebsockets.md)
* [Kom igång med Microsoft Speech API via REST](GetStartedREST.md)

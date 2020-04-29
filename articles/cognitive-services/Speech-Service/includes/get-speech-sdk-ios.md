---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 7ce193c2c2f5e10a27550da68a4c2d2fdcd1db7f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399976"
---
:::row:::
    :::column span="3":::
        När du utvecklar för iOS finns det två tal-SDK: er tillgängliga. Mål-C-talet SDK är internt tillgängligt som ett iOS CocoaPod-paket. .NET Speech SDK kan också användas med Xamarin. iOS eftersom det implementerar .NET standard 2,0.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Mer information om hur du använder mål-C tal-SDK med Swift finns i <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Importera mål- <span class="docon docon-navigate-external x-hidden-focus"> </span>c till SWIFT </a>.

### <a name="system-requirements"></a>Systemkrav

- MacOS version 10,3 eller senare
- Mål iOS 9,3 eller senare

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        IOS CocoaPod-paketet är tillgängligt för hämtning och användning med <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode-9.4.1 (eller senare) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Integrated Development Environment (IDE). Börja <a href="https://aka.ms/csspeech/iosbinary" target="_blank">med att hämta binärfilen <span class="docon docon-navigate-external x-hidden-focus"> </span>CocoaPod </a>. Extrahera Pod i samma katalog för den avsedda användningen, skapa en *Podfile* och lista `pod` som `target`.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin. iOS visar hela iOS SDK för .NET-utvecklare. Bygg helt inbyggda iOS-appar med C# eller F # i Visual Studio. Mer information finns i <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin. iOS <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;❤️ &nbsp;        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Ytterligare resurser

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS tal SDK snabb start mål – C källkod<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS Speech SDK snabb starts källa kod<span class="docon docon-navigate-external x-hidden-focus"></span></a>
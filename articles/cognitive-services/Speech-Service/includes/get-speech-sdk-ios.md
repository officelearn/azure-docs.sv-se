---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 1f3864b28ba6f5bdbffe6601d059a21328d56a5e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656734"
---
:::row:::
    :::column span="3":::
        När du utvecklar för iOS finns det två tal-SDK:er tillgängliga. Objective-C Speech SDK är tillgängligt internt som ett iOS CocoaPod-paket. Alternativt kan .NET Speech SDK användas med Xamarin.iOS när den implementerar .NET Standard 2.0.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Mer information om hur du använder Objective-C-talet SDK med Swift finns <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">i Importera <span class="docon docon-navigate-external x-hidden-focus"> </span>Objective-C till Swift </a>.

### <a name="system-requirements"></a>Systemkrav

- En macOS-version 10.3 eller senare
- Rikta in dig på iOS 9.3 eller senare

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        IOS CocoaPod-paketet finns tillgängligt för nedladdning och användning med den integrerade utvecklingsmiljön <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (eller senare) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> (IDE). Först <a href="https://aka.ms/csspeech/iosbinary" target="_blank">ladda ner den <span class="docon docon-navigate-external x-hidden-focus"> </span>binära CocoaPod </a>. Extrahera podden i samma katalog för dess avsedda användning, skapa `pod` en `target` *Podfile* och lista som en .
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
        Xamarin.iOS exponerar hela iOS SDK för .NET-utvecklare. Skapa helt inbyggda iOS-appar med C# eller F# i Visual Studio. Mer information finns i <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin.iOS <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;&nbsp; ❤️        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Ytterligare resurser

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS Speech SDK snabbstart Objective-C källkod<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">snabbstarts Swift-källkod för iOS-tal SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>
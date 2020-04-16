---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 57de7f1e7c37fec66cda666d3f144e52849a026f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400048"
---
När du utvecklar för macOS finns det tre Tal-SDK:er tillgängliga.

- Objective-C Speech SDK finns internt som ett CocoaPod-paket
- .NET Speech SDK kan användas med **Xamarin.Mac** när den implementerar .NET Standard 2.0
- Python Speech SDK finns som en PyPI-modul

> [!TIP]
> Mer information om hur du använder Objective-C-talet SDK med Swift finns <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">i Importera <span class="docon docon-navigate-external x-hidden-focus"> </span>Objective-C till Swift </a>.

### <a name="system-requirements"></a>Systemkrav

- En macOS version 10.13 eller senare

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        MacOS CocoaPod-paketet är tillgängligt för nedladdning och användning med <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (eller senare) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> integrerad utvecklingsmiljö (IDE). Först <a href="https://aka.ms/csspeech/macosbinary" target="_blank">ladda ner den <span class="docon docon-navigate-external x-hidden-focus"> </span>binära CocoaPod </a>. Extrahera podden i samma katalog för dess avsedda användning, skapa `pod` en `target` *Podfile* och lista som en .
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

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.11.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac (på andra)](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac exponerar hela macOS SDK för .NET-utvecklare för att skapa inbyggda Mac-program med C#. Mer information finns i <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin.Mac <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Ytterligare resurser

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS Tal SDK snabbstart Objective-C källkod<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS Tal SDK snabbstart Snabb källkod<span class="docon docon-navigate-external x-hidden-focus"></span></a>
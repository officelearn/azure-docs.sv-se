---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 788c8f8093cbc70857727b5c3f89e1719f3ac171
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035813"
---
:::row:::
    :::column span="3":::
        Tal-SDK för Java Script är tillgängligt som ett NPM-paket, se <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices-Speech <span class="docon docon-navigate-external x-hidden-focus"></span> -SDK</a> och det är Companion GitHub databas <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">kognitiv-Services-Speech-SDK <span class="docon docon-navigate-external x-hidden-focus"></span> -JS </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Även om tal-SDK för Java Script är tillgängligt som ett NPM-paket, så kan både Node.js och klient webbläsare använda IT-överväga de olika arkitektoniska konsekvenserna av varje miljö. <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Dokument objekts modellen (dom <span class="docon docon-navigate-external x-hidden-focus"></span> )</a> är till exempel inte tillgänglig för program på Server sidan, precis som <a href="https://nodejs.org/api/fs.html" target="_blank">fil systemet <span class="docon docon-navigate-external x-hidden-focus"></span> </a> inte är tillgängligt för program på klient sidan.

### <a name="nodejs-package-manager-npm"></a>Node.js Package Manager (NPM)

Om du vill installera tal-SDK för Java Script kör du följande `npm install` kommando nedan.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Mer information finns i <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">snabb <span class="docon docon-navigate-external x-hidden-focus"></span> starten förNode.js tal SDK </a>.

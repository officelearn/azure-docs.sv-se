---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 6dd4dfd0edd334005cc2af51a46f9ca2e634272f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399863"
---
:::row:::
    :::column span="3":::
        JavaScript Speech SDK är tillgängligt som ett npm-paket, se <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> och det är följeslagare GitHub-databasen <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Även om JavaScript Speech SDK är tillgängligt som ett npm-paket, kan därför både Node.js och klientwebbläsare konsumera det - tänk på de olika arkitektoniska konsekvenserna av varje miljö. <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Dokumentobjektmodellen <span class="docon docon-navigate-external x-hidden-focus"></span> (DOM)</a> är till exempel inte tillgänglig för serverprogram på samma sätt som <a href="https://nodejs.org/api/fs.html" target="_blank">filsystemet <span class="docon docon-navigate-external x-hidden-focus"></span> </a> inte är tillgängligt för klientprogram.

### <a name="nodejs-package-manager-npm"></a>Nod.js pakethanterare (NPM)

Om du vill installera JavaScript Speech `npm install` SDK kör du följande kommando nedan.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Mer information finns i <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">snabbstarten <span class="docon docon-navigate-external x-hidden-focus"> </span>För nod.js Tal SDK </a>.

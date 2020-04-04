---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 1f1512fbd766faed3922e4bdf8a47dba0de69864
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656502"
---
:::row:::
    :::column span="3":::
        JavaScript Speech SDK är tillgängligt som ett npm-paket, se <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> och det är följeslagare GitHub-databasen <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Även om JavaScript Speech SDK är tillgängligt som ett npm-paket, kan därför både klientwebbläsare och Node.js konsumera det - tänk på de olika arkitektoniska konsekvenserna av varje miljö. <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Dokumentobjektmodellen <span class="docon docon-navigate-external x-hidden-focus"></span> (DOM)</a> är till exempel inte tillgänglig för serverprogram på samma sätt som <a href="https://nodejs.org/api/fs.html" target="_blank">filsystemet <span class="docon docon-navigate-external x-hidden-focus"></span> </a> inte är tillgängligt för klientprogram.

### <a name="nodejs-package-manager-npm"></a>Nod.js pakethanterare (NPM)

Om du vill installera JavaScript Speech `npm install` SDK kör du följande kommando nedan.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML-skripttagg

Du kan också direkt `<script>` inkludera en tagg `<head>` i HTMLs-elementet och förlita dig på <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM-syndikatet <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

Mer information finns i <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">snabbstarten <span class="docon docon-navigate-external x-hidden-focus"> </span>för tal i webbläsarens tal </a>.

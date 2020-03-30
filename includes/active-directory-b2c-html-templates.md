---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189043"
---
## <a name="sample-templates"></a>Exempelmallar
Du hittar exempelmallar för anpassning av användargränssnittet här:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Det här projektet innehåller följande mallar:
- [Ocean Blå](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Skiffer Grå](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Så här använder du exemplet:

1. Klona reporäntan på din lokala dator. Välj en `/ocean_blue` mallmapp eller `/slate_gray`.
1. Ladda upp alla filer under `/assets` mallmappen och mappen till Blob-lagring enligt beskrivningen i föregående avsnitt.
1. Öppna sedan `\*.html` varje fil i `/ocean_blue` roten på antingen eller `/slate_gray`, ersätta alla instanser av relativa webbadresser med url:erna för de css-, images- och teckensnittsfiler som du laddade upp i steg 2. Ett exempel:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Till
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Spara `\*.html` filerna och ladda upp dem till Blob-lagring.
1. Ändra nu principen och peka på HTML-filen, som tidigare nämnts.
1. Om teckensnitt, bilder eller CSS saknas visas kontrollerar du dina \*referenser i tilläggsprincipen och HTML-filerna.

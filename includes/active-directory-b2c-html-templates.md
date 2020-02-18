---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: marsma
ms.openlocfilehash: 264604bfdf0c514e6464854f431addbc9d2dcdef
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373099"
---
## <a name="sample-templates"></a>Exempelmallar
Du hittar exempel på mallar för anpassning av användar gränssnittet här:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Det här projektet innehåller följande mallar:
- [Havet, blå](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Platta, grå](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Så här använder du exemplet:

1. Klona lagrings platsen på den lokala datorn. Välj en mall för mappen `/ocean_blue` eller `/slate_gray`.
1. Ladda upp alla filer under mappen mallar och mappen `/assets`, till Blob Storage enligt beskrivningen i föregående avsnitt.
1. Sedan öppnar du varje `\*.html` fil i roten för antingen `/ocean_blue` eller `/slate_gray`, ersätter alla instanser av relativa URL: er med URL: erna för de CSS-, bilder-och fonts-filer som du laddade upp i steg 2. Exempel:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Till 
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Spara `\*.html`-filerna och överför dem till Blob Storage.
1. Ändra principen genom att peka på HTML-filen som tidigare nämnts.
1. Om du ser saknade teckensnitt, bilder eller CSS, kontrollerar du referenserna i tillägg-principen och \*. html-filer.

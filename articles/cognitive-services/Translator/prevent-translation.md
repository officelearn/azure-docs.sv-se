---
title: Förhindra innehålls översättning – Translator Text API
titleSuffix: Azure Cognitive Services
description: Förhindra översättning av innehåll med Translator Text API. Med Translator Text API kan du tagga innehåll så att det inte översätts.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: c7be4a0ea1a9d24a8b262132632a0bbb63ae1b96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80052487"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Förhindra översättning av innehåll med Translator Text API

Med Translator Text API kan du tagga innehåll så att det inte översätts. Till exempel kanske du vill tagga kod, ett varumärkes namn eller ett ord/en fras som inte är meningsfull när den lokaliseras.

## <a name="methods-for-preventing-translation"></a>Metoder för att förhindra Översättning

1. Tagga ditt innehåll med `notranslate`. Det är avsiktligt att detta fungerar endast när textType anges som HTML

   Exempel:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Använd den [dynamiska ord listan](dynamic-dictionary.md) för att ange en speciell översättning.

3. Överför inte strängen till Translator Text API för översättning.

4. Anpassad översättare: Använd en [ord lista i en anpassad översättare](custom-translator/what-is-dictionary.md) för att föreskriva översättning av en fras med en sannolikhet på 100%.


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Undvik översättning i ditt Translator API-anrop](reference/v3-0-translate.md)

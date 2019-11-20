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
ms.date: 11/18/2019
ms.author: swmachan
ms.openlocfilehash: dd3684cbd7c03851bfcc75293a9690f77b4652b2
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184821"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Förhindra översättning av innehåll med Translator Text API

Med Translator Text API kan du tagga innehåll så att det inte översätts. Till exempel kanske du vill tagga kod, ett varumärkes namn eller ett ord/en fras som inte är meningsfull när den lokaliseras.

## <a name="methods-for-preventing-translation"></a>Metoder för att förhindra Översättning
1. Återvänd till en Twitter-tagg @somethingtopassthrough eller #somethingtopassthrough. Avescape efter översättning. Detta är det reguljära uttrycket för giltiga Twitter-Taggar: `\B@[A-Za-z]+[A-Za-z0-9_]+)`. En tagg ska inledas med ett @-tecken följt av ett tecken och följt av ett eller flera tecken, siffror eller under streck. Vi rekommenderar att du håller taggarna korta och att den inledande taggen måste föregås av ett blank steg.

2. Tagga ditt innehåll med `notranslate`.

   Exempel:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Använd den [dynamiska ord listan](dynamic-dictionary.md) för att ange en speciell översättning.

4. Överför inte strängen till Translator Text API för översättning.

5. Anpassad översättare: Använd en [ord lista i en anpassad översättare](custom-translator/what-is-dictionary.md) för att föreskriva översättning av en fras med en sannolikhet på 100%.


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Undvik översättning i ditt Translator API-anrop](reference/v3-0-translate.md)

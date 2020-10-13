---
title: Förhindra innehålls översättning – översättare
titleSuffix: Azure Cognitive Services
description: Förhindra översättning av innehåll med Translator. Med Translator kan du tagga innehåll så att det inte översätts.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: ec39b3692a90f22409e85b5502d3ea874e3282d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91742068"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Förhindra översättning av innehåll med Translator

Med Translator kan du tagga innehåll så att det inte översätts. Det kan till exempel vara bra att tagga kod, ett varumärkesnamn eller ett ord/en fras som inte är begriplig när den lokaliseras.

## <a name="methods-for-preventing-translation"></a>Metoder för att förhindra Översättning

1. Tagga ditt innehåll med `notranslate` . Det är avsiktligt att detta fungerar endast när textType anges som HTML

   Exempel:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Tagga ditt innehåll med `translate="no"` . Detta fungerar bara när textType anges som HTML

   Exempel:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>

3. Use the [dynamic dictionary](dynamic-dictionary.md) to prescribe a specific translation.

4. Don't pass the string to the Translator for translation.

5. Custom Translator: Use a [dictionary in Custom Translator](custom-translator/what-is-dictionary.md) to prescribe the translation of a phrase with 100% probability.


## Next steps
> [!div class="nextstepaction"]
> [Use the Translate operation to translate text](reference/v3-0-translate.md)

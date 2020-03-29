---
title: Förhindra innehållsöversättning - Translator Text API
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052487"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Så här förhindrar du översättning av innehåll med Translator Text API

Med Translator Text API kan du tagga innehåll så att det inte översätts. Du kanske till exempel vill tagga kod, ett varumärke eller ett ord/en fras som inte är meningsfullt när den är lokaliserad.

## <a name="methods-for-preventing-translation"></a>Metoder för att förhindra översättning

1. Tagga ditt `notranslate`innehåll med . Det är avsiktligt att detta fungerar endast när indatatextType är inställd som HTML

   Exempel:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Använd den [dynamiska ordlistan](dynamic-dictionary.md) för att ordinera en viss översättning.

3. Skicka inte strängen till Translator Text API för översättning.

4. Anpassad översättare: Använd en [ordlista i Custom Translator](custom-translator/what-is-dictionary.md) för att ordinera översättning av en fras med 100% sannolikhet.


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Undvik översättning i ditt Translator API-anrop](reference/v3-0-translate.md)

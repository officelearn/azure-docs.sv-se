---
title: Förhindra att innehåll translation – Translator Text API
titlesuffix: Azure Cognitive Services
description: Förhindra översättning av innehåll med Translator Text API.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: a9590a9a38859818e0b609d64fc12e30afd2e09e
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915279"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Hur du förhindrar översättning av innehåll med Translator Text API

Translator Text API kan du tagga innehåll så att de inte konverteras. Du kanske exempelvis vill tagga kod, ett varumärke eller ett ord eller en viss fras som inte passar när lokaliserade.

## <a name="methods-for-preventing-translation"></a>Metoder för att förhindra översättning
1. Återvänd till en Twitter-tagg @somethingtopassthrough eller #somethingtopassthrough. Ta bort-escape efter översättning.

2. Tagga ditt innehåll med `notranslate`.

   Exempel:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Använd den [dynamisk ordlista](dynamic-dictionary.md) att ange en specifik översättning.

4. Strängen inte skicka till Translator Text API för översättning.

5. Anpassade Translator: Använd en [ordlista i anpassade Translator](custom-translator/what-is-dictionary.md) vidtar den föreskrivna översättningen av en mening med 100% sannolikhet.


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Undvika translation i Translator API-anrop](reference/v3-0-translate.md)

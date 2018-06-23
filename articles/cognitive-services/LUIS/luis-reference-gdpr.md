---
title: Export och borttagning av kundinformation - THOMAS - kognitiva Azure-tjänster. Microsoft Docs
description: Referens för export och borttagning av kundinformation från språk förstå service (THOMAS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/23/2018
ms.author: v-geberr;
ms.openlocfilehash: f684b8ab875e2fbb774dc4a29bce25be41b24e6d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355446"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportera och ta bort din kundinformation i språk förstå (THOMAS) i kognitiva Services

## <a name="summary-of-customer-data-request-features"></a>Sammanfattning av kunden datafunktioner för begäran
Språk förstå Intelligent Service (THOMAS) bevarar kunden innehåll för att använda tjänsten, men THOMAS användaren har full kontroll över visa, exportera och ta bort deras data. Detta kan göras via webben THOMAS [portal](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-reference-regions) eller [THOMAS programmässiga API: er](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Kunden innehåll lagras krypterade i Microsoft regionala Azure storage, inklusive följande:

- Kontot användarinnehåll som samlas in vid registreringen
- Utbildningsdata som krävs för att bygga modeller (d.v.s. avsikt & enheter)
- Användarfrågor loggas vid körning för att förbättra användaren modeller
  - Användare kan stänga av loggning av frågor genom att lägga till `&log=false` på begäran information [här](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-resources-faq#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Om du tar bort kundinformation
THOMAS användare har fullständig behörighet att ta bort allt användarinnehåll, antingen via webbportalen THOMAS eller THOMAS programmässiga API: er. I följande tabell visas länkar hjälpa till med båda:

| | **Användarkonto** | **Programmet** | **Utterance(s)** | **Slutanvändarens frågor** |
| --- | --- | --- | --- | --- |
| **Portal** | [Länk](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-account-settings) | [Länk](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) | [Länk](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) | [Länk](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) |
| **API:er** | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportera kundinformation
THOMAS användare har fullständig behörighet att visa information på portalen, men den måste exporteras via THOMAS programmässiga API: er. I följande tabell visas länkar hjälpa till med export av data via THOMAS programmässiga API: er:

| | **Användarkonto** | **Programmet** | **Utterance(s)** | **Slutanvändarens frågor** |
| --- | --- | --- | --- | --- |
| **API:er** | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [THOMAS regioner referens](./luis-reference-regions.md)

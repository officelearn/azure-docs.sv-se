---
title: Export och borttagning av kunddata - LUIS - Azure Cognitive Services || Microsoft Docs
description: Referens för export och borttagning av kunddata från tjänst för Språkförståelse (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/23/2018
ms.author: v-geberr;
ms.openlocfilehash: af3a96c978a3b22fcbc6296642e4749c863dff9e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928581"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportera och ta bort dina kunddata i Språkförståelse (LUIS) i Cognitive Services

## <a name="summary-of-customer-data-request-features"></a>Sammanfattning av kundens datafunktioner
Language Understanding Intelligent Service (LUIS) bevarar kunden innehåll att driva tjänsten, men LUIS-användaren har fullständig kontroll över visa, exportera och ta bort sina data. Detta kan göras via webben LUIS [portal](luis-reference-regions.md) eller [LUIS programmatisk API: er](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Kunden innehåll lagras krypterade i Microsoft regionala Azure-lagring, inklusive följande:

- Användarinnehåll för kontot som samlas in vid registrering
- Träningsdata som krävs för att skapa modeller (d.v.s. avsikt & entiteter)
- Användarfrågor loggas vid körning för att förbättra användare-modeller
  - Användare kan stänga av loggning av frågor genom att lägga till `&log=false` på begäran, beskriver [här](luis-resources-faq.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Tar bort kunddata
LUIS-användare har fullständig behörighet att ta bort allt användarinnehåll, antingen via webbportalen LUIS eller LUIS programmatisk API: er. I följande tabell visas länkar som hjälper till med båda:

| | **Användarkonto** | **Programmet** | **Utterance(s)** | **Slutanvändaren frågor** |
| --- | --- | --- | --- | --- |
| **Portal** | [Länk](luis-how-to-account-settings.md) | [Länk](luis-how-to-start-new-app.md#delete-app) | [Länk](luis-how-to-start-new-app.md#delete-app) | [Länk](luis-how-to-start-new-app.md#delete-app) |
| **API:er** | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportera kunddata
LUIS-användare har fullständig behörighet att visa data i portal, men den måste exporteras via LUIS programmatisk API: er. I följande tabell visas länkar som hjälper till med data export LUIS-programmatisk API: er:

| | **Användarkonto** | **Programmet** | **Utterance(s)** | **Slutanvändaren frågor** |
| --- | --- | --- | --- | --- |
| **API:er** | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [LUIS regioner referens](./luis-reference-regions.md)

---
title: Exportera och ta bort data
titleSuffix: Azure Cognitive Services
description: 'Language Understanding Intelligent Service (LUIS) bevarar kunden innehåll att driva tjänsten, men LUIS-användaren har fullständig kontroll över visa, exportera och ta bort sina data. Detta kan göras via webbportalen LUIS eller LUIS programmatisk API: er.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 62d6dce4ccb5d4badbcf9b02cbbcda576ee63212
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869261"
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

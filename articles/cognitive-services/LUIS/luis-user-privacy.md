---
title: Exportera & ta bort data - LUIS
titleSuffix: Azure Cognitive Services
description: Du har full kontroll över visning, export och radering av deras data. Ta bort kunddata för att säkerställa sekretess och efterlevnad.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 4e3e0d04b0086905b80e26fb4f838c36b5b5545e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273366"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportera och ta bort dina kunddata i Språk understanding (LUIS) i Cognitive Services

Ta bort kunddata för att säkerställa sekretess och efterlevnad.

## <a name="summary-of-customer-data-request-features"></a>Sammanfattning av funktionerna för kunddatabegäran
Språk understanding Intelligent Service (LUIS) bevarar kundinnehåll för att driva tjänsten, men LUIS-användaren har full kontroll över visning, export och radering av sina data. Detta kan göras via LUIS [webbportal eller](luis-reference-regions.md) [LUIS Authoring (även känd som Programmatic) API: er](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Kundinnehåll lagras krypterat i Microsofts regionala Azure-lagring och omfattar:

- Användarkontoinnehåll som samlas in vid registreringen
- Utbildningsdata som krävs för att bygga modellerna
- Loggade användarfrågor som används av [aktiv inlärning](luis-concept-review-endpoint-utterances.md) för att förbättra modellen
  - Användare kan inaktivera frågeloggning `&log=false` genom att lägga till begäran, information [här](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Ta bort kunddata
LUIS-användare har full kontroll över att ta bort användarinnehåll, antingen via LUIS-webbportalen eller LUIS Authoring -API:erna (kallas även Programmatic). I följande tabell visas länkar som hjälper till med båda:

| | **Användarkonto** | **Program** | **Exempel yttrande(er)** | **Frågor från slutanvändare** |
| --- | --- | --- | --- | --- |
| **Portal** | [Länk](luis-concept-data-storage.md#delete-an-account) | [Länk](luis-how-to-start-new-app.md#delete-app) | [Länk](luis-concept-data-storage.md#utterances-in-an-intent) | [Aktiva inlärningsyttranden](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Yttranden för loggade uttryck](luis-concept-data-storage.md#disable-logging-utterances) |
| **API:er** | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportera kunddata
LUIS-användare har full kontroll för att visa data på portalen, men det måste exporteras via LUIS Authoring (kallas även Programmatic) API: er. I följande tabell visas länkar som hjälper till med dataexport via LUIS Authoring (kallas även Programmatic) API:er:

| | **Användarkonto** | **Program** | **Yttrande(er)** | **Frågor från slutanvändare** |
| --- | --- | --- | --- | --- |
| **API:er** | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Plats för aktivt lärande

För att aktivera [aktiv inlärning](luis-how-to-review-endpoint-utterances.md#enable-active-learning)lagras användarnas loggade yttranden, som tas emot vid de publicerade LUIS-slutpunkterna, i följande Azure-geografiska områden:

* [Europa](#europe)
* [Australien](#australia)
* [USA](#united-states)

Med undantag för aktiva inlärningsdata (nedan) följer LUIS [datalagringsmetoderna för regionala tjänster](https://azuredatacentermap.azurewebsites.net/).

### <a name="europe"></a>Europa

Den [eu.luis.ai](https://eu.luis.ai) portalen och Europa-redigeringen (kallas även programmatiska API:er) finns i Azures Geografi i Europa. Den eu.luis.ai portalen och Europa-redigering (kallas även Programmatiska API:er) stöder distribution av slutpunkter till följande Azure-geografiska områden:

* Europa
* Frankrike
* Storbritannien

När du distribuerar till dessa Azure-geografiska områden lagras de yttranden som tas emot av slutpunkten från slutanvändare av din app i Azures Europa-geografi för aktiv inlärning. Du kan inaktivera aktiv inlärning, se [Inaktivera aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Information om hur du hanterar lagrade yttranden finns i [Ta bort uttryck](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="australia"></a>Australien

[Den au.luis.ai](https://au.luis.ai) portalen och Australien Authoring (kallas även Programmatiska API:er) finns i Azures Australiengeografi. Den au.luis.ai portalen och Australien Authoring (kallas även Programmatic API: er) stöder distribution av slutpunkter till följande Azure-geografiska områden:

* Australien

När du distribuerar till dessa Azure-geografiska områden lagras de yttranden som tas emot av slutpunkten från slutanvändare av din app i Azures Australien-geografi för aktiv inlärning. Du kan inaktivera aktiv inlärning, se [Inaktivera aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Information om hur du hanterar lagrade yttranden finns i [Ta bort uttryck](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="united-states"></a>USA

Den [luis.ai](https://www.luis.ai) portalen och USA Authoring (kallas även Programmatiska API:er) finns i Azures geografi i USA. Den luis.ai portalen och USA Authoring (kallas även Programmatic API: er) stöder distribution av slutpunkter till följande Azure-geografiska områden:

* Azure-geografiska områden som inte stöds av regioner som författarregioner i Europa eller Australien

När du distribuerar till dessa Azure-geografiska områden lagras de yttranden som tas emot av slutpunkten från slutanvändare av din app i Azures geografiska geografi i USA för aktiv inlärning. Du kan inaktivera aktiv inlärning, se [Inaktivera aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Information om hur du hanterar lagrade yttranden finns i [Ta bort uttryck](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [LUIS-regionreferens](./luis-reference-regions.md)

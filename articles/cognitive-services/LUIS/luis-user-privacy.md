---
title: Exportera och ta bort data
titleSuffix: Azure Cognitive Services
description: Ta bort kunddata för att säkerställa sekretess och efterlevnad.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: eb125133138c6de173fdeb90024a9e5d961a929d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597103"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportera och ta bort dina kunddata i Språkförståelse (LUIS) i Cognitive Services

Ta bort kunddata för att säkerställa sekretess och efterlevnad. 

## <a name="summary-of-customer-data-request-features"></a>Sammanfattning av kundens datafunktioner
Language Understanding Intelligent Service (LUIS) bevarar kunden innehåll att driva tjänsten, men LUIS-användaren har fullständig kontroll över visa, exportera och ta bort sina data. Detta kan göras via webben LUIS [portal](luis-reference-regions.md) eller [LUIS redigering (även kallat programmässiga) API: er](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Kunden innehåll lagras krypterade i Microsoft regionala Azure-lagring, inklusive följande:

- Användarinnehåll för kontot som samlas in vid registrering
- Träningsdata som krävs för att skapa modeller
- Inloggade användare frågorna som används av [aktiv inlärning](luis-concept-review-endpoint-utterances.md) för att förbättra modellen
  - Användare kan stänga av loggning av frågor genom att lägga till `&log=false` på begäran, beskriver [här](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Tar bort kunddata
LUIS-användare har fullständig behörighet att ta bort alla användare som är innehåll, antingen via webbportalen LUIS eller API: erna LUIS redigering (även kallat Programmatic). I följande tabell visas länkar som hjälper till med båda:

| | **Användarkonto** | **Programmet** | **Exempel Utterance(s)** | **Slutanvändaren frågor** |
| --- | --- | --- | --- | --- |
| **Portal** | [Länk](luis-concept-data-storage.md#delete-an-account) | [Länk](luis-how-to-start-new-app.md#delete-app) | [Länk](luis-concept-data-storage.md#utterances-in-an-intent) | [Aktiv inlärning yttranden](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Loggade yttranden](luis-concept-data-storage.md#disable-logging-utterances) |
| **API:er** | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportera kunddata
LUIS-användare har fullständig behörighet att visa data i portal, men den måste exporteras via LUIS redigering (även kallat programmässiga) API: erna. I följande tabell visas länkar som hjälper till med export av data via LUIS redigering (även kallat programmässiga) API: erna:

| | **Användarkonto** | **Programmet** | **Utterance(s)** | **Slutanvändaren frågor** |
| --- | --- | --- | --- | --- |
| **API:er** | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Platsen för aktiv inlärning

Aktivera [aktiv inlärning](luis-how-to-review-endpoint-utterances.md#enable-active-learning), användarnas loggade yttranden, tas emot på de publicerade LUIS-slutpunkterna, lagras i de följande Azure geografiska områden:

* [Europa](#europe)
* [Australien](#australia)
* [USA](#united-states)

Med undantag för aktiv inlärning data (beskrivs nedan), LUIS följer den [metoder för lagring av data för regionala tjänster](http://azuredatacentermap.azurewebsites.net/). 

### <a name="europe"></a>Europa

Den [eu.luis.ai](https://eu.luis.ai) portal och Europa Redigera (även kallat programmatisk API: er) finns i Azures Europa geografi. Eu.luis.ai-portalen och Europa Redigera (även kallat programmatisk API: er) stöder distribution av slutpunkter till följande Azure områden:

* Europa
* Frankrike
* Storbritannien

När du distribuerar till dessa geografiska Azure-områden, kommer yttranden som tagits emot av slutpunkten från slutanvändare i din app att lagras i Azures Europa geografiskt område för aktiv inlärning. Du kan inaktivera aktiv inlärning, se [inaktivera aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning). För att hantera lagrade yttranden Se [ta bort uttryck](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Australien

Den [au.luis.ai](https://au.luis.ai) portal och Australien Redigera (även kallat programmatisk API: er) finns i Azures Australien geografi. Au.luis.ai-portalen och Australien Redigera (även kallat programmatisk API: er) stöder distribution av slutpunkter till följande Azure områden:

* Australien

När du distribuerar till dessa geografiska Azure-områden, kommer yttranden som tagits emot av slutpunkten från slutanvändare i din app att lagras i Azures Australien geografiskt område för aktiv inlärning. Du kan inaktivera aktiv inlärning, se [inaktivera aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning). För att hantera lagrade yttranden Se [ta bort uttryck](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>USA

Den [luis.ai](https://www.luis.ai) portal och USA Redigera (även kallat programmatisk API: er) finns i Azures USA geografi. Luis.ai-portalen och USA Redigera (även kallat programmatisk API: er) stöder distribution av slutpunkter till följande Azure områden:

* Azure geografiska områden som inte stöds av Europa eller Australien redigering regioner

När du distribuerar till dessa geografiska Azure-områden, kommer yttranden som tagits emot av slutpunkten från slutanvändare i din app att lagras i Azures USA geografiskt område för aktiv inlärning. Du kan inaktivera aktiv inlärning, se [inaktivera aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning). För att hantera lagrade yttranden Se [ta bort uttryck](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [LUIS regioner referens](./luis-reference-regions.md)

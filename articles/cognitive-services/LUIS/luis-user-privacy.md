---
title: Exportera & ta bort data – LUIS
titleSuffix: Azure Cognitive Services
description: Du har fullständig kontroll över att visa, exportera och ta bort data. Ta bort kund information för att säkerställa sekretess och efterlevnad.
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273366"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportera och ta bort dina kunddata i Språkförståelse (LUIS) i Cognitive Services

Ta bort kund information för att säkerställa sekretess och efterlevnad.

## <a name="summary-of-customer-data-request-features"></a>Sammanfattning av kundens datafunktioner
Language Understanding Intelligent Service (LUIS) bevarar kunden innehåll att driva tjänsten, men LUIS-användaren har fullständig kontroll över visa, exportera och ta bort sina data. Detta kan göras via LUIS- [webbportalen](luis-reference-regions.md) eller [Luis Authoring (kallas även programmerings programmering)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Kunden innehåll lagras krypterade i Microsoft regionala Azure-lagring, inklusive följande:

- Användarinnehåll för kontot som samlas in vid registrering
- Tränings data som krävs för att bygga modeller
- Loggade användar frågor som används av [aktiv inlärning](luis-concept-review-endpoint-utterances.md) för att förbättra modellen
  - Användare kan stänga av loggning av frågor genom att lägga till `&log=false` i begäran, information [här](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Tar bort kunddata
LUIS-användare har fullständig kontroll för att ta bort alla användar innehåll, antingen via LUIS-webbportalen eller LUIS redigering (även kallat programmatisk) API: er. I följande tabell visas länkar som hjälper till med båda:

| | **Användar konto** | **Applicering** | **Exempel på uttryck** | **Slut användar frågor** |
| --- | --- | --- | --- | --- |
| **Portal** | [Länk](luis-concept-data-storage.md#delete-an-account) | [Länk](luis-how-to-start-new-app.md#delete-app) | [Länk](luis-concept-data-storage.md#utterances-in-an-intent) | [Active Learning-yttranden](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Loggade yttranden](luis-concept-data-storage.md#disable-logging-utterances) |
| **API:er** | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportera kunddata
LUIS-användare har fullständig kontroll för att visa data på portalen, men de måste exporteras genom LUIS-redigering (även kallat programmering). I följande tabell visas länkar som hjälper dig med data exporter via LUIS Authoring (kallas även program mässig) API: er:

| | **Användar konto** | **Applicering** | **Uttryck (s)** | **Slut användar frågor** |
| --- | --- | --- | --- | --- |
| **API:er** | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Plats för aktiv inlärning

För att aktivera [aktiv inlärning](luis-how-to-review-endpoint-utterances.md#enable-active-learning)lagras användarens loggade yttranden som togs emot vid de publicerade Luis-slutpunkterna i följande Azure-geografiska områden:

* [Europa](#europe)
* [Australien](#australia)
* [USA](#united-states)

Med undantag för aktiva inlärnings data (beskrivs nedan) följer LUIS [data lagrings metoder för regionala tjänster](https://azuredatacentermap.azurewebsites.net/).

### <a name="europe"></a>Europa

[EU.Luis.AI](https://eu.luis.ai) -portalen och Europa-redigering (även kallat programmerings-API: er) finns i Azures Europa geografi. Eu.luis.ai-portalen och Europa-redigering (även kallat programmerings-API: er) stöder distribution av slut punkter till följande Azure-geografiska områden:

* Europa
* Frankrike
* Storbritannien

När du distribuerar till dessa Azure-geografiska områden kommer yttranden som tagits emot av slut punkten från slutanvändare av appen att lagras i Azures Europa geografi för aktiv inlärning. Du kan inaktivera aktiv inlärning i [inaktivera aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Information om hur du hanterar lagrade yttranden finns i [ta bort uttryck](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="australia"></a>Australien

[Au.Luis.AI](https://au.luis.ai) -portalen och Australien-redigering (även kallat programmerings-API: er) finns i Azures geografi region i Australien. Au.luis.ai-portalen och Australien-redigering (även kallat programmerings-API: er) stöder distribution av slut punkter till följande Azure-geografiska områden:

* Australien

När du distribuerar till dessa Azure-geografiska områden kommer de yttranden som tas emot av slut punkten från slutanvändare av appen att lagras i Azures region för Australien för aktiv inlärning. Du kan inaktivera aktiv inlärning i [inaktivera aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Information om hur du hanterar lagrade yttranden finns i [ta bort uttryck](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="united-states"></a>USA

[Luis.AI](https://www.luis.ai) -portalen och USA redigering (även kallat programmerings-API: er) finns i azures USA geografi. Luis.ai-portalen och USA redigering (även kallat programmerings-API: er) stöder distribution av slut punkter till följande Azure-geografiska områden:

* Azure-geografiska områden stöds inte av redigerings regionerna Europa eller Australien

När du distribuerar till dessa Azure-geografiska områden kommer yttranden som tagits emot av slut punkten från slutanvändare av appen att lagras i Azures USA geografi för aktiv inlärning. Du kan inaktivera aktiv inlärning i [inaktivera aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Information om hur du hanterar lagrade yttranden finns i [ta bort uttryck](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för LUIS-regioner](./luis-reference-regions.md)

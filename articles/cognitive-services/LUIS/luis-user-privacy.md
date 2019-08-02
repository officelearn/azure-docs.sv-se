---
title: Exportera & ta bort data – LUIS
titleSuffix: Azure Cognitive Services
description: Ta bort kund information för att säkerställa sekretess och efterlevnad.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: e234f88d6e735f33be253cacb373baef63c605c0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559986"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportera och ta bort dina kunddata i Språkförståelse (LUIS) i Cognitive Services

Ta bort kund information för att säkerställa sekretess och efterlevnad. 

## <a name="summary-of-customer-data-request-features"></a>Sammanfattning av kundens datafunktioner
Language Understanding Intelligent Service (LUIS) bevarar kunden innehåll att driva tjänsten, men LUIS-användaren har fullständig kontroll över visa, exportera och ta bort sina data. Detta kan göras via LUIS-webbportalen eller [Luis Authoring (kallas även programmerings programmering)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f). [](luis-reference-regions.md)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Kunden innehåll lagras krypterade i Microsoft regionala Azure-lagring, inklusive följande:

- Användarinnehåll för kontot som samlas in vid registrering
- Tränings data som krävs för att bygga modeller
- Loggade användar frågor som används av [aktiv inlärning](luis-concept-review-endpoint-utterances.md) för att förbättra modellen
  - Användare kan stänga av loggning av frågor genom att lägga till `&log=false` på begäran, beskriver [här](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Tar bort kunddata
LUIS-användare har fullständig kontroll för att ta bort alla användar innehåll, antingen via LUIS-webbportalen eller LUIS redigering (även kallat programmatisk) API: er. I följande tabell visas länkar som hjälper till med båda:

| | **Användarkonto** | **Programmet** | **Exempel på uttryck** | **Slutanvändaren frågor** |
| --- | --- | --- | --- | --- |
| **Portal** | [Länk](luis-concept-data-storage.md#delete-an-account) | [Länk](luis-how-to-start-new-app.md#delete-app) | [Länk](luis-concept-data-storage.md#utterances-in-an-intent) | [Active Learning-yttranden](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Loggade yttranden](luis-concept-data-storage.md#disable-logging-utterances) |
| **API:er** | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportera kunddata
LUIS-användare har fullständig kontroll för att visa data på portalen, men de måste exporteras genom LUIS-redigering (även kallat programmering). I följande tabell visas länkar som hjälper dig med data exporter via LUIS Authoring (kallas även program mässig) API: er:

| | **Användarkonto** | **Programmet** | **Utterance(s)** | **Slutanvändaren frågor** |
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
* Storbritannien och Nordirland

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
> [LUIS regioner referens](./luis-reference-regions.md)

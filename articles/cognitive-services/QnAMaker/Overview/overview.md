---
title: Vad är QnA Maker?
titleSuffix: Azure Cognitive Services
description: Med QnA Maker kan du driva en tjänst för frågor och svar från halvstrukturerat innehåll som Vanliga frågor och svar-dokument, URL:er och produkthandböcker.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: overview
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 16cb2d21094e70e7968e5ca45ec2582b7e60a430
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45541946"
---
# <a name="what-is-qna-maker"></a>Vad är QnA Maker?

Med [QnA Maker](https://qnamaker.ai) kan du driva en tjänst för frågor och svar från halvstrukturerat innehåll som Vanliga frågor och svar-dokument, URL:er och produkthandböcker. Du kan skapa en modell för frågor och svar som är flexibel för användarens frågor och ger svar som du tränar en robot att använda på ett naturligt sätt i konversationsstil.

Tack vare det lättanvända grafiska användargränssnittet kan du skapa, hantera, träna och driftsätta tjänsten utan att behöva utvecklingserfarenhet.

![Översikt](../media/qnamaker-overview-learnabout/overview.png)

## <a name="highlights"></a>Höjdpunkter

- Ett komplett upplevelse **utan kod** för att [skapa en robot för vanliga frågor och svar](https://aka.ms/qnamaker-docs-create-faqbot).
- **Slipp nätverksbegränsningar**. Betala för värd för tjänsten i stället för antalet transaktioner. Mer information finns på [prissättningssidan](https://aka.ms/qnamaker-docs-pricing).
- **Skala enligt dina behov**. Välj lämpliga SKU: er för de enskilda komponenter som passar ditt scenario. Se hur du [väljer kapacitet](https://aka.ms/qnamaker-docs-capacity) för din QnA Maker-tjänst.
- **Fullständig dataefterlevnad**. Data och körningskomponenter distribueras i användarens Azure-prenumeration och inom efterlevnadsgränsen. Mer information finns nedan.

## <a name="key-qna-maker-processes"></a>Nyckelprocesser för QnA Maker

Med en QnA Maker får du två nyckeltjänster för data:

* **Extrahering**: strukturerade data med frågor och svar extraheras från halvstrukturerade datakällor som Vanliga frågor och svar samt produkthandböcker. Extraheringen görs när du skapar kunskapsbasen. Skapa din kunskapsbas [här](https://aka.ms/qnamaker-docs-createkb).

* **Matchning**: när kunskapsbasen har [tränats och testats](https://aka.ms/qnamaker-docs-trainkb) [publicerar](https://aka.ms/qnamaker-docs-publishkb) du den. Det här aktiverar en slutpunkt i din QnA Maker-kunskapsbas som du sedan kan använda i din robot eller app. Den här slutpunkten accepterar en användarfråga och svarar med den bästa fråga/svar-matchningen i kunskapsbasen samt förtroendepoäng för matchningen.

## <a name="qna-maker-architecture"></a>QnA Maker-arkitektur

QnA Maker-stacken består av följande delar:

1. **QnA Maker-hanteringstjänster (kontrollplan)**: hanteringsfunktionerna för QnA Maker-kunskapsbasen inklusive det ursprungliga skapandet, uppdatering, träning och publicering. Des här aktiviteterna kan utföras via [portalen](https://qnamaker.ai) eller [hanterings-API:erna](https://aka.ms/qnamaker-v4-apis). Hanteringstjänsterna kommunicerar med körningskomponenten nedan.

2. **QnA Maker-körning (dataplan)**: data och körning distribueras i användarens Azure-prenumeration i den region som väljs. Kundens fråga/svar-innehåll lagras i [Azure Search](https://azure.microsoft.com/services/search/), och körningen distribueras som en [apptjänst](https://azure.microsoft.com/services/app-service/). Du kan även välja att distribuera en [Application Insights](https://azure.microsoft.com/services/application-insights/)-resurs för analys.

![Arkitektur](../media/qnamaker-overview-learnabout/architecture.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en QnA Maker-tjänst](../how-to/set-up-qnamaker-service-azure.md)

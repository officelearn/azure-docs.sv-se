---
title: Vad är QnA Maker?
titleSuffix: Azure Cognitive Services
description: QnA Maker är en molnbaserade API-tjänst som tillämpar anpassad maskininlärningsintelligens för en fråga på användarens naturliga språk för att tillhandahålla det bästa svaret.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 01/24/2019
ms.author: tulasim
ms.openlocfilehash: 1295d071fa3ad99aabd2649f7b7e0b7fe1f86584
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226867"
---
# <a name="what-is-qna-maker"></a>Vad är QnA Maker?

QnA Maker är en molnbaserad API-tjänst som skapar en konversationsanpassat lager för frågor och svar över dina data. 

Med QnA Maker kan du skapa en kunskapsbas (KB) från ditt halvstrukturerade innehåll som Vanliga frågor och svar (FAQ), URL:er, produkthandböcker, supportdokument och anpassade frågor och svar. QnA Maker-tjänsten svarar på dina användares frågor på naturligt språk genom att matcha med det bästa möjliga svaret från vanliga frågor och svar i kunskapsbasen.

Den lättanvända [webbportalen](https://qnamaker.ai) gör att du kan skapa, hantera, träna och publicera tjänsten utan någon utvecklarerfarenhet. När tjänsten har publicerats till en slutpunkt kan ett klientprogram som en chattrobot hantera konversationen med en användare för att få frågor och svara med svaren. 

![Översikt](../media/qnamaker-overview-learnabout/overview.png)

## <a name="key-qna-maker-processes"></a>Nyckelprocesser för QnA Maker

Med en QnA Maker får du två nyckeltjänster för data:

* **Extrahering**: Strukturerade data med frågor och svar extraheras från strukturerade och halvstrukturerade [datakällor](../Concepts/data-sources-supported.md) som Vanliga frågor och svar samt produkthandböcker. Den här extraheringen kan göras som en del av [skapandet](https://aka.ms/qnamaker-docs-createkb) av KB eller senare som en del av redigeringsprocessen.

* **Matchning**: När kunskapsbasen har [tränats och testats](https://aka.ms/qnamaker-docs-trainkb) [publicerar](https://aka.ms/qnamaker-docs-publishkb) du den. Det här aktiverar en slutpunkt i din QnA Maker-kunskapsbas som du sedan kan använda i din robot eller klientapp. Den här slutpunkten accepterar en användarfråga och svarar med det bästa svaret i kunskapsbasen samt förtroendepoäng för matchningen.

```JSON
{
    "answers": [
        {
            "questions": [
                "How do I share a knowledge base with other?"
            ],
            "answer": "Sharing works at the level of a QnA Maker service, i.e. all knowledge bases in the services will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base)how to collaborate on a knowledge base.",
            "score": 70.95,
            "id": 4,
            "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
            "metadata": []
        }
    ]
}

```

## <a name="qna-maker-architecture"></a>QnA Maker-arkitektur

QnA Maker-arkitekturen består av följande två komponenter:

1. **QnA Maker-hanteringstjänster**: Hanteringsfunktionerna för QnA Maker-kunskapsbasen, vilket omfattar den ursprungliga genereringen, uppdateringen, träningen och publiceringen. Des här aktiviteterna kan utföras via [portalen](https://qnamaker.ai) eller [hanterings-API:erna](https://aka.ms/qnamaker-v4-apis). 

2. **Data och körning för QnA Maker**: Detta distribueras i din Azure-prenumeration i din angivna region. Ditt KB-innehåll lagras i [Azure Search](https://azure.microsoft.com/services/search/), och slutpunkten distribueras som en [apptjänst](https://azure.microsoft.com/services/app-service/). Du kan även välja att distribuera en [Application Insights](https://azure.microsoft.com/services/application-insights/)-resurs för analys.

![Arkitektur](../media/qnamaker-overview-learnabout/architecture.png)


## <a name="service-highlights"></a>Tjänstehöjdpunkter

- Ett komplett upplevelse **utan kod** för att [skapa en robot för vanliga frågor och svar](https://aka.ms/qnamaker-docs-create-faqbot).
- **Inga nätverksbegränsningar för förutsägelser**. Betala för värd för tjänsten i stället för antalet transaktioner. Mer information finns på [prissättningssidan](https://aka.ms/qnamaker-docs-pricing).
- **Skala efter behov**. Välj lämpliga SKU: er för de enskilda komponenter som passar ditt scenario. Se hur du [väljer kapacitet](https://aka.ms/qnamaker-docs-capacity) för din QnA Maker-tjänst.
- **Fullständig dataefterlevnad**. Komponenterna för förutsägelsetjänsten distribueras i Azure-prenumerationen och inom efterlevnadsgränsen.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en QnA Maker-tjänst](../how-to/set-up-qnamaker-service-azure.md)

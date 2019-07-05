---
title: Knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: En kunskapsbas med QnA Maker består av en uppsättning fråga/svar (frågor och svar)-par och valfria metadata som är associerade med varje par med frågor och svar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e40af9b2362ee52a1d00f29cdc112d3c2b9a842
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565857"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Vad är en Maker kunskapsbaser?

En kunskapsbas med QnA Maker består av en uppsättning fråga/svar (frågor och svar)-par och valfria metadata som är associerade med varje par med frågor och svar.

## <a name="key-knowledge-base-concepts"></a>Viktiga kunskapsbas-begrepp

* **Frågor** – en fråga som innehåller text som bäst representerar en användarfråga. 
* **Svar** -svar är svaret som returneras när en användarfråga matchas med associerade frågan.  
* **Metadata** -Metadata är taggar som är associerad med ett par frågor och svar och visas i form av nyckel / värde-par. Metadatataggarna används för att filtrera QnA-par och begränsa de över vilken fråga matchar utförs.

En enda QnA som representeras av ett numeriskt QnA-ID har flera varianter av en fråga (alternativa frågor) att mappas till ett enda svar. Dessutom kan varje sådan par kan ha flera metadatafält som är kopplade till den: en nyckel och ett värde.

![QnA Maker kunskapsbaser](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Kunskapsbas innehållsformat

När du mata in formaterade innehållet i en kunskapsbas försöker QnA Maker konvertering av innehåll till markdown. Läs [detta](https://aka.ms/qnamaker-docs-markdown-support) blogg om du vill förstå markdown formaterar klusterverifieringsrapportschemat de flesta chatt-klienter.

Metadatafält består av nyckel / värde-par avgränsade med kolon **(produkt: fragmentering)** . Både nyckel och värde måste vara endast text. Metadatanyckel får inte innehålla några blanksteg. Metadata stöder bara ett värde per nyckel.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Hur QnA Maker bearbetar en användarfråga för att välja det bästa svaret

Den tränade och [publicerade](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker kunskapsbas tar emot en användarfråga, från en bot eller andra klientprogram på den [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Följande diagram illustrerar processen när användarfrågan tas emot.

![Rangordning processen för en användarfråga](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

Processen beskrivs i följande tabell:

|Steg|Syfte|
|--|--|
|1|Klientprogrammet skickar användarfråga till den [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|Qna Maker Förbearbeta användarfråga med språkidentifiering och spellers orddelare.|
|3|Den här Förbearbeta tas att ändra användarfråga för bästa resultat från.|
|4|Den här ändrade frågan skickas till Azure Search-Index, ta emot den `top` antal resultat. Om det inte finns i de här resultaten returneras rätt svar, öka värdet på `top` något. Allmänt värdet 10 för `top` fungerar i 90% av frågor.|
|5|QnA Maker gäller avancerad funktionalisering för att fastställa för hämtade Azure Search-resultaten för användarfråga. |
|6|Den tränade modellen för rankningen använder funktionen poäng från steg 5, att rangordnas Azure-sökresultaten.|
|7|Nya resultaten returneras till klientprogrammet i rankad ordning.|
|||

Funktioner som används inkludera, men är inte begränsad till word semantik och termen på servernivå betydelse i en Kristi och djup inlärda semantiska modeller för att fastställa likheter och relevans mellan två textsträngar.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Livscykeln för utveckling av en kunskapsbas](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Se också

[Översikt över QnA Maker](../Overview/overview.md)

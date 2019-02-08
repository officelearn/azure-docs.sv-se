---
title: Knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: En kunskapsbas med QnA Maker består av en uppsättning fråga/svar (frågor och svar)-par och valfria metadata som är associerade med varje par med frågor och svar.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/18/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 2173bc46471fec6bfbacbda9362e5530075faf18
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857353"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Vad är en Maker kunskapsbaser?

En kunskapsbas med QnA Maker består av en uppsättning fråga/svar (frågor och svar)-par och valfria metadata som är associerade med varje par med frågor och svar.

## <a name="key-knowledge-base-concepts"></a>Viktiga kunskapsbas-begrepp

* **Frågor** – en fråga som innehåller text som bäst representerar en användarfråga. 
* **Svar** -svar är svaret som returneras när en användarfråga matchas med associerade frågan.  
* **Metadata** -Metadata är taggar som är associerad med ett par frågor och svar och visas i form av nyckel / värde-par. Metadatataggarna används för att filtrera QnA-par och begränsa de över vilken fråga matchar utförs.

En enda QnA som representeras av ett numeriskt QnA-ID har flera varianter av en fråga (alternativa frågor) att mappas till ett enda svar. Varje sådan par kan dessutom ha flera metadatafält som är kopplade till den.

![QnA Maker kunskapsbaser](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Kunskapsbas innehållsformat

När du mata in formaterade innehållet i en kunskapsbas försöker QnA Maker konvertering av innehåll till markdown. Läs [detta](https://aka.ms/qnamaker-docs-markdown-support) blogg om du vill förstå markdown formaterar klusterverifieringsrapportschemat de flesta chatt-klienter.

Metadatafält består av nyckel / värde-par avgränsade med kolon **(produkt: fragmentering)**. Både nyckel och värde måste vara endast text. Metadatanyckel får inte innehålla några blanksteg.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Livscykeln för utveckling av en kunskapsbas](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Se också

[Översikt över QnA Maker](../Overview/overview.md)

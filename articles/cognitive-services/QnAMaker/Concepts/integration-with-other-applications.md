---
title: Integrera med andra program – QnA Maker
description: QnA Maker integreras i klient program som chatt-robotar, samt med andra språk bearbetnings tjänster som Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: ff5e64dd31542b1e31df6d9ca709567b322995ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776535"
---
# <a name="design-knowledge-base-for-client-applications"></a>Utforma kunskaps bas för klient program

QnA Maker integreras i klient program som chatt-robotar, samt med andra språk bearbetnings tjänster som Language Understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integrering med en konversations klient

QnA Maker integreras med konversations klient program som [Microsoft bot Framework](https://dev.botframework.com/). Texten som skickas till QnA Maker behöver inte rensas eller omvandlas. QnA Maker accepterar naturliga språk och returnerar det bästa svaret.

## <a name="create-a-bot-without-writing-any-code"></a>Skapa en bot utan att skriva någon kod

När du har publicerat din kunskaps bas skapar du en robot från **publicerings** sidan genom att välja knappen **skapa robot** . Använd [bot-självstudien](../Quickstarts/create-publish-knowledge-base.md) för att lära dig vad som händer när du har valt knappen.

## <a name="providing-multi-turn-conversations"></a>Tillhandahålla flera-turn-konversationer

En bot-klient ger det bästa valda svaret från din kunskaps bas och kan tillhandahålla uppföljnings anvisningar om svaret är en del av ett QnA-par med flera varv. Lär dig [hur du](../how-to/multiturn-conversation.md) lägger till konversations frågor och svars uppsättningar med flera varv i din kunskaps bas.

## <a name="natural-language-processing"></a>Bearbetning av naturligt språk

Medan QnA Maker bearbetar frågor som använder naturlig språk bearbetning kan den också användas som en del av ett större system som svarar på frågor från flera kunskaps baser. Du kan kombinera QnA Maker med en annan kognitiv tjänst, Language Understanding (LUIS) för att tillhandahålla naturlig språk bearbetning innan du får en specifik kunskaps bas. Lär dig mer om när och hur du använder [Luis och QNA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) tillsammans.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om utvecklings cykel [koncept](development-lifecycle-knowledge-base.md) för QNA Maker.
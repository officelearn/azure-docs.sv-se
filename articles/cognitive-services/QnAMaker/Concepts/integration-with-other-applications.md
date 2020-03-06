---
title: Integrera med andra program – QnA Maker
description: QnA Maker integreras i klient program som chatt-robotar, samt med andra språk bearbetnings tjänster som Language Understanding (LUIS).
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 38b275aa2b8cf5768a2bc95634a7ff4892893eda
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300527"
---
# <a name="design-knowledge-base-for-client-applications"></a>Utforma kunskaps bas för klient program

QnA Maker integreras i klient program som chatt-robotar, samt med andra språk bearbetnings tjänster som Language Understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integrering med en konversations klient

QnA Maker integreras med konversations klient program som [Microsoft bot Framework](https://dev.botframework.com/). Texten som skickas till QnA Maker behöver inte rensas eller omvandlas. QnA Maker accepterar naturliga språk och returnerar det bästa svaret.

## <a name="create-a-bot-without-writing-any-code"></a>Skapa en bot utan att skriva någon kod

När du har publicerat din kunskaps bas skapar du en robot från **publicerings** sidan genom att välja knappen **skapa robot** . Använd [bot-självstudien](../Quickstarts/create-publish-knowledge-base.md) för att lära dig vad som händer när du har valt knappen.

## <a name="providing-multi-turn-conversations"></a>Tillhandahålla flera-turn-konversationer

En bot-klient ger det bästa valda svaret från din kunskaps bas och kan tillhandahålla uppföljnings anvisningar om svaret är en del av en QnA uppsättning med flera ingångar. Lär dig [hur du](../how-to/multiturn-conversation.md) lägger till konversations frågor och svars uppsättningar med flera varv i din kunskaps bas.

## <a name="natural-language-processing"></a>Bearbetning av naturligt språk

Medan QnA Maker bearbetar frågor som använder naturlig språk bearbetning kan den också användas som en del av ett större system som svarar på frågor från flera kunskaps baser. Du kan kombinera QnA Maker med en annan kognitiv tjänst, Language Understanding (LUIS) för att tillhandahålla naturlig språk bearbetning innan du får en specifik kunskaps bas. Lär dig mer om när och hur du använder [Luis och QNA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) tillsammans.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om utvecklings cykel [koncept](development-lifecycle-knowledge-base.md) för QNA Maker.
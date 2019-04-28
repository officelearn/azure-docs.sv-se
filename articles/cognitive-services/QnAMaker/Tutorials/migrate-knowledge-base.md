---
title: Migrera kunskapsbaser – QnA Maker
titleSuffix: Azure Cognitive Services
description: Migrera en kunskapsbas kräver exportera från en kunskapsbas och sedan importerar till en annan.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: eac5e43c69cc09c5945316827a35f729c158d47a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61431250"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrera en kunskapsbas med hjälp av export-import

Migrera en kunskapsbas kräver exportera från en kunskapsbas och sedan importerar till en annan. 

## <a name="prerequisites"></a>Nödvändiga komponenter

* Skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* Ställa in en ny [QnA Maker-tjänsten](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrera en kunskapsbas från QnA Maker
1. Logga in på [QnA Maker portal](https://qnamaker.ai).
1. Välj det kunskapsbas som du vill migrera.

1. På den **inställningar** väljer **exportera kunskapsbas** att ladda ned en TSV-fil som innehåller innehållet i kunskapsbasen - frågor, svar, metadata, och datakällan namn från som de var extraherade.

1. Välj **skapa en kunskapsbas** sedan skapa en tom kunskapsbas på den översta menyn. 

    ![Set-datakällor](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Ge din tjänst en **namn.** Dubblettnamn som stöds och specialtecken stöds också.

1. Välj **Skapa**.

    ![Skapa KB](../media/qnamaker-how-to-create-kb/create-kb.png)

1. I den här nya knowledge base, öppna den **inställningar** fliken och markera **Import kunskapsbas**. Detta importerar de frågor, svar och metadata och behåller de namn på datakällor från vilken de extraherades.

   ![Importera kunskapsbas](../media/qnamaker-how-to-migrate-kb/Import.png)

1. **Testa** nya kunskapsbasen panelen Test. Lär dig hur du [testa kunskapsbasen](../How-To/test-knowledge-base.md).
1. **Publicera** i knowledge base. Lär dig hur du [publicera din kunskapsbas](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. Använda slutpunkten i ditt program eller robotar kod. Se här så [skapa QnA-roboten](../Tutorials/create-qna-bot.md).

    ![QnA Maker-värden](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    Nu kan alla kunskapsbas innehållet - frågor, svar och metadata, tillsammans med namnen på källfilerna och URL: er, importeras till nya kunskapsbasen. 

## <a name="chat-logs-and-alterations"></a>Chattloggarna och ändringar
Skiftlägeskänsliga ändringar (synonymer) importeras inte automatiskt. Använd den [V2 API: er](https://aka.ms/qnamaker-v2-apis) att exportera ändringar från den gamla kunskapen och [V4-API: er](https://aka.ms/qnamaker-v4-apis) att flytta ändringar i nya knowledge base.

Det går inte att migrera chattloggarna, eftersom nya kunskapsbasen använder Application Insights för att lagra chattloggarna. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Redigera en kunskapsbas](../How-To/edit-knowledge-base.md)

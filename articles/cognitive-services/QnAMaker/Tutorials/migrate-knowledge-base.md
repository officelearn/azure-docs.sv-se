---
title: Migrera kunskapsbaser förhandsversion – Qna Maker
titleSuffix: Azure Cognitive Services
description: Så här importerar du en kunskapsbas
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: a06a04ba992c8d7e9691e4838d38faaafd48de7a
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48853920"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrera en kunskapsbas med hjälp av export-import
QnA Maker meddelat allmän tillgänglighet den 7 maj 2018 på den \\\build\ konferensen. QnA har Maker en ny arkitektur som bygger på Azure. Kunskapsbaser som skapats med QnA Maker kostnadsfri förhandsversion måste migreras till QnA Maker GA. QnA Maker förhandsversionen kommer att inaktualiseras i November 2018. Mer information om ändringar i QnA Maker GA finns i QnA Maker allmänheten [blogginlägget](https://aka.ms/qnamakerga-blog).

QnA Maker har nu en [prissättningsmodellen](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/qna-maker/).

Förutsättningar
> [!div class="checklist"]
> * Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
> * Konfigurera en ny [QnA Maker-tjänsten](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker-preview-portal"></a>Migrera en kunskapsbas från QnA Maker Preview-portalen
1. Gå till [QnA Maker Preview-portalen](https://aka.ms/qnamaker-old-portal
) och klicka på **Mina tjänster**.
2. Välj kunskapsbas som du vill migrera genom att klicka på redigeringsikonen.

    ![Redigera kunskapsbas](../media/qnamaker-how-to-migrate-kb/preview-editkb.png)

3. Klicka på **hämta kunskapsbas** att ladda ned en TSV-fil som innehåller innehållet i kunskapsbasen - frågor, svar, metadata, och datakällan namn från vilken de extraherades.

    ![Ladda ned kunskapsbas](../media/qnamaker-how-to-migrate-kb/preview-download.png)

4. Logga in på att den [QnA Maker portal](https://qnamaker.ai) med dina autentiseringsuppgifter för azure och klicka på **skapa en kunskapsbas**.
    
5. Om du inte redan har skapat en QnA Maker-tjänsten väljer **skapar du en tjänst för frågor och svar om**. Annars väljer du en QnA Maker-tjänsten från listrutor i steg 2. Välj QnA Maker-tjänsten som är värd för Knowledge Base.

    ![Konfigurera frågor och svar om tjänsten](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

6. Skapa en tom kunskapsbas 

    ![Set-datakällor](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Ge din tjänst en **namn.** Dubblettnamn som stöds och specialtecken stöds också.
    - Hoppa över ladda upp filer eller URL: er som du vill använda data från förhandsversion kunskapsbasen. Nu skapar du en tom kunskapsbas.

7. Välj **Skapa**.

    ![Skapa KB](../media/qnamaker-how-to-create-kb/create-kb.png)

8. I den här nya Knowledge base, öppna den **inställningar** fliken och klicka på **Import kunskapsbas**. Detta importerar de frågor, svar och metadata och behåller de namn på datakällor från vilken de extraherades.

   ![Importera kunskapsbas](../media/qnamaker-how-to-migrate-kb/Import.png)

9. **Testa** nya kunskapsbasen panelen Test. Lär dig hur du [testa kunskapsbasen](../How-To/test-knowledge-base.md).
10. **Publicera** i knowledge base. Lär dig hur du [publicera din kunskapsbas](../How-To/publish-knowledge-base.md).
11. Använd slutpunkt nedan i program / robotar koden. Se här så [skapa QnA-roboten](../Tutorials/create-qna-bot.md).

    ![QnA Maker-värden](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

Nu kan alla kunskapsbas innehållet - frågor, svar och metadata, tillsammans med namnen på källfilerna och URL: er, importeras till nya kunskapsbasen. 

## <a name="chatlogs-and-alterations"></a>Chatlogs och ändringar
Ändringar (synonymer) importeras inte automatiskt. Använd den [V2 API: er](https://aka.ms/qnamaker-v2-apis) att exportera ändringar från förhandsversion stacken och [V4-API: er](https://aka.ms/qnamaker-v4-apis) ska ersättas i den nya stacken.

Det går inte att migrera chatlogs, eftersom den nya stacken använder Application Insights för att lagra chatlogs. Du kan dock hämta chatlogs från den [förhandsversionsportalen](https://aka.ms/qnamaker-old-portal).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Redigera en kunskapsbas](../How-To/edit-knowledge-base.md)

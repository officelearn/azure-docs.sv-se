---
title: Så här importerar du en knowledge base - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Så här importerar du en kunskapsbas
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: ce8f98f9bdb37d5f326e942fe5b5e815e5272c56
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355942"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrera en kunskapsbas med hjälp av export-import
Frågor och svar om Maker annonserats allmän tillgänglighet på 7 maj 2018 på den \\\build\ konferensrum. Frågor och svar om Maker GA har en ny arkitektur som bygger på Azure. Knowledge databaser som skapats med frågor och svar om Maker kostnadsfri förhandsversion måste migreras till frågor och svar om Maker GA. Frågor och svar om Maker Preview kommer att inaktualiseras i November 2018. Mer information om ändringar i frågor och svar om Maker GA finns frågor och svar om Maker GA-meddelande [blogginlägget](https://aka.ms/qnamakerga-blog).

Frågor och svar om Maker har nu en [Prismodell](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/qna-maker/).

Förutsättningar
> [!div class="checklist"]
> * Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
> * Konfigurera en ny [frågor och svar om Maker service](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker-preview-portal"></a>Migrera en kunskapsbas från frågor och svar om Maker Preview portal
1. Gå till [frågor och svar om Maker Preview portal](https://aka.ms/qnamaker-old-portal
) och klicka på **Mina tjänster**.
2. Välj i knowledge base om du vill migrera genom att klicka på redigeringsikonen.

    ![Redigera kunskapsbas](../media/qnamaker-how-to-migrate-kb/preview-editkb.png)

3. Klicka på **hämta kunskapsbas** att hämta en TSV-fil som innehåller innehållet i kunskapsbasen - frågor, svar, metadata, och datakällan namn från vilken de extraherades.

    ![Hämta kunskapsbas](../media/qnamaker-how-to-migrate-kb/preview-download.png)

4. Logga in på att den [frågor och svar om Maker portal](https://qnamaker.ai) med dina autentiseringsuppgifter för azure och klicka på **Skapa ny tjänst**.

    ![Skapa KB ](../media/qnamaker-how-to-create-kb/create-new-service.png)
    
5. Om du inte redan har skapat en tjänst för frågor och svar om Maker väljer **skapar du en tjänst för frågor och svar om**. Annars väljer du en tjänst för frågor och svar om Maker från listrutorna i steg 2. Välj tjänsten frågor och svar om Maker som är värd för Knowledge Base.

    ![Ställ frågor och svar om tjänsten](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

6. Skapa en tom kunskapsbas 

    ![Set-datakällor](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Ge din tjänst en **namn.** Dubblettnamn som stöds och specialtecken stöds också.
    - Hoppa över överföring av filer eller URL: er som du vill använda data från din Preview knowledge base. Nu skapar du en tom knowledge base.

7. Välj **Skapa**.

    ![Skapa KB](../media/qnamaker-how-to-create-kb/create-kb.png)

8. Öppna i nytt Knowledge base på **inställningar** och klicka på **importera kunskapsbas**. Detta importerar frågor, svar och metadata och behåller datakällnamn från vilken de extraherades.

   ![Importera kunskapsbas](../media/qnamaker-how-to-migrate-kb/Import.png)

9. **Testa** nya kunskapsbasen med hjälp av panelen Test. Lär dig hur du [testa kunskapsbasen](../How-To/test-knowledge-base.md).
10. **Publicera** i knowledge base. Lär dig hur du [publicera kunskapsbasen](../How-To/publish-knowledge-base.md).
11. Använd slutpunkten nedan i programmet eller bot koden. Här finns så [skapa frågor och svar om bot](../Tutorials/create-qna-bot.md).

    ![Frågor och svar om Maker värden](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

Nu allt kunskapsbas innehåll - frågor, svar och metadata tillsammans med namnen på källfilerna och URL: er, importeras till den nya knowledge basen. 

## <a name="chatlogs-and-alterations"></a>Chatlogs och ändringar
Ändringar (synonymer) importeras inte automatiskt. Använd den [V2 API: er](https://aka.ms/qnamaker-v2-apis) att exportera ändringar från stacken förhandsgranskning och [V4-API: er](https://aka.ms/qnamaker-v4-apis) ska ersätta i den nya stacken.

Det går inte att migrera chatlogs, eftersom den nya stacken använder Application Insights för att lagra chatlogs. Du kan dock hämta chatlogs från den [förhandsgranskningsportalen](https://aka.ms/qnamaker-old-portal).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Redigera en kunskapsbas](../How-To/edit-knowledge-base.md)

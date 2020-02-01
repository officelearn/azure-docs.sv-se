---
title: Migrera kunskaps baser – QnA Maker
titleSuffix: Azure Cognitive Services
description: För att migrera en kunskaps bas måste du exportera från en kunskaps bas och sedan importera till en annan.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b441eb1e6531030a998fe628ae833b29a5d9fe5a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902047"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrera en kunskapsbas med hjälp av export-import

För att migrera en kunskaps bas måste du exportera från en kunskaps bas och sedan importera till en annan.

## <a name="prerequisites"></a>Krav

* Skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* Konfigurera en ny [QNA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrera en kunskaps bas från QnA Maker
1. Logga in på [QNA Maker Portal](https://qnamaker.ai).
1. Välj den ursprungliga kunskaps basen som du vill migrera.

1. På sidan **Inställningar** väljer du **Exportera kunskaps bas** för att ladda ned en. TSV-fil som innehåller innehållet i ditt ursprung i kunskaps basen – frågor, svar, metadata, uppföljnings frågor och data käll namn som de extraherades från.

1. Välj **skapa en kunskaps bas** på den översta menyn och skapa en _Tom_ kunskaps bas. Det är tomt eftersom när du skapar den, kommer du inte att lägga till några URL: er eller filer. De läggs till under steget importera när du har skapat.

    Konfigurera kunskaps basen. Ange endast det nya namnet på kunskaps basen. Dubblettnamn som stöds och specialtecken stöds också.

    Välj inte något från steg 4 eftersom dessa värden kommer att skrivas över när du importerar filen.

1. I steg 5 väljer du **skapa**.

1. I den här nya kunskaps basen öppnar du fliken **Inställningar** och väljer **Importera kunskaps bas**. Detta importerar frågor, svar, metadata, uppföljnings frågor och behåller de data käll namn som de extraherades från.

   > [!div class="mx-imgBorder"]
   > [![importera kunskaps bas](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Testa** nya kunskapsbasen panelen Test. Lär dig hur du [testa kunskapsbasen](../How-To/test-knowledge-base.md).
1. **Publicera** i knowledge base. Lär dig hur du [publicera din kunskapsbas](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. Använd slut punkten i ditt program eller din robot kod. Se här så [skapa QnA-roboten](../Tutorials/create-qna-bot.md).

    ![QnA Maker-värden](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    Nu kan alla kunskapsbas innehållet - frågor, svar och metadata, tillsammans med namnen på källfilerna och URL: er, importeras till nya kunskapsbasen.

## <a name="chat-logs-and-alterations"></a>Chat-loggar och ändringar
Skift läges känsliga ändringar (synonymer) importeras inte automatiskt. Använd [v4-API: er](https://go.microsoft.com/fwlink/?linkid=2092179) för att flytta ändringarna i den nya kunskaps basen.

Det finns inget sätt att migrera chatt loggar, eftersom den nya kunskaps basen använder Application Insights för att lagra chatt loggar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Redigera en kunskapsbas](../How-To/edit-knowledge-base.md)

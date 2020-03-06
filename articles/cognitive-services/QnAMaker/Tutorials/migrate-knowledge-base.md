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
ms.openlocfilehash: 942932c229ace82a0bf66da7a5421f936b028088
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302567"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrera en kunskapsbas med hjälp av export-import

För att migrera en kunskaps bas måste du exportera från en kunskaps bas och sedan importera till en annan.

## <a name="prerequisites"></a>Förutsättningar

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

1. **Testa** den nya kunskaps basen med hjälp av test panelen. Lär dig hur du [testar din kunskaps bas](../How-To/test-knowledge-base.md).

1. **Publicera** kunskaps basen och skapa en chatt-robot. Lär dig hur du [publicerar din kunskaps bas](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="chat-logs-and-alterations"></a>Chat-loggar och ändringar
Skift läges känsliga ändringar (synonymer) importeras inte automatiskt. Använd [v4-API: er](https://go.microsoft.com/fwlink/?linkid=2092179) för att flytta ändringarna i den nya kunskaps basen.

Det finns inget sätt att migrera chatt loggar, eftersom den nya kunskaps basen använder Application Insights för att lagra chatt loggar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Redigera en kunskaps bas](../How-To/edit-knowledge-base.md)

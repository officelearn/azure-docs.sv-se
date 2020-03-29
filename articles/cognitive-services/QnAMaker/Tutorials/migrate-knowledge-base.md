---
title: Migrera kunskapsbaser - QnA Maker
description: Migrera en kunskapsbas kräver export från en kunskapsbas och sedan importera till en annan.
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 13e5e79bf4eaf6ec59e41b3e12aa1bb23f2c1578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258098"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrera en kunskapsbas med exportimport

Migrering är processen att skapa en ny kunskapsbas från en befintlig kunskapsbas. Du kan göra detta av flera skäl:

* säkerhetskopiering och återställning
* CI/CD-pipeline
* flytta regioner

Migrera en kunskapsbas kräver export från en befintlig kunskapsbas och sedan importera till en annan.

## <a name="prerequisites"></a>Krav

* Skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* Konfigurera en ny [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrera en kunskapsbas från QnA Maker
1. Logga in på [QnA Maker-portalen](https://qnamaker.ai).
1. Välj den ursprungskunskapsbas som du vill migrera.

1. På sidan **Inställningar** väljer du **Exportera kunskapsbas** för att hämta en TSV-fil som innehåller innehållet i din ursprungskunskapsbas - frågor, svar, metadata, uppföljningsanspråk och de datakällnamn som de extraherades från.

1. Välj **Skapa en kunskapsbas** på den övre menyn och skapa sedan en _tom_ kunskapsbas. Den är tom eftersom du inte kommer att lägga till några webbadresser eller filer när du skapar den. Dessa läggs till under importsteget, efter att de har skapats.

    Konfigurera kunskapsbasen. Ange endast det nya kunskapsbasnamnet. Dubblettnamn stöds och specialtecken stöds också.

    Välj inte något från steg 4 eftersom dessa värden skrivs över när du importerar filen.

1. I steg 5 väljer du **Skapa**.

1. Öppna fliken **Inställningar** i den här nya kunskapsbasen och välj **Importera kunskapsbas**. Detta importerar frågor, svar, metadata, uppföljningsanspråk och behåller de datakällnamn som de extraherades från.

   > [!div class="mx-imgBorder"]
   > [![Importera kunskapsbas](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Testa** den nya kunskapsbasen med hjälp av testpanelen. Lär dig hur du [testar din kunskapsbas](../How-To/test-knowledge-base.md).

1. **Publicera** kunskapsbasen och skapa en chattrobot. Läs om hur du [publicerar din kunskapsbas](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Programmässigt migrera en kunskapsbas från QnA Maker

Migreringsprocessen är programmässigt tillgänglig med hjälp av följande REST-API:er:

**Exportera**

* [Ladda ner API för kunskapsbas](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**Importera**

* [Ersätt API (ladda om med samma kunskapsbas-ID)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [Skapa API (ladda med nytt kunskapsbas-ID)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Chattloggar och ändringar
Skiftlägeskänsliga ändringar (synonymer) importeras inte automatiskt. Använd [V4 API:erna](https://go.microsoft.com/fwlink/?linkid=2092179) för att flytta ändringarna i den nya kunskapsbasen.

Det finns inget sätt att migrera chattloggar, eftersom den nya kunskapsbasen använder Application Insights för att lagra chattloggar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Redigera en kunskapsbas](../How-To/edit-knowledge-base.md)

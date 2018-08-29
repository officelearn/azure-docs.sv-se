---
title: Lägga till scheman för XML-verifiering – Azure Logic Apps | Microsoft Docs
description: Skapa scheman som validerar XML-dokument i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.date: 07/29/2016
ms.openlocfilehash: e03346da1c2b77f885c39d5329f990684979c56e
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123081"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validera XML med scheman i Azure Logic Apps med Enterprise-Integrationspaket

Scheman bekräfta att XML-dokument som du får är giltiga och har förväntade data i ett fördefinierat format. Scheman hjälper också att verifiera meddelanden som utbyts i ett scenario med B2B.

## <a name="add-a-schema"></a>Lägg till ett schema

1. I Azure-portalen väljer du **alla tjänster**.

    ![Azure-portalen ”alla tjänster”](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. I sökrutan filtrera anger **integrering**, och välj **Integrationskonton** i resultatlistan.

    ![Filtrera sökrutan](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. Välj den **integrationskontot** där du vill lägga till schemat.

    ![Lista över integrationskonton](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. Välj den **scheman** panelen.

    ![Integrationskontot exempel, ”scheman”](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>Lägg till en schemafil som är mindre än 2 MB

1. I den **scheman** bladet som öppnas (från föregående steg), Välj **Lägg till**.

    ![Scheman bladet ”Lägg till”](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. Ange ett namn för ditt schema. Ladda upp schemafilen genom att välja mappikonen bredvid den **schemat** box. När uppladdningen är klar väljer du **OK**.

    ![Skärmbild av ”Lägg till Schema”, med ”liten” markerat](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>Lägg till en schemafil som är större än 2 MB (upp till 8 MB max)

De här stegen variera beroende på åtkomstnivå för blob-behållare: **offentliga** eller **ingen anonym åtkomst**.

**Att fastställa den här åtkomstnivån**

1.  Öppna **Azure Storage Explorer**. 

2.  Under **Blobbehållare**, Välj den blob-behållare som du vill. 

3.  Välj **Security**, **åtkomstnivå**.

Om blob-säkerhetsåtkomstnivå **offentliga**, Följ stegen nedan.

![Azure Storage Explorer med ”Blob-behållare”, ”säkerhet” och ”offentliga” markerat](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. Ladda upp schemat till ditt lagringskonto och kopiera URI: N.

    ![Storage-konto med URI markerat](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. I **Lägg till Schema**väljer **stora filer**, och ange URI: N i den **innehålls-URI** textrutan.

    ![Scheman med knappen ”Lägg till” och ”stora filer” markerat](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Om blob-säkerhetsåtkomstnivå **ingen anonym åtkomst**, Följ stegen nedan.

![Azure Storage Explorer med ”Blob-behållare”, ”säkerhet” och ”ingen anonym åtkomst” markerat](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. Ladda upp schemat till ditt lagringskonto.

    ![Lagringskonto](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. Generera en signatur för delad åtkomst för schemat.

    ![Storage-kontot med delad åtkomst signaturer fliken markerat](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. I **Lägg till Schema**väljer **stora filer**, och ange signatur för delad åtkomst URI: N i den **innehålls-URI** textrutan.

    ![Scheman med knappen ”Lägg till” och ”stora filer” markerat](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. I den **scheman** -bladet i ditt integrationskonto och nyligen tillagda schemat ska visas.

    ![Ditt integrationskonto med ”scheman” och det nya schemat markerat](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Redigera scheman

1. Välj den **scheman** panelen.

2. Efter den **scheman** konfigurationsbladet öppnas, Välj det schema som du vill redigera.

3. På den **scheman** bladet välj **redigera**.

    ![Scheman bladet](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. Välj schemafilen som du vill redigera och välj sedan **öppna**.

    ![Öppna schemafilen att redigera](media/logic-apps-enterprise-integration-schemas/edit-31.png)

Azure visar ett meddelande att schemat har överförts.

## <a name="delete-schemas"></a>Ta bort scheman

1. Välj den **scheman** panelen.

2. Efter den **scheman** konfigurationsbladet öppnas, Välj det schema som du vill ta bort.

3. På den **scheman** bladet välj **ta bort**.

    ![Scheman bladet](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. För att bekräfta att du vill ta bort det valda schemat, Välj **Ja**.

    ![”Ta bort schema” bekräftelsemeddelande](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    I den **scheman** bladet listan schemat uppdateras och innehåller det schema som du tog bort inte längre.

    ![Din integrering konto med ”scheman” markerat](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>Nästa steg
* [Mer information om Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md "Lär dig mer om enterprise-integrationspaketet").  


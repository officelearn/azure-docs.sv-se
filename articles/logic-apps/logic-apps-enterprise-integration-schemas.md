---
title: Scheman för XML-verifiering - Azure Logic Apps | Microsoft Docs
description: Validera XML-dokument med scheman för Logikappar i Azure och Enterprise-Integrationspaket
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: e23500ec3c16e66b8dc74fcba29e9b58f0b41790
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299070"
---
# <a name="validate-xml-with-schemas-for-azure-logic-apps-and-the-enterprise-integration-pack"></a>Validera XML med scheman för Logikappar i Azure och Enterprise-Integrationspaket

Scheman bekräfta att XML-dokument som du får är giltiga och har de förväntade data i ett fördefinierat format. Scheman kan också hjälpa Validera meddelanden som utbyts i ett B2B-scenario.

## <a name="add-a-schema"></a>Lägger till ett schema

1. Välj i Azure-portalen **alla tjänster**.

    ![Azure-portalen ”alla tjänster”](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. Skriv i sökrutan filtrera **integrering**, och välj **Integrationskonton** från resultatlistan över.

    ![Filtrera sökrutan](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. Välj den **integrering konto** där du vill lägga till schemat.

    ![Lista över integrationskonton](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. Välj den **scheman** panelen.

    ![Exempel integration kontot, ”scheman”](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>Lägga till en schemafil som är mindre än 2 MB

1. I den **scheman** bladet som öppnas (från föregående steg), väljer **Lägg till**.

    ![Scheman bladet ”Lägg till”](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. Ange ett namn för schemat. Överföra schemafilen genom att välja mappikonen bredvid den **schemat** rutan. När överföringen är klar väljer du **OK**.

    ![Skärmbild av ”Lägg till schemat” med ”liten fil” markerat](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>Lägga till en schemafil som är större än 2 MB (upp till 8 MB maximalt)

De här stegen variera beroende på åtkomstnivå för blob-behållare: **offentliga** eller **ingen anonym åtkomst**.

**Att fastställa den här åtkomstnivån**

1.  Öppna **Azure Lagringsutforskaren**. 

2.  Under **Blobbbehållare**, Välj blob-behållare som du vill använda. 

3.  Välj **säkerhet**, **åtkomstnivå**.

Om blob-säkerhetsåtkomstnivå **offentliga**, Följ dessa steg.

![Azure Lagringsutforskaren med ”Blob-behållare”, ”säkerhet” och ”offentliga” markerat](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. Överför schemat till ditt lagringskonto och kopiera URI: N.

    ![Storage-konto med URI markerat](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. I **Lägg till Schema**väljer **stora filer**, och ange URI i den **innehåll URI** textruta.

    ![Scheman med knappen ”Lägg till” och ”stora filer” markerat](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Om blob-säkerhetsåtkomstnivå **ingen anonym åtkomst**, Följ dessa steg.

![Azure Lagringsutforskaren med ”Blob-behållare”, ”säkerhet” och ”ingen anonym åtkomst” markerat](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. Överför schemat till ditt lagringskonto.

    ![Lagringskonto](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. Generera en signatur för delad åtkomst för schemat.

    ![Storage-konto med delad åtkomst signaturer fliken markerat](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. I **Lägg till Schema**väljer **stora filer**, och ange signatur för delad åtkomst URI i den **innehåll URI** textruta.

    ![Scheman med knappen ”Lägg till” och ”stora filer” markerat](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. I den **scheman** bladet för ditt konto integration nytillagda schemat ska visas.

    ![Kontot integrering med ”scheman” och det nya schemat markerat](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Redigera scheman

1. Välj den **scheman** panelen.

2. Efter den **scheman** blad öppnas väljer du det schema som du vill redigera.

3. På den **scheman** bladet välj **redigera**.

    ![Scheman bladet](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. Välj schemafilen som du vill redigera och välj sedan **öppna**.

    ![Öppna schemafilen redigera](media/logic-apps-enterprise-integration-schemas/edit-31.png)

Azure visar ett meddelande att schemat har överförts.

## <a name="delete-schemas"></a>Ta bort scheman

1. Välj den **scheman** panelen.

2. Efter den **scheman** öppnas bladet Välj det schema som du vill ta bort.

3. På den **scheman** bladet välj **ta bort**.

    ![Scheman bladet](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. För att bekräfta att du vill ta bort det aktuella schemat, Välj **Ja**.

    ![Schemat bekräftelse-meddelande](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    I den **scheman** bladet listan schemat uppdateras och innehåller inte längre det schema som du har tagit bort.

    ![Din integrering konto med ”scheman” markerat](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>Nästa steg
* [Mer information om Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md "Lär dig mer om enterprise-integrationspaket").  


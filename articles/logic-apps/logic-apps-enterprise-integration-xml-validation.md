---
title: Validera XML - Azure Logikappar | Microsoft Docs
description: "Validera XML med scheman för Logikappar i Azure och B2B-scenarier med hjälp av Enterprise-Integrationspaket"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 9c4b2c1b2fdd9bf70775e5fd4369d1633258ae2a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="validate-xml-for-enterprise-integration"></a>Validera XML för företagsintegration

Ofta i B2B-scenarier måste partners i ett avtal se till att de meddelanden som är giltiga för databearbetning ska kunna starta. Du kan validera dokument mot ett fördefinierat schema genom att använda Använd XML-verifiering kopplingen i Enterprise-Integrationspaket.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Validera ett dokument med XML-verifiering connector

1. Skapa en logikapp och [koppla appen till kontot integration](../logic-apps/logic-apps-enterprise-integration-accounts.md "Lär dig hur du länkar ett integration konto till en logikapp") som har det schema som du vill använda för att validera XML-data.

2. Lägg till en **begäran - när en HTTP-begäran tas emot** utlösaren till din logikapp.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1.png)

3. Att lägga till den **XML-verifiering** åtgärd, Välj **lägga till en åtgärd**.

4. Om du vill filtrera alla åtgärder som du vill ange *xml* i sökrutan. Välj **XML-verifiering**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-2.png)

5. Välj för att ange XML-innehåll som du vill validera **innehåll**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1-5.png)

6. Välj body-tagg som det innehåll som du vill validera.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-3.png)

7. Ange schemat som du vill använda för att validera den tidigare *innehåll* indata, Välj **SCHEMANAMNET**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-4.png)

8. Spara ditt arbete  

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-5.png)

Du är nu klar med att konfigurera validering-anslutningen. I ett verkligt program kanske du vill lagra validerade data i en line-of-business (LOB)-app som SalesForce. Lägg till en åtgärd för att skicka verifierade utdata till Salesforce.

Gör en begäran om du vill testa åtgärden verifiering för HTTP-slutpunkten.

## <a name="next-steps"></a>Nästa steg
[Mer information om Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")   


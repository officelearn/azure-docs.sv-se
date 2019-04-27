---
title: Lägg till affärspartner för B2B - integrering med Azure Logic Apps | Microsoft Docs
description: Skapa handelspartner för ditt integrationskonto i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.date: 07/08/2016
ms.openlocfilehash: 137ed89c276338b534cad8fdf81ec31b5e5610b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60846079"
---
# <a name="add-trading-partners-for-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Lägg till affärspartner för integrationskonton i Azure Logic Apps med Enterprise-Integrationspaket

Partner är entiteter som ingår i business-to-business (B2B) transaktioner och utbyta meddelanden mellan varandra. Innan du kan skapa partners som representerar du och en annan organisation i de här transaktionerna, måste du både dela information som identifierar och validerar meddelanden som skickas av varandra. När du diskuterar dessa uppgifter och är redo att påbörja din verksamhet relation, skapar du partner i ditt integrationskonto för att representera du båda.

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>Vilka roller spelar partner i ditt integrationskonto?

Om du vill definiera information om meddelanden som utbyts mellan partners, kan du skapa avtal mellan partner. Men innan du kan skapa ett avtal, du måste ha lagt till minst två partner till din integrationskontot. Din organisation måste vara en del i avtalet som den **värdpartner**. Den andra partnern eller **gästpartner** representerar organisationen som utbyter meddelanden med din organisation. Gästpartner kan vara ett annat företag eller även en avdelning i din organisation.

När du lägger till dessa partner, skapar du ett avtal.

Ta emot och skicka inställningar är riktade från perspektivet för värd-Partner. Till exempel avgör ta emot inställningarna i ett avtal hur värdbaserade partnern tar emot meddelanden från en gästpartner. På samma sätt anger inställningarna för Skicka i avtalet hur värdbaserade partnern skickar meddelanden till gästpartner.

## <a name="create-partner"></a>Skapa partner

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj på Azure-huvudmenyn **alla tjänster**. Ange ”-integration” i sökrutan och välj sedan **integrationskonton**.

   ![Hitta integrationskontot](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. Under **Integrationskonton**, Välj integrationskontot där du vill lägga till dina partner.

   ![Välj integrationskontot](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. Välj den **partner** panelen.

   ![Välj ”Partners”](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. Under **partner**, Välj **Lägg till**.

   ![Välj ”Lägg till”](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Ange ett namn för din partner och välj sedan en **kvalificerare**. Ange en **värdet** att identifiera dokument som får dina appar. När du är klar väljer du **OK**.

   ![Lägg till information om partner](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Välj den **partner** panelen igen.

   ![Välj panelen ”Partners”](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   Din nya partner visas nu. 

   ![Visa ny partner](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>Redigera partner

1. I den [Azure-portalen](https://portal.azure.com), hitta och välj ditt integrationskonto. Välj den **partner** panelen.

   ![Välj panelen ”Partners”](./media/logic-apps-enterprise-integration-partners/edit.png)

2. Under **partner**, markerar du den partner som du vill redigera.

   ![Välj partner att ta bort](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. Under **uppdatering Partner**, gör dina ändringar.
När du är klar väljer **spara**. 

   ![Se och spara dina ändringar](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   Om du vill avbryta ändringarna, Välj **Ignorera**.

## <a name="delete-partner"></a>Ta bort partner

1. I den [Azure-portalen](https://portal.azure.com), hitta och välj ditt integrationskonto. Välj den **partner** panelen.

   ![Välj panelen ”Partners”](./media/logic-apps-enterprise-integration-partners/delete.png)

2. Under **partner**, markerar du den partner som du vill ta bort.
Välj **ta bort**.

   ![Ta bort partner](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Nästa steg

* [Mer information om avtalen](../logic-apps/logic-apps-enterprise-integration-agreements.md "Lär dig mer om enterprise integration-avtal")  


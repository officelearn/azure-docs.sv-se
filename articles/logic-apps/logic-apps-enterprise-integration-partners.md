---
title: Skapa partners för business-to-business (B2B) meddelanden - Azure Logic Apps | Microsoft Docs
description: Lär dig hur du lägger till partners till ditt konto för integrering med Logic Apps och Enterprise-Integrationspaket
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4abfdaa8145f14bc6496e57ebce0da10cdd2c365
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299747"
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>Lägg till eller uppdatera partner i business-to-business-avtalen i arbetsflödet

Partners är enheter som deltar i business-to-business (B2B) transaktioner och utbyte av meddelanden mellan varandra. Innan du kan skapa partners som representerar du och en annan organisation i dessa transaktioner, måste du både dela information som identifierar och validerar meddelanden som skickas av varandra. Du kan skapa partners i ditt konto integration som representerar du båda när du diskutera dessa uppgifter och är redo att börja business-relation.

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>Vilka roller spelar partners i integration kontot?

Om du vill definiera information om meddelanden som utbyts mellan partners, kan du skapa avtal mellan dessa partner. Men innan du kan skapa ett avtal, du måste ha lagt till minst två parter till ditt konto för integrering. Din organisation måste vara en del av avtal som den **värden partner**. Den andra partnern eller **gäst partner** representerar den organisation som utbyter meddelanden med din organisation. Gäst-partner kan vara ett annat företag eller även en avdelning i din organisation.

När du lägger till dessa tillverkare, skapar du ett avtal.

Ta emot och skicka inställningarna går från synvinkel för den Partner som värd. Exempelvis avgör ta emot inställningarna i ett avtal hur värdbaserade partnern tar emot meddelanden som skickas från en gäst-partner. På samma sätt anger skicka inställningarna i avtalet hur värdbaserade partnern skickar meddelanden till gäst-partner.

## <a name="create-partner"></a>Skapa partner

1. Logga in på [Azure Portal](https://portal.azure.com).

2. På Azure huvudmenyn, Välj **alla tjänster**. I sökrutan anger du ”integration” och välj sedan **integrationskonton**.

   ![Hitta integration konto](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. Under **Integrationskonton**, väljer du det integration konto där du vill lägga till dina partners.

   ![Välj integration konto](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. Välj den **Partners** panelen.

   ![Välj ”Partners”](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. Under **Partners**, Välj **Lägg till**.

   ![Välj ”Lägg till”](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Ange ett namn för din partner och välj sedan en **kvalificerare**. Ange en **värdet** att identifiera dokument som tar emot dina appar. När du är klar väljer **OK**.

   ![Lägg till information om resurspartner](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Välj den **Partners** panelen igen.

   ![Välj ikonen ”Partners”](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   Din nya partner visas nu. 

   ![Visa ny partner](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>Redigera partner

1. I den [Azure-portalen](https://portal.azure.com), söka efter och välj ditt konto för integrering. Välj den **Partners** panelen.

   ![Välj ikonen ”Partners”](./media/logic-apps-enterprise-integration-partners/edit.png)

2. Under **Partners**, markerar du den partner som du vill redigera.

   ![Välj partner att ta bort](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. Under **uppdatering Partner**, gör ändringarna.
När du är klar väljer **spara**. 

   ![Skapa och spara ändringarna](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   Om du vill avbryta ändringarna, Välj **Ignorera**.

## <a name="delete-partner"></a>Ta bort partner

1. I den [Azure-portalen](https://portal.azure.com), söka efter och välj ditt konto för integrering. Välj den **Partners** panelen.

   ![Välj ikonen ”Partners”](./media/logic-apps-enterprise-integration-partners/delete.png)

2. Under **Partners**, markerar du den partner som du vill ta bort.
Välj **ta bort**.

   ![Ta bort partner](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Nästa steg

* [Mer information om avtalen](../logic-apps/logic-apps-enterprise-integration-agreements.md "Lär dig mer om enterprise integration-avtal")  


---
title: "Skapa partners för business-to-business (B2B) meddelanden - Azure Logic Apps | Microsoft Docs"
description: "Lär dig hur du lägger till partners till ditt konto för integrering med Logic Apps och Enterprise-Integrationspaket"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 950cb449b53f400f0f0f860caf5415bbb5212269
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>Lägg till eller uppdatera partner i business-to-business-avtalen i arbetsflödet

Partners är enheter som deltar i business-to-business (B2B) transaktioner och utbyte av meddelanden mellan varandra. Innan du kan skapa partners som representerar du och en annan organisation i dessa transaktioner, måste du både dela information som identifierar och validerar meddelanden som skickas av varandra. Du kan skapa partners i ditt konto integration som representerar du båda när du diskutera dessa uppgifter och är redo att börja business-relation.

## <a name="what-roles-do-partners-have-in-your-integration-account"></a>Vilka roller har partners i integration kontot?

Om du vill definiera information om meddelanden som utbyts mellan partners, kan du skapa avtal mellan dessa partner. Men innan du kan skapa ett avtal, du måste ha lagt till minst två parter till ditt konto för integrering. Din organisation måste vara en del av avtal som den **värden partner**. Den andra partnern eller **gäst partner** representerar den organisation som utbyter meddelanden med din organisation. Gäst-partner kan vara ett annat företag eller även en avdelning i din organisation.

När du lägger till dessa tillverkare, skapar du ett avtal.

Ta emot och skicka inställningarna går från synvinkel för den Partner som värd. Exempelvis avgör ta emot inställningarna i ett avtal hur värdbaserade partnern tar emot meddelanden som skickas från en gäst-partner. På samma sätt anger skicka inställningarna i avtalet hur värdbaserade partnern skickar meddelanden till gäst-partner.

## <a name="how-to-create-a-partner"></a>Så här skapar du en partner?

1. Välj i Azure-portalen **Bläddra**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. Skriv i sökrutan filtrera **integrering**och välj **Integrationskonton** i resultatlistan.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Välj integration konto där du vill lägga till dina partners.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Välj den **Partners** panelen.

    ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. I bladet Partners väljer **Lägg till**.

    ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Ange ett namn för din partner och välj sedan en **kvalificerare**. Du kan använda en **värdet** för att identifiera dokument som finns i dina appar.

    ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Om du vill se förloppet för ditt partner-processen, Välj den *bell* meddelandeikonen.

    ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)

8. För att bekräfta att din nya partner har lagts till, Välj den **Partners** panelen.

    ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)

    När du har valt Partners panelen visas också nytillagda partners i bladet Partners.

    ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="how-to-edit-existing-partners-in-your-integration-account"></a>Hur du redigerar befintliga partners i ditt konto för integrering

1. Välj den **Partners** panelen.
2. När det öppnas bladet Partners, markerar du den partner som du vill redigera.
3. På den **uppdatering Partner** panelen, gör ändringarna.
4. När du är klar väljer **spara**, eller om du vill avbryta ändringarna, Välj **Ignorera**.

    ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)

## <a name="how-to-delete-a-partner"></a>Hur du tar bort en partner

1. Välj den **Partners** panelen.
2. När det öppnas bladet Partner, markerar du den partner som du vill ta bort.
3. Välj **ta bort**.

    ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Nästa steg
* [Mer information om avtalen](../logic-apps/logic-apps-enterprise-integration-agreements.md "Lär dig mer om enterprise integration-avtal")  


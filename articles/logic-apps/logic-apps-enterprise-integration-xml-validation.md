---
title: Validera XML för B2B enterprise-integration – Azure Logic Apps | Microsoft Docs
description: Validera XML med scheman för B2B-lösningar i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.date: 07/08/2016
ms.openlocfilehash: e25f4e633b8cf9030bb0ce395f093f630db59f38
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43121121"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validera XML för B2B enterprise-integration i Azure Logic Apps med Enterprise-Integrationspaket

Ofta i B2B-scenarier, måste partners på ett avtal se till att vilka meddelanden som är giltiga innan bearbetning kan starta. Du kan validera dokument med ett fördefinierat schema med att använda anslutningstjänsten XML-verifiering i Enterprise-Integrationspaketet.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Verifiera ett dokument med XML-verifiering connector

1. Skapa en logikapp och [länka appen till integrationskontot](../logic-apps/logic-apps-enterprise-integration-accounts.md "Lär dig hur du länkar ett integrationskonto till en logikapp") som har det schema som du vill använda för att verifiera XML-data.

2. Lägg till en **begäran – när en HTTP-begäran tas emot** utlösare i logikappen.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1.png)

3. Att lägga till den **XML-verifiering** åtgärd, Välj **Lägg till en åtgärd**.

4. Om du vill filtrera åtgärderna som ska det som du vill ange *xml* i sökrutan. Välj **XML-verifiering**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-2.png)

5. Välj för att ange XML-innehåll som du vill validera **innehåll**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1-5.png)

6. Välj brödtextstaggen som det innehåll som du vill validera.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-3.png)

7. Att ange det schema som du vill använda för att verifiera den tidigare *innehåll* indata, Välj **SCHEMANAMN**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-4.png)

8. Spara ditt arbete  

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-5.png)

Du är nu klar med att konfigurera din anslutningsapp för verifiering. I en verklig tillämpning kanske du vill lagra verifierade data i en line-of-business (LOB) appar som SalesForce. Lägg till en åtgärd för att skicka verifierade utdata till Salesforce.

Testa verifieringen åtgärden genom att skicka en förfrågan till HTTP-slutpunkten.

## <a name="next-steps"></a>Nästa steg
[Mer information om Enterprise-Integrationspaketet](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")   


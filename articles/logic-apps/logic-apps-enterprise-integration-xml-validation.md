---
title: Validera XML för integrering av B2B-företag
description: Validera XML med hjälp av scheman i Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792158"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Verifiera XML för B2B-företagsintegrering i Azure Logic Apps med Enterprise Integration Pack

Ofta i B2B-scenarier måste handelspartnerna i ett avtal se till att de meddelanden de utbyter är giltiga innan någon databehandling kan starta. Du kan validera dokument mot ett fördefinierat schema med hjälp av XML-valideringsåtgärden, som är tillgänglig med Enterprise Integration Pack.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en prenumeration ännu [registrerar du dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En tom eller befintlig logikapp där du vill använda XML-valideringsåtgärden. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är associerat med din Azure-prenumeration är länkat till logikappen där du planerar att använda XML-valideringsåtgärden och innehåller det schema som du vill använda för att validera XML-innehåll. Både logikappen och integrationskontot måste finnas på samma plats eller Azure-region.

## <a name="add-xml-validation-action"></a>Lägg till XML-valideringsåtgärd

1. Öppna logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Om du har en tom logikapp anger du `HTTP request` som filter i sökrutan i Logikappdesignern och väljer utlösaren När en **HTTP-begäran tas emot.** Annars fortsätter du till nästa steg.

1. Under det sista steget i arbetsflödet väljer du **Nytt steg**.

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du pekaren över pilen som kopplar dessa steg så att plustecknet (**+**) visas. Markera det plustecknet och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**väljer du **Inbyggd**. Ange som filter `xml validation` i sökrutan. Välj **XML-validering**i åtgärdslistan .

   ![Sök efter och välj åtgärden XML-validering](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Om du vill ange det XML-innehåll som du vill validera klickar du i rutan **Innehåll** så att listan med dynamiskt innehåll visas.

   ![Öppna listan över dynamiskt innehåll](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   Listan med dynamiskt innehåll visar egenskapstoken som representerar utdata från föregående steg i arbetsflödet. Om listan inte visar någon förväntad egenskap kontrollerar du utlösaren eller åtgärdsrubriken om du kan välja **Visa mer**.

1. Välj den egenskap som har det innehåll som du vill validera i listan med dynamiskt innehåll.

   I det här exemplet markeras **brödtextutdata** från utlösaren.

   ![Välj innehåll som ska valideras](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Om du vill ange det schema som du vill använda för validering öppnar du listan **Schemanamn** och väljer det valideringsschema som du har lagt till i det länkade integrationskontot.

   ![Välj schema som ska användas för validering](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Spara din logikapp.

   Du är nu klar med att konfigurera valideringen. I en app i verkliga världen kanske du vill lagra validerade data i en LOB-app (line-of-business), till exempel SalesForce. Om du vill skicka det validerade utdata till Salesforce lägger du till en åtgärd.

1. Om du vill testa valideringsåtgärden kan du skicka en begäran för att utlösa logikappens arbetsflöde.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
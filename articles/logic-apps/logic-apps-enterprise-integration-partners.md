---
title: Lägg till handelspartner för B2B-integrationer
description: Skapa handelspartner i ditt integrationskonto som ska användas med Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792438"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Lägga till handelspartner i integrationskonton för Azure Logic Apps

I [Azure Logic Apps](../logic-apps/logic-apps-overview.md)kan du skapa automatiska B2B-integrationsarbetsflöden (Business-to-Business) med hjälp av ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) med dina logikappar. För att representera din organisation och andra skapar och lägger du till handelspartner som artefakter i ditt integrationskonto. Partners är entiteter som deltar i B2B-transaktioner och utbyter meddelanden med varandra.

Innan du skapar dessa partner, se till att diskutera och dela information med dina partner om hur du identifierar och validerar de meddelanden som den andra skickar. När du har kommit överens om dessa uppgifter är du redo att skapa partner i ditt integrationskonto.

## <a name="partner-roles-in-integration-accounts"></a>Partnerroller i integrationskonton

Om du vill definiera information om de meddelanden som utbyts med dina partner skapar och lägger du till [avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md) som artefakter i ditt integrationskonto. Avtal kräver minst två partner i ditt integrationskonto. Din organisation är alltid *värdpartner* i avtalet. Organisationen som utbyter meddelanden med din organisation är *gästpartnern*. En gästpartner kan vara ett annat företag, eller till och med en avdelning i din egen organisation. När du har lagt till dessa partner kan du skapa ett avtal.

I ett avtal anger du information om hur du hanterar inkommande och utgående meddelanden från värdpartnerns perspektiv. För inkommande meddelanden anger **mottagningsinställningarna** hur värdpartnern tar emot meddelanden från gästpartnern i avtalet. För utgående meddelanden anger **skicka inställningar** hur värdpartnern skickar meddelanden till gästpartnern.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration ännu [registrerar du dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) för att lagra dina partner, avtal och andra B2B-artefakter. Det här integrationskontot måste vara kopplat till din Azure-prenumeration.

## <a name="create-partner"></a>Skapa partner

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. På huvudmenyn i Azure väljer du **Alla tjänster**. I sökrutan anger du "integration" och väljer **Integrationskonton**.

   ![Välj "Integrationskonton"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. Under **Integrationskonton**väljer du det integrationskonto där du vill lägga till dina partner.

   ![Välj integrationskonto](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Välj panelen **Partners.**

   ![Välj panel "Partners"](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. Under **Partners**väljer du **Lägg till**. Under **Lägg till partner**anger du partnerns information enligt beskrivningen i tabellen nedan.

   ![Välj "Lägg till" och ange partnerinformation](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn** | Ja | Partnerns namn |
   | **Kvalificerare** | Ja | Den autentiseringsorgan som tillhandahåller unika affärsidentiteter för organisationer, till exempel **D-U-N-S (Dun & Bradstreet).** <p>Partner kan välja en ömsesidigt definierad affärsidentitet. För dessa scenarier väljer du **Ömsesidigt definierad** för EDIFACT eller **Ömsesidigt definierade (X12)** för X12. <p>För RosettaNet väljer du bara **DUNS**, vilket är standard. |
   | **Värde** | Ja | Ett värde som identifierar de dokument som dina logikappar får. <p>För RosettaNet måste det här värdet vara ett niosiffrigt tal som motsvarar DUNS-numret. |
   ||||

   > [!NOTE]
   > För partner som använder RosettaNet kan du ange ytterligare information genom att först skapa dessa partner och sedan [redigera dem efteråt](#edit-partner).

1. När du är klar väljer du **OK**.

   Din nya partner visas nu på **partnerlistan.** Panelen **Partners** uppdaterar också det aktuella antalet partner.

   ![Ny partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Redigera partner

1. Leta reda på och välj ditt integrationskonto i [Azure-portalen.](https://portal.azure.com)
Välj panelen **Partners.**

   ![Välj panel "Partners"](./media/logic-apps-enterprise-integration-partners/edit.png)

1. Under **Partners**väljer du den partner som du vill redigera och väljer **Redigera**. Gör ändringarna under **Redigera.**

   ![Gör och spara ändringarna](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   För RosettaNet kan du under **RosettaNet Partner Properties**ange den här ytterligare informationen:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Klassificering av partner** | Inga | Partnerns organisationstyp |
   | **Kod för försörjningskedja** | Inga | Partnerns leveranskedja kod, till exempel "Informationsteknik" eller "Elektroniska komponenter" |
   | **Kontaktnamn** | Inga | Partnerns kontaktnamn |
   | **Email** | Inga | Partnerns e-postadress |
   | **Fax** | Inga | Partnerns faxnummer |
   | **Telefon** | Inga | Partnerns telefonnummer |
   ||||

1. När du är klar väljer du **OK** för att spara ändringarna.

## <a name="delete-partner"></a>Ta bort partner

1. Leta reda på och välj ditt integrationskonto i [Azure-portalen.](https://portal.azure.com) Välj panelen **Partners.**

   ![Välj panel "Partners"](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. Under **Partners**väljer du den partner som du vill ta bort. Välj **Ta bort**.

   ![Ta bort partner](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md)
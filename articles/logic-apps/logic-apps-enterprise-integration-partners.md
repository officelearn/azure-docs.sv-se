---
title: Lägg till affärspartner för B2B - integrering med Azure Logic Apps
description: Skapa handelspartner i ditt integrationskonto ska användas med Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 681f16132c1de2ec5f3b27f80633d32879b0746c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330139"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Lägga till affärspartner till integrationskonton för Azure Logic Apps

I [Azure Logic Apps](../logic-apps/logic-apps-overview.md), du kan skapa automatiserade arbetsflöden för integrering av business-to-business (B2B) med hjälp av en [integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) med logic apps. För att representera organisationen och andra du skapar och lägga till affärspartner som artefakter i ditt integrationskonto. Partner är entiteter som ingår i B2B-transaktioner och utbyta meddelanden med varandra.

Innan du skapar dessa partner måste du se till att diskutera och dela information med dina partner om hur du identifiera och verifiera de meddelanden som den andra skickar. När ni är överens om detaljerna, är du redo att skapa partner i ditt integrationskonto.

## <a name="partner-roles-in-integration-accounts"></a>Resurspartner i integrationskonton

Om du vill definiera informationen om meddelanden som utbyts med dina partner du skapar och lägger till [avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md) som artefakter till ditt integrationskonto. Avtal kräver minst två partner i ditt integrationskonto. Din organisation är alltid den *värdpartner* i avtalet. Organisationen som utbyter meddelanden med din organisation är den *gästpartner*. En gästpartner kan vara ett annat företag eller även en avdelning i din organisation. När du lägger till dessa partner, skapar du ett avtal.

I ett avtal anger du information för hantering av inkommande och utgående meddelanden från den värdpartner perspektiv. För inkommande meddelanden i **ta emot inställningar** anger hur värdpartnern tar emot meddelanden från gästpartner i avtalet. För utgående meddelanden den **skicka inställningar** anger hur den mottagande partnern skickar meddelanden till gästpartner.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har en Azure-prenumeration än, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).

* En [integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) för att lagra dina partners, avtal och andra B2B-artefakter. Den här integrationskontot måste vara associerad med din Azure-prenumeration.

## <a name="create-partner"></a>Skapa partner

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj på Azure-huvudmenyn **alla tjänster**. Ange ”-integration” i sökrutan och välj **integrationskonton**.

   ![Välj ”integrationskonton”](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. Under **Integrationskonton**, Välj integrationskontot där du vill lägga till dina partner.

   ![Välj integrationskontot](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Välj den **partner** panelen.

   ![Välj panelen ”Partners”](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. Under **partner**, Välj **Lägg till**. Under **Lägg till Partner**, anger partnerns informationen enligt beskrivningen av tabellen nedan.

   ![Välj ”Lägg till” och ange information om partner](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn** | Ja | Partnerns namn |
   | **Kvalificeraren** | Ja | Den autentiserande brödtext som innehåller unika företagsidentiteter för organisationer, till exempel **D-U-N-S (Dun & Bradstreet)** . <p>Partner kan välja en ömsesidigt definierade företagsidentitet. Dessa scenarier kan du välja **ömsesidigt definierats** för EDIFACT eller **ömsesidigt definierats (X12)** för X12. <p>RosettaNet, Välj endast **DUNS**, vilket är standard. |
   | **Värde** | Ja | Ett värde som identifierar de dokument som dina logic apps får. <p>Det här värdet måste vara ett nummer i nio siffror som motsvarar DUNS-nummer för RosettaNet. |
   ||||

   > [!NOTE]
   > För partner som använder RosettaNet, du kan ange ytterligare information genom att först skapa dessa partner och sedan [redigerar dem efteråt](#edit-partner).

1. När du är klar väljer du **OK**.

   Din nya partner visas nu på den **partner** lista. Dessutom den **partner** panel uppdaterar det aktuella antalet partner.

   ![Ny partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Redigera partner

1. I den [Azure-portalen](https://portal.azure.com), hitta och välj ditt integrationskonto.
Välj den **partner** panelen.

   ![Välj panelen ”Partners”](./media/logic-apps-enterprise-integration-partners/edit.png)

1. Under **partner**, markerar du den partner som du vill redigera och välj **redigera**. Under **redigera**, gör dina ändringar.

   ![Se och spara dina ändringar](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   För RosettaNet, under **RosettaNet Partner egenskaper**, du kan ange ytterligare information:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Partner klassificering** | Nej | Partnerns organisationstyp |
   | **Ange koden för kedja** | Nej | Partnerns strömförsörjning kedja kod, till exempel ”Information Technology” eller ”elektroniska komponenter” |
   | **Kontaktnamn** | Nej | Partnerns namn |
   | **E-post** | Nej | Partnerns e-postadress |
   | **Fax** | Nej | Partnerns faxnummer |
   | **Telefon** | Nej | Partnerns telefonnummer |
   ||||

1. När du är klar väljer **OK** att spara dina ändringar.

## <a name="delete-partner"></a>Ta bort partner

1. I den [Azure-portalen](https://portal.azure.com), hitta och välj ditt integrationskonto. Välj den **partner** panelen.

   ![Välj panelen ”Partners”](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. Under **partner**, markerar du den partner som du vill ta bort. Välj **ta bort**.

   ![Ta bort partner](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md)
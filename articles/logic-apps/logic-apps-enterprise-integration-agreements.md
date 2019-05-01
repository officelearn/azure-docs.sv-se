---
title: Skapa och hantera handelspartneravtal – Azure Logic Apps
description: Skapa och hantera avtal mellan handelspartner med hjälp av Azure Logic Apps och Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.date: 04/05/2019
ms.openlocfilehash: 26d653b873e959f0804e0456ed87ee68c39413e5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720672"
---
# <a name="create-and-manage-trading-partner-agreements-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Skapa och hantera handelspartneravtal med hjälp av Azure Logic Apps och Enterprise-Integrationspaket

En [handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*avtal* hjälper organisationer och företag sömlöst kommunicera med varandra genom att definiera det specifika standardiserade protokollet som ska användas när du skickar Business-to-business (B2B) meddelanden. Avtal ange vanliga fördelar, till exempel:

* Organisationerna kan utbyta information med hjälp av ett välkänt format.
* Förbättra effektiviteten vid B2B-transaktioner.
* Det är enkelt att skapa, hantera och använda för att skapa enterprise integrationslösningar.

Den här artikeln visar hur du skapar en AS2, EDIFACT eller X12 avtal som du kan använda när du skapar enterprise integrationslösningar för B2B-scenarier med hjälp av den [Enterprise-Integrationspaketet](../logic-apps/logic-apps-enterprise-integration-overview.md) och [Azure Logic Apps](../logic-apps/logic-apps-overview.md). När du skapar ett avtal kan du sedan kan använda AS2, EDIFACT eller X12 kopplingar för utbyte av B2B-meddelanden.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har en Azure-prenumeration än, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).

* En [integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) för att lagra avtalet och andra B2B-artefakter. Den här integrationskontot måste vara associerad med din Azure-prenumeration.

* Minst två [handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md) som du redan har skapat i ditt integrationskonto. Ett avtal kräver både en värdpartner och en gästpartner. Båda parter måste använda samma ”företagsidentitet”-kvalificerare som det avtal som du vill skapa till exempel AS2-, X 12 eller EDIFACT.

* Valfritt: Logikappen där du vill använda avtalet och en utlösare som startar logikappens arbetsflöde. Om du vill skapa bara din integreringskontot och B2B-artefakter, behöver du inte en logikapp. Innan din logikapp kan använda B2B-artefakterna i ditt integrationskonto, måste du koppla din integrationskontot till logikappen. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabbstarten: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Skapa avtal

1. Logga in på [Azure Portal](https://portal.azure.com).
Välj på Azure-huvudmenyn **alla tjänster**. I sökrutan anger du ”-integration” som filter. Välj den här resursen från resultatet: **Integrationskonton**

   ![Hitta ditt integrationskonto](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. Under **integrationskonton**, Välj integrationskontot där du vill skapa avtalet.

   ![Välj var du vill skapa avtalet för integrationskontot](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. I den högra rutan under **komponenter**, Välj den **avtal** panelen.

   ![Välj ”avtal”](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. Under **avtal**, Välj **Lägg till**. I den **Lägg till** rutan innehåller information om ditt avtal, till exempel:

   ![Välj ”Lägg till”](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Egenskap  | Krävs | Value | Beskrivning |
   |----------|----------|-------|-------------|
   | **Namn** | Ja | <*agreement-name*> | Namn för ditt avtal |
   | **Avtalstyp** | Ja | **AS2**, **X12**, eller **EDIFACT** | Protokolltypen för ditt avtal. När du har skapat ditt avtal fil måste innehållet i filen matcha avtalstypen. | |  
   | **Värdpartner** | Ja | <*host-partner-name*> | Den mottagande partnern representerar organisationen som anger avtalet |
   | **Värd-identitet** | Ja | <*host-partner-identifier*> | Värd-partnerns ID |
   | **Gästpartner** | Ja | <*guest-partner-name*> | Gästpartner representerar den organisation som gör affärer med värdpartner |
   | **Gästidentitet** | Ja | <*guest-partner-identifier*> | Gäst-partnerns ID |
   | **Ta emot inställningarna** | Varierar | Varierar | Dessa egenskaper anger hur du hanterar alla inkommande meddelanden som tas emot av avtalet. Mer information finns i respektive avtalstyp: <p>- [Inställningar för AS2-meddelande](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT-Meddelandeinställningar](logic-apps-enterprise-integration-edifact.md) <br>- [X12 meddelande inställningar](logic-apps-enterprise-integration-x12.md) |
   | **Skicka inställningar** | Varierar | Varierar | Dessa egenskaper anger hur du hanterar alla utgående meddelanden som skickas av avtalet. Mer information finns i respektive avtalstyp: <p>- [Inställningar för AS2-meddelande](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT-Meddelandeinställningar](logic-apps-enterprise-integration-edifact.md) <br>- [X12 meddelande inställningar](logic-apps-enterprise-integration-x12.md) |
   |||||

1. När du är klar skapar ditt avtal på den **Lägg till** väljer **OK**, och gå tillbaka till ditt integrationskonto.

   Den **avtal** listan visas nu ditt nya avtal.

## <a name="edit-agreements"></a>Redigera avtal

1. I den [Azure-portalen](https://portal.azure.com), på Azure-huvudmenyn väljer **alla tjänster**.

1. I sökrutan anger du ”-integration” som filter. Välj den här resursen från resultatet: **Integrationskonton**

1. Under **integrationskonton**, Välj integrationskontot som har det avtal som du vill redigera.

1. I den högra rutan under **komponenter**, Välj den **avtal** panelen.

1. Under **avtal**, Välj ditt avtal och välj **redigera**.

1. Se och spara sedan ändringarna.

## <a name="delete-agreements"></a>Ta bort avtal

1. I den [Azure-portalen](https://portal.azure.com), på Azure-huvudmenyn väljer **alla tjänster**.

1. I sökrutan anger du ”-integration” som filter. Välj den här resursen från resultatet: **Integrationskonton**

1. Under **integrationskonton**, Välj integrationskontot som har det avtal som du vill ta bort.

1. I den högra rutan under **komponenter**, Välj den **avtal** panelen.

1. Under **avtal**, Välj ditt avtal och välj **ta bort**.

1. Bekräfta att du vill ta bort det valda avtalet.

## <a name="next-steps"></a>Nästa steg

* [Utbyta AS2-meddelanden](logic-apps-enterprise-integration-as2.md)
* [Utbyta EDIFACT-meddelanden](logic-apps-enterprise-integration-edifact.md)
* [Exchange X12 meddelanden](logic-apps-enterprise-integration-x12.md)

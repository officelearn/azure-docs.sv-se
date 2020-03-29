---
title: Handelspartneravtal
description: Skapa och hantera avtal mellan handelspartner med hjälp av Azure Logic Apps och Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 521a0ef4053be55e6c7322da5af26ccfc6c844e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790733"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Skapa och hantera handelspartneravtal i Azure Logic Apps

Ett [handelspartneravtal](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*agreement* hjälper organisationer och företag att kommunicera sömlöst med varandra genom att definiera det specifika branschstandardprotokoll som ska användas vid utbyte av B2B-meddelanden (Business-to-Business). Avtal ger gemensamma fördelar, till exempel:

* Gör det möjligt för organisationer att utbyta information med hjälp av ett välkänt format.
* Förbättra effektiviteten när du utför B2B-transaktioner.
* Är enkla att skapa, hantera och använda för att bygga lösningar för företagsintegration.

Den här artikeln visar hur du skapar ett AS2-, EDIFACT- eller X12-avtal som du kan använda när du skapar företagsintegrationslösningar för B2B-scenarier med hjälp av [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) och Azure Logic [Apps](../logic-apps/logic-apps-overview.md). När du har skapat ett avtal kan du sedan använda AS2-, EDIFACT- eller X12-kopplingarna för utbyte av B2B-meddelanden.

Mer om hur du skapar avtal för utbyte av RosettaNet-meddelanden finns i [Exchange RosettaNet-meddelanden](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration ännu [registrerar du dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) för att lagra ditt avtal och andra B2B-artefakter. Det här integrationskontot måste vara kopplat till din Azure-prenumeration.

* Minst två [handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md) som du redan har skapat i ditt integrationskonto. Ett avtal kräver både en värdpartner och en gästpartner. Båda partnerna måste använda samma "business identity"-kvalificerare som det avtal du vill skapa, till exempel AS2, X12 eller EDIFACT.

* Valfritt: Logikappen där du vill använda ditt avtal och en utlösare som startar logikappens arbetsflöde. Om du bara vill skapa ditt integrationskonto och B2B-artefakter behöver du ingen logikapp. Men innan logikappen kan använda B2B-artefakterna i ditt integrationskonto måste du länka ditt integrationskonto till logikappen. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Skapa avtal

1. Logga in på [Azure-portalen](https://portal.azure.com).
På huvudmenyn i Azure väljer du **Alla tjänster**. Ange "integration" som filter i sökrutan. Välj den här resursen i resultatet: **Integrationskonton**

   ![Hitta ditt integrationskonto](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. Under **Integrationskonton**väljer du det integrationskonto där du vill skapa avtalet.

   ![Välj integrationskontot där avtalet ska skapas](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. Välj panelen **Avtal** under **Komponenter**i den högra rutan.

   ![Välj "Avtal"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. Under **Avtal**väljer du **Lägg till**. I fönstret **Lägg till** anger du information om ditt avtal, till exempel:

   ![Välj "Lägg till"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Namn** | Ja | <*avtalsnamn*> | Namnet på ditt avtal |
   | **Typ av avtal** | Ja | **AS2,** **X12**eller **EDIFACT** | Protokolltypen för ditt avtal. När du skapar avtalsfilen måste innehållet i filen matcha avtalstypen. | |  
   | **Värdpartner** | Ja | <*värd-partner-namn*> | Värdpartnern representerar den organisation som anger avtalet |
   | **Värdidentitet** | Ja | <*värd-partner-identifierare*> | Värdpartnerns identifierare |
   | **Gästpartner** | Ja | <*gäst-partner-namn*> | Gästpartnern representerar organisationen som gör affärer med värdpartnern |
   | **Gästidentitet** | Ja | <*gäst-partner-identifierare*> | Gästpartnerns identifierare |
   | **Ta emot inställningar** | Varierar | Varierar | Dessa egenskaper anger hur värdpartnern tar emot alla inkommande meddelanden från gästpartnern i avtalet. Mer information finns i respektive avtalstyp: <p>- [INSTÄLLNINGAR för AS2-meddelanden](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT-meddelandeinställningar](logic-apps-enterprise-integration-edifact.md) <br>- [Inställningar för X12-meddelanden](logic-apps-enterprise-integration-x12.md) |
   | **Skicka inställningar** | Varierar | Varierar | De här egenskaperna anger hur värdpartnern skickar alla utgående meddelanden till gästpartnern i avtalet. Mer information finns i respektive avtalstyp: <p>- [INSTÄLLNINGAR för AS2-meddelanden](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT-meddelandeinställningar](logic-apps-enterprise-integration-edifact.md) <br>- [Inställningar för X12-meddelanden](logic-apps-enterprise-integration-x12.md) |
   |||||

1. När du är klar med att skapa ditt avtal väljer du **OK**på sidan **Lägg** till och går tillbaka till ditt integrationskonto.

   Listan **Avtal** visar nu ditt nya avtal.

## <a name="edit-agreements"></a>Redigera avtal

1. I [Azure-portalen](https://portal.azure.com)väljer du **Alla tjänster**på Azure-menyn .

1. Ange "integration" som filter i sökrutan. Välj den här resursen i resultatet: **Integrationskonton**

1. Under **Integrationskonton**väljer du det integrationskonto som har det avtal som du vill redigera.

1. Välj panelen **Avtal** under **Komponenter**i den högra rutan.

1. Under **Avtal**väljer du ditt avtal och väljer **Redigera**.

1. Gör och spara ändringarna.

## <a name="delete-agreements"></a>Ta bort avtal

1. I [Azure-portalen](https://portal.azure.com)väljer du **Alla tjänster**på Azure-menyn .

1. Ange "integration" som filter i sökrutan. Välj den här resursen i resultatet: **Integrationskonton**

1. Under **Integrationskonton**väljer du det integrationskonto som har det avtal som du vill ta bort.

1. Välj panelen **Avtal** under **Komponenter**i den högra rutan.

1. Under **Avtal**väljer du ditt avtal och väljer **Ta bort**.

1. Bekräfta att du vill ta bort det markerade avtalet.

## <a name="next-steps"></a>Nästa steg

* [Utbyta AS2-meddelanden](logic-apps-enterprise-integration-as2.md)
* [Utbyta EDIFACT-meddelanden](logic-apps-enterprise-integration-edifact.md)
* [Utbyta X12-meddelanden](logic-apps-enterprise-integration-x12.md)

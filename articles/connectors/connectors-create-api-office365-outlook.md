---
title: Anslut till Office 365 Outlook
description: Automatisera uppgifter och arbetsflöden som hanterar e-post, kontakter och kalendrar i Office 365 Outlook med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732737"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Hantera e-post, kontakter och kalendrar i Office 365 Outlook med hjälp av Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och Office [365 Outlook-anslutningsappen](/connectors/office365connector/)kan du skapa automatiserade uppgifter och arbetsflöden som hanterar ditt Office 365-konto genom att skapa logikappar. Du kan till exempel automatisera dessa uppgifter:

* Skaffa, skicka och svara på e-post. 
* Schemalägg möten i kalendern.
* Lägg till och redigera kontakter. 

Du kan använda valfri utlösare för att starta arbetsflödet, till exempel när ett nytt e-postmeddelande anländer, när ett kalenderobjekt uppdateras eller när en händelse inträffar i en skillnadstjänst, till exempel Salesforce. Du kan använda åtgärder som svarar på utlösarhändelsen, till exempel skicka ett e-postmeddelande eller skapa en ny kalenderhändelse. 

> [!NOTE]
> Om du vill @outlook.com automatisera @hotmail.com uppgifter för ett konto eller ett konto använder [du Outlook.com-kopplingen](../connectors/connectors-create-api-outlook.md).

## <a name="prerequisites"></a>Krav

* Ett [Office 365-konto](https://www.office.com/)

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Logikappen där du vill komma åt ditt Office 365 Outlook-konto. Om du vill starta arbetsflödet med en Office 365 Outlook-utlösare måste du ha en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill lägga till en Office 365 Outlook-åtgärd i arbetsflödet måste logikappen redan ha en utlösare.

## <a name="add-a-trigger"></a>Lägga till en utlösare

En [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) är en händelse som startar arbetsflödet i logikappen. I det här exemplet använder logikappen en "avsökning" som söker efter en uppdaterad kalenderhändelse i ditt e-postkonto, baserat på det angivna intervallet och frekvensen.

1. Öppna din tomma logikapp i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Ange som filter `office 365 outlook` i sökrutan. Det här exemplet väljer **När ett kommande evenemang startar snart**.
   
   ![Välj utlösare för att starta logikappen](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Om du uppmanas att logga in anger du dina Office 365-autentiseringsuppgifter så att logikappen kan ansluta till ditt konto. Annars, om anslutningen redan finns, ange informationen för utlösarens egenskaper.

   I det här exemplet markeras kalendern som utlösaren kontrollerar, till exempel:

   ![Konfigurera utlösarens egenskaper](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. I utlösaren ställer du in **frekvens-** och **intervallvärdena.** Om du vill lägga till andra tillgängliga utlösaregenskaper, till exempel **Tidszon,** markerar du dessa egenskaper i listan **Lägg till ny parameter.**

   Om du till exempel vill att utlösaren ska kontrollera kalendern var 15:e minut ställer du in **Frekvens** till **Minut**och ställer in **Intervall** till `15`. 

   ![Ange frekvens och intervall för utlösaren](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Välj **Spara**i designerverktygsfältet .

Lägg nu till en åtgärd som körs när utlösaren har avfyrats. Du kan till exempel lägga till meddelandeåtgärden Twilio **Send,** som skickar en text när en kalenderhändelse startar om 15 minuter.

## <a name="add-an-action"></a>Lägga till en åtgärd

En [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är en åtgärd som körs av arbetsflödet i logikappen. Logikappen skapar ett nytt kontakt i Office 365 Outlook i det här exemplet. Du kan använda utdata från en annan utlösare eller åtgärd för att skapa kontakten. Anta till exempel att logikappen använder Dynamics 365-utlösaren **när en post skapas**. Du kan lägga till **kontaktåtgärden** Skapa kontakt i Office 365 Outlook och använda utdata från SalesForce-utlösaren för att skapa den nya kontakten.

1. Öppna logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Om du vill lägga till en åtgärd som det sista steget i arbetsflödet väljer du **Nytt steg**. 

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan dessa steg. Markera plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange som filter `office 365 outlook` i sökrutan. I det här exemplet markeras **Skapa kontakt**.

   ![Välj den åtgärd som ska köras i logikappen](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Om du uppmanas att logga in anger du dina Office 365-autentiseringsuppgifter så att logikappen kan ansluta till ditt konto. Annars, om anslutningen redan finns, ange informationen för åtgärdens egenskaper.

   I det här exemplet markeras den kontaktmapp där åtgärden skapar den nya kontakten, till exempel:

   ![Konfigurera åtgärdens egenskaper](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Om du vill lägga till andra tillgängliga åtgärdsegenskaper markerar du dessa egenskaper i listan **Lägg till ny parameter.**

1. Välj **Spara**i designerverktygsfältet .

## <a name="connector-specific-details"></a>Anslutningsspecifik information

Teknisk information om utlösare, åtgärder och begränsningar enligt beskrivningen i kopplingens Swagger-fil finns på [kopplingens referenssida](/connectors/office365connector/). 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)

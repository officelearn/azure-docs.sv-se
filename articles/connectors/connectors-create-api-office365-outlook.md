---
title: Ansluta till Office 365 Outlook - Azure Logikappar | Microsoft Docs
description: 'Hantera e-post, kontakter och kalender med Office 365 REST API: er och Azure Logic Apps'
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 10/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: aa900a486b9b0bd0f570da866f3d0ab78c31bc6e
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296160"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Kom igång med Office 365 Outlook connector
Office 365 Outlook connector kan interaktion med Outlook i Office 365. Använd den här anslutningen för att skapa, redigera och uppdatera kontakter och kalenderobjekt, och också få, skicka och svara på e-post.

Med Office 365 Outlook kan du:

* Skapa arbetsflödet med hjälp av funktionerna i Office 365 e-post och kalender. 
* Använd utlösare för att starta arbetsflödet när det finns en ny e-post, när ett kalenderobjekt uppdateras med mera.
* Använd åtgärder för att skicka ett e-postmeddelande, skapa en ny händelse och mycket mer. Till exempel när det finns ett nytt objekt i Salesforce (en utlösare), skicka ett e-postmeddelande till din Office 365 Outlook (en åtgärd). 

Den här artikeln visar hur du använder Office 365 Outlook-anslutningen i en logikapp och visar också utlösare och åtgärder.

> [!NOTE]
> Den här versionen av artikeln gäller för Logic Apps allmän tillgänglighet (GA).
> 
> 

Läs mer om Logic Apps i [vad är logic apps](../logic-apps/logic-apps-overview.md) och [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Ansluta till Office 365
Innan din logikapp får åtkomst till alla tjänster måste du först skapa en *anslutning* till tjänsten. En anslutning kan du ansluta en logikapp och en annan tjänst. Till exempel för att ansluta till Office 365 Outlook, måste du först en Office 365 *anslutning*. Ange de autentiseringsuppgifter som du vanligtvis använder för att få åtkomst till tjänsten som du vill ansluta till om du vill skapa en anslutning. Så med Office 365 Outlook ange autentiseringsuppgifterna för ditt Office 365-konto för att skapa anslutningen.

## <a name="create-the-connection"></a>Skapa anslutningen
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Använda en utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definierats i en logikapp. Utlösare avsöker ”” tjänsten på ett intervall och frekvens som du vill. [Mer information om utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. I logikappen, skriver du ”office 365” om du vill hämta en lista över utlösare:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Välj **Office 365 Outlook - när en kommande händelse startas snart**. Om det finns redan en anslutning, väljer du en kalender från den nedrullningsbara listan.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Om du uppmanas att logga in, anger du tecknet i informationen för att skapa anslutningen. [Skapa anslutningen](connectors-create-api-office365-outlook.md#create-the-connection) innehåller stegen i det här avsnittet. 
   
   > [!NOTE]
   > I det här exemplet körs logikappen när en händelse har uppdaterats. Lägg till en annan åtgärd som skickar ett SMS till dig om du vill se resultatet av den här utlösaren. Till exempel lägga till Twilio *skickar ett meddelande* åtgärd som texter när händelse startar i 15 minuter. 
   > 
   > 
3. Välj den **redigera** knappen och ange den **frekvens** och **intervall** värden. Till exempel om du vill att utlösaren ska avsöka var 15: e minut, anger du den **frekvens** till **minut**, och ange den **intervall** till **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Spara** dina ändringar (övre vänstra hörnet i verktygsfältet). Din logikapp sparas och aktiveras automatiskt.

## <a name="use-an-action"></a>Använda en åtgärd
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. [Mer information om åtgärder](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Klicka på plustecknet. Du ser flera alternativ: **lägga till en åtgärd**, **Lägg till ett villkor**, eller en av de **mer** alternativ.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Välj **lägga till en åtgärd**.
3. Skriv ”office 365” om du vill hämta en lista över alla tillgängliga åtgärder i textrutan.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. I vårt exempel väljer **Office 365 Outlook - skapa kontakt**. Om det finns redan en anslutning, väljer du den **mappen ID**, **Förnamn**, och andra egenskaper:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Om du uppmanas att ange anslutningsinformation anger du information för att skapa anslutningen. [Skapa anslutningen](connectors-create-api-office365-outlook.md#create-the-connection) i det här avsnittet beskriver dessa egenskaper. 
   
   > [!NOTE]
   > I det här exemplet skapar vi en ny kontakt i Office 365 Outlook. Du kan använda utdata från en annan utlösare för att skapa kontakten. Lägg exempelvis till SalesForce *när ett objekt skapas* utlösare. Lägg sedan till Office 365 Outlook *skapa kontakt* åtgärd som SalesForce-fälten används för att skapa ny ny kontakt i Office 365. 
   > 
   > 
5. **Spara** dina ändringar (övre vänstra hörnet i verktygsfältet). Din logikapp sparas och aktiveras automatiskt.

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/office365connector/). 

## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Utforska andra tillgängliga kopplingar i Logic Apps på vår [API: er listan](apis-list.md).


---
title: Ansluta till Office 365 Outlook
description: 'Hantera e-post, kontakter och kalendrar med Office 365 REST-API: er och Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 858366947fe21a20d6f112fc51899d1533a36472
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789602"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Kom igång med Office 365 Outlook Connector
Office 365 Outlook Connector möjliggör interaktion med Outlook i Office 365. Använd den här anslutningen för att skapa, redigera och uppdatera kontakter och Kalender objekt och även hämta, skicka och svara på e-post.

Med Office 365 Outlook kan du:

* Bygg arbets flödet med e-post och kalender funktioner i Office 365. 
* Använd utlösare för att starta arbets flödet när det finns ett nytt e-postmeddelande när ett Kalender objekt uppdateras, med mera.
* Använd åtgärder för att skicka ett e-postmeddelande, skapa en ny kalender händelse med mera. Om det till exempel finns ett nytt objekt i Salesforce (en utlösare) skickar du ett e-postmeddelande till Office 365 Outlook (en åtgärd). 

Den här artikeln visar hur du använder Office 365 Outlook Connector i en Logic app och listar utlösare och åtgärder.

> [!NOTE]
> Den här versionen av artikeln gäller Logic Apps allmän tillgänglighet (GA).
> 
> 

Mer information om Logic Apps finns i [Vad är](../logic-apps/logic-apps-overview.md) Logi Kap par och [skapa en Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Ansluta till Office 365
Innan din Logi Kap par kan komma åt en tjänst skapar du först en *anslutning* till tjänsten. En anslutning ger anslutning mellan en Logic app och en annan tjänst. Om du till exempel vill ansluta till Office 365 Outlook behöver du först en Office 365- *anslutning*. Om du vill skapa en anslutning anger du de autentiseringsuppgifter som du använder normalt för att få åtkomst till den tjänst som du vill ansluta till. Så med Office 365 Outlook anger du autentiseringsuppgifterna för ditt Office 365-konto för att skapa anslutningen.

## <a name="create-the-connection"></a>Skapa anslutningen
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Använda en utlösare
En utlösare är en händelse som kan användas för att starta arbets flödet som definierats i en Logic app. Utlöser "avsökning"-tjänsten med ett intervall och en frekvens som du vill använda. [Läs mer om utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. I Logic-appen skriver du "Office 365" för att hämta en lista över utlösare:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Välj **Office 365 Outlook – när en kommande händelse startar snart**. Om det redan finns en anslutning väljer du en kalender i list rutan.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Om du uppmanas att logga in anger du inloggnings informationen för att skapa anslutningen. [Skapa anslutningen](connectors-create-api-office365-outlook.md#create-the-connection) i det här avsnittet som beskriver stegen. 
   
   > [!NOTE]
   > I det här exemplet körs Logic app när en kalender händelse uppdateras. Om du vill se resultatet av den här utlösaren lägger du till en annan åtgärd som skickar ett textmeddelande. Du kan till exempel lägga till åtgärden Twilio *Skicka meddelande* som texter när kalender händelsen startar om 15 minuter. 
   > 
   > 
3. Välj knappen **Redigera** och ange värdena för **frekvens** och **intervall** . Om du till exempel vill att utlösaren ska avsöka var 15: e minut anger du **frekvensen** till **minut**och anger **intervallet** till **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Spara** ändringarna (övre vänstra hörnet i verktygsfältet). Din Logic app sparas och kan aktive ras automatiskt.

## <a name="use-an-action"></a>Använd en åtgärd
En åtgärd är en åtgärd som utförs av arbets flödet som definierats i en Logic app. [Läs mer om åtgärder](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Välj plus tecknet. Du ser flera alternativ: **Lägg till en åtgärd**, **Lägg till ett villkor**eller något av de **fler** alternativen.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Välj **Lägg till en åtgärd**.
3. Skriv "Office 365" i text rutan för att hämta en lista över alla tillgängliga åtgärder.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. I vårt exempel väljer du **Office 365 Outlook – skapa kontakt**. Om det redan finns en anslutning väljer du **mappens ID**, det **namn**och andra egenskaper:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Om du uppmanas att ange anslutnings informationen anger du informationen för att skapa anslutningen. [Skapa anslutningen](connectors-create-api-office365-outlook.md#create-the-connection) i det här avsnittet beskriver dessa egenskaper. 
   
   > [!NOTE]
   > I det här exemplet skapar vi en ny kontakt i Office 365 Outlook. Du kan använda utdata från en annan utlösare för att skapa kontakten. Lägg till exempel till SalesForce *när ett objekt skapas* utlösare. Lägg sedan till Office 365 Outlook *skapa kontakt* åtgärd som använder Salesforce-fälten för att skapa den nya kontakten i Office 365. 
   > 
   > 
5. **Spara** ändringarna (övre vänstra hörnet i verktygsfältet). Din Logic app sparas och kan aktive ras automatiskt.

## <a name="connector-specific-details"></a>Anslutningsspecifika Detaljer

Visa eventuella utlösare och åtgärder som definierats i Swagger och se även eventuella begränsningar i [anslutnings informationen](/connectors/office365connector/). 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
---
title: Anslut till Office 365 Outlook – Azure Logic Apps | Microsoft Docs
description: 'Hantera e-post, kontakter och kalendrar med Office 365 REST API: er och Azure Logic Apps'
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
ms.openlocfilehash: 0355f121a09e1ba89f98a8af5037eb1371db2242
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215646"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Kom igång med Office 365 Outlook-anslutning
Anslutningsappen för Office 365 Outlook gör det möjligt för interaktion med Outlook i Office 365. Använd den här anslutningen för att skapa, redigera, och uppdatera kontakter och kalenderobjekt, och även hämta, skicka och svara på e-post.

Med Office 365 Outlook kan du:

* Skapa ditt arbetsflöde med hjälp av e-postmeddelanden och funktioner i Office 365. 
* Använd utlösare för att starta arbetsflödet när det finns en ny e-post, när ett kalenderobjekt uppdateras och mycket mer.
* Använda åtgärder för att skicka ett e-postmeddelande, skapa en ny kalenderhändelse och mycket mer. Till exempel när det finns ett nytt objekt i Salesforce (en utlösare), skicka ett e-postmeddelande till din Office 365 Outlook (en åtgärd). 

Den här artikeln visar hur du använder Office 365 Outlook-anslutningen i en logikapp och listar även utlösare och åtgärder.

> [!NOTE]
> Den här versionen av artikeln gäller för Logic Apps allmän tillgänglighet (GA).
> 
> 

Läs mer om Logic Apps i [vad är logic apps](../logic-apps/logic-apps-overview.md) och [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Ansluta till Office 365
Innan din logikapp kan komma åt alla tjänster måste du först skapa en *anslutning* till tjänsten. En anslutning tillhandahåller anslutningen mellan en logikapp och en annan tjänst. Till exempel för att ansluta till Office 365 Outlook, måste du först en Office 365 *anslutning*. Ange de autentiseringsuppgifter som du vanligtvis använder för att få åtkomst till tjänsten som du vill ansluta till för att skapa en anslutning. Så med Office 365 Outlook, ange autentiseringsuppgifterna till ditt Office 365-konto för att skapa anslutningen.

## <a name="create-the-connection"></a>Skapa anslutningen
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Använda en utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definierats i en logikapp. Utlösare avsöker ”” tjänsten på ett intervall och frekvens som du vill. [Mer information om utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Skriv ”office 365” om du vill hämta en lista över vilka utlösare i logikappen:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Välj **Office 365 Outlook – när en kommande händelse börjar snart**. Om det finns redan en anslutning, väljer du en kalender från den nedrullningsbara listan.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Om du uppmanas att logga in, anger du tecknet i information för att skapa anslutningen. [Skapa anslutningen](connectors-create-api-office365-outlook.md#create-the-connection) innehåller stegen i det här avsnittet. 
   
   > [!NOTE]
   > I det här exemplet kör logikappen när en händelse uppdateras. Lägg till en annan åtgärd som skickar du ett textmeddelande för att se resultatet av den här utlösaren. Till exempel lägga till Twilio *skicka meddelande* åtgärd som skickar ett SMS när calendar-händelse startar i 15 minuter. 
   > 
   > 
3. Välj den **redigera** knappen och ange den **frekvens** och **intervall** värden. Till exempel om du vill att utlösaren ska avsöka varje kvart sedan ange den **frekvens** till **minut**, och ange den **intervall** till **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Spara** ändringarna (övre vänstra hörnet av verktygsfältet). Logikappen sparas och aktiveras automatiskt.

## <a name="use-an-action"></a>Använd en åtgärd
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. [Läs mer om åtgärder](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Klicka på plustecknet. Du ser flera alternativ: **Lägg till en åtgärd**, **Lägg till ett villkor**, eller någon av de **mer** alternativ.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Välj **Lägg till en åtgärd**.
3. I textrutan skriver du ”office 365” om du vill hämta en lista över alla tillgängliga åtgärder.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. I vårt exempel väljer **Office 365 Outlook – skapa kontakt**. Om det finns redan en anslutning, väljer den **mapp-ID**, **Förnamn**, och andra egenskaper:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Om du uppmanas att ange informationen som du anger du information som du vill skapa anslutningen. [Skapa anslutningen](connectors-create-api-office365-outlook.md#create-the-connection) i det här avsnittet beskriver de här egenskaperna. 
   
   > [!NOTE]
   > I det här exemplet skapar vi en ny kontakt i Office 365 Outlook. Du kan använda utdata från en annan utlösare för att skapa kontakten. Lägg exempelvis till SalesForce *när ett objekt skapas* utlösaren. Lägg sedan till Office 365 Outlook *skapa kontakt* åtgärd som SalesForce-fälten används för att skapa en ny kontakt i Office 365. 
   > 
   > 
5. **Spara** ändringarna (övre vänstra hörnet av verktygsfältet). Logikappen sparas och aktiveras automatiskt.

## <a name="connector-specific-details"></a>Information om specifika

Visa alla utlösare och åtgärder som definierats i swagger och får även eventuella gränser i den [anslutningsinformationen](/connectors/office365connector/). 

## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Utforska andra tillgängliga anslutningsappar i Logic Apps på vår [API: er lista](apis-list.md).


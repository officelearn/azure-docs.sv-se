---
title: Anslut till OneDrive – Azure Logic Apps | Microsoft Docs
description: 'Ladda upp och hantera filer med OneDrive REST API: er och Azure Logic Apps'
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
ms.openlocfilehash: 840a010f8606387a250552d884621a96d0031f90
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106235"
---
# <a name="get-started-with-the-onedrive-connector"></a>Kom igång med OneDrive-anslutningsapp
Ansluta till OneDrive för att hantera dina filer, inklusive överföring, hämta, ta bort filer och mycket annat. 

Med OneDrive kan du: 

* Bygg ditt arbetsflöde genom att lagra filer i OneDrive eller uppdaterar befintliga filer i OneDrive. 
* Använd utlösare för att starta arbetsflödet när en fil skapas eller uppdateras i din OneDrive.
* Använda åtgärder för att skapa en fil, ta bort en fil och mycket mer. Till exempel när en ny Office 365-e-postmeddelande tas emot med en bifogad fil (en utlösare), skapa en ny fil i OneDrive (en åtgärd).

Den här artikeln visar hur du använder OneDrive-anslutningen i en logikapp och listar även utlösare och åtgärder.

Läs mer om Logic Apps i [vad är logic apps](../logic-apps/logic-apps-overview.md) och [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Anslut till OneDrive
Innan din logikapp kan komma åt alla tjänster måste du först skapa en *anslutning* till tjänsten. En anslutning tillhandahåller anslutningen mellan en logikapp och en annan tjänst. Till exempel för att ansluta till OneDrive, måste du först en OneDrive *anslutning*. Ange de autentiseringsuppgifter som du vanligtvis använder för att få åtkomst till tjänsten som du vill ansluta till för att skapa en anslutning. Därför med OneDrive, ange autentiseringsuppgifterna till ditt OneDrive-konto för att skapa anslutningen.

### <a name="create-the-connection"></a>Skapa anslutningen
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>Använda en utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definierats i en logikapp. Utlösare avsöker ”” tjänsten på ett intervall och frekvens som du vill. [Mer information om utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. I logikapp, skriver du ”onedrive” om du vill hämta en lista över vilka utlösare:  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Välj **när en fil ändras**. Om det finns redan en anslutning, väljer du knappen Visa väljare och väljer en mapp.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Om du uppmanas att logga in, anger du tecknet i information för att skapa anslutningen. [Skapa anslutningen](connectors-create-api-onedrive.md#create-the-connection) innehåller stegen i den här artikeln. 
   
   > [!NOTE]
   > I det här exemplet logikappen körs när en fil i mappen som du väljer uppdateras. Lägg till en annan åtgärd som skickar du ett e-postmeddelande om du vill se resultatet av den här utlösaren. Till exempel lägga till Office 365 Outlook *skicka ett e-postmeddelande* åtgärd som e-postmeddelande när en fil har uppdaterats. 

3. Välj den **redigera** knappen och ange den **frekvens** och **intervall** värden. Till exempel om du vill att utlösaren ska avsöka varje kvart sedan ange den **frekvens** till **minut**, och ange den **intervall** till **15**. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **Spara** ändringarna (övre vänstra hörnet av verktygsfältet). Logikappen sparas och aktiveras automatiskt.

## <a name="use-an-action"></a>Använd en åtgärd
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. [Läs mer om åtgärder](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Klicka på plustecknet. Du ser flera alternativ: **Lägg till en åtgärd**, **Lägg till ett villkor**, eller någon av de **mer** alternativ.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Välj **Lägg till en åtgärd**.
3. I textrutan skriver du ”onedrive” om du vill hämta en lista över alla tillgängliga åtgärder.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. I vårt exempel väljer **OneDrive – skapa fil**. Om det finns redan en anslutning, väljer du den **mappsökväg** om du vill placera filen, ange den **filnamn**, och välj den **filinnehåll** du vill:  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Om du uppmanas att ange informationen som du anger du information som du vill skapa anslutningen. [Skapa anslutningen](connectors-create-api-onedrive.md#create-the-connection) i den här artikeln beskriver de här egenskaperna. 
   
   > [!NOTE]
   > I det här exemplet skapar vi en ny fil i en OneDrive-mapp. Du kan använda utdata från en annan utlösare för att skapa OneDrive-fil. Till exempel lägga till Office 365 Outlook *när ett nytt e-postmeddelande* utlösaren. Lägg sedan till OneDrive *skapa fil* åtgärd som använder bifogade filer och Content-Type fält inom en ForEach för att skapa den nya filen i OneDrive. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Spara** ändringarna (övre vänstra hörnet av verktygsfältet). Logikappen sparas och aktiveras automatiskt.


## <a name="connector-specific-details"></a>Information om specifika

Visa alla utlösare och åtgärder som definierats i swagger och får även eventuella gränser i den [anslutningsinformationen](/connectors/onedriveconnector/).

## <a name="more-connectors"></a>Fler anslutningsappar
Gå tillbaka till den [API: er lista](apis-list.md).
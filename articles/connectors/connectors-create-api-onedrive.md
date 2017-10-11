---
title: "Lägg till OneDrive-koppling i dina Logic Apps | Microsoft Docs"
description: "Översikt över OneDrive anslutningen med REST API-parametrar"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 47a8582a-1b1a-4fc3-beb5-97c60c4306fe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 63bd33bf4e09b98aa53dcfec9fcc4a0109204952
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-onedrive-connector"></a>Kom igång med OneDrive-koppling
Ansluta till OneDrive för att hantera dina filer, inklusive överföring, hämta, ta bort filer och mycket mer. 

Med OneDrive kan du: 

* Skapa ditt arbetsflöde genom att lagra filer i OneDrive eller uppdatera befintliga filer i OneDrive. 
* Använd utlösare för att starta arbetsflödet när en fil skapas eller uppdateras i OneDrive.
* Använd åtgärder för att skapa en fil, ta bort en fil med mera. Till exempel när en ny Office 365 e-post tas emot med en bifogad fil (en utlösare), skapa en ny fil i OneDrive (en åtgärd).

Det här avsnittet beskrivs hur du använder OneDrive-anslutningen i en logikapp och visar också utlösare och åtgärder.

Läs mer om Logic Apps i [vad är logic apps](../logic-apps/logic-apps-what-are-logic-apps.md) och [skapa en logikapp](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-onedrive"></a>Ansluta till OneDrive
Innan din logikapp får åtkomst till alla tjänster måste du först skapa en *anslutning* till tjänsten. En anslutning kan du ansluta en logikapp och en annan tjänst. Till exempel för att ansluta till OneDrive, måste du först en OneDrive *anslutning*. Ange de autentiseringsuppgifter som du vanligtvis använder för att få åtkomst till tjänsten som du vill ansluta till om du vill skapa en anslutning. Så, OneDrive, ange autentiseringsuppgifterna till OneDrive-konto för att skapa anslutningen.

### <a name="create-the-connection"></a>Skapa anslutningen
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>Använda en utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definierats i en logikapp. Utlösare avsöker ”” tjänsten på ett intervall och frekvens som du vill. [Mer information om utlösare](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Skriv ”onedrive” i logikappen, om du vill hämta en lista över utlösare:  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Välj **när en fil ändras**. Om det finns redan en anslutning, väljer du knappen Visa objektväljaren och väljer en mapp.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Om du uppmanas att logga in, anger du tecknet i informationen för att skapa anslutningen. [Skapa anslutningen](connectors-create-api-onedrive.md#create-the-connection) innehåller stegen i det här avsnittet. 
   
   > [!NOTE]
   > I det här exemplet körs logikappen när en fil i mappen som du väljer uppdateras. Lägg till en annan åtgärd som skickar ett e-postmeddelande om du vill se resultatet av den här utlösaren. Till exempel lägga till Office 365 Outlook *skickar ett e-* åtgärd som e-postmeddelanden när en fil har uppdaterats. 

3. Välj den **redigera** knappen och ange den **frekvens** och **intervall** värden. Till exempel om du vill att utlösaren ska avsöka var 15: e minut, anger du den **frekvens** till **minut**, och ange den **intervall** till **15**. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **Spara** dina ändringar (övre vänstra hörnet i verktygsfältet). Din logikapp sparas och aktiveras automatiskt.

## <a name="use-an-action"></a>Använda en åtgärd
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. [Mer information om åtgärder](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Klicka på plustecknet. Du ser flera alternativ: **lägga till en åtgärd**, **Lägg till ett villkor**, eller en av de **mer** alternativ.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Välj **lägga till en åtgärd**.
3. I rutan skriver du ”onedrive” om du vill hämta en lista över alla tillgängliga åtgärder.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. I vårt exempel väljer **OneDrive – skapa filen**. Om det finns redan en anslutning, väljer du den **mappsökväg** för att placera filen, ange den **filnamn**, och välj den **filinnehåll** du vill:  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Om du uppmanas att ange anslutningsinformation anger du information för att skapa anslutningen. [Skapa anslutningen](connectors-create-api-onedrive.md#create-the-connection) i det här avsnittet beskriver dessa egenskaper. 
   
   > [!NOTE]
   > I det här exemplet skapar vi en ny fil i en mapp i OneDrive. Du kan använda utdata från en annan utlösare för att skapa filen OneDrive. Till exempel lägga till Office 365 Outlook *när en ny e-postmeddelandet* utlösare. Lägg sedan till OneDrive *skapa fil* åtgärd som använder bilagor och Content-Type fält inom en ForEach att skapa den nya filen i OneDrive. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Spara** dina ändringar (övre vänstra hörnet i verktygsfältet). Din logikapp sparas och aktiveras automatiskt.


## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/onedriveconnector/).

## <a name="more-connectors"></a>Flera kopplingar
Gå tillbaka till den [API: er listan](apis-list.md).
---
title: Anslut till Dropbox – Azure Logic Apps | Microsoft Docs
description: 'Ladda upp och hantera filer med Dropbox REST API: er och Azure Logic Apps'
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 07/15/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 256a0b34d5050e17abe5bb98ca0c13ab0b61787e
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094446"
---
# <a name="get-started-with-the-dropbox-connector"></a>Kom igång med Dropbox-anslutningsapp
Anslut till Dropbox för att hantera dina filer. Du kan utföra olika åtgärder som att överföra, uppdatera, hämta och ta bort filer i Dropbox.

Att använda [alla anslutningar](apis-list.md), måste du först skapa en logikapp. Du kan komma igång med [nu skapa en Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-dropbox"></a>Anslut till Dropbox
Innan din logikapp kan komma åt alla tjänster måste du först måste du skapa en *anslutning* till tjänsten. En anslutning tillhandahåller anslutningen mellan en logikapp och en annan tjänst. Till exempel för att ansluta till Dropbox, måste du först en Dropbox *anslutning*. Om du vill skapa en anslutning, skulle du behöva ange de autentiseringsuppgifter som du vanligtvis använder för att få åtkomst till tjänsten som du vill ansluta till. Därför i Dropbox-exemplet skulle du behöva autentiseringsuppgifterna ditt Dropbox-konto för att skapa anslutningen till Dropbox. 

### <a name="create-a-connection-to-dropbox"></a>Skapa en anslutning till Dropbox
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Använda en Dropbox-utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definierats i en logikapp. [Mer information om utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts).

I det här exemplet ska vi använda den **när en fil skapas** utlösaren. När den här utlösaren inträffar, vi kan kalla den **hämta filinnehåll med hjälp av sökvägen** Dropbox-åtgärden. 

1. Ange *dropbox* i sökrutan på Logic Apps-designern och markera den **Dropbox – när en fil skapas** utlösaren.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Välj den mapp där du vill spåra filer skapas. Välj... (som identifieras i den röda rutan) och bläddra till den mapp som du vill välja för utlösaren användarens indata.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Använda en Dropbox-åtgärden
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. [Läs mer om åtgärder](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Nu när utlösaren har lagts till, kan du följa stegen nedan för att lägga till en åtgärd som hämtar innehållet för den nya filen.

1. Välj **+ nytt steg** att lägga till den åtgärd som du vill göra när en ny fil skapas.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Välj **Lägg till en åtgärd**. Den här öppnas sökrutan där du kan söka efter alla åtgärder du vill göra.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Ange *dropbox* att söka efter åtgärder som är relaterade till Dropbox.  
4. Välj **Dropbox – hämta filinnehåll med hjälp av sökvägen** som åtgärden som ska vidtas när en ny fil skapas i den valda Dropbox-mappen. Åtgärden kontroll block öppnas. Du uppmanas att godkänna din logikapp för att få åtkomst till ditt Dropbox-konto om du inte har gjort det tidigare.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Välj... (finns på höger sida av den **filsökväg** kontroll) och bläddra till sökvägen till filen som du vill använda. Du kan också använda den **filsökväg** token för att skapa din logic-appar snabbare.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Spara ditt arbete och skapa en ny fil i Dropbox för att aktivera ditt arbetsflöde.  

## <a name="connector-specific-details"></a>Information om specifika

Visa alla utlösare och åtgärder som definierats i swagger och får även eventuella gränser i den [anslutningsinformationen](/connectors/dropbox/).

## <a name="more-connectors"></a>Fler anslutningsappar
Gå tillbaka till den [API: er lista](apis-list.md).

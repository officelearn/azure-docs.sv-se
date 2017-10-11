---
title: Dropbox-anslutningen i Azure Logic Apps | Microsoft Docs
description: "Skapa logikappar med Azure App service. Ansluta till Dropbox att hantera dina filer. Du kan utföra olika åtgärder, till exempel ladda upp, uppdatera, hämta och ta bort filer i Dropbox."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 0d09580c60fd620811b539147439d0922839fe7e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-dropbox-connector"></a>Kom igång med Dropbox-koppling
Ansluta till Dropbox att hantera dina filer. Du kan utföra olika åtgärder, till exempel ladda upp, uppdatera, hämta och ta bort filer i Dropbox.

Att använda [alla anslutningar](apis-list.md), måste du först skapa en logikapp. Du kan komma igång med [att skapa en logikapp nu](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-dropbox"></a>Ansluta till Dropbox
Innan din logikapp kan komma åt någon tjänst, måste du först skapa en *anslutning* till tjänsten. En anslutning kan du ansluta en logikapp och en annan tjänst. Till exempel för att ansluta till Dropbox, måste du först en Dropbox *anslutning*. Om du vill skapa en anslutning skulle du behöva ange autentiseringsuppgifter som du vanligtvis använder för att få åtkomst till tjänsten som du vill ansluta till. Så i Dropbox-exemplet skulle du behöva autentiseringsuppgifterna Dropbox-konto för att skapa anslutningen till Dropbox. [Mer information om anslutningar]()

### <a name="create-a-connection-to-dropbox"></a>Skapa en anslutning till Dropbox
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Använda en Dropbox-utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definierats i en logikapp. [Mer information om utlösare](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

I det här exemplet ska vi använda den **när en fil skapas** utlösare. När den här utlösaren uppstår kan vi ringer den **hämta innehåll med sökvägen** Dropbox-åtgärd. 

1. Ange *dropbox* i sökrutan på Logic Apps designer väljer den **Dropbox - när en fil skapas** utlösare.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Välj den mapp där du vill spåra skapas. Välj... (som identifieras i den röda rutan) och bläddra till mappen som du vill välja för utlösaren input.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Använda en Dropbox-åtgärd
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. [Mer information om åtgärder](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Nu när utlösaren har lagts till, Följ dessa steg för att lägga till en åtgärd som får den nya filen innehåll.

1. Välj **+ nytt steg** att lägga till den åtgärd som ska vidtas när en ny fil skapas.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Välj **lägga till en åtgärd**. Den här öppnas sökrutan där du kan söka efter något du vill göra.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Ange *dropbox* att söka efter åtgärder som rör Dropbox.  
4. Välj **Dropbox - Get-filinnehåll med sökvägen till** som åtgärden som ska vidtas när en ny fil skapas i den valda Dropbox-mappen. Åtgärden kontrollen block öppnas. Du uppmanas att godkänna logikappen åtkomst till Dropbox-konto om du inte har gjort det tidigare.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Välj... (på höger sida av den **filsökväg** kontroll) och bläddra till sökvägen till filen som du vill använda. Eller Använd den **filsökväg** token för att påskynda genereringen av din app logik.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Spara ditt arbete och skapa en ny fil i Dropbox aktivera arbetsflödet.  

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/dropbox/).

## <a name="more-connectors"></a>Flera kopplingar
Gå tillbaka till den [API: er listan](apis-list.md).
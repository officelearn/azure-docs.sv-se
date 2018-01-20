---
title: SMTP-anslutaren i Azure Logic Apps | Microsoft Docs
description: "Skapa logikappar med Azure App service. Ansluta till SMTP för att skicka e-post."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 6544a8e81a1c84fbd09e60b3f06c5fd308a18dcf
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-smtp-connector"></a>Kom igång med SMTP-koppling
Ansluta till SMTP för att skicka e-post.

Att använda [alla anslutningar](apis-list.md), måste du först skapa en logikapp. Du kan komma igång med [att skapa en logikapp nu](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-smtp"></a>Ansluta till SMTP
Innan din logikapp kan komma åt någon tjänst, måste du först skapa en *anslutning* till tjänsten. En [anslutning](connectors-overview.md) tillhandahåller anslutningen mellan en logikapp och en annan tjänst. Till exempel för att ansluta till SMTP måste du först en SMTP *anslutning*. Ange de autentiseringsuppgifter som du vanligtvis använder för att få åtkomst till tjänsten som du ansluter till om du vill skapa en anslutning. Så i SMTP-exemplet anger du autentiseringsuppgifterna till Anslutningsnamn, SMTP-serveradressen och inloggningsinformation för användare att skapa anslutning till SMTP.  

### <a name="create-a-connection-to-smtp"></a>Skapa en anslutning till SMTP
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>Använda en SMTP-utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definierats i en logikapp. [Mer information om utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts).

I det här exemplet SMTP har inte en utlösare egna, använder vi den **Salesforce - när ett objekt skapas** utlösare. Den här utlösaren aktiveras när ett nytt objekt skapas i Salesforce. I vårt exempel vi ska konfigurera den så att varje gång en ny lead skapas i Salesforce, en *skicka e-post* sker via SMTP-anslutningen med ett meddelande för ny lead håller på att skapas.

1. Ange *salesforce* i sökrutan på logic apps designer väljer den **Salesforce - när ett objekt skapas** utlösare.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. Den **när ett objekt skapas** kontrollen visas.
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. Välj den **objekttyp** Välj *leda* från listan över objekt. I det här steget anger du att du skapar en utlösare som meddelar logikappen när en ny lead skapas i Salesforce.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. Utlösaren har skapats.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Använda en SMTP-åtgärden
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. [Mer information om åtgärder](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Nu när utlösaren har lagts till, Följ dessa steg för att lägga till en SMTP-åtgärden som utförs när en ny lead skapas i Salesforce.

1. Välj **+ nytt steg** att lägga till den åtgärd som ska vidtas när en ny lead skapas.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. Välj **lägga till en åtgärd**. Den här öppnas sökrutan där du kan söka efter något du vill göra.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. Ange *smtp* att söka efter åtgärder som rör SMTP.  
4. Välj **SMTP - skicka e-post** som åtgärden som ska vidtas när ny lead skapas. Åtgärden kontrollen block öppnas. Du måste upprätta en smtp-anslutning i designern blocket om du inte har gjort det tidigare.  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. Ange önskad e-information på den **SMTP - skicka e-post** block.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. Spara arbetet för att aktivera arbetsflödet.  

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/smtpconnector/).

## <a name="more-connectors"></a>Flera kopplingar
Gå tillbaka till den [API: er listan](apis-list.md).
---
title: Anslut till Twitter från Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som övervaka och hantera tweets och hämta data om följare, dina användare som följs, andra användare, tidslinjer och mer från ditt Twitter-konto med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: eea70d979a69a4855b6eeb892d1705ecadaa8434
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918653"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Övervaka och hantera Twitter med hjälp av Azure Logic Apps

Med Azure Logic Apps och Twitter-anslutningen kan du kan skapa automatiserade uppgifter och arbetsflöden som övervaka och hantera data som intresserar dig i Twitter som tweetar följare, användare och följt, tidslinjer med mera, tillsammans med andra åtgärder, till exempel:

* Övervaka, publicera och söka tweets.
* Hämta data, till exempel följare, användare som följs, tidslinjer med mera.

Du kan använda utlösare som få svar från ditt Twitter-konto och se utdata som är tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med ditt Twitter-konto. Du kan också ha andra åtgärder som använder utdata från Twitter-åtgärder. När en ny tweet med en specifik hashtagg visas, kan du till exempel skicka meddelanden med Slack-anslutningsprogrammet. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Dina autentiseringsuppgifter för Twitter-konto och användare

   Dina autentiseringsuppgifter för tillåta din logikapp för att skapa en anslutning och komma åt ditt Twitter-konto.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt Twitter-konto. Du kommer igång med en Twitter-utlösare, [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Twitter-åtgärd, starta din logikapp med en annan utlösare, till exempel, **upprepning** utlösaren.

## <a name="connect-to-twitter"></a>Ansluta till Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. Välj en sökväg: 

   * För tom logic apps i sökrutan anger du ”twitter” som filter. 
   Välj utlösaren som du vill under listan över utlösare. 

     ELLER

   * För befintliga logic apps: 
   
     * Under det sista steget där du vill lägga till en åtgärd, väljer **nytt steg**. 

       ELLER

     * Mellan stegen där du vill lägga till en åtgärd, flyttar du pekaren över pilen mellan stegen. 
     Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.
     
       I sökrutan anger du ”twitter” som filter. 
       Välj vilken åtgärd du önska under åtgärder.

1. Om du uppmanas att logga in på Twitter, logga in nu så att du kan godkänna åtkomst för din logikapp.

1. Ange informationen som krävs för din valda utlösare eller åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="examples"></a>Exempel

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Twitter-utlösare: när en ny tweet publiceras

Den här utlösaren startar en logikapparbetsflöde när utlösaren upptäcker en ny tweet, till exempel hashtag, #Seattle. Till exempel när dessa tweets hittas, du kan lägga till en fil med innehållet i dessa tweets lagring, till exempel ett Dropbox-konto med hjälp av Dropbox-anslutningen. 

Du kan ta ett villkor som berättigade tweets måste komma från användare med minst ett angivet antal följare.

**Enterprise exempel**: du kan använda den här utlösaren för att övervaka tweets om ditt företag och laddar upp den tweets innehåll till en SQL-databas.

### <a name="twitter-action-post-a-tweet"></a>Twitter-åtgärd: publicera en tweet

Den här åtgärden skickar en tweet, men du kan konfigurera åtgärden så att tweeten innehåller innehållet från tweets som hittades av den tidigare beskrivna utlösaren. 

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/twitterconnector/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)

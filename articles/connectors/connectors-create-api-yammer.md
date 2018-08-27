---
title: Anslut till Yammer från Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som övervakar, publicera och hantera meddelanden, flöden och annat i Yammer med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 41855d6e562ddbb78df5d1d8794127e1064cc2ca
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918303"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Övervaka och hantera ditt Yammer-konto med hjälp av Azure Logic Apps

Du kan skapa automatiserade uppgifter och arbetsflöden som övervakar och hanterar meddelanden, flöden och mer i ditt Yammer-konto, tillsammans med andra åtgärder, till exempel med Azure Logic Apps och Yammer-anslutningen:

* Övervakare för när nya meddelanden visas i följt feeds och grupper.
* Få meddelanden, grupper, nätverk, användarnas information och mer.
* Publicera och liknande meddelanden.

Du kan använda utlösare som få svar från dina Yammer-konto och se utdata som är tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med dina Yammer-konto. Du kan också ha andra åtgärder som använder utdata från Yammer-åtgärder. När nya meddelanden visas i feeds eller grupper kan dela du dessa meddelanden med Slack-anslutningsprogrammet. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Dina autentiseringsuppgifter för Yammer-konto och användare

   Dina autentiseringsuppgifter för tillåta din logikapp för att skapa en anslutning och komma åt ditt Yammer-konto.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt Yammer-konto. Du kommer igång med en Yammer-utlösare, [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Yammer-åtgärd, starta din logikapp med en annan utlösare, till exempel, **upprepning** utlösaren.

## <a name="connect-to-yammer"></a>Anslut till Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. Välj en sökväg: 

   * För tom logic apps i sökrutan anger du ”yammer” som filter. 
   Välj utlösaren som du vill under listan över utlösare. 

     ELLER

   * För befintliga logic apps: 
   
     * Under det sista steget där du vill lägga till en åtgärd, väljer **nytt steg**. 

       ELLER

     * Mellan stegen där du vill lägga till en åtgärd, flyttar du pekaren över pilen mellan stegen. 
     Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.
     
       I sökrutan anger du ”yammer” som filter. 
       Välj vilken åtgärd du önska under åtgärder.

1. Om du uppmanas att logga in på Yammer, logga in nu loggar nu så att du kan tillåta åtkomst.

1. Ange informationen som krävs för din valda utlösare eller åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/yammer/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
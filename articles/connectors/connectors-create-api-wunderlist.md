---
title: Anslut till Wunderlist från Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som övervakar och hanterar listor, uppgifter, påminnelser och mycket mer i din Wunderlist-konto med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 7226b59504c7112c039061ab0c184fe14f6e59d0
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918679"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Övervaka och hantera Wunderlist med hjälp av Azure Logic Apps

Med Azure Logic Apps och Wunderlist-anslutningsapp kan skapa du automatiserade uppgifter och arbetsflöden som övervakar och hanterar att göra-listor, uppgifter, påminnelser och mycket mer i din Wunderlist-konto, tillsammans med andra åtgärder, till exempel:

* Övervaka när nya aktiviteter skapas, när de förfaller eller påminnelser inträffa.
* Skapa och hantera listor, anteckningar, uppgifter, underaktiviteter och mycket mer.
* Ange påminnelser.
* Hämta listor, uppgifter, underaktiviteter, påminnelser, filer, anteckningar, kommentarer och mycket mer.

[Wunderlist](https://www.wunderlist.com/) är en tjänst som hjälper dig att planera, hantera och slutför ditt projekt, att göra-listor och uppgifter – på alla enheter, var som helst. Du kan använda utlösare som få svar från din Wunderlist-konto och se utdata som är tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med din Wunderlist-konto. Du kan också ha andra åtgärder som använder utdata från Wunderlist åtgärder. När nya aktiviteter förfaller, kan du till exempel skicka meddelanden med Slack-anslutningsprogrammet. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Ditt konto och användarens autentiseringsuppgifter för Wunderlist

   Dina autentiseringsuppgifter för tillåta din logikapp för att skapa en anslutning och komma åt ditt konto för Wunderlist.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt Yammer-konto. Du kommer igång med en Wunderlist-utlösare, [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Wunderlist-åtgärd, starta din logikapp med en annan utlösare, till exempel, **upprepning** utlösaren.

## <a name="connect-to-wunderlist"></a>Anslut till Wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. Välj en sökväg: 

   * För tom logic apps i sökrutan anger du ”wunderlist” som filter. 
   Välj utlösaren som du vill under listan över utlösare. 

     ELLER

   * För befintliga logic apps: 
   
     * Under det sista steget där du vill lägga till en åtgärd, väljer **nytt steg**. 

       ELLER

     * Mellan stegen där du vill lägga till en åtgärd, flyttar du pekaren över pilen mellan stegen. 
     Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.
     
       I sökrutan anger du ”wunderlist” som filter. 
       Välj vilken åtgärd du önska under åtgärder.

1. Om du uppmanas att logga in till Wunderlist, logga in nu så att du kan tillåta åtkomst.

1. Ange informationen som krävs för din valda utlösare eller åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/wunderlist/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
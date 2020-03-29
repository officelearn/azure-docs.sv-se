---
title: Ansluta till Yammer från Azure Logic Apps
description: Automatisera uppgifter och arbetsflöden som övervakar, publicerar och hanterar meddelanden, feeds med mera i Yammer med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 194c08d034d44ba0a4472b3b516fc45d1d262d28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789077"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Övervaka och hantera ditt Yammer-konto med hjälp av Azure Logic Apps

Med Azure Logic Apps och Yammer-anslutningen kan du skapa automatiserade uppgifter och arbetsflöden som övervakar och hanterar meddelanden, feeds med mera i yammerkontot, tillsammans med andra åtgärder, till exempel:

* Övervaka när nya meddelanden visas i följda feeds och grupper.
* Hämta meddelanden, grupper, nätverk, användarnas information med mera.
* Posta och gilla meddelanden.

Du kan använda utlösare som får svar från yammerkontot och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med yammerkontot. Du kan också låta andra åtgärder använda utdata från Yammer-åtgärder. När nya meddelanden till exempel visas i feeds eller grupper kan du dela dessa meddelanden med Slack-kopplingen. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Yammer-konto och användaruppgifter

   Dina autentiseringsuppgifter ger logikappen behörighet att skapa en anslutning och komma åt yammerkontot.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt Yammer-konto. Om du vill börja med en Yammer-utlösare [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Yammer-åtgärd startar du **Recurrence** logikappen med en annan utlösare, till exempel upprepningsutlösaren.

## <a name="connect-to-yammer"></a>Ansluta till Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. Välj en bana: 

   * För tomma logikappar anger du "yammer" som filter i sökrutan. 
   Markera den utlösare du vill använda under listan utlösare. 

     ELLER

   * För befintliga logikappar: 
   
     * Under det sista steget där du vill lägga till en åtgärd väljer du **Nytt steg**. 

       ELLER

     * Mellan de steg där du vill lägga till en åtgärd flyttar du pekaren över pilen mellan stegen. 
     Välj plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.
     
       Skriv "yammer" som filter i sökrutan. 
       Välj den åtgärd du vill använda under åtgärdslistan.

1. Om du uppmanas att logga in på Yammer loggar du in nu så att du kan tillåta åtkomst.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](/connectors/yammer/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
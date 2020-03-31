---
title: Ansluta till Wunderlist från Azure Logic Apps
description: Automatisera uppgifter och arbetsflöden som övervakar och hanterar listor, uppgifter, påminnelser med mera i ditt Wunderlist-konto med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5ac13595bd77238aaede5fa3bdc3a35ef69e8504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789128"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Övervaka och hantera Wunderlist med hjälp av Azure Logic Apps

Med Azure Logic Apps och Wunderlist-kopplingen kan du skapa automatiserade uppgifter och arbetsflöden som övervakar och hanterar todo-listor, uppgifter, påminnelser med mera i ditt Wunderlist-konto, tillsammans med andra åtgärder, till exempel:

* Övervaka när nya uppgifter skapas när uppgifter förfaller eller påminnelser inträffar.
* Skapa och hantera listor, anteckningar, uppgifter, underaktiviteter med mera.
* Ställ in påminnelser.
* Hämta listor, uppgifter, underaktiviteter, påminnelser, filer, anteckningar, kommentarer med mera.

[Wunderlist](https://www.wunderlist.com/) är en tjänst som hjälper dig att planera, hantera och slutföra dina projekt, att göra-listor och uppgifter – på alla enheter, var som helst. Du kan använda utlösare som får svar från ditt Wunderlist-konto och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med ditt Wunderlist-konto. Du kan också låta andra åtgärder använda utdata från Wunderlist-åtgärder. När nya uppgifter ska göras kan du till exempel skicka meddelanden med slackkopplingen. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ditt Wunderlist-konto och dina användaruppgifter

   Dina autentiseringsuppgifter ger logikappen behörighet att skapa en anslutning och komma åt ditt Wunderlist-konto.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt Yammer-konto. Om du vill börja med en Wunderlist-utlösare [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Wunderlist-åtgärd startar du logikappen med en annan utlösare, till exempel **utlösaren För återkommande.**

## <a name="connect-to-wunderlist"></a>Anslut till Wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. Välj en bana: 

   * För tomma logikappar anger du "wunderlist" som filter i sökrutan. 
   Markera den utlösare du vill använda under listan utlösare. 

     ELLER

   * För befintliga logikappar: 
   
     * Under det sista steget där du vill lägga till en åtgärd väljer du **Nytt steg**. 

       ELLER

     * Mellan de steg där du vill lägga till en åtgärd flyttar du pekaren över pilen mellan stegen. 
     Välj plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.
     
       I sökrutan anger du "wunderlist" som filter. 
       Välj den åtgärd du vill använda under åtgärdslistan.

1. Om du uppmanas att logga in på Wunderlist loggar du in nu så att du kan tillåta åtkomst.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](/connectors/wunderlist/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
---
title: Ansluta till Trello från Azure Logic Apps
description: Automatisera uppgifter och arbetsflöden som övervakar och hanterar listor, anslagstavlor och kort i Dina Trello-projekt med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5c4fcb9b4fea1a4d982b5cf665564599d371b7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789145"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Övervaka och hantera Trello med Azure Logic Apps

Med Azure Logic Apps och Trello-anslutningen kan du skapa automatiserade uppgifter och arbetsflöden som övervakar och hanterar dina Trello-listor, kort, anslagstavlor, gruppmedlemmar och så vidare, till exempel:

* Övervaka när nya kort läggs till i anslagstavlor och listor. 
* Skapa, hämta och hantera anslagstavlor, kort och listor.
* Lägg till kommentarer och medlemmar i korten.
* Listtavlor, anslagstavlor, kort på anslagstavlor, kortkommentarer, kortmedlemmar, gruppmedlemmar och team där du är medlem. 
* Hämta team.

Du kan använda utlösare som får svar från ditt Trello-konto och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med ditt Trello-konto. Du kan också låta andra åtgärder använda utdata från Trello-åtgärder. När ett nytt kort till exempel läggs till i anslagstavlan eller listan kan du skicka meddelanden med Slack-kopplingen. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Dina Trello-konto- och användaruppgifter

  Dina autentiseringsuppgifter ger logikappen behörighet att skapa en anslutning och komma åt ditt Trello-konto.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt Trello-konto. Om du vill börja med en Trello-utlösare [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Trello-åtgärd startar du **Recurrence** logikappen med en utlösare, till exempel upprepningsutlösaren.

## <a name="connect-to-trello"></a>Anslut till Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. För tomma logikappar anger du "trello" som filter i sökrutan. Markera den utlösare du vill använda under listan utlösare. 

   ELLER

   För befintliga logikappar väljer du **Nytt steg**under det sista steget där du vill lägga till en åtgärd . 
   Skriv "trello" som filter i sökrutan. 
   Välj den åtgärd du vill använda under åtgärdslistan.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. Om du uppmanas att logga in på Trello godkänner du åtkomst till logikappen och loggar in.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](/connectors/trello/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
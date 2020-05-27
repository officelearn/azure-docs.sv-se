---
title: Anslut till Trello från Azure Logic Apps
description: Automatisera aktiviteter och arbets flöden som övervakar och hanterar listor, tavlor och kort i dina Trello-projekt med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: d1426bbe2064067b26f53a8f8e5d4aa842ede8c0
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829706"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Övervaka och hantera Trello med Azure Logic Apps

Med Azure Logic Apps och Trello-anslutningen kan du skapa automatiserade uppgifter och arbets flöden som övervakar och hanterar dina Trello-listor, kort, kort, team medlemmar osv. till exempel:

* Övervaka när nya kort läggs till i kort och listor. 
* Skapa, hämta och hantera kort, kort och listor.
* Lägg till kommentarer och medlemmar till kort.
* Lista tavlor, kort etiketter, kort på kort, kort kommentarer, kort medlemmar, team medlemmar och team där du är medlem. 
* Hämta team.

Du kan använda utlösare som får svar från ditt Trello-konto och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med ditt Trello-konto. Du kan också använda de andra åtgärderna för att använda utdata från Trello-åtgärder. Till exempel när ett nytt kort läggs till i tavlan eller listan, kan du skicka meddelanden med slack-anslutningen. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ditt Trello-konto och användarautentiseringsuppgifter

  Dina autentiseringsuppgifter ger din Logic-app behörighet att skapa en anslutning och komma åt ditt Trello-konto.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt ditt Trello-konto. Börja med en Trello-utlösare genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Trello-åtgärd startar du din Logic-app med en utlösare, till exempel utlösaren **upprepning** .

## <a name="connect-to-trello"></a>Anslut till Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. För tomma Logi Kap par anger du "Trello" som filter i rutan Sök. Välj den utlösare som du vill använda under listan utlösare. 

   \- eller -

   För befintliga Logic Apps, under det sista steget där du vill lägga till en åtgärd, väljer du **nytt steg**. 
   I rutan Sök anger du "Trello" som filter. 
   Under listan åtgärder väljer du den åtgärd som du vill använda.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. 
   Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Om du uppmanas att logga in på Trello ska du godkänna åtkomsten för din Logic app och logga in.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/trello/).

## <a name="get-support"></a>Få support

* För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
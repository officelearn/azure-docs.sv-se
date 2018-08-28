---
title: Anslut till Trello från Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som övervakar och hanterar listor, tavlor och kort i Trello-projekt med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 4ae8d3dff108f14844c31d7b9d0b0871326832a3
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046157"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Övervaka och hantera Trello med Azure Logic Apps

Med Azure Logic Apps och Trello-anslutningsapp kan skapa du automatiserade uppgifter och arbetsflöden som övervakar och hanterar din Trello listor, kort, anslagstavlor, gruppmedlemmar och så vidare, till exempel:

* Övervakare för när nya kort läggs till: er och listor. 
* Skapa, hämta och hantera tavlor, kort och listor.
* Lägga till kommentarer och medlemmar i kort.
* Lista tavlor, tavla etiketter, kort på tavlor, kort kommentarer, kortmedlemmar, gruppmedlemmar och team där du är medlem. 
* Hämta team.

Du kan använda utlösare som får svar från Trello-kontot och göra utdata som är tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med Trello-konto. Du kan också ha andra åtgärder som använder utdata från Trello åtgärder. När ett nytt kort läggs till tavlan eller lista, kan du till exempel skicka meddelanden med Slack-anslutningsprogrammet. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Ditt konto och användarens autentiseringsuppgifter för Trello

  Dina autentiseringsuppgifter för tillåta din logikapp för att skapa en anslutning och komma åt ditt konto i Trello.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt konto i Trello. Du kommer igång med en Trello-utlösare, [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Trello-åtgärd, börja din logikapp med en utlösare, till exempel, **upprepning** utlösaren.

## <a name="connect-to-trello"></a>Anslut till Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. För tom logic apps i sökrutan anger du ”trello” som filter. Välj utlösaren som du vill under listan över utlösare. 

   ELLER

   För befintliga logikappar under det sista steget där du vill lägga till en åtgärd, Välj **nytt steg**. 
   I sökrutan anger du ”trello” som filter. 
   Välj vilken åtgärd du önska under åtgärder.

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

1. Om du uppmanas att logga in på Trello godkänna åtkomst för din logikapp och logga in.

1. Ange informationen som krävs för din valda utlösare eller åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/trello/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
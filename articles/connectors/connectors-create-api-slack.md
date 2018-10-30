---
title: Anslut till Slack från Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som övervakar filer och hantera kanaler, grupper och meddelanden i din Slack-konto med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 675e37120b06af3add58b564495f22875647a0fa
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230360"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Övervaka och hantera Slack med Azure Logic Apps

Du kan skapa automatiserade uppgifter och arbetsflöden som övervakar din Slack-filer och hanterar din Slack-kanaler, meddelanden, grupper och så vidare, till exempel med Azure Logic Apps och Slack connector:

* Övervakare för när nya filer skapas.
* Skapa, visa, och delta i kanaler 
* Skicka meddelanden.
* Skapa grupper och Ställ in stör ej.

Du kan använda utlösare som få svar från din Slack-konto och se utdata som är tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med ditt Slack-konto. Du kan också ha andra åtgärder som använder utdata från Slack åtgärder. När en ny fil skapas, kan du skicka e-postmeddelande med Office 365 Outlook-anslutningen. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Din [Slack](https://slack.com/) autentiseringsuppgifter för kontot och användare

  Dina autentiseringsuppgifter för tillåta din logikapp för att skapa en anslutning och komma åt ditt Slack-konto.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt Slack-konto. Du kommer igång med en Slack-utlösare, [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Slack åtgärd, starta din logikapp med en utlösare, till exempel en Slack utlösare eller en annan utlösare, som den **upprepning** utlösaren.

## <a name="connect-to-slack"></a>Anslut till Slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. För tom logic apps i sökrutan anger du ”slack” som filter. Välj utlösaren som du vill under listan över utlösare. 

   ELLER

   För befintliga logikappar under det sista steget där du vill lägga till en åtgärd, Välj **nytt steg**. 
   I sökrutan anger du ”slack” som filter. 
   Välj vilken åtgärd du önska under åtgärder.

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

1. Om du uppmanas att logga in på Slack, kan du logga in på din Slack-arbetsyta. 

   ![Logga in på Slack arbetsyta](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Tillåter åtkomst för din logikapp.

   ![Bevilja åtkomst till Slack](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Ange informationen som krävs för din valda utlösare eller åtgärd. Lägga till fler åtgärder för att fortsätta att skapa logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/slack/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)

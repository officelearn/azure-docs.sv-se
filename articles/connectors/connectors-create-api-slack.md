---
title: Ansluta till Slack från Azure Logic Apps
description: Automatisera uppgifter och arbetsflöden som övervakar filer och hanterar kanaler, grupper och meddelanden i ditt Slack-konto med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5f61009ee7b43be618e37acb4a783a54dbf11e55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789179"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Övervaka och hantera Slack med Azure Logic Apps

Med Azure Logic Apps och Slack-anslutningsappen kan du skapa automatiserade uppgifter och arbetsflöden som övervakar dina Slack-filer och hanterar dina Slack-kanaler, meddelanden, grupper och så vidare, till exempel:

* Övervaka när nya filer skapas.
* Skapa, lista och gå med kanaler 
* Skicka meddelanden.
* Skapa grupper och ange stör ej.

Du kan använda utlösare som får svar från ditt Slack-konto och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med ditt Slack-konto. Du kan också låta andra åtgärder använda utdata från Slack-åtgärder. När en ny fil skapas kan du till exempel skicka e-post med Office 365 Outlook-kopplingen. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ditt [Slack-konto](https://slack.com/) och dina användaruppgifter

  Dina autentiseringsuppgifter ger logikappen behörighet att skapa en anslutning och komma åt ditt Slack-konto.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt Slack-konto. Om du vill börja med en Slack-utlösare [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Slack-åtgärd startar du logikappen med en utlösare, till exempel en Slack-utlösare eller en annan utlösare, till exempel **utlösaren För återkommande.**

## <a name="connect-to-slack"></a>Anslut till Slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. För tomma logikappar anger du "slack" som filter i sökrutan. Markera den utlösare du vill använda under listan utlösare. 

   ELLER

   För befintliga logikappar väljer du **Nytt steg**under det sista steget där du vill lägga till en åtgärd . 
   Skriv "slack" som filter i sökrutan. 
   Välj den åtgärd du vill använda under åtgärdslistan.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. Om du uppmanas att logga in på Slack loggar du in på din Slack-arbetsyta. 

   ![Logga in på Slack-arbetsytan](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Auktorisera åtkomst för din logikapp.

   ![Auktorisera åtkomst till Slack](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Ange nödvändiga uppgifter för den valda utlösaren eller åtgärden. Om du vill fortsätta att skapa logikappens arbetsflöde lägger du till fler åtgärder.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](/connectors/slack/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)

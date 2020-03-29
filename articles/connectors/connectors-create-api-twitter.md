---
title: Ansluta till Twitter från Azure Logic Apps
description: Automatisera uppgifter och arbetsflöden som övervakar och hanterar tweets, samt få data om följare, dina följda användare, andra användare, tidslinjer med mera från ditt Twitter-konto med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 8ffd0fd558cf759fadd912de9dff4acf49d9659f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789094"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Övervaka och hantera Twitter med hjälp av Azure Logic Apps

Med Azure Logic Apps och Twitter-anslutningsappen kan du skapa automatiserade uppgifter och arbetsflöden som övervakar och hanterar data som du bryr dig om på Twitter, till exempel tweets, följare, användare och följda användare, tidslinjer med mera, tillsammans med andra åtgärder, till exempel:

* Övervaka, publicera och söka tweets.
* Hämta data som följare, följda användare, tidslinjer med mera.

Du kan använda utlösare som får svar från ditt Twitter-konto och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med ditt Twitter-konto. Du kan också låta andra åtgärder använda utdata från Twitter-åtgärder. När en ny tweet med en viss hashtag visas kan du till exempel skicka meddelanden med Slack-kopplingen. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ditt Twitter-konto och dina användaruppgifter

   Dina autentiseringsuppgifter ger logikappen behörighet att skapa en anslutning och komma åt ditt Twitter-konto.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt Twitter-konto. Om du vill börja med en Twitter-utlösare [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Twitter-åtgärd startar du logikappen med en annan utlösare, till exempel **utlösaren För återkommande.**

## <a name="connect-to-twitter"></a>Ansluta till Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. Välj en bana: 

   * För tomma logikappar anger du "Twitter" som filter i sökrutan. 
   Markera den utlösare du vill använda under listan utlösare. 

     ELLER

   * För befintliga logikappar: 
   
     * Under det sista steget där du vill lägga till en åtgärd väljer du **Nytt steg**. 

       ELLER

     * Mellan de steg där du vill lägga till en åtgärd flyttar du pekaren över pilen mellan stegen. 
     Välj plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.
     
       I sökrutan anger du "Twitter" som filter. 
       Välj den åtgärd du vill använda under åtgärdslistan.

1. Om du uppmanas att logga in på Twitter loggar du in nu så att du kan auktorisera åtkomst för logikappen.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa logikappens arbetsflöde.

## <a name="examples"></a>Exempel

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Twitter trigger: När en ny tweet publiceras

Den här utlösaren startar ett logikapparbetsflöde när utlösaren identifierar en ny tweet, till exempel med hashtaggen, #Seattle. Så till exempel när dessa tweets hittas kan du lägga till en fil med tweetsinnehållet i lagring, till exempel ett Dropbox-konto med dropbox-kontakten. 

Du kan också inkludera ett villkor som kvalificerade tweets måste komma från användare med minst ett angivet antal följare.

**Företagsexempel**: Du kan använda den här utlösaren för att övervaka tweets om ditt företag och ladda upp tweetsinnehållet till en SQL-databas.

### <a name="twitter-action-post-a-tweet"></a>Twitter åtgärd: Post en tweet

Den här åtgärden publicerar en tweet, men du kan ställa in åtgärden så att tweeten innehåller innehållet från tweets som hittades av den tidigare beskrivna utlösaren. 

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](/connectors/twitterconnector/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)

---
title: Anslut till Twitter från Azure Logic Apps
description: Automatisera aktiviteter och arbets flöden som övervakar och hanterar tweets, plus hämta data om följare, dina användare, andra användare, tids linjer och annat från ditt Twitter-konto genom att använda Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: f2db6d614c3c12cb1be87724e79d79a16769d6b8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829604"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Övervaka och hantera Twitter genom att använda Azure Logic Apps

Med Azure Logic Apps och Twitter-anslutningen kan du skapa automatiserade uppgifter och arbets flöden som övervakar och hanterar data som du bryr dig om i Twitter, till exempel tweets, följare, användare och följda användare, tids linjer och mer, tillsammans med andra åtgärder, till exempel:

* Övervaka, posta och Sök tweets.
* Hämta data som följare, följda användare, tids linjer och mycket annat.

Du kan använda utlösare som får svar från ditt Twitter-konto och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med ditt Twitter-konto. Du kan också använda andra åtgärder för att använda utdata från Twitter-åtgärder. Till exempel, när en ny tweet med en angiven hashtagg visas, kan du skicka meddelanden med slack-kopplingen. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ditt Twitter-konto och användarautentiseringsuppgifter

   Dina autentiseringsuppgifter ger din Logic-app behörighet att skapa en anslutning och komma åt ditt Twitter-konto.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt ditt Twitter-konto. Börja med en Twitter-utlösare genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Twitter-åtgärd startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

## <a name="connect-to-twitter"></a>Ansluta till Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Välj en sökväg: 

   * För tomma Logi Kap par anger du "Twitter" som filter i rutan Sök. 
   Välj den utlösare som du vill använda under listan utlösare. 

     \- eller -

   * För befintliga Logic Apps: 
   
     * Under det sista steget där du vill lägga till en åtgärd väljer du **nytt steg**. 

       \- eller -

     * Mellan stegen där du vill lägga till en åtgärd flyttar du pekaren över pilen mellan stegen. 
     Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.
     
       I rutan Sök anger du "Twitter" som filter. 
       Under listan åtgärder väljer du den åtgärd som du vill använda.

1. Om du uppmanas att logga in på Twitter loggar du in nu så att du kan ge åtkomst till din Logic app.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="examples"></a>Exempel

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Twitter-utlösare: när en ny tweet publiceras

Den här utlösaren startar ett Logic app-arbetsflöde när utlösaren identifierar en ny tweet, till exempel med hashtagg, #Seattle. Så till exempel när dessa tweets hittas kan du lägga till en fil med tweets-innehållet i lagringen, till exempel ett Dropbox-konto med hjälp av Dropbox-kopplingen. 

Alternativt kan du lägga till ett villkor som berättigade tweets måste komma från användare med minst ett angivet antal följare.

**Enterprise-exempel**: du kan använda den här utlösaren för att övervaka Tweets om ditt företag och ladda upp tweets-innehållet till en SQL-databas.

### <a name="twitter-action-post-a-tweet"></a>Twitter-åtgärd: publicera en tweet

Den här åtgärden publicerar en tweet, men du kan ställa in åtgärden så att tweeten innehåller innehållet från Tweets som påträffats av den tidigare beskrivna utlösaren. 

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/twitterconnector/).

## <a name="get-support"></a>Få support

* För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)

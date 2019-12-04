---
title: Ansluta till Yammer från Azure Logic Apps
description: Automatisera aktiviteter och arbets flöden som övervakar, skickar och hanterar meddelanden, feeds och mer i Yammer med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 194c08d034d44ba0a4472b3b516fc45d1d262d28
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789077"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Övervaka och hantera ditt Yammer-konto genom att använda Azure Logic Apps

Med Azure Logic Apps och Yammer-anslutningen kan du skapa automatiserade uppgifter och arbets flöden som övervakar och hanterar meddelanden, feeds och mer i ditt Yammer-konto, tillsammans med andra åtgärder, till exempel:

* Övervaka när nya meddelanden visas i följda flöden och grupper.
* Hämta meddelanden, grupper, nätverk, användares information med mera.
* Publicera och gilla meddelanden.

Du kan använda utlösare som får svar från ditt Yammer-konto och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med ditt Yammer-konto. Du kan också använda andra åtgärder för att använda utdata från Yammer-åtgärder. Till exempel när nya meddelanden visas i matningar eller grupper kan du dela dessa meddelanden med slack-kopplingen. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ditt Yammer-konto och användarautentiseringsuppgifter

   Dina autentiseringsuppgifter ger din Logic-app behörighet att skapa en anslutning och komma åt ditt Yammer-konto.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt ditt Yammer-konto. [Skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md)för att starta med en Yammer-utlösare. Om du vill använda en Yammer-åtgärd startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

## <a name="connect-to-yammer"></a>Anslut till Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Välj en sökväg: 

   * För tomma Logi Kap par anger du "Yammer" som filter i rutan Sök. 
   Välj den utlösare som du vill använda under listan utlösare. 

     ELLER

   * För befintliga Logic Apps: 
   
     * Under det sista steget där du vill lägga till en åtgärd väljer du **nytt steg**. 

       ELLER

     * Mellan stegen där du vill lägga till en åtgärd flyttar du pekaren över pilen mellan stegen. 
     Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.
     
       I rutan Sök anger du "Yammer" som filter. 
       Under listan åtgärder väljer du den åtgärd som du vill använda.

1. Om du uppmanas att logga in på Yammer loggar du in nu så att du kan tillåta åtkomst.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/yammer/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
---
title: Anslut till Wunderlist från Azure Logic Apps
description: Automatisera uppgifter och arbets flöden som övervakar och hanterar listor, uppgifter, påminnelser och mer i ditt Wunderlist-konto med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5ac13595bd77238aaede5fa3bdc3a35ef69e8504
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789128"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Övervaka och hantera Wunderlist med hjälp av Azure Logic Apps

Med Azure Logic Apps och Wunderlist-kopplingen kan du skapa automatiserade uppgifter och arbets flöden som övervakar och hanterar att göra-listor, uppgifter, påminnelser och mer i ditt Wunderlist-konto, tillsammans med andra åtgärder, till exempel:

* Övervaka när nya aktiviteter skapas, när aktiviteter förfaller eller påminnelser inträffar.
* Skapa och hantera listor, anteckningar, uppgifter, under aktiviteter med mera.
* Ställ in påminnelser.
* Hämta listor, uppgifter, under aktiviteter, påminnelser, filer, anteckningar, kommentarer och mycket annat.

[Wunderlist](https://www.wunderlist.com/) är en tjänst som hjälper dig att planera, hantera och slutföra dina projekt, göra listor och uppgifter – på valfri enhet, var som helst. Du kan använda utlösare som får svar från ditt Wunderlist-konto och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför uppgifter med ditt Wunderlist-konto. Du kan också använda andra åtgärder för att använda utdata från Wunderlist-åtgärder. Till exempel när nya aktiviteter förfaller kan du skicka meddelanden med slack-kopplingen. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ditt Wunderlist-konto och användarautentiseringsuppgifter

   Dina autentiseringsuppgifter ger din Logic-app behörighet att skapa en anslutning och komma åt ditt Wunderlist-konto.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt ditt Yammer-konto. [Skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md)för att börja med en Wunderlist-utlösare. Om du vill använda en Wunderlist-åtgärd startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

## <a name="connect-to-wunderlist"></a>Anslut till Wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Välj en sökväg: 

   * För tomma Logi Kap par anger du "Wunderlist" som filter i rutan Sök. 
   Välj den utlösare som du vill använda under listan utlösare. 

     ELLER

   * För befintliga Logic Apps: 
   
     * Under det sista steget där du vill lägga till en åtgärd väljer du **nytt steg**. 

       ELLER

     * Mellan stegen där du vill lägga till en åtgärd flyttar du pekaren över pilen mellan stegen. 
     Välj plus tecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.
     
       I rutan Sök anger du "Wunderlist" som filter. 
       Under listan åtgärder väljer du den åtgärd som du vill använda.

1. Om du uppmanas att logga in till Wunderlist loggar du in nu så att du kan tillåta åtkomst.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/wunderlist/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
---
title: Ansluta till Twilio från Azure Logic Apps
description: Automatisera uppgifter och arbetsflöden som hanterar globala SMS-, MMS- och IP-meddelanden via ditt Twilio-konto med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: e5b218efd9c8cfaad99d76d8118d181390a977c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789111"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Hantera meddelanden i Twilio med Azure Logic Apps

Med Azure Logic Apps och Twilio-anslutningen kan du skapa automatiserade uppgifter och arbetsflöden som hämtar, skickar och listmeddelanden i Twilio, som innehåller globala SMS-, MMS- och IP-meddelanden. Du kan använda dessa åtgärder för att utföra uppgifter med ditt Twilio-konto. Du kan också låta andra åtgärder använda utdata från Twilio-åtgärder. När ett nytt meddelande till exempel anländer kan du skicka meddelandeinnehållet med Slack-kopplingen. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Från [Twilio](https://www.twilio.com/): 

  * Ditt Twilio-konto-ID och [autentiseringstoken](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), som du hittar på instrumentpanelen i Twilio

    Dina autentiseringsuppgifter ger logikappen behörighet att skapa en anslutning och komma åt ditt Twilio-konto från logikappen. 
    Om du använder ett testkonto för Twilio kan du bara skicka SMS till *verifierade* telefonnummer.

  * Ett verifierat Twilio-telefonnummer som kan skicka SMS

  * Ett verifierat Twilio-telefonnummer som kan ta emot SMS

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt Twilio-konto. Om du vill använda en Twilio-åtgärd startar du **Recurrence** logikappen med en annan utlösare, till exempel upprepningsutlösaren.

## <a name="connect-to-twilio"></a>Anslut till Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. Välj en bana: 

     * Under det sista steget där du vill lägga till en åtgärd väljer du **Nytt steg**. 

       ELLER

     * Mellan de steg där du vill lägga till en åtgärd flyttar du pekaren över pilen mellan stegen. 
     Välj plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.
     
       Skriv "twilio" som filter i sökrutan. 
       Välj den åtgärd du vill använda under åtgärdslistan.

1. Ange nödvändiga uppgifter för din anslutning och välj sedan **Skapa:**

   * Namnet som ska användas för anslutningen
   * Ditt Twilio-konto-ID 
   * Din Twilio-åtkomst (autentisering) token

1. Ange nödvändig information för den valda åtgärden och fortsätt att skapa logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](/connectors/twilio/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
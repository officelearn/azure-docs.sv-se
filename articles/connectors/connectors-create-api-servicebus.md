---
title: Skicka och ta emot meddelanden med Azure Service Bus-Azure Logic Apps | Microsoft Docs
description: Konfigurera meddelanden i företags molnet med Azure Service Bus i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 944bac44c1fc6504dfe1a93df5760ccf4ee46fa0
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982211"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>Exchange-meddelanden i molnet med Azure Service Bus och Azure Logic Apps

Med Azure Logic Apps och Azure Service Bus-anslutningsprogrammet kan du skapa automatiserade uppgifter och arbetsflöden som överför data, till exempel försäljnings- och inköpsorder, journaler och inventeringsförflyttningar mellan program för din organisation. Anslutningen övervakar, skickar och hanterar inte bara meddelanden, utan utför även åtgärder med köer, sessioner, ämnen, prenumerationer och så vidare, till exempel:

* Övervaka när meddelanden tas emot (kompletteras automatiskt) eller tas emot (Peek-lock) i köer, ämnen och ämnes prenumerationer. 
* Skicka meddelanden.
* Skapa och ta bort ämnes prenumerationer.
* Hantera meddelanden i köer och ämnes prenumerationer, till exempel hämta, Hämta, slutföra, skjuta upp, överge och obeställbara meddelanden.
* Förnya lås på meddelanden och sessioner i köer och ämnes prenumerationer.
* Stäng sessioner i köer och ämnen.

Du kan använda utlösare som får svar från Service Bus och göra utdata tillgängliga för andra åtgärder i dina Logic Apps. Du kan också använda andra åtgärder för att använda utdata från Service Bus åtgärder. Om du är nybörjare på Service Bus och Logic Apps kan du läsa mer i [Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) och [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* En Service Bus namnrymd och meddelande enhet, till exempel en kö. Om du inte har dessa objekt kan du läsa om hur du [skapar Service Bus namnrymd och en kö](../service-bus-messaging/service-bus-create-namespace-portal.md). 

  Dessa objekt måste finnas i samma Azure-prenumeration som dina Logic Apps som använder dessa objekt.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill använda Service Bus. Din Logic app måste finnas i samma Azure-prenumeration som din Service Bus. Börja med en Service Bus utlösare genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Service Bus åtgärd startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Kontrol lera behörigheter

Bekräfta att din Logic app har behörighet att komma åt din Service Bus-namnrymd. 

1. Logga in på [Azure Portal](https://portal.azure.com). 

2. Gå till din Service Bus *namnrum*. På sidan namn område under **Inställningar**väljer du **principer för delad åtkomst**. Under **anspråk**kontrollerar du att du har behörighet att **Hantera** för det namn området

   ![Hantera behörigheter för din Service Bus-namnrymd](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Hämta anslutnings strängen för Service Bus namn området. Du behöver den här strängen när du anger din anslutnings information i din Logic app.

   1. Välj **RootManageSharedAccessKey**. 
   
   1. Klicka på knappen Kopiera bredvid din primära anslutnings sträng. Spara anslutnings strängen för senare användning.

      ![Kopiera Service Bus namn områdets anslutnings sträng](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Om du vill kontrol lera att anslutnings strängen är kopplad till din Service Bus-namnrymd eller en meddelande enhet, till exempel en kö, söker du efter `EntityPath`  parameterns anslutnings sträng. Om du hittar den här parametern är anslutnings strängen för en speciell entitet och är inte rätt sträng som ska användas med din Logic app.

## <a name="add-trigger-or-action"></a>Lägg till utlösare eller åtgärd

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Om du vill lägga till en utlösare i en tom Logic-app skriver du "Azure Service Bus" som filter i sökrutan. Välj den utlösare som du vill använda under listan utlösare. 

   Om du till exempel vill utlösa din Logi Kap par när ett nytt objekt skickas till en Service Bus kö väljer du den här utlösaren: **När ett meddelande tas emot i en kö (komplettera automatiskt)**

   ![Välj Service Bus utlösare](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Vissa utlösare kan returnera ett eller meddelanden, till exempel utlösaren **när ett eller flera meddelanden anländer i en kö (komplettera automatiskt)** . När utlösarna utlöses, returnerar de mellan ett och antalet meddelanden som anges av utlösarens egenskap för **maximalt antal meddelanden** .

   *Alla Service Bus-utlösare är långa*avsökningar, vilket innebär att när utlösaren utlöses, bearbetar utlösaren alla meddelanden och väntar sedan 30 sekunder för att fler meddelanden ska visas i kön eller ämnes prenumerationen. 
   Om inga meddelanden visas på 30 sekunder hoppas utlösarens körning över. 
   Annars fortsätter utlösaren att läsa meddelanden tills kön eller ämnes prenumerationen är tom. Nästa Utlös ande avsökning baseras på upprepnings intervallet som anges i utlösarens egenskaper.

1. Följ dessa steg om du vill lägga till en *åtgärd* i en befintlig Logic-app: 

   1. Under det sista steget där du vill lägga till en åtgärd väljer du **nytt steg**. 

      Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. 
      Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

   1. I rutan Sök anger du "Azure Service Bus" som filter. 
   Under listan åtgärder väljer du den åtgärd som du vill använda. 
 
      Välj till exempel den här åtgärden: **Skicka meddelande**

      ![Välj Service Bus åtgärd](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Om du ansluter din Logic app till din Service Bus-namnrymd för första gången, uppmanas du nu att ange din anslutnings information i Logic Apps designer. 

   1. Ange ett namn för anslutningen och välj Service Bus namn område.

      ![Skapa Service Bus anslutning, del 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Om du vill ange anslutnings strängen manuellt i stället väljer du **Ange anslutnings information manuellt**. 
      Om du inte har anslutnings strängen kan du läsa om [hur du hittar anslutnings strängen](#permissions-connection-string).

   1. Välj din Service Bus-princip och välj sedan **skapa**.

      ![Skapa Service Bus anslutning, del 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. I det här exemplet väljer du den meddelande enhet som du vill ha, till exempel en kö eller ett ämne. I det här exemplet väljer du din Service Bus-kö. 
   
   ![Välj Service Bus kö](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. Ange nödvändig information för utlösaren eller åtgärden. I det här exemplet följer du de relevanta stegen för utlösaren eller åtgärden: 

   * **För exempel**utlösaren: Ange avsöknings intervall och frekvens för att kontrol lera kön.

     ![Konfigurera avsöknings intervall](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     När du är klar fortsätter du att skapa ditt Logic Apps-arbetsflöde genom att lägga till de åtgärder du vill ha. Du kan till exempel lägga till en åtgärd som skickar e-post när ett nytt meddelande tas emot.
     När utlösaren kontrollerar kön och hittar ett nytt meddelande, kör din Logic-app dina valda åtgärder för det hittade meddelandet.

   * **För exempel åtgärden**: Ange meddelande innehållet och annan information. 

     ![Tillhandahåll meddelande innehåll och information](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     När du är klar fortsätter du att skapa ditt Logic app-arbetsflöde genom att lägga till andra åtgärder som du vill ha. Du kan till exempel lägga till en åtgärd som skickar e-post som bekräftar att meddelandet har skickats.

1. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

## <a name="connector-reference"></a>Referens för anslutningsapp

Service Bus-anslutningen kan spara upp till 1 500 unika sessioner i taget från en Service Bus till kopplingens cacheminne. Om antalet sessioner överskrider den här gränsen tas gamla sessioner bort från cachen. För annan teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI (tidigare Swagger), granskar du kopplingens [referens sida](/connectors/servicebus/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)

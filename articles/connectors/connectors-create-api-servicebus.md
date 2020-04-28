---
title: Exchange-meddelanden med Azure Service Bus
description: Skapa automatiserade uppgifter och arbets flöden som skickar och tar emot meddelanden med hjälp av Azure Service Bus i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 1b38b8508dbe17d42bf191149410f5db638cf834
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76261627"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Exchange-meddelanden i molnet med hjälp av Azure Logic Apps och Azure Service Bus

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) Connector kan du skapa automatiserade uppgifter och arbets flöden som överför data, till exempel försäljnings-och inköps order, journaler och inventerings förflyttningar mellan program för din organisation. Anslutningen övervakar, skickar och hanterar inte bara meddelanden, utan utför även åtgärder med köer, sessioner, ämnen, prenumerationer och så vidare, till exempel:

* Övervaka när meddelanden tas emot (kompletteras automatiskt) eller tas emot (Peek-lock) i köer, ämnen och ämnes prenumerationer.
* Skicka meddelanden.
* Skapa och ta bort ämnes prenumerationer.
* Hantera meddelanden i köer och ämnes prenumerationer, till exempel hämta, Hämta, slutföra, skjuta upp, överge och obeställbara meddelanden.
* Förnya lås på meddelanden och sessioner i köer och ämnes prenumerationer.
* Stäng sessioner i köer och ämnen.

Du kan använda utlösare som får svar från Service Bus och göra utdata tillgängliga för andra åtgärder i dina Logic Apps. Du kan också använda andra åtgärder för att använda utdata från Service Bus åtgärder. Om du är nybörjare på Service Bus och Logic Apps kan du läsa mer i [Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) och [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En Service Bus namnrymd och meddelande enhet, till exempel en kö. Dessa objekt och din Logic app måste använda samma Azure-prenumeration. Om du inte har dessa objekt kan du läsa om hur du [skapar Service Bus namnrymd och en kö](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic-app där du använder Service Bus namnrymd och meddelande enhet. Din Logic app och Service Bus måste använda samma Azure-prenumeration. [Skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md)för att starta arbets flödet med en Service Bus utlösare. Om du vill använda en Service Bus-åtgärd i arbets flödet startar du din Logic-app med en annan utlösare, till exempel [utlösaren upprepning](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Kontrol lera behörigheter

Bekräfta att din Logic app har behörighet att komma åt din Service Bus-namnrymd.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till din Service Bus *namnrum*. På sidan namn område under **Inställningar**väljer du **principer för delad åtkomst**. Under **anspråk**kontrollerar du att du har behörighet att **Hantera** för det namn området.

   ![Hantera behörigheter för Service Bus namnrymd](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Hämta anslutnings strängen för Service Bus namn området. Du behöver den här strängen när du anger anslutnings informationen i din Logic app.

   1. I fönstret **principer för delad åtkomst** väljer du **RootManageSharedAccessKey**.
   
   1. Välj knappen Kopiera bredvid din primära anslutnings sträng. Spara anslutnings strängen för senare användning.

      ![Kopiera Service Bus namn områdets anslutnings sträng](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Om du vill kontrol lera att anslutnings strängen är kopplad till din Service Bus-namnrymd eller en meddelande enhet, till exempel en kö, söker du efter `EntityPath`  parameterns anslutnings sträng. Om du hittar den här parametern är anslutnings strängen för en speciell entitet och är inte rätt sträng som ska användas med din Logic app.

## <a name="add-service-bus-trigger"></a>Lägg till Service Bus-utlösare

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din tomma Logic-app i Logic App Designer.

1. I rutan Sök anger du "Azure Service Bus" som filter. Välj den utlösare som du vill använda från listan utlösare.

   Om du till exempel vill utlösa din Logi Kap par när ett nytt objekt skickas till en Service Bus-kö, markerar du utlösaren **när ett meddelande tas emot i en kö (automatisk komplettering)** .

   ![Välj Service Bus utlösare](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Alla Service Bus-utlösare är *tids avsöknings* utlösare. Den här beskrivningen innebär att utlösaren bearbetar alla meddelanden när utlösaren utlöses och väntar sedan 30 sekunder på att fler meddelanden ska visas i kön eller ämnes prenumerationen. Om inga meddelanden visas på 30 sekunder hoppas utlösarens körning över. Annars fortsätter utlösaren att läsa meddelanden tills kön eller ämnes prenumerationen är tom. Nästa Utlös ande avsökning baseras på upprepnings intervallet som anges i utlösarens egenskaper.

   Vissa utlösare, till exempel **när ett eller flera meddelanden tas emot i en kö (automatisk komplettering)** , kan returnera ett eller flera meddelanden. När utlösarna utlöses, returnerar de mellan ett och antalet meddelanden som anges av utlösarens egenskap för **maximalt antal meddelanden** .

1. Om utlösaren ansluter till Service Bus namn området för första gången följer du de här stegen när du uppmanas att ange anslutnings information i Logic Apps designer.

   1. Ange ett namn för anslutningen och välj Service Bus namn område.

      ![Skapa Service Bus anslutning, del 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Om du vill ange anslutnings strängen manuellt i stället väljer du **Ange anslutnings information manuellt**. Om du inte har anslutnings strängen kan du läsa om [hur du hittar anslutnings strängen](#permissions-connection-string).

   1. Välj din Service Bus-princip och välj **skapa**.

      ![Skapa Service Bus anslutning, del 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Välj den meddelande enhet som du vill använda, till exempel en kö eller ett ämne. I det här exemplet väljer du din Service Bus-kö.
   
      ![Välj Service Bus kö](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Ange den information som krävs för den valda utlösaren. Om du vill lägga till andra tillgängliga egenskaper till åtgärden öppnar du listan **Lägg till ny parameter** och väljer de egenskaper som du vill använda.

   I det här exemplets utlösare väljer du avsöknings intervall och frekvens för att kontrol lera kön.

   ![Konfigurera avsöknings intervall](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Mer information om tillgängliga utlösare och egenskaper finns på kopplingens [referens sida](/connectors/servicebus/).

1. Fortsätt att skapa din Logic app genom att lägga till de åtgärder som du vill ha.

   Du kan till exempel lägga till en åtgärd som skickar e-post när ett nytt meddelande tas emot. När utlösaren kontrollerar kön och hittar ett nytt meddelande, kör din Logic-app dina valda åtgärder för det hittade meddelandet.

## <a name="add-service-bus-action"></a>Lägg till Service Bus åtgärd

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer.

1. Under steget där du vill lägga till en åtgärd väljer du **nytt steg**.

   Eller om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet (**+**) som visas och välj **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**i rutan Sök anger du "Azure Service Bus" som filter. I listan åtgärder väljer du den åtgärd som du vill använda. 

   I det här exemplet väljer du åtgärden **Skicka meddelande** .

   ![Välj Service Bus åtgärd](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Om åtgärden ansluter till Service Bus namn området för första gången följer du de här stegen när du uppmanas att ange anslutnings information i Logic Apps designer.

   1. Ange ett namn för anslutningen och välj Service Bus namn område.

      ![Skapa Service Bus anslutning, del 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Om du vill ange anslutnings strängen manuellt i stället väljer du **Ange anslutnings information manuellt**. Om du inte har anslutnings strängen kan du läsa om [hur du hittar anslutnings strängen](#permissions-connection-string).

   1. Välj din Service Bus-princip och välj **skapa**.

      ![Skapa Service Bus anslutning, del 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Välj den meddelande enhet som du vill använda, till exempel en kö eller ett ämne. I det här exemplet väljer du din Service Bus-kö.

      ![Välj Service Bus kö](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Ange nödvändig information för den valda åtgärden. Om du vill lägga till andra tillgängliga egenskaper till åtgärden öppnar du listan **Lägg till ny parameter** och väljer de egenskaper som du vill använda.

   Välj till exempel egenskaperna **innehåll** och **innehålls typ** så att du lägger till dem i åtgärden. Ange sedan innehållet för det meddelande som du vill skicka.

   ![Tillhandahåll meddelande innehåll och information](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Mer information om tillgängliga åtgärder och deras egenskaper finns på kopplingens [referens sida](/connectors/servicebus/).

1. Fortsätt att skapa din Logic app genom att lägga till andra åtgärder som du vill ha.

   Du kan till exempel lägga till en åtgärd som skickar e-post för att bekräfta att meddelandet har skickats.

1. Spara din logikapp. I verktygsfältet designer väljer du **Spara**.

## <a name="connector-reference"></a>Referens för anslutningsapp

Service Bus-anslutningen kan spara upp till 1 500 unika sessioner i taget från en Service Bus till kopplingens cacheminne. Om antalet sessioner överskrider den här gränsen tas gamla sessioner bort från cachen. Mer information finns i [Message sessions](../service-bus-messaging/message-sessions.md).

För annan teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI (tidigare Swagger), granskar du kopplingens [referens sida](/connectors/servicebus/). Mer information om Azure Service Bus meddelanden finns i [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)?

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)

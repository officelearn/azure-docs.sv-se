---
title: Ställ in med Azure Service Bus-meddelanden för Logikappar i Azure | Microsoft Docs
description: Skicka och ta emot meddelanden med logic apps med hjälp av Azure Service Bus
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 02/06/2018
ms.author: ladocs
ms.openlocfilehash: d5a4760e1e0f38fd81fd779786985f5753d77eab
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="send-and-receive-messages-with-the-azure-service-bus-connector"></a>Skicka och ta emot meddelanden med Azure Service Bus-koppling

För att skicka och ta emot meddelanden med din logikapp, ansluta till [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Du kan utföra åtgärder, till exempel skicka till en kö och skicka till ett ämne, ta emot från en kö och ta emot från en prenumeration. Lär dig mer om [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) och [hur priser fungerar för Logic Apps utlöser](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Förutsättningar

Du måste ha dessa artiklar som måste finnas i samma Azure-prenumerationen så att de är synliga för andra innan du kan använda Service Bus-anslutningen:

* En [Service Bus-namnrymd och meddelandeentitet, till exempel en kö](../service-bus-messaging/service-bus-create-namespace-portal.md)
* En [logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-service-bus"></a>Ansluta till Azure Service Bus

Innan din logikapp kan komma åt någon tjänst, måste du skapa en [ *anslutning* ](./connectors-overview.md) mellan logikappen och tjänsten om du inte redan har gjort. Den här anslutningen ger logikappen åtkomst till data. Kontrollera dina behörigheter för din logikapp att komma åt Service Bus-kontot.

1. Logga in på [Azure Portal](https://portal.azure.com "Azure Portal"). 

2. Gå till Service Bus *namnområde*, inte en specifik ”meddelandeentitet”. På sidan namnområde under **inställningar**, Välj **principer för delad åtkomst**. Under **anspråk**, kontrollera att du har **hantera** behörigheter för det namnområdet.

   ![Hantera behörigheter för ditt Service Bus-namnområde](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Om du vill ange anslutningsinformationen senare manuellt kan du hämta anslutningssträngen för Service Bus-namnrymd. Välj **RootManageSharedAccessKey**. Välj kopieringsknappen bredvid din primära nyckel anslutningssträngen. Spara anslutningssträngen för senare användning.

   ![Kopiera anslutningssträngen för Service Bus-namnområde](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Sök för att bekräfta om anslutningssträngen är associerat med Service Bus-namnrymd eller med en specifik entitet anslutningssträngen för den `EntityPath` parameter. Om du hittar den här parametern anslutningssträngen är för en specifik entitet och är inte rätt strängen som ska användas med din logikapp.

## <a name="trigger-workflow-when-your-service-bus-gets-new-messages"></a>Utlöser arbetsflödet när Service Bus hämtar nya meddelanden

En [ *utlösaren* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) är en händelse som startar ett arbetsflöde i din logikapp. Följ dessa steg för att lägga till utlösare som identifierar dessa meddelanden om du vill starta ett arbetsflöde när nya meddelanden skickas till Service Bus.

1. I den [Azure-portalen](https://portal.azure.com "Azure-portalen"), gå till din befintliga logikapp eller skapa en tom logikapp.

2. Ange ”service bus” i sökrutan som filter i Logic Apps Designer. Välj den **Service Bus** koppling. 

   ![Välj Service Bus-koppling](./media/connectors-create-api-azure-service-bus/select-service-bus-connector.png) 

3. Välj utlösare som du vill använda. Om du vill köra en logikapp när ett nytt objekt skickas till en Service Bus-kö, väljer du exempelvis den här utlösaren: **Service Bus - när ett meddelande tas emot i en kö (automatisk komplettering)**

   ![Välj Service Bus-utlösare](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Vissa utlöser returnerade en eller meddelanden, som den *Service Bus - när en eller flera meddelanden tas emot i en kö (automatisk komplettering)* utlösare.
   > När dessa utlösare eller de returnera mellan ett och antalet meddelanden som anges av utlösarens **maximalt meddelandet antal** egenskapen.

   1. Om du inte redan har en anslutning till Service Bus-namnrymd, uppmanas du att skapa den här anslutningen nu. Namnge din anslutning och välj Service Bus-namnområde som du vill använda.

      ![Skapa Service Bus-anslutning](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Om du vill ange anslutningssträngen manuellt välja **manuellt ange anslutningsinformationen**. 
      Läs [så att hitta anslutningssträngen](#permissions-connection-string).
      

   2. Nu väljer Service Bus-principen för att använda, och välj **skapa**.

      ![Skapa Service Bus-anslutning, del 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

4. Välj Service Bus-kö för att använda och ställa in intervall och frekvens för när du vill söka i kön.

   ![Välj Service Bus-kö, konfigurera avsökningsintervall](./media/connectors-create-api-azure-service-bus/select-service-bus-queue.png)

   > [!NOTE]
   > Alla Service Bus-utlösare **lång avsökning** utlösare, vilket innebär att när en utlösare utlöses utlösaren bearbetar alla meddelanden och väntar sedan på 30 sekunder för fler meddelanden i kö eller ett ämne prenumerationen.
   > Om inga meddelanden tas emot i 30 sekunder ignoreras utlösare för körning. Annars fortsätter utlösaren läsa meddelanden tills kö eller ett ämne prenumerationen är tom.
   > Nästa utlösaren omröstningen baseras på Upprepningsintervall som angetts i egenskaperna för den utlösaren.

5. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

Nu när logikappen kontrollerar den valda kön och söker efter ett nytt meddelande, körs utlösaren åtgärderna i din logikapp för meddelandet hittades.

## <a name="send-messages-from-your-logic-app-to-your-service-bus"></a>Skicka meddelanden från din logikapp till Service Bus

En [*åtgärd*](../logic-apps/logic-apps-overview.md#logic-app-concepts) är en aktivitet som utförs av logikapparbetsflödet. När du lägger till en utlösare i logikappen kan du lägga till en åtgärd för att utföra åtgärder med data som genereras av den utlösaren. Följ dessa steg om du vill skicka ett meddelande till din Service Bus-meddelanden entitet från din logikapp.

1. I Logic Apps Designer utlösaren, Välj under **+ nytt steg** > **lägga till en åtgärd**.

2. I sökrutan anger du ”service bus” som filter. Välj den här anslutningen: **Service Bus**

   ![Välj Service Bus-koppling](./media/connectors-create-api-azure-service-bus/select-service-bus-connector-for-action.png) 

3. Välj den här åtgärden: **Service Bus - Send-meddelande**

   ![Välj ”Service Bus - skicka meddelandet”](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png)

4. Välj meddelandeentitet som heter kö eller ett ämne, att skicka meddelandet. Ange sedan innehållet i meddelandet och annan information.

   ![Välj meddelandeentitet och ange meddelandeinformation](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)    

5. Spara din logikapp. 

Du har nu skapat en åtgärd som skickar meddelanden från din logikapp. 

## <a name="connector-specific-details"></a>Connector-specifik information

Om du vill veta mer om utlösare och åtgärder som definierats i Swagger-filen och alla gränser, granska den [connector information](/connectors/servicebus/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [övriga kopplingar för Azure Logic apps](../connectors/apis-list.md)
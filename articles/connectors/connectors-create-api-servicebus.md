---
title: Utbyta meddelanden med Azure Service Bus
description: Skapa automatiserade uppgifter och arbetsflöden som skickar och ta emot meddelanden med hjälp av Azure Service Bus i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 1b38b8508dbe17d42bf191149410f5db638cf834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261627"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Exchange-meddelanden i molnet med hjälp av Azure Logic Apps och Azure Service Bus

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och Azure Service Bus-anslutningsappen kan du skapa automatiserade uppgifter och arbetsflöden som överför data, till exempel försäljnings- och inköpsorder, journaler och lagerförflyttningar över program för din organisation. [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) Kopplingen övervakar, skickar och hanterar inte bara meddelanden, utan utför även åtgärder med köer, sessioner, ämnen, prenumerationer och så vidare, till exempel:

* Övervaka när meddelanden anländer (slutförs automatiskt) eller tas emot (peek-lock) i köer, ämnen och ämnesprenumerationer.
* Skicka meddelanden.
* Skapa och ta bort ämnesprenumerationer.
* Hantera meddelanden i köer och ämnesprenumerationer, till exempel få, få uppskjuten, fullständig, skjuta upp, överge och obeställa.
* Förnya lås på meddelanden och sessioner i köer och ämnesprenumerationer.
* Stäng sessioner i köer och ämnen.

Du kan använda utlösare som får svar från Service Bus och gör utdata tillgängliga för andra åtgärder i logikapparna. Du kan också låta andra åtgärder använda utdata från Service Bus-åtgärder. Om du inte har tidigare till Service Bus och Logic Apps läser du [Vad är Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) och Vad är Azure Logic [Apps?](../logic-apps/logic-apps-overview.md)

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En servicebussnamnområde och meddelandeentitet, till exempel en kö. Dessa objekt och din logikapp måste använda samma Azure-prenumeration. Om du inte har dessa objekt kan du läsa om hur du [skapar namnområdet för Service Bus och en kö](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du använder servicebussens namnområde och meddelandeentitet. Logikappen och servicebussen måste använda samma Azure-prenumeration. Om du vill starta arbetsflödet med en Service Bus-utlösare [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Service Bus-åtgärd i arbetsflödet startar du logikappen med en annan utlösare, till exempel [utlösaren För återkommande](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Kontrollera behörigheter

Bekräfta att logikappen har behörighet för åtkomst till ditt servicebussnamnområde.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till *tjänstbussens namnområde*. Välj Principer för **delad åtkomst**under **Inställningar**på namnområdessidan. Under **Anspråk**kontrollerar du att du har **hantera** behörigheter för det namnområdet.

   ![Hantera behörigheter för Service Bus-namnområde](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Hämta anslutningssträngen för servicebussens namnområde. Du behöver den här strängen när du anger anslutningsinformationen i logikappen.

   1. I fönstret **Principer för delad åtkomst** väljer du **RootManageSharedAccessKey**.
   
   1. Välj kopieringsknappen bredvid den primära anslutningssträngen. Spara anslutningssträngen för senare användning.

      ![Kopiera anslutningssträng för servicebussnamnområde](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Om du vill bekräfta om anslutningssträngen är associerad med tjänstbussens namnområde `EntityPath`  eller en meddelandeentitet, till exempel en kö, söker du i anslutningssträngen efter parametern. Om du hittar den här parametern är anslutningssträngen för en viss entitet och är inte rätt sträng som ska användas med logikappen.

## <a name="add-service-bus-trigger"></a>Lägga till servicebussutlösare

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna din tomma logikapp i Logic App Designer.

1. I sökrutan anger du "azure service bus" som filter. Välj den utlösare du vill använda i listan utlösare.

   Om du till exempel vill utlösa logikappen när ett nytt objekt skickas till en servicebusskö väljer du **när ett meddelande tas emot i en kö (slutför automatiskt).**

   ![Välj servicebussutlösare](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Alla Service Bus-utlösare är *utlösare för långavsökning.* Den här beskrivningen innebär att när utlösaren utlöses bearbetar utlösaren alla meddelanden och väntar sedan 30 sekunder på att fler meddelanden ska visas i kö- eller ämnesprenumerationen. Om inga meddelanden visas inom 30 sekunder hoppas utlösarkörningen över. Annars fortsätter utlösaren att läsa meddelanden tills kö- eller ämnesprenumerationen är tom. Nästa utlösaravsökning baseras på det upprepningsintervall som anges i utlösarens egenskaper.

   Vissa utlösare, till exempel **När ett eller flera meddelanden anländer i en köutlösare (komplettera automatiskt),** kan returnera ett eller flera meddelanden. När dessa utlösare utlöses returnerar de mellan ett och antalet meddelanden som anges av utlösarens **egenskap Maximum message count.**

1. Om utlösaren ansluter till tjänstbussens namnområde för första gången följer du dessa steg när Logic App Designer uppmanar dig att ange anslutningsinformation.

   1. Ange ett namn för anslutningen och välj namnområdet Service Bus.

      ![Skapa servicebussanslutning, del 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Om du vill ange anslutningssträngen manuellt i stället väljer du **Ange anslutningsinformation manuellt**. Om du inte har anslutningssträngen kan du läsa om hur du [hittar anslutningssträngen](#permissions-connection-string).

   1. Välj policyn för servicebuss och välj **Skapa**.

      ![Skapa Service Bus-anslutning, del 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Välj den meddelandeentitet du vill använda, till exempel en kö eller ett ämne. I det här exemplet väljer du din Service Bus-kö.
   
      ![Välj servicebusskö](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Ange nödvändig information för den valda utlösaren. Om du vill lägga till andra tillgängliga egenskaper i åtgärden öppnar du listan **Lägg till ny parameter** och väljer önskade egenskaper.

   För det här exemplets utlösare väljer du avsökningsintervall och frekvens för att kontrollera kön.

   ![Ställ in avsökningsintervall](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Mer information om tillgängliga utlösare och egenskaper finns på kopplingens [referenssida](/connectors/servicebus/).

1. Fortsätt att skapa logikappen genom att lägga till de åtgärder du vill ha.

   Du kan till exempel lägga till en åtgärd som skickar e-post när ett nytt meddelande anländer. När utlösaren kontrollerar din kö och hittar ett nytt meddelande kör logikappen dina valda åtgärder för det hittade meddelandet.

## <a name="add-service-bus-action"></a>Åtgärden Lägg till servicebuss

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer.

1. Välj **Nytt steg**under det steg där du vill lägga till en åtgärd .

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan dessa steg. Markera plustecknet**+**( ) som visas och välj **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**anger du "azure service bus" som filter i sökrutan. Välj den åtgärd du vill använda i åtgärdslistan. 

   I det här exemplet väljer du åtgärden **Skicka meddelande.**

   ![Välj åtgärd servicebuss](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Om åtgärden ansluter till namnområdet Service Bus för första gången följer du dessa steg när Logic App Designer uppmanar dig att ange anslutningsinformation.

   1. Ange ett namn för anslutningen och välj namnområdet Service Bus.

      ![Skapa servicebussanslutning, del 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Om du vill ange anslutningssträngen manuellt i stället väljer du **Ange anslutningsinformation manuellt**. Om du inte har anslutningssträngen kan du läsa om hur du [hittar anslutningssträngen](#permissions-connection-string).

   1. Välj policyn för servicebuss och välj **Skapa**.

      ![Skapa Service Bus-anslutning, del 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Välj den meddelandeentitet du vill använda, till exempel en kö eller ett ämne. I det här exemplet väljer du din Service Bus-kö.

      ![Välj servicebusskö](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Ange nödvändiga uppgifter för den valda åtgärden. Om du vill lägga till andra tillgängliga egenskaper i åtgärden öppnar du listan **Lägg till ny parameter** och väljer önskade egenskaper.

   Välj till exempel egenskaperna **Innehåll** och **innehållstyp** så att du lägger till dem i åtgärden. Ange sedan innehållet för meddelandet som du vill skicka.

   ![Ange meddelandeinnehåll och information](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Mer information om tillgängliga åtgärder och deras egenskaper finns på kopplingens [referenssida](/connectors/servicebus/).

1. Fortsätt att skapa logikappen genom att lägga till andra åtgärder som du vill ha.

   Du kan till exempel lägga till en åtgärd som skickar e-post för att bekräfta att meddelandet har skickats.

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

## <a name="connector-reference"></a>Referens för anslutningsapp

Service Bus-anslutningen kan spara upp till 1 500 unika sessioner åt gången från en servicebuss till anslutningscachen. Om antalet sessioner överskrider den här gränsen tas gamla sessioner bort från cacheminnet. Mer information finns i [Meddelandesessioner](../service-bus-messaging/message-sessions.md).

Mer information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](/connectors/servicebus/). Mer information om Azure Service Bus Messaging finns i [Vad är Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)

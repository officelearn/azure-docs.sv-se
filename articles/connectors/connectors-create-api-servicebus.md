---
title: Skicka och ta emot meddelanden med Azure Service Bus – Azure Logic Apps | Microsoft Docs
description: Konfigurera molnmeddelandehantering med Azure Service Bus i Azure Logic Apps enterprise
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
ms.openlocfilehash: 813df5b4ef37ad1264df48863aa8f0ed5a4d4789
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048782"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>Exchange-meddelanden i molnet med Azure Service Bus och Azure Logic Apps

Med Azure Logic Apps och Azure Service Bus-anslutningen kan skapa du automatiserade uppgifter och arbetsflöden som överför data, till exempel försäljning och inköpsorder, journaler och inventering förflyttningar i program för din organisation. Anslutningen inte bara övervakar, skickar, och hanterar meddelanden, men utför även åtgärder med köer, sessioner, ämnen, prenumerationer och så vidare, till exempel:

* Övervakare för när meddelanden tas emot (Komplettera automatiskt) eller mottagna (peek-lock) i köer, ämnen och ämnesprenumerationer. 
* Skicka meddelanden.
* Skapa och ta bort ämnesprenumerationer.
* Hantera meddelanden i köer och ämnesprenumerationer, till exempel få, hämta skjutas upp, Slutför, skjuta upp, avbrott och förlorade.
* Förnya lås på meddelanden och sessioner i köer och ämnesprenumerationer.
* Stäng sessioner i köer och ämnen.

Du kan använda utlösare som får svar från Service Bus och göra utdata ska vara tillgängliga för andra åtgärder i dina logic apps. Du kan också ha andra åtgärder som använder utdata från Service Bus-åtgärder. Om du är nybörjare på Service Bus och Logic Apps kan du granska [vad är Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) och [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Ett Service Bus-namnområde och en meddelandeentitet, till exempel en kö. Om du inte har de här objekten kan du lära dig hur du [skapa Service Bus-namnområdet och en kö](../service-bus-messaging/service-bus-create-namespace-portal.md). 

  Dessa objekt måste finnas i samma Azure-prenumeration som dina logikappar som använder de här objekten.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill använda Service Bus. Logikappen måste finnas i samma Azure-prenumeration som din service bus. Du kommer igång med Service Bus-utlösare [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Service Bus-åtgärd, starta din logikapp med en annan utlösare, till exempel, **upprepning** utlösaren.

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Kontrollera behörigheter

Bekräfta att logikappen har behörighet för att komma åt Service Bus-namnområdet. 

1. Logga in på [Azure Portal](https://portal.azure.com). 

2. Gå till Service Bus *namnområde*. På sidan namnområde under **inställningar**väljer **principer för delad åtkomst**. Under **anspråk**, kontrollera att du har **hantera** behörigheter för det här namnområdet

   ![Hantera behörigheter för Service Bus-namnområdet](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Hämta anslutningssträngen för ditt Service Bus-namnområde. Du behöver den här strängen när du anger information om din i din logikapp.

   1. Välj **RootManageSharedAccessKey**. 
   
   1. Välj på kopieringsknappen bredvid din primär anslutningssträng. Spara anslutningssträngen för senare användning.

      ![Kopiera anslutningssträng för Service Bus-namnområde](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > För att bekräfta om din anslutningssträng är kopplad till ditt Service Bus-namnområde eller en meddelandeentitet, till exempel en kö, söka i anslutningssträngen för den `EntityPath` parametern. Om du hittar den här parametern anslutningssträngen är för en specifik enhet och är inte rätt strängen som ska användas med din logikapp.

## <a name="add-trigger-or-action"></a>Lägg till utlösare eller åtgärd

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. Att lägga till en *utlösaren* till en tom logikapp i sökrutan anger du ”Azure Service Bus” som filter. Välj utlösaren som du vill under listan över utlösare. 

   Till exempel för att utlösa logikappen när ett nytt objekt skickas till en Service Bus-kö, Välj den här utlösaren: **när ett meddelande tas emot i en kö (Komplettera automatiskt)**

   ![Välj Service Bus-utlösare](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Vissa utlösare kan returnera ett eller meddelanden, till exempel utlösaren **när en eller flera meddelanden anländer i en kö (Komplettera automatiskt)**. När dessa utlösare utlöses de tillbaka mellan ett och hur många meddelanden som anges av utlösarens **maximala meddelandeantalet** egenskapen.

   *Alla Service Bus-utlösare är-longpolling utlösare*, vilket innebär att när utlösaren utlöses skapar utlösaren bearbetar alla meddelanden och väntar sedan 30 sekunder för fler meddelanden i kö eller ämne prenumerationen. 
   Om inga meddelanden visas i 30 sekunder kan hoppas utlösarkörning. 
   I annat fall fortsätter utlösaren läser meddelanden tills kön eller ämnet prenumerationen är tom. Nästa utlösaren omröstningen baseras på det återkommande intervall som anges i utlösarens egenskaper.

1. Att lägga till en *åtgärd* i en befintlig logic app följer du dessa steg: 

   1. Under det sista steget där du vill lägga till en åtgärd, väljer **nytt steg**. 

      Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. 
      Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

   1. I sökrutan anger du ”Azure Service Bus” som filter. 
   Välj vilken åtgärd du önska under åtgärder. 
 
      Till exempel väljer den här åtgärden: **skicka meddelande**

      ![Välj Service Bus-åtgärd](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Om du vill ansluta logikappen till Service Bus-namnområdet för första gången, efterfrågas Logic App Designer nu anslutningsinformation. 

   1. Ange ett namn för anslutningen och välj Service Bus-namnområdet.

      ![Skapa Service Bus-anslutning, del 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      För att manuellt ange anslutningssträngen i stället, Välj **ange anslutningsinformationen manuellt**. 
      Om du inte har anslutningssträngen kan du lära dig [hitta anslutningssträngen](#permissions-connection-string).

   1. Välj nu din Service Bus-princip och välj sedan **skapa**.

      ![Skapa Service Bus-anslutning, del 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. I det här exemplet väljer du den asynkrona entitet som du vill, till exempel en kö eller ämne. I det här exemplet väljer du din Service Bus-kö. 
   
   ![Välj Service Bus-kö](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. Ange informationen som krävs för din utlösare eller åtgärd. I det här exemplet gör de relevanta för din utlösare eller åtgärd: 

   * **För exemplet utlösaren**: ange avsökningsintervall och frekvens för att kontrollera kön.

     ![Konfigurera avsökningsintervall](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     När du är klar kan du fortsätta att bygga din logikapp arbetsflöde genom att lägga till de åtgärder som du vill. Du kan till exempel lägga till en åtgärd som skickar e-postmeddelande när ett nytt meddelande anländer.
     När utlösaren kontrollerar din kö och söker efter ett nytt meddelande, körs logikappen dina valda åtgärder för meddelandet hittades.

   * **För åtgärden exempel**: Ange meddelandeinnehåll och annan information. 

     ![Ange meddelandeinnehåll och information](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     När du är klar kan du fortsätta att bygga din logikapp arbetsflöde genom att lägga till andra åtgärder som du vill. Du kan till exempel lägga till en åtgärd som skickar e-postmeddelande som bekräftar din meddelandet skickades.

1. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/servicebus/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
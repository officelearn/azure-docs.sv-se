---
title: Ansluta till IBM MQ-server
description: Skicka och hämta meddelanden med en Azure- eller lokalt IBM MQ-server och Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 6bfd626c1ce69029ee720d24b0b143e7b4c3dd56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650955"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Ansluta till en IBM MQ-server från Azure Logic Apps

IBM MQ-anslutningen skickar och hämtar meddelanden som lagras i en IBM MQ-server lokalt eller i Azure. Den här anslutningen innehåller en Microsoft MQ-klient som kommunicerar med en fjärr-IBM MQ-server över ett TCP/IP-nätverk. Den här artikeln innehåller en startguide för att använda MQ-kontakten. Du kan börja med att bläddra i ett enskilt meddelande i en kö och sedan prova andra åtgärder.

IBM MQ-anslutningen innehåller dessa åtgärder men ger inga utlösare:

- Bläddra bland ett enda meddelande utan att ta bort meddelandet från IBM MQ-servern
- Bläddra bland en grupp meddelanden utan att ta bort meddelanden från IBM MQ-servern
- Ta emot ett enda meddelande och ta bort meddelandet från IBM MQ-servern
- Ta emot en bunt meddelanden och ta bort meddelandena från IBM MQ-servern
- Skicka ett enda meddelande till IBM MQ-servern

## <a name="prerequisites"></a>Krav

* Om du använder en lokal MQ-server [installerar du den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) på en server i nätverket. Servern där den lokala datagatewayen är installerad måste också ha .NET Framework 4.6 installerat för att MQ-anslutningen ska fungera. Du måste också skapa en resurs i Azure för den lokala datagatewayen. Mer information finns i [Konfigurera datagatewayanslutningen](../logic-apps/logic-apps-gateway-connection.md).

  Men om din MQ-server är allmänt tillgänglig eller tillgänglig i Azure behöver du inte använda datagatewayen.

* Ibm WebSphere MQ-versioner som officiellt stöds:

  * MQ 7,5
  * MQ 8,0
  * MQ 9,0

* Logikappen där du vill lägga till MQ-åtgärden. Den här logikappen måste använda samma plats som din lokala datagatewayanslutning och måste redan ha en utlösare som startar arbetsflödet. 

  MQ-kopplingen har inga utlösare, så du måste lägga till en utlösare i logikappen först. Du kan till exempel använda utlösaren Återkommande. Om du inte har tidigare i logikappar kan du prova den här [snabbstarten för att skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Bläddra bland ett enskilt meddelande

1. Välj **Nytt steg**under utlösaren eller en annan åtgärd i logikappen. 

1. Skriv "mq" i sökrutan och välj den här åtgärden: **Bläddra i meddelandet**

   ![Bläddra i meddelande](media/connectors-create-api-mq/Browse_message.png)

1. Om du inte har en befintlig MQ-anslutning skapar du anslutningen:  

   1. I åtgärden väljer du **Anslut via lokal datagateway**.
   
   1. Ange egenskaperna för MQ-servern.  

      För **Server**kan du ange MQ-servernamnet eller ange IP-adressen följt av ett kolon och portnumret.
    
   1. Öppna **gatewaylistan,** som visar alla tidigare konfigurerade gatewayanslutningar. Välj din gateway.
    
   1. När du är klar väljer du **Skapa**. 
   
      Anslutningen ser ut så här:

      ![Anslutningsegenskaper](media/connectors-create-api-mq/Connection_Properties.png)

1. Ställ in åtgärdens egenskaper:

   * **Kö**: Ange en kö som skiljer sig från anslutningen.

   * **MessageId,** **CorrelationId,** **GroupId**och andra egenskaper: Bläddra efter ett meddelande baserat på de olika MQ-meddelandeegenskaperna

   * **IncludeInfo**: Ange **Sant** om du vill inkludera ytterligare meddelandeinformation i utdata. Du kan också ange **Falskt** om du inte vill ta med ytterligare meddelandeinformation i utdata.

   * **Timeout**: Ange ett värde för att bestämma hur länge ett meddelande ska komma fram i en tom kö. Om inget anges hämtas det första meddelandet i kön och det finns ingen tid att vänta på att ett meddelande ska visas.

     ![Bläddra bland meddelandeegenskaper](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Spara** ändringarna och **kör** sedan logikappen.

   ![Spara och kör](media/connectors-create-api-mq/Save_Run.png)

   När körningen är klar visas stegen från körningen och du kan granska utdata.

1. Om du vill granska informationen för varje steg väljer du den gröna bocken. Om du vill granska mer information om utdata väljer du **Visa råutdata**.

   ![Bläddra bland meddelandeutdata](media/connectors-create-api-mq/Browse_message_output.png)  

   Här är några exempel råutgång:

   ![Bläddra bland råutdata för meddelanden](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Om du ställer in **IncludeInfo** på true visas följande utdata:

   ![Bläddra bland meddelande inkluderar information](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Bläddra bland flera meddelanden

Åtgärden **Bläddra bland meddelanden** innehåller alternativet **BatchSize** för att ange hur många meddelanden som ska returneras från kön.  Om **BatchSize** inte har någon post returneras alla meddelanden. Den returnerade utdata är en matris med meddelanden.

1. När du lägger till åtgärden **Bläddra bland meddelanden väljs** den första tidigare konfigurerade anslutningen som standard. Om du vill skapa en ny anslutning väljer du **Ändra anslutning**. Du kan också välja en annan anslutning.

1. När logikappen har körts är det här några exempelutdata från åtgärden **Bläddra bland meddelanden:**

   ![Bläddra bland meddelanden som matas ut](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Ta emot ett meddelande

Åtgärden **Ta emot meddelande** har samma in- och utdata som åtgärden Bläddra igenom **meddelande.** När du använder **Meddelande ta emot**tas meddelandet bort från kön.

## <a name="receive-multiple-messages"></a>Ta emot flera meddelanden

Åtgärden **Ta emot meddelanden** har samma in- och utdata som åtgärden Bläddra bland **meddelanden.** När du använder **Ta emot meddelanden**tas meddelandena bort från kön.

Om det inte finns några meddelanden i kön när du gör en bläddring eller en mottagning, misslyckas steget med den här utdata:  

![MQ inget meddelandefel](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Skicka meddelande

När du lägger till åtgärden **Skicka meddelanden väljs** den första tidigare konfigurerade anslutningen som standard. Om du vill skapa en ny anslutning väljer du **Ändra anslutning**. Du kan också välja en annan anslutning.

1. Välj en giltig meddelandetyp: **Datagram,** **Svara**eller **Begär**  

   ![Skicka Msg Rekvisita](media/connectors-create-api-mq/Send_Msg_Props.png)

1. När logikappen har körts är det här några exempelutdata från åtgärden **Skicka meddelande:**

   ![Skicka Msg-utgång](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här kopplingen, till exempel utlösare, åtgärder och begränsningar enligt beskrivningen i kopplingens Swagger-fil, finns på [kopplingens referenssida](https://docs.microsoft.com/connectors/mq/).

> [!NOTE]
> För logikappar i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandegränserna](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)

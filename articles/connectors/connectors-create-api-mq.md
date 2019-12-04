---
title: Ansluta till IBM MQ Server
description: Skicka och hämta meddelanden med en Azure-eller lokal IBM MQ-Server och Azure Logic Apps
services: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: ef9e91b526055ece58ce283572deb98cff951653
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789587"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Ansluta till en IBM MQ-Server från Azure Logic Apps

IBM MQ Connector skickar och hämtar meddelanden som lagras i en IBM MQ-server lokalt eller i Azure. Den här anslutningen innehåller en Microsoft MQ-klient som kommunicerar med en IBM MQ-server i ett TCP/IP-nätverk. Den här artikeln innehåller en start guide för att använda MQ-anslutaren. Du kan börja med att bläddra i ett enskilt meddelande i en kö och sedan försöka med andra åtgärder.

IBM MQ Connector innehåller följande åtgärder men ger inga utlösare:

- Bläddra i ett enskilt meddelande utan att ta bort meddelandet från IBM MQ Server
- Bläddra i en grupp med meddelanden utan att ta bort meddelandena från IBM MQ Server
- Ta emot ett enda meddelande och ta bort meddelandet från IBM MQ Server
- Ta emot en batch med meddelanden och ta bort meddelandena från IBM MQ Server
- Skicka ett enskilt meddelande till IBM MQ Server

## <a name="prerequisites"></a>Krav

* Om du använder en lokal MQ-Server [installerar du den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) på en server i nätverket. Servern där den lokala datagatewayen är installerad måste också ha .NET Framework 4,6 installerat för att MQ-kopplingen ska fungera. Du måste också skapa en resurs i Azure för den lokala datagatewayen. Mer information finns i [Konfigurera data Gateway-anslutningen](../logic-apps/logic-apps-gateway-connection.md).

  Men om din MQ-Server är offentligt tillgänglig eller tillgänglig i Azure behöver du inte använda data gatewayen.

* Officiellt stödda IBM WebSphere MQ-versioner:

  * MQ 7,5
  * MQ 8,0
  * MQ 9,0

* Den Logic app där du vill lägga till åtgärden MQ. Den här Logic-appen måste använda samma plats som din lokala datagateway-anslutning och måste redan ha en utlösare som startar arbets flödet. 

  MQ-anslutningen har inga utlösare, så du måste först lägga till en utlösare i din Logic app. Du kan till exempel använda upprepnings utlösaren. Om du inte har använt Logic Apps igen kan du prova den här [snabb starten för att skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Bläddra i ett enskilt meddelande

1. I din Logic app, under utlösaren eller en annan åtgärd, väljer du **nytt steg**. 

1. Skriv "MQ" i rutan Sök och välj den här åtgärden: Bläddra i **meddelande**

   ![Bläddra i meddelande](media/connectors-create-api-mq/Browse_message.png)

1. Om du inte har en befintlig MQ-anslutning skapar du anslutningen:  

   1. I åtgärden väljer du **Anslut via lokal datagateway**.
   
   1. Ange egenskaperna för din MQ-Server.  

      För **Server**kan du ange namnet på MQ-servern eller ange IP-adressen följt av ett kolon och port numret.
    
   1. Öppna listan **Gateway** som visar alla tidigare konfigurerade Gateway-anslutningar. Välj din gateway.
    
   1. När du är klar väljer du **Skapa**. 
   
      Anslutningen ser ut som i det här exemplet:

      ![Anslutnings egenskaper](media/connectors-create-api-mq/Connection_Properties.png)

1. Konfigurera åtgärdens egenskaper:

   * **Kö**: Ange en kö som skiljer sig från anslutningen.

   * **Messageid**, **correlationId**, **Egenskaper**och andra egenskaper: Bläddra efter ett meddelande baserat på de olika egenskaperna för MQ-meddelanden

   * **IncludeInfo**: ange **True** om du vill inkludera ytterligare meddelande information i utdata. Eller ange **falskt** om du inte vill inkludera ytterligare meddelande information i utdata.

   * **Timeout**: Ange ett värde för att avgöra hur lång tid det tar att ta emot ett meddelande i en tom kö. Om inget anges hämtas det första meddelandet i kön och det finns ingen tids åtgång i väntan på att ett meddelande ska visas.

     ![Bläddra i meddelande egenskaper](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Spara** ändringarna och **Kör** sedan din Logic app.

   ![Spara och kör](media/connectors-create-api-mq/Save_Run.png)

   När körningen är klar visas stegen från körningen och du kan granska utdata.

1. Om du vill granska informationen för varje steg väljer du den gröna bock markeringen. Om du vill granska mer information om utdata väljer du **Visa rå**data.

   ![Bläddra i meddelandets utdata](media/connectors-create-api-mq/Browse_message_output.png)  

   Här följer några exempel på rå utdata:

   ![Bläddra i meddelandets RAW-utdata](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Om du anger **IncludeInfo** till true visas följande utdata:

   ![Bläddra bland meddelandet innehåller info](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Bläddra bland flera meddelanden

Åtgärden **Bläddra meddelanden** innehåller ett **batchSize** -alternativ för att ange hur många meddelanden som ska returneras från kön.  Om **batchSize** inte har någon post returneras alla meddelanden. Returnerade utdata är en matris med meddelanden.

1. När du lägger till åtgärden **Bläddra meddelanden** väljs den första tidigare konfigurerade anslutningen som standard. Välj **ändra anslutning**om du vill skapa en ny anslutning. Eller Välj en annan anslutning.

1. När Logic app-körningen är klar är följande exempel på utdata från åtgärden **Bläddra meddelanden** :

   ![Bläddra meddelanden i utdata](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Ta emot ett enskilt meddelande

Åtgärden **ta emot meddelande** har samma indata och utdata som åtgärd för att **söka efter meddelanden** . När du **använder meddelandet tas meddelandet bort**från kön.

## <a name="receive-multiple-messages"></a>Ta emot flera meddelanden

Åtgärden **ta emot meddelanden** har samma indata och utdata som åtgärd för att **söka meddelanden** . När du använder **mottagna meddelanden**tas meddelandena bort från kön.

Om det inte finns några meddelanden i kön vid en bläddring eller en mottagning, Miss lyckas steget med följande utdata:  

![Fel i MQ-meddelande](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Skicka meddelande

När du lägger till åtgärden **skicka meddelanden** väljs den första tidigare konfigurerade anslutningen som standard. Välj **ändra anslutning**om du vill skapa en ny anslutning. Eller Välj en annan anslutning.

1. Välj en giltig meddelande typ: **datagram**, **Reply**eller **Request**  

   ![Skicka meddelande propor](media/connectors-create-api-mq/Send_Msg_Props.png)

1. När Logic-appen har körts klart är följande exempel på utdata från åtgärden **Skicka meddelande** :

   ![Skicka MSG-utdata](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/mq/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)

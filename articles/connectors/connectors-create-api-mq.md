---
title: Anslut till IBM MQ-server – Azure Logic Apps
description: Skicka och ta emot meddelanden med en Azure eller lokalt IBM MQ-server och Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: a2894799946d069916b27a4f5bcc7bd3244705b2
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273118"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Ansluta till en IBM MQ-server från Azure Logic Apps

IBM MQ-kopplingen skickar och hämtar meddelanden som lagras i en IBM MQ-server lokalt eller i Azure. Denna koppling inkluderar en Microsoft MQ-klient som kommunicerar med en fjärransluten IBM MQ-server i ett TCP/IP-nätverk. Den här artikeln innehåller en startguide att använda anslutningstjänsten MQ. Du kan starta genom att bläddra i ett enda meddelande i en kö och försök sedan andra åtgärder.

IBM MQ-kopplingen innehåller de här åtgärderna men innehåller inga utlösare:

- Bläddra ett enskilt meddelande utan att ta bort meddelandet från IBM MQ-server
- Bläddra bland en grupp med meddelanden utan att ta bort meddelandena från IBM MQ-server
- Ett enda meddelande och ta bort meddelandet från IBM MQ-server
- Ta emot en grupp med meddelanden och ta bort meddelandena från IBM MQ-server
- Skicka ett enda meddelande till IBM MQ-server

## <a name="prerequisites"></a>Nödvändiga komponenter

* Om du använder en lokal MQ-server [installera den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) på en server i nätverket. Den server där den lokala datagatewayen har installerats måste också ha .NET Framework 4.6 installerat för MQ-anslutningen ska fungera. Du måste också skapa en resurs i Azure för den lokala datagatewayen. Mer information finns i [konfigurera dataanslutningen för gateway](../logic-apps/logic-apps-gateway-connection.md).

  Om din MQ-server är offentligt tillgängligt eller som är tillgängligt i Azure kan har du inte du använder datagateway.

* Officiellt IBM WebSphere MQ-versioner som stöds:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* Logikappen där du vill lägga till MQ-åtgärd. Den här logikappen måste använda samma plats som din lokala data gateway-anslutning och måste redan ha en utlösare som startar arbetsflödet. 

  MQ-anslutningen ha inte utlösare, så du måste lägga till en utlösare i logikappen först. Du kan till exempel använda upprepningsutlösaren. Om du är nybörjare till logic apps kan du prova följande [Snabbstart för att skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Bläddra ett enskilt meddelande

1. I din logikapp, utlösaren eller någon annan åtgärd väljer **nytt steg**. 

1. Skriv ”mq” i sökrutan och välj den här åtgärden: **Bläddra meddelande**

   ![Bläddra meddelande](media/connectors-create-api-mq/Browse_message.png)

1. Om du inte har en befintlig MQ-anslutning kan du skapa anslutningen:  

   1. I åtgärden, väljer **Anslut via lokal datagateway**.
   
   1. Ange egenskaper för MQ-server.  

      För **Server**, du kan ange MQ-servernamnet eller ange IP-adress följt av ett kolon och portnumret.
    
   1. Öppna den **gateway** lista som visar alla tidigare konfigurerade gateway-anslutningar. Välj din gateway.
    
   1. När du är klar väljer du **Skapa**. 
   
      Anslutningen ser ut som i följande exempel:

      ![Anslutningsegenskaper](media/connectors-create-api-mq/Connection_Properties.png)

1. Ställ in åtgärdens egenskaper:

   * **kön**: Ange en kö som skiljer sig från anslutningen.

   * **MessageId**, **CorrelationId**, **GroupId**, och andra egenskaper: Bläddra efter ett meddelande baserat på olika egenskaper för MQ-meddelande

   * **IncludeInfo**: Ange **SANT** att inkludera information om ytterligare meddelande i utdata. Alternativt kan du ange **FALSKT** att inte inkludera information om ytterligare meddelande i utdata.

   * **Timeout**: Ange ett värde för att avgöra hur lång tid att vänta tills ett meddelande tas emot i en tom kö. Om inget anges det första meddelandet i kön hämtas och det finns ingen tid att vänta tills meddelandet visas.

     ![Bläddra meddelandeegenskaper](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Spara** dina ändringar och sedan **kör** logikappen.

   ![Spara och kör](media/connectors-create-api-mq/Save_Run.png)

   När körningen är klar visas stegen från körningen och du kan granska resultatet.

1. Välj grön bockmarkering om du vill granska informationen för varje steg. Om du vill granska mer information om utdata, Välj **visa råutdata**.

   ![Bläddra meddelande utdata](media/connectors-create-api-mq/Browse_message_output.png)  

   Här är några exempel på raw utdata:

   ![Bläddra meddelande råutdata](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Om du ställer in **IncludeInfo** true, följande utdata visas:

   ![Bläddra-meddelandet inkluderar information](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Bläddra bland flera meddelanden

Den **Bläddra meddelanden** åtgärd innehåller en **BatchSize** alternativ för att ange hur många meddelanden som ska returneras från kön.  Om **BatchSize** har ingen post returneras alla meddelanden. Returnerade utdata är en matris med meddelanden.

1. När du lägger till den **Bläddra meddelanden** åtgärd, den första tidigare konfigurerad anslutning väljs som standard. Om du vill skapa en ny anslutning, Välj **ändra anslutning**. Eller välj en annan anslutning.

1. När logikappen körs slutförs, här är några exempel på utdata från den **Bläddra meddelanden** åtgärd:

   ![Bläddra meddelanden utdata](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Enkel meddelande

Den **ta emot meddelandet** åtgärden har samma indata och utdata som den **Bläddra meddelande** åtgärd. När du använder **ta emot meddelandet**, meddelandet tas bort från kön.

## <a name="receive-multiple-messages"></a>Ta emot flera meddelanden

Den **ta emot meddelanden** åtgärden har samma indata och utdata som den **Bläddra meddelanden** åtgärd. När du använder **ta emot meddelanden**, meddelanden tas bort från kön.

Om det finns inga meddelanden i kön när du gör en Bläddra eller mottagning kan misslyckas steget med dessa utdata:  

![MQ Nej meddelande fel](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Skicka meddelande

När du lägger till den **skicka meddelanden** åtgärd, den första tidigare konfigurerad anslutning väljs som standard. Om du vill skapa en ny anslutning, Välj **ändra anslutning**. Eller välj en annan anslutning.

1. Välj en giltig Meddelandetyp: **Datagram**, **svara**, eller **för begäran**  

   ![Skicka Msg sammanställer](media/connectors-create-api-mq/Send_Msg_Props.png)

1. När logikappen är klar här är några exempel på utdata från den **skicka meddelande** åtgärd:

   ![Skicka Msg utdata](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/mq/).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)

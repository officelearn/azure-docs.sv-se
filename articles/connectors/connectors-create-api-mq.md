---
title: Ansluta till IBM MQ Server
description: Skicka och hämta meddelanden med en Azure-eller lokal IBM MQ-Server och Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, estfan, logicappspm
ms.topic: article
ms.date: 05/14/2020
tags: connectors
ms.openlocfilehash: 17143257fcb6b9c71bb56e1f4c4958dce503c234
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652460"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Ansluta till en IBM MQ-Server från Azure Logic Apps

IBM MQ Connector skickar och hämtar meddelanden som lagras i en IBM MQ-server lokalt eller i Azure. Den här anslutningen innehåller en Microsoft MQ-klient som kommunicerar med en IBM MQ-server i ett TCP/IP-nätverk. Den här artikeln innehåller en start guide för att använda MQ-anslutaren. Du kan börja med att bläddra i ett enskilt meddelande i en kö och sedan försöka med andra åtgärder.

IBM MQ Connector innehåller följande åtgärder men ger inga utlösare:

- Bläddra i ett enskilt meddelande utan att ta bort meddelandet från IBM MQ-servern.
- Bläddra i en batch med meddelanden utan att ta bort meddelandena från IBM MQ-servern.
- Ta emot ett enda meddelande och ta bort meddelandet från IBM MQ-servern.
- Ta emot en batch med meddelanden och ta bort meddelandena från IBM-MQ-servern.
- Skicka ett enskilt meddelande till IBM MQ Server.

Här är de officiellt IBM WebSphere MQ-versioner som stöds:

  * MQ 7,5
  * MQ 8,0
  * MQ 9,0
  * MQ 9,1

## <a name="prerequisites"></a>Krav

* Om du använder en lokal MQ-Server [installerar du den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) på en server i nätverket. Servern där den lokala datagatewayen är installerad måste också ha .NET Framework 4,6 installerat för att MQ-kopplingen ska fungera.

  När du har installerat gatewayen måste du också skapa en resurs i Azure för den lokala datagatewayen. Mer information finns i [Konfigurera data Gateway-anslutningen](../logic-apps/logic-apps-gateway-connection.md).

  Om din MQ-Server är offentligt tillgänglig eller tillgänglig i Azure behöver du inte använda data gatewayen.

* Den Logic app där du vill lägga till åtgärden MQ. Den här Logic-appen måste använda samma plats som din lokala datagateway-anslutning och måste redan ha en utlösare som startar arbets flödet.

  MQ-anslutningen har inga utlösare, så du måste först lägga till en utlösare i din Logic app. Du kan till exempel använda upprepnings utlösaren. Om du inte har använt Logic Apps igen kan du prova den här [snabb starten för att skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>Skapa MQ-anslutning

Om du inte redan har en MQ-anslutning när du lägger till en MQ-åtgärd uppmanas du att skapa anslutningen, till exempel:

![Ange anslutnings information](media/connectors-create-api-mq/connection-properties.png)

1. Om du ansluter till en lokal MQ-server väljer du **Anslut via lokal datagateway**.

1. Ange anslutnings informationen för din MQ-Server.

   * För **Server**kan du ange namnet på MQ-servern eller ange IP-adressen följt av ett kolon och port numret.

   * Om du vill använda Secure Sockets Layer (SSL) väljer du **Aktivera SSL?**.

     MQ-anslutaren stöder för närvarande endast serverautentisering, inte klientautentisering. Mer information finns i [problem med anslutning och autentisering](#connection-problems).

1. I avsnittet **Gateway** följer du dessa steg:

   1. I listan **prenumeration** väljer du den Azure-prenumeration som är associerad med din Azure gateway-resurs.

   1. Välj den Azure gateway-resurs som du vill använda från listan **anslutnings-Gateway** .

1. När du är klar väljer du **Skapa**.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Problem med anslutning och autentisering

När din Logi Kap par försöker ansluta till din lokala MQ-Server kan du få följande fel meddelande:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Om du använder MQ-anslutaren direkt i Azure måste MQ-servern använda ett certifikat som utfärdats av en betrodd [certifikat utfärdare](https://www.ssl.com/faqs/what-is-a-certificate-authority/).

* Om du använder den lokala datagatewayen kan du försöka använda ett certifikat som utfärdats av en betrodd [certifikat utfärdare](https://www.ssl.com/faqs/what-is-a-certificate-authority/) när det är möjligt. Men om det här alternativet inte är möjligt kan du använda ett självsignerat certifikat, som inte har utfärdats av en betrodd [certifikat utfärdare](https://www.ssl.com/faqs/what-is-a-certificate-authority/) och anses vara mindre säkert.

  Om du vill installera serverns självsignerade certifikat kan du använda verktyget **Windows Certification Manager** (certmgr. msc). I det här scenariot måste du installera certifikatet i den **lokala datorns** certifikat Arkiv på nivån **betrodda rot certifikat utfärdare** på den lokala datorn där den lokala data Gateway-tjänsten körs.

  1. Öppna Start-menyn på datorn där den lokala data Gateway-tjänsten körs, leta upp och välj **hantera användar certifikat**.

  1. När Windows Certificate Manager-verktyget har öppnats går du till mappen **certifikat – lokal dator**för  >   **betrodda rot certifikat utfärdare** och installerar certifikatet.

     > [!IMPORTANT]
     > Se till att du installerar certifikat i arkivet **certifikat – lokal dator**  >  **betrodd rot certifikat utfärdare** .

* MQ-servern kräver att du definierar den chiffersviter som du vill använda för SSL-anslutningar. SsLStream i .NET tillåter dock inte att du anger ordningen för chiffrering av specifikationer. För att undvika den här begränsningen kan du ändra konfigurationen för MQ-servern så att den matchar den första chiffer-specifikationen i den svit som anslutningen skickar i SSL-förhandlingen.

  När du försöker ansluta loggar MQ-servern ett händelse meddelande som anger att anslutningen misslyckades på grund av att den andra parten använde felaktig chiffrering-specifikation. Händelse meddelandet innehåller den cipher-specifikation som visas först i listan. Uppdatera cipher-specifikationen i kanal konfigurationen så att den matchar chiffer-specifikationen i händelse meddelandet.

## <a name="browse-single-message"></a>Bläddra i ett enskilt meddelande

1. I din Logic app, under utlösaren eller en annan åtgärd, väljer du **nytt steg**.

1. I sökrutan anger du `mq` och väljer åtgärden **Bläddra meddelande** .

   ![Välj åtgärd för att bläddra meddelande](media/connectors-create-api-mq/browse-message.png)

1. Om du inte redan har skapat en MQ-anslutning uppmanas du att [skapa anslutningen](#create-connection).

1. När du har skapat anslutningen ställer du in egenskaperna för åtgärden **Bläddra meddelande** :

   | Egenskap | Beskrivning |
   |----------|-------------|
   | **Kö** | Om det skiljer sig från den kö som anges i anslutningen anger du den kön. |
   | **Messageid**, **correlationId**, **Egenskaper**och andra egenskaper | Bläddra efter ett meddelande som baseras på de olika egenskaperna för MQ-meddelanden |
   | **IncludeInfo** | Om du vill inkludera ytterligare meddelande information i utdata väljer du **Sant**. Om du vill utelämna ytterligare meddelande information i utdata väljer du **falskt**. |
   | **Standardvärde** | Ange ett värde för att avgöra hur lång tid det tar innan ett meddelande kommer till en tom kö. Om inget anges hämtas det första meddelandet i kön och det finns ingen tids åtgång i väntan på att ett meddelande ska visas. |
   |||

   Till exempel:

   ![Egenskaper för åtgärden "Bläddra meddelande"](media/connectors-create-api-mq/browse-message-properties.png)

1. När du är klar väljer du **Spara**i verktygsfältet designer. Om du vill testa appen väljer du **Kör**.

   När körningen är klar visar designern arbets flödes stegen och deras status så att du kan granska utdata.

1. Om du vill visa information om varje steg klickar du på stegets namn List. Om du vill granska mer information om ett stegs utdata väljer du **Visa rå data**.

   ![Bläddra i meddelandets utdata](media/connectors-create-api-mq/browse-message-output.png)

   Här följer några exempel på rå utdata:

   ![Bläddra i meddelandets RAW-utdata](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Om du anger **IncludeInfo** till **Sant**visas ytterligare utdata:

   ![Bläddra bland meddelandet innehåller info](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Bläddra bland flera meddelanden

Åtgärden **Bläddra meddelanden** innehåller ett **batchSize** -alternativ för att ange hur många meddelanden som ska returneras från kön. Om **batchSize** inte har något värde returneras alla meddelanden. Returnerade utdata är en matris med meddelanden.

1. Följ föregående steg, men Lägg till åtgärden **Sök meddelanden** i stället.

1. Om du inte redan har skapat en MQ-anslutning uppmanas du att [skapa anslutningen](#create-connection). Annars används den första tidigare konfigurerade anslutningen som standard. Om du vill skapa en ny anslutning väljer du **ändra anslutning**. Eller Välj en annan anslutning.

1. Ange informationen för åtgärden.

1. Spara och kör Logic-appen.

   När Logic-appen har körts klart är följande exempel på utdata från åtgärden **Bläddra meddelanden** :

   ![Exempel på utdata från "Bläddra meddelanden"](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Ta emot ett enskilt meddelande

Åtgärden **ta emot meddelande** har samma indata och utdata som åtgärd för att **söka efter meddelanden** . När **du använder meddelandet tas meddelandet bort**från kön.

## <a name="receive-multiple-messages"></a>Ta emot flera meddelanden

Åtgärden **ta emot meddelanden** har samma indata och utdata som åtgärd för att **söka meddelanden** . När du använder **mottagna meddelanden**tas meddelandena bort från kön.

> [!NOTE]
> När du kör en browse-eller Receive-åtgärd i en kö som inte har några meddelanden, Miss lyckas åtgärden med följande utdata:
>
> ![Felet "inget meddelande" i MQ](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Skicka meddelande

1. Följ föregående steg, men Lägg till åtgärden **Skicka meddelande** i stället.

1. Om du inte redan har skapat en MQ-anslutning uppmanas du att [skapa anslutningen](#create-connection). Annars används den första tidigare konfigurerade anslutningen som standard. Om du vill skapa en ny anslutning väljer du **ändra anslutning**. Eller Välj en annan anslutning.

1. Ange informationen för åtgärden. För **MessageType**väljer du en giltig meddelande typ: **datagram**, **Reply**eller **Request**

   ![Egenskaper för "Skicka meddelande åtgärd"](media/connectors-create-api-mq/send-message-properties.png)

1. Spara och kör Logic-appen.

   När Logic-appen har körts klart är följande exempel på utdata från åtgärden **Skicka meddelande** :

   ![Exempel på "Skicka meddelande"-utdata](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om åtgärder och begränsningar, som beskrivs av kopplingens Swagger beskrivning, finns på kopplingens [referens sida](/connectors/mq/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)

---
title: Ansluta till IBM MQ-server
description: Skicka och hämta meddelanden med en Azure- eller lokalt IBM MQ-server och Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 03/31/2020
tags: connectors
ms.openlocfilehash: 737c5b90b216156ca08346f4a64fd0b421ad6c19
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410277"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Ansluta till en IBM MQ-server från Azure Logic Apps

IBM MQ-anslutningen skickar och hämtar meddelanden som lagras i en IBM MQ-server lokalt eller i Azure. Den här anslutningen innehåller en Microsoft MQ-klient som kommunicerar med en fjärr-IBM MQ-server över ett TCP/IP-nätverk. Den här artikeln innehåller en startguide för att använda MQ-kontakten. Du kan börja med att bläddra i ett enskilt meddelande i en kö och sedan prova andra åtgärder.

IBM MQ-anslutningen innehåller dessa åtgärder men ger inga utlösare:

- Bläddra bland ett enda meddelande utan att ta bort meddelandet från IBM MQ-servern.
- Bläddra bland en grupp meddelanden utan att ta bort meddelanden från IBM MQ-servern.
- Ta emot ett enda meddelande och ta bort meddelandet från IBM MQ-servern.
- Ta emot en bunt meddelanden och ta bort meddelandena från IBM MQ-servern.
- Skicka ett enda meddelande till IBM MQ-servern.

Här är de officiellt stödda IBM WebSphere MQ-versionerna:

  * MQ 7,5
  * MQ 8,0
  * MQ 9,0

## <a name="prerequisites"></a>Krav

* Om du använder en lokal MQ-server [installerar du den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) på en server i nätverket. Servern där den lokala datagatewayen är installerad måste också ha .NET Framework 4.6 installerat för att MQ-anslutningen ska fungera.

  När du har installerat gatewayen måste du också skapa en resurs i Azure för den lokala datagatewayen. Mer information finns i [Konfigurera datagatewayanslutningen](../logic-apps/logic-apps-gateway-connection.md).

  Om din MQ-server är allmänt tillgänglig eller tillgänglig i Azure behöver du inte använda datagatewayen.

* Logikappen där du vill lägga till MQ-åtgärden. Den här logikappen måste använda samma plats som din lokala datagatewayanslutning och måste redan ha en utlösare som startar arbetsflödet.

  MQ-kopplingen har inga utlösare, så du måste lägga till en utlösare i logikappen först. Du kan till exempel använda utlösaren Återkommande. Om du inte har tidigare i logikappar kan du prova den här [snabbstarten för att skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>Skapa MQ-anslutning

Om du inte redan har en MQ-anslutning när du lägger till en MQ-åtgärd uppmanas du att skapa anslutningen, till exempel:

![Ange anslutningsinformation](media/connectors-create-api-mq/connection-properties.png)

1. Om du ansluter till en lokal MQ-server väljer du **Anslut via lokal datagateway**.

1. Ange anslutningsinformation för MQ-servern.

   * För **Server**kan du ange MQ-servernamnet eller ange IP-adressen följt av ett kolon och portnumret.

   * Om du vill använda SSL (Secure Sockets Layer) väljer du **Aktivera SSL?**.

     MQ-anslutningen stöder för närvarande endast serverautentisering, inte klientautentisering. Mer information finns i [Problem med anslutning och autentisering](#connection-problems).

1. Gör så här i **avsnittet gateway:**

   1. Välj den Azure-prenumeration som är associerad med din Azure gateway-resurs i listan **Prenumeration.**

   1. Välj den Azure gateway-resurs som du vill använda i listan **Anslutningsgateway.**

1. När du är klar väljer du **Skapa**.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Problem med anslutning och autentisering

När logikappen försöker ansluta till den lokala MQ-servern kan du få det här felet:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Om du använder MQ-anslutningen direkt i Azure måste MQ-servern använda ett certifikat som utfärdas av en betrodd [certifikatutfärdar .](https://www.ssl.com/faqs/what-is-a-certificate-authority/)

* Om du använder den lokala datagatewayen kan du försöka använda ett certifikat som utfärdas av en betrodd [certifikatutfärdar när](https://www.ssl.com/faqs/what-is-a-certificate-authority/) det är möjligt. Om det här alternativet inte är möjligt kan du använda ett självsignerat certifikat som inte utfärdas av en betrodd [certifikatutfärdare](https://www.ssl.com/faqs/what-is-a-certificate-authority/) och anses vara mindre säkert.

  Om du vill installera serverns självsignerade certifikat kan du använda **windowscertifikathanteraren** (certmgr.msc). I det här fallet på den lokala datorn där den lokala datagateway-tjänsten körs måste du installera certifikatet i certifikatarkivet **för lokala datorer** på nivån **Betrodda rotcertifikatutfärdare.**

  1. Öppna startmenyn på datorn där den lokala datagatewaytjänsten körs, söka efter och välja **Hantera användarcertifikat**.

  1. När windows-certifieringshanteraren har **öppnats**går du till mappen Certifikat – >  **betrodda rotcertifikatutfärdare** för lokal dator och installerar certifikatet.

     > [!IMPORTANT]
     > Kontrollera att du installerar certifikat i arkivet Certifikat –**betrodda rotcertifikatutfärdare** för **lokal dator.** > 

* MQ-servern kräver att du definierar den chifferspecifikation som du vill använda för SSL-anslutningar. SsLStream i .NET tillåter dock inte att du anger ordningen för chifferspecifikationer. Om du vill begränsa den här begränsningen kan du ändra MQ-serverkonfigurationen så att den matchar den första chifferspecifikationen i paketet som anslutningen skickar i SSL-förhandlingen.

  NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶kning fÃ¶rsÃ¤ntar MQ-servern ett händelsemeddelande som anger att anslutningen misslyckades eftersom den andra änden anvÃ¤nder den felaktiga chifferspecifikationen. Händelsemeddelandet innehåller den chifferspecifikation som visas först i listan. Uppdatera chifferspecifikationen i kanalkonfigurationen så att den matchar chifferspecifikationen i händelsemeddelandet.

## <a name="browse-single-message"></a>Bläddra bland ett enda meddelande

1. Välj **Nytt steg**under utlösaren eller en annan åtgärd i logikappen.

1. Skriv in `mq`i sökrutan och välj åtgärden **Bläddra i meddelande.**

   ![Välj åtgärd "Bläddra meddelande"](media/connectors-create-api-mq/browse-message.png)

1. Om du inte redan har skapat en MQ-anslutning uppmanas du att [skapa anslutningen](#create-connection).

1. När du har skapat anslutningen ställer du in åtgärden **Bläddra meddelandes** egenskaper:

   | Egenskap | Beskrivning |
   |----------|-------------|
   | **Kö** | Om du skiljer dig från den kö som anges i anslutningen anger du den kön. |
   | **MessageId,** **CorrelationId,** **GroupId**och andra egenskaper | Bläddra efter ett meddelande som baseras på de olika MQ-meddelandeegenskaperna |
   | **InkluderaInfo** | Om du vill inkludera ytterligare meddelandeinformation i utdata väljer du **sant**. Om du vill utelämna ytterligare meddelandeinformation i utdata väljer du **false**. |
   | **Timeout** | Ange ett värde för att bestämma hur länge ett meddelande ska komma fram i en tom kö. Om inget anges hämtas det första meddelandet i kön och det finns ingen tid att vänta på att ett meddelande ska visas. |
   |||

   Ett exempel:

   ![Egenskaper för åtgärden "Bläddra meddelande"](media/connectors-create-api-mq/browse-message-properties.png)

1. När du är klar väljer du **Spara**i designerverktygsfältet . Om du vill testa appen väljer du **Kör**.

   När körningen är klar visar designern arbetsflödesstegen och deras status så att du kan granska utdata.

1. Om du vill visa information om varje steg klickar du på stegets namnlist. Om du vill granska mer information om ett stegs utdata väljer du **Visa råutdata**.

   ![Bläddra bland meddelandeutdata](media/connectors-create-api-mq/browse-message-output.png)

   Här är några exempel råutgång:

   ![Bläddra bland råutdata för meddelanden](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Om du anger **IncludeInfo** till **true**visas ytterligare utdata:

   ![Bläddra bland meddelande inkluderar information](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Bläddra bland flera meddelanden

Åtgärden **Bläddra bland meddelanden** innehåller alternativet **BatchSize** för att ange hur många meddelanden som ska returneras från kön. Om **BatchSize** inte har något värde returneras alla meddelanden. Den returnerade utdata är en matris med meddelanden.

1. Följ föregående steg, men lägg till åtgärden **Bläddra i meddelanden** i stället.

1. Om du inte redan har skapat en MQ-anslutning uppmanas du att [skapa anslutningen](#create-connection). Annars används som standard den första tidigare konfigurerade anslutningen. Om du vill skapa en ny anslutning väljer du **Ändra anslutning**. Du kan också välja en annan anslutning.

1. Ange information för åtgärden.

1. Spara och kör logikappen.

   När logikappen har körts är det här några exempelutdata från åtgärden **Bläddra meddelanden:**

   ![Exempel på utdata för "Bläddra bland meddelanden"](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Ta emot ett meddelande

Åtgärden **Ta emot meddelande** har samma in- och utdata som åtgärden Bläddra igenom **meddelande.** När du använder **Ta emot meddelande**tas meddelandet bort från kön.

## <a name="receive-multiple-messages"></a>Ta emot flera meddelanden

Åtgärden **Ta emot meddelanden** har samma in- och utdata som åtgärden Bläddra bland **meddelanden.** När du använder **Ta emot meddelanden**tas meddelandena bort från kön.

> [!NOTE]
> När du kör en bläddring eller en mottagningsåtgärd i en kö som inte har några meddelanden misslyckas åtgärden med den här utdata:
>
> ![MQ "inget meddelande" fel](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Skicka meddelande

1. Följ föregående steg, men lägg till åtgärden **Skicka meddelande** i stället.

1. Om du inte redan har skapat en MQ-anslutning uppmanas du att [skapa anslutningen](#create-connection). Annars används som standard den första tidigare konfigurerade anslutningen. Om du vill skapa en ny anslutning väljer du **Ändra anslutning**. Du kan också välja en annan anslutning.

1. Ange information för åtgärden. För **MessageType**väljer du en giltig meddelandetyp: **Datagram,** **Svara**eller **Begär**

   ![Egenskaper för "Skicka meddelandeåtgärd"](media/connectors-create-api-mq/send-message-properties.png)

1. Spara och kör logikappen.

   När logikappen har körts är det här några exempelutdata från åtgärden **Skicka meddelande:**

   ![Exempel på utdata för "Skicka meddelande"](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om åtgärder och begränsningar, som beskrivs i kopplingens Swagger-beskrivning, läser du kopplingens [referenssida](/connectors/mq/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)

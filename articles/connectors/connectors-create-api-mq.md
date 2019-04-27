---
title: Ansluta till MQ-server – Azure Logic Apps | Microsoft Docs
description: Skicka och ta emot meddelanden med en Azure eller lokala MQ-server och Azure Logic Apps
author: valrobb
ms.author: valthom
ms.date: 06/01/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 9e6ae5cb0afd75a1e87fe4d4d0cf307abab5a02a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60688867"
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>Ansluta till en IBM MQ-server från logikappar med MQ-kopplingen

Microsoft Connector för MQ skickar och hämtar meddelanden som lagras i en MQ lokalt eller i Azure. Denna koppling inkluderar en Microsoft MQ-klient som kommunicerar med en fjärransluten IBM MQ-server i ett TCP/IP-nätverk. Det här dokumentet är en startguide använda MQ-anslutningen. Vi rekommenderar att du börjar genom att bläddra ett enskilt meddelande i en kö och sedan testa de andra åtgärderna.

MQ-koppling inkluderar följande åtgärder. Det finns inga utlösare.

- Bläddra ett enskilt meddelande utan att ta bort meddelandet från IBM MQ-Server
- Bläddra bland en grupp med meddelanden utan att ta bort meddelandena från IBM MQ-Server
- Ett enda meddelande och ta bort meddelandet från IBM MQ-Server
- Ta emot en grupp med meddelanden och ta bort meddelandena från IBM MQ-Server
- Skicka ett enda meddelande till IBM MQ-Server

## <a name="prerequisites"></a>Nödvändiga komponenter

* Om du använder en lokal MQ-server [installera den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) på en server i nätverket. Om MQ-Server är offentligt tillgängligt, eller som är tillgängligt i Azure, sedan datagateway inte används eller krävs.

    > [!NOTE]
    > Den server där den lokala Datagatewayen har installerats måste också ha .NET Framework 4.6 installerat för MQ-anslutningen ska fungera.

* Skapa Azure-resursen för den lokala datagatewayen - [konfigurera dataanslutningen för gateway](../logic-apps/logic-apps-gateway-connection.md).

* Officiellt IBM WebSphere MQ-versioner som stöds:
    * MQ 7.5
    * MQ 8.0

## <a name="create-a-logic-app"></a>Skapa en logikapp

1. I den **Azure starta tavla**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, till exempel MQTestApp, **prenumeration**, **resursgrupp**, och **plats** (använda platsen där lokalt Data Gateway-anslutning har konfigurerats). Välj **fäst på instrumentpanelen**, och välj **skapa**.  
![Skapa Logikapp](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>Lägg till en utlösare

> [!NOTE]
> MQ-anslutningen har inte utlösare. Därför använder ännu en utlösare för att starta logikappen, som den **upprepning** utlösaren.

1. Den **Logic Apps Designer** öppnas, Välj **upprepning** i listan över vanliga utlösare.
2. Välj **redigera** inom Upprepningsutlösaren.
3. Ange den **frekvens** till **dag**, och ange den **intervall** till **7**.

## <a name="browse-a-single-message"></a>Bläddra ett enskilt meddelande
1. Välj **+ nytt steg**, och välj **Lägg till en åtgärd**.
2. I sökrutan skriver `mq`, och välj sedan **MQ - meddelande för Bläddra**.  
![Bläddra meddelande](media/connectors-create-api-mq/Browse_message.png)

3. Om det inte finns en befintlig MQ-anslutning, skapar du anslutningen:  

    1. Välj **Anslut via lokal datagateway**, och ange egenskaperna för MQ-server.  
    För **Server**, du kan ange MQ-servernamnet eller ange IP-adress följt av ett kolon och portnumret.
    2. Den **gateway** listrutan visar en lista över befintliga gatewayanslutningar som har konfigurerats. Välj din gateway.
    3. Välj **Skapa** när du är klar. Anslutningen ser ut ungefär så här:  
    ![Anslutningsegenskaper](media/connectors-create-api-mq/Connection_Properties.png)

4. I egenskaperna för åtgärd kan du:  

    * Använd den **kö** egenskapen att få åtkomst till en annan könamn än det som definieras i anslutningen
    * Använd den **MessageId**, **CorrelationId**, **GroupId**, och andra egenskaper och bläddra efter ett meddelande baserat på olika egenskaper för MQ-meddelande
    * Ange **IncludeInfo** till **SANT** att inkludera information om ytterligare meddelande i utdata. Eller ange den till **FALSKT** att inte inkludera information om ytterligare meddelande i utdata.
    * Ange en **Timeout** värde för att bestämma hur lång tid att vänta tills ett meddelande tas emot i en tom kö. Om inget anges det första meddelandet i kön hämtas och det finns ingen tid att vänta tills meddelandet visas.  
    ![Bläddra meddelandeegenskaper](media/connectors-create-api-mq/Browse_message_Props.png)

5. **Spara** dina ändringar och sedan **kör** logikappen:  
![Spara och kör](media/connectors-create-api-mq/Save_Run.png)

6. Stegen i körningen visas efter några sekunder och du kan titta på utdata. Välj den gröna bockmarkeringen för att visa information om varje steg. Välj **finns i råutdata** att visa ytterligare information om utdata.  
![Bläddra meddelande utdata](media/connectors-create-api-mq/Browse_message_output.png)  

    Rå utdata:  
    ![Bläddra meddelande råutdata](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. När den **IncludeInfo** alternativet är inställt på true, visas följande utdata:  
![Bläddra-meddelandet inkluderar information](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Bläddra bland flera meddelanden
Den **Bläddra meddelanden** åtgärd innehåller en **BatchSize** alternativ för att ange hur många meddelanden som ska returneras från kön.  Om **BatchSize** har ingen post returneras alla meddelanden. Returnerade utdata är en matris med meddelanden.

1. När du lägger till den **Bläddra meddelanden** åtgärden, den första anslutningen som har konfigurerats är valt som standard. Välj **ändra anslutning** att skapa en ny anslutning eller välj en annan anslutning.

2. Utdata från Bläddra meddelanden visas:  
![Bläddra meddelanden utdata](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>Ett enda meddelande
Den **ta emot meddelandet** åtgärden har samma indata och utdata som den **Bläddra meddelande** åtgärd. När du använder **ta emot meddelandet**, meddelandet tas bort från kön.

## <a name="receive-multiple-messages"></a>Ta emot flera meddelanden
Den **ta emot meddelanden** åtgärden har samma indata och utdata som den **Bläddra meddelanden** åtgärd. När du använder **ta emot meddelanden**, meddelanden tas bort från kön.

Om det finns inga meddelanden i kön när du gör en Bläddra eller mottagning kan misslyckas steget med följande utdata:  
![MQ Nej meddelande fel](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>Skicka ett meddelande
1. När du lägger till den **skicka meddelande** åtgärden, den första anslutningen som har konfigurerats är valt som standard. Välj **ändra anslutning** att skapa en ny anslutning eller välj en annan anslutning. Det giltiga **meddelandetyper** är **Datagram**, **svara**, eller **begära**.  
![Skicka Msg sammanställer](media/connectors-create-api-mq/Send_Msg_Props.png)

2. Utdata för Skicka meddelande som ser ut som följande:  
![Skicka Msg utdata](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>Information om specifika

Visa alla utlösare och åtgärder som definierats i swagger och får även eventuella gränser i den [anslutningsinformationen](/connectors/mq/).

## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Utforska andra tillgängliga anslutningsappar i Logic Apps på vår [API: er lista](apis-list.md).

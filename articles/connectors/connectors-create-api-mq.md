---
title: Ansluta till servern för MQ - Azure Logic Apps | Microsoft Docs
description: Skicka och hämta meddelanden med en Azure eller lokalt MQ-server och Azure Logic Apps
author: valthom
manager: cfowler
ms.author: valthom
ms.date: 06/01/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 0877d0e9394d1a54eae5482001bd53da0e1a83b0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34609456"
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>Ansluta till en IBM MQ-server från logikappar med MQ-koppling 

Microsoft Connector för MQ skickar och tar emot meddelanden som lagras i ett MQ-Server lokalt eller i Azure. Denna koppling inkluderar en Microsoft MQ-klient som kommunicerar med en fjärransluten IBM MQ-server i ett TCP/IP-nätverk. Det här dokumentet är en start-guide för att använda anslutningstjänsten MQ. Vi rekommenderar att du börjar genom att bläddra ett enda meddelande i en kö och försök andra åtgärder.    

MQ-koppling inkluderar följande åtgärder. Det finns inga utlösare.

-   Bläddra i ett enda meddelande utan att ta bort meddelandet från IBM MQ-Server
-   Bläddra i en grupp med meddelanden utan att ta bort meddelandena från IBM MQ-Server
-   Ett enda meddelande och ta bort meddelandet från IBM MQ-Server
-   Ta emot en grupp med meddelanden och ta bort meddelandena från IBM MQ-Server
-   Skicka ett meddelande till IBM MQ-Server 

## <a name="prerequisites"></a>Förutsättningar

* Om du använder en lokal MQ-server [installera lokala datagateway](../logic-apps/logic-apps-gateway-install.md) på en server i nätverket. Om MQ-Server är offentligt tillgänglig, eller tillgängliga i Azure, sedan datagateway inte används eller krävs.

    > [!NOTE]
    > Den server där lokala Data Gateway har installerats måste också ha .net Framework 4.6 installerad för MQ-anslutningen ska fungera.

* Skapa Azure-resurs för lokala datagateway - [konfigurera dataanslutningen gateway](../logic-apps/logic-apps-gateway-connection.md).

* Officiellt IBM WebSphere MQ-versioner som stöds:
   * MQ 7.5
   * MQ 8.0

## <a name="create-a-logic-app"></a>Skapa en logikapp

1. I den **Azure startar board**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**. 
2. Ange den **namn**, till exempel MQTestApp, **prenumeration**, **resursgruppen**, och **plats** (använda den plats där den lokala Data Gateway-anslutningen har konfigurerats). Välj **fäst på instrumentpanelen**, och välj **skapa**.  
![Skapa Logikapp](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>Lägg till en utlösare

> [!NOTE]
> MQ-anslutningen har inte utlösare. Använda en annan utlösare så för att starta din logikapp som den **återkommande** utlösare. 

1. Den **Logic Apps Designer** öppnas väljer **återkommande** i listan över vanliga utlösare.
2. Välj **redigera** i återkommande utlösare. 
3. Ange den **frekvens** till **dag**, och ange den **intervall** till **7**. 

## <a name="browse-a-single-message"></a>Bläddra i ett enda meddelande
1. Välj **+ nytt steg**, och välj **lägga till en åtgärd**.
2. I sökrutan skriver `mq`, och välj sedan **MQ - Bläddra meddelandet**.  
![Bläddra meddelande](media/connectors-create-api-mq/Browse_message.png)

3. Om det inte finns en befintlig MQ-anslutning, skapar du anslutningen:  

    1. Välj **Anslut via lokala datagateway**, och ange egenskaperna för MQ-server.  
    För **Server**, du kan ange namnet på MQ-server eller ange IP-adressen följt av ett kolon och portnumret. 
    2. Den **gateway** listan visar alla befintliga gateway-anslutningar som har konfigurerats. Välj din gateway.
    3. Välj **Skapa** när du är klar. Anslutningen ser ut ungefär så här:   
    ![Anslutningsegenskaper](media/connectors-create-api-mq/Connection_Properties.png)

4. I Egenskaper för åtgärden kan du:  

    * Använd den **kön** egenskapen åtkomst till en annan kö namn än vad som har definierats i anslutningen
    * Använd den **MessageId**, **CorrelationId**, **GroupId**, och andra egenskaper för att leta efter ett meddelande utifrån olika egenskaper för MQ-meddelande
    * Ange **IncludeInfo** till **SANT** att inkludera ytterligare meddelandeinformation i utdata. Eller anger **FALSKT** att inte inkludera ytterligare meddelandeinformation i utdata.
    * Ange en **Timeout** värde för att bestämma hur lång tid att vänta på ett meddelande som ska tas emot i en tom kö. Om inget anges det första meddelandet i kön har hämtats och det finns ingen tid väntar på att ett meddelande ska visas.  
    ![Bläddra meddelandeegenskaper](media/connectors-create-api-mq/Browse_message_Props.png)

5. **Spara** dina ändringar och sedan **kör** logikappen:  
![Spara och köra](media/connectors-create-api-mq/Save_Run.png)

6. Efter några sekunder visas hur du kör och titta på utdata. Välj en grön markering visas detaljerad information om varje steg. Välj **finns rådata utdata** vill visa ytterligare information om utdata.  
![Bläddra meddelandet utdata](media/connectors-create-api-mq/Browse_message_output.png)  

    Rådata utdata:  
    ![Bläddra meddelandet raw-utdata](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. När den **IncludeInfo** alternativet är inställt på true, visas följande utdata:  
![Bläddra meddelandet innehålla info](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Bläddra flera meddelanden
Den **Bläddra meddelanden** åtgärden inkluderar en **BatchSize** alternativ för att ange hur många meddelanden som ska returneras från kön.  Om **BatchSize** har ingen post returneras alla meddelanden. Det returnerade resultatet är en matris av meddelanden.

1. När du lägger till den **Bläddra meddelanden** åtgärd, den första anslutningen har konfigurerats är markerad som standard. Välj **ändra anslutningen** att skapa en ny anslutning eller välj en annan anslutning.

2. Utdata från Bläddra meddelanden visas:  
![Bläddra meddelanden utdata](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>Ett enda meddelande
Den **ta emot meddelandet** åtgärden har samma indata och utdata som den **Bläddra meddelandet** åtgärd. När du använder **ta emot meddelandet**, tas meddelandet bort från kön.

## <a name="receive-multiple-messages"></a>Ta emot flera meddelanden
Den **ta emot meddelanden** åtgärden har samma indata och utdata som den **Bläddra meddelanden** åtgärd. När du använder **ta emot meddelanden**, meddelandena tas bort från kön.

Om det finns inga meddelanden i kön när du gör en Bläddra eller en receive, misslyckas steget med följande utdata:  
![MQ Nej visas fel](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>Skicka ett meddelande
1. När du lägger till den **skickar ett meddelande** åtgärd, den första anslutningen har konfigurerats är markerad som standard. Välj **ändra anslutningen** att skapa en ny anslutning eller välj en annan anslutning. Det giltiga **meddelandetyper** är **Datagram**, **svar**, eller **begära**.  
![Skicka meddelande sammanställer](media/connectors-create-api-mq/Send_Msg_Props.png)

2. Utdata för Skicka meddelande ser ut som följande:  
![Skicka meddelande](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/mq/).

## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Utforska andra tillgängliga kopplingar i Logic Apps på vår [API: er listan](apis-list.md).

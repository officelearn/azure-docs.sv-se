---
title: IoT fjärrövervakning och aviseringar med Azure Logic Apps | Microsoft Docs
description: Använd Azure Logic Apps för övervakning av IoT temperatur på IoT hub och automatiskt skicka e-postmeddelanden till din postlåda för eventuella avvikelser som har identifierats.
author: rangv
manager: ''
keywords: IOT övervakningsaviseringar, iot, övervakning av iot temperatur
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: adda4e948c11f84517b1e8dd01e6cfe42155e1ca
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409449"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT fjärrövervakning och aviseringar med Azure Logic Apps ansluter dina IoT-hubb och postlåda

![Slutpunkt till slutpunkt-diagram](media/iot-hub-get-started-e2e-diagram/7.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Med Azure Logic Apps är ett sätt att automatisera processer som en serie steg. En logikapp kan ansluta mellan olika tjänster och protokoll. Det börjar med en utlösare som ”när ett konto läggs”, och följt av en kombination av åtgärder, något som ”skicka push-meddelanden”. Den här funktionen gör Logic Apps en perfekt IoT-lösning för IoT övervakning, till exempel att aviseringen efter avvikelser, bland andra Användningsscenarier.

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig hur du skapar en logikapp som ansluter din IoT-hubb och postlådan för temperaturövervakning och aviseringar. När temperaturen är över 30 C, klientprogrammet markerar `temperatureAlert = "true"` i meddelandet som skickas till din IoT-hubb. Meddelandet utlöser logikappen så att den skickar dig ett e-postmeddelande.

## <a name="what-you-do"></a>Vad du gör

* Skapa en service bus-namnområde och Lägg till en kö.
* Lägga till en slutpunkt och en regel för vidarebefordran till din IoT hub.
* Skapa, konfigurera och testa en logikapp.

## <a name="what-you-need"></a>Vad du behöver

* Självstudien [konfigurera enheten](iot-hub-raspberry-pi-kit-node-get-started.md) slutförts som omfattar följande krav:
  * En aktiv Azure-prenumeration.
  * Azure IoT hub i din prenumeration.
  * Ett klientprogram som skickar meddelanden till din Azure IoT hub.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Skapa service bus-namnområde och Lägg till en kö

### <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för service bus

1. På den [Azure-portalen](https://portal.azure.com/), klickar du på **skapa en resurs** > **Företagsintegrering** > **Service Bus**.
1. Ange följande information:

   **Namn på**: namnet på service bus.

   **Prisnivå**: Klicka på **grundläggande** > **Välj**. Basic-nivån är tillräcklig för den här självstudien.

   **Resursgrupp**: Använd samma resursgrupp som din IoT-hubb använder.

   **Plats**: använda samma plats som använder din IoT-hubb.
1. Klicka på **Skapa**.

   ![Skapa en service bus-namnområde i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Lägg till en service bus-kö

1. Öppna service bus-namnområdet och klicka sedan på **+ kö**.
1. Ange ett namn för kön och klicka sedan på **skapa**.
1. Öppna service bus-kö och klicka sedan på **principer för delad åtkomst** > **+ Lägg till**.
1. Ange ett namn för principen, kontrollera **hantera**, och klicka sedan på **skapa**.

   ![Lägg till en service bus-kö i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-rule-to-your-iot-hub"></a>Lägga till en slutpunkt och en regel för vidarebefordran till din IoT-hubb

### <a name="add-an-endpoint"></a>Lägga till en slutpunkt

1. Öppna din IoT-hubb, klicka på slutpunkter > + Lägg till.
1. Ange följande information:

   **Namn på**: namnet på slutpunkten.

   **Typ av slutpunkt**: Välj **Service Bus-kö**.

   **Service Bus-namnområdet**: Välj det namnområde som du skapade.

   **Service Bus-kö**: väljer du den kö som du skapade.
1. Klicka på **OK**.

   ![Lägga till en slutpunkt i din IoT-hubb i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Lägga till en hanteringsregel

1. Klicka på i din IoT-hubb **vägar** > **+ Lägg till**.
1. Ange följande information:

   **Namn på**: namnet på regel för vidarebefordran.

   **Datakällan**: Välj **DeviceMessages**.

   **Slutpunkten**: Välj den slutpunkt som du skapade.

   **Frågesträng**: Ange `temperatureAlert = "true"`.
1. Klicka på **Spara**.

   ![Lägg till en regel för vidarebefordran i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>Skapa och konfigurera en logikapp

### <a name="create-a-logic-app"></a>Skapa en logikapp

1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **skapa en resurs** > **Företagsintegrering** > **Logikapp**.
1. Ange följande information:

   **Namn på**: namnet på logikappen.

   **Resursgrupp**: Använd samma resursgrupp som din IoT-hubb använder.

   **Plats**: använda samma plats som använder din IoT-hubb.
1. Klicka på **Skapa**.

### <a name="configure-the-logic-app"></a>Konfigurera logikappen

1. Öppna logikappen som öppnas i Logic Apps Designer.
1. I Logic Apps Designer klickar du på **tom Logikapp**.

   ![Börja med en tom logikapp i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

1. Klicka på **Service Bus**.

   ![Välj Service Bus för att börja skapa din logikapp i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Klicka på **Service Bus – när en eller flera meddelanden anländer i en kö (Komplettera automatiskt)**.
1. Skapa en service bus-anslutning.
   1. Ange ett namn på anslutningen.
   1. Klicka på service bus-namnområdet > service bus-princip > **skapa**.

      ![Skapa en service bus-anslutning för din logikapp i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Klicka på **Fortsätt** när service bus-anslutning har skapats.
   1. Välj den kö som du har skapat och ange `175` för **maximalt antal för meddelanden**

      ![Ange det maximala meddelandeantalet för service bus-anslutning i din logikapp](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)
   1. Klicka på ”Spara” för att spara ändringarna.

1. Skapa en anslutning för SMTP-tjänsten.
   1. Klicka på **nytt steg** > **Lägg till en åtgärd**.
   1. Typ `SMTP`, klickar du på den **SMTP** tjänsten i sökresultatet och klicka sedan på **SMTP - skicka e-post**.

      ![Skapa en SMTP-anslutning i din logikapp i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   1. Ange SMTP-information för din postlåda och klicka sedan på **skapa**.

      ![Ange SMTP-anslutningsinformation i din logikapp i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Få den SMTP-informationen för [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en), och [Yahoo e-post](https://help.yahoo.com/kb/SLN4075.html).
   1. Ange din e-postadress för **från** och **till**, och `High temperature detected` för **ämne** och **brödtext**.
   1. Klicka på **Spara**.

Logikappen är i fungerar korrekt när du sparar den.

## <a name="test-the-logic-app"></a>Testa logikappen

1. Starta det klientprogram som du distribuerar till din enhet i [ansluta ESP8266 till Azure IoT Hub](iot-hub-arduino-huzzah-esp8266-get-started.md).
1. Miljö temperatur runt SensorTag att vara högre än 30 C. Till exempel dra en stearinljusstapel runt dina SensorTag.
1. Du bör få ett e-postmeddelande som skickas av logikappen.

   > [!NOTE]
   > Din e-post-leverantör kan behöva kontrollera identiteten avsändaren att kontrollera att det är du som skickar e-postmeddelandet.

## <a name="next-steps"></a>Nästa steg

Du skapat har en logikapp som ansluter din IoT-hubb och postlådan för temperaturövervakning och aviseringar.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

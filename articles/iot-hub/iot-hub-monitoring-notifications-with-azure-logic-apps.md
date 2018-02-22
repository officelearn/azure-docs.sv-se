---
title: "IoT fjärrövervaknings och meddelanden med Azure Logikappar | Microsoft Docs"
description: "Använd Azure Logikappar för övervakning av IoT temperatur på din IoT-hubb och skicka e-postmeddelanden automatiskt till din postlåda efter eventuella avvikelser som upptäckts."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "IOT övervakningsaviseringar, iot, iot temperaturövervakning"
ms.assetid: 43043067-2e1f-42c9-953d-e2dce8fd86df
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: xshi
ms.openlocfilehash: 0caa3397723f9cd0476e85d52a8d30ae283b6e47
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT fjärrövervaknings och meddelanden med Azure Logikappar ansluta din IoT-hubb och postlåda

![Diagram för slutpunkt till slutpunkt](media/iot-hub-get-started-e2e-diagram/7.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Med Azure Logikappar är ett sätt att automatisera processer som en serie steg. En logikapp kan ansluta över olika tjänster och protokoll. Den börjar med en utlösare som ”när ett konto har lagts till”, och följas av en kombination av åtgärder, så som 'Skicka ett push-meddelande'. Den här funktionen gör Logic Apps en perfekt IoT-lösning för IoT övervakning, till exempel används avisering efter avvikelser bland andra Användningsscenarier.

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig hur du skapar en logikapp som ansluter din IoT-hubb och postlådan för temperaturövervakning och aviseringar. När temperaturen över 30 C klientprogrammet markerar `temperatureAlert = "true"` i meddelandet som skickas till din IoT-hubb. Meddelandet utlöser logikappen om du vill skicka ett e-postmeddelande.

## <a name="what-you-do"></a>Vad du gör

* Skapa ett namnområde för service bus och Lägg till en kö.
* Lägga till en slutpunkt och en routningsregel för din IoT-hubb.
* Skapa, konfigurera och testa en logikapp.

## <a name="what-you-need"></a>Vad du behöver

* Kursen [konfigurera enheten](iot-hub-raspberry-pi-kit-node-get-started.md) slutförts som omfattar följande krav:
  * En aktiv Azure-prenumeration.
  * En Azure IoT-hubb i din prenumeration.
  * Ett klientprogram som skickar meddelanden till din Azure IoT-hubb.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Skapa service bus-namnrymd och lägga till en kö

### <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för service bus

1. På den [Azure-portalen](https://portal.azure.com/), klickar du på **skapar du en resurs** > **Enterprise Integration** > **Service Bus**.
1. Ange följande information:

   **Namnet**: namnet på service bus.

   **Prisnivån**: Klicka på **grundläggande** > **Välj**. Den grundläggande nivån är tillräcklig för den här kursen.

   **Resursgruppen**: använda samma resursgrupp som använder din IoT-hubb.

   **Plats**: använda samma plats som använder din IoT-hubb.
1. Klicka på **Skapa**.

   ![Skapa ett namnområde för service bus i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Lägg till en service bus-kö

1. Öppna service bus-namnrymd och klicka sedan på **+ kö**.
1. Ange ett namn för kön och klicka sedan på **skapa**.
1. Öppna service bus-kö och klicka sedan på **principer för delad åtkomst** > **+ Lägg till**.
1. Ange ett namn för principen, kontrollera **hantera**, och klicka sedan på **skapa**.

   ![Lägg till en service bus-kö i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-rule-to-your-iot-hub"></a>Lägga till en slutpunkt och en regel för vidarebefordran till din IoT-hubb

### <a name="add-an-endpoint"></a>Lägga till en slutpunkt

1. Öppna din IoT-hubb, klicka på slutpunkter > + Lägg till.
1. Ange följande information:

   **Namnet**: namnet på slutpunkten.

   **Slutpunktstypen**: Välj **Service Bus-kö**.

   **Service Bus-namnrymd**: Markera det namnområde som du skapade.

   **Service Bus-kö**: väljer du den kö som du skapade.
1. Klicka på **OK**.

   ![Lägg till en slutpunkt i din IoT-hubb i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Lägg till en regel för vidarebefordran

1. Klicka på din IoT-hubb **vägar** > **+ Lägg till**.
1. Ange följande information:

   **Namnet**: namnet på regeln för routning.

   **Datakällan**: Välj **DeviceMessages**.

   **Slutpunkt**: Välj den slutpunkt som du skapade.

   **Frågesträng**: Ange `temperatureAlert = "true"`.
1. Klicka på **Spara**.

   ![Lägg till en regel för vidarebefordran i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>Skapa och konfigurera en logikapp

### <a name="create-a-logic-app"></a>Skapa en logikapp

1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **skapar du en resurs** > **Enterprise Integration** > **Logikapp**.
1. Ange följande information:

   **Namnet**: namnet på logikappen.

   **Resursgruppen**: använda samma resursgrupp som använder din IoT-hubb.

   **Plats**: använda samma plats som använder din IoT-hubb.
1. Klicka på **Skapa**.

### <a name="configure-the-logic-app"></a>Konfigurera logikappen

1. Öppna logikappen som öppnas i Logic Apps Designer.
1. I Logic Apps Designer klickar du på **tom Logikapp**.

   ![Börja med en tom logikapp i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

1. Klicka på **Service Bus**.

   ![Välj Service Bus för att skapa din logikapp i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Klicka på **Service Bus – när en eller flera meddelanden tas emot i en kö (automatisk komplettering)**.
1. Skapa en service bus-anslutning.
   1. Ange ett anslutningsnamn.
   1. Klicka på service bus-namnrymd > service bus princip > **skapa**.

      ![Skapa en service bus-anslutning för logikappen i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Klicka på **Fortsätt** när service bus-anslutning har skapats.
   1. Välj den kö som du skapade och ange `175` för **maximalt antal för meddelande**

      ![Ange det maximala antalet för service bus-anslutning i din logikapp](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)
   1. Klicka på ”Spara” för att spara ändringarna.

1. Skapa en SMTP-tjänst-anslutningen.
   1. Klicka på **nytt steg** > **lägga till en åtgärd**.
   1. Typen `SMTP`, klicka på den **SMTP** -tjänsten i sökresultatet och klicka sedan på **SMTP - skicka e-post**.

      ![Skapa en SMTP-anslutning i logikappen i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   1. Ange SMTP-information för din postlåda och klicka sedan på **skapa**.

      ![Ange SMTP-anslutningsinformation i logikappen i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Hämta SMTP-information för [Hotmail/Outlook.com](https://support.office.com/en-us/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en), och [Yahoo e-post](https://help.yahoo.com/kb/SLN4075.html).
   1. Ange din e-postadress för **från** och **till**, och `High temperature detected` för **ämne** och **brödtext**.
   1. Klicka på **Spara**.

Logikappen är fungerar när du sparar den.

## <a name="test-the-logic-app"></a>Testa logikappen

1. Starta klientprogrammet som du distribuerar till din enhet i [ansluta ESP8266 till Azure IoT Hub](iot-hub-arduino-huzzah-esp8266-get-started.md).
1. Miljö temperatur runt SensorTag vara över 30 C. Till exempel ljus en stearinljusstapel runt din SensorTag.
1. Du bör få ett e-postmeddelande som skickats av logikappen.

   > [!NOTE]
   > Din e-post-leverantör kan behöva kontrollera identiteten avsändaren att se till att det du som skickar e-postmeddelandet.

## <a name="next-steps"></a>Nästa steg

Du har skapat en logikapp som ansluter din IoT-hubb och postlådan för temperaturövervakning och aviseringar.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

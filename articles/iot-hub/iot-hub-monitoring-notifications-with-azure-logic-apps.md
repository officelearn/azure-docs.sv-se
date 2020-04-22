---
title: IoT fjärrövervakning och meddelanden med Azure Logic Apps | Microsoft-dokument
description: Använd Azure Logic Apps för IoT-temperaturövervakning på din IoT-hubb och skicka automatiskt e-postmeddelanden till postlådan för eventuella avvikelser som upptäckts.
author: robinsh
keywords: iot övervakning, iot anmälningar, iot temperatur övervakning
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: 2720f9acfa308294b30f9203ba80e3f9b426e1e9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680711"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT fjärrövervakning och meddelanden med Azure Logic Apps som ansluter din IoT-hubb och postlåda

![Diagram från på sluten tid](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) kan hjälpa dig att orkestrera arbetsflöden över lokala tjänster och molntjänster, ett eller flera företag och i olika protokoll. En logikapp börjar med en utlösare, som sedan följs av en eller flera åtgärder som kan sekvenseras med hjälp av inbyggda kontroller, till exempel villkor och iteratorer. Den här flexibiliteten gör Logic Apps till en idealisk IoT-lösning för IoT-övervakningsscenarier. Till exempel kan ankomsten av telemetridata från en enhet vid en IoT Hub-slutpunkt initiera logikapparbetsflöden för att lagra data i en Azure Storage-blob, skicka e-postaviseringar för att varna för dataavvikelser, schemalägga ett teknikerbesök om en enhet rapporterar ett fel och så vidare.

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig hur du skapar en logikapp som ansluter din IoT-hubb och din postlåda för temperaturövervakning och meddelanden.

Klientkoden som körs på enheten anger `temperatureAlert`en programegenskap, på varje telemetrimeddelande som skickas till din IoT-hubb. När klientkoden upptäcker en temperatur över 30 C `true`ställs den in den här egenskapen till ; Annars anger den egenskapen till `false`.

Meddelanden som anländer till din IoT-hubb liknar följande, med telemetridata som `temperatureAlert` finns i brödtexten och egenskapen i applikationsegenskaperna (systemegenskaper visas inte):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

Mer information om meddelandeformat för IoT Hub finns i [Skapa och läsa IoT Hub-meddelanden](iot-hub-devguide-messages-construct.md).

I det här avsnittet ställer du in routning på IoT-hubben för att skicka meddelanden där egenskapen `temperatureAlert` är `true` till en Service Bus-slutpunkt. Du kan sedan ställa in en logikapp som utlöser meddelanden som anländer till servicebussslutpunkten och skickar ett e-postmeddelande till dig.

## <a name="what-you-do"></a>Vad du gör

* Skapa ett servicebussnamnområde och lägg till en servicebusskö i den.
* Lägg till en anpassad slutpunkt och en routningsregel i IoT-hubben för att dirigera meddelanden som innehåller en temperaturavisering till servicebusskön.
* Skapa, konfigurera och testa en logikapp för att använda meddelanden från din Service Bus-kö och skicka e-postmeddelanden till en önskad mottagare.

## <a name="what-you-need"></a>Vad du behöver

* Slutför [Raspberry Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) handledning eller en av enheten tutorials; till exempel [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Dessa omfattar följande krav:

  * En aktiv Azure-prenumeration.
  * En Azure IoT-hubb under din prenumeration.
  * Ett klientprogram som körs på din enhet som skickar telemetrimeddelanden till din Azure IoT-hubb.

## <a name="create-service-bus-namespace-and-queue"></a>Skapa servicebussnamnområde och -kö

Skapa ett namnområde och en kö för Service Bus. Senare i det här avsnittet skapar du en routningsregel i IoT-hubben för att dirigera meddelanden som innehåller en temperaturavisering till Service Bus-kön, där de kommer att plockas upp av en logikapp och utlösa den för att skicka ett e-postmeddelande.

### <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för Service Bus

1. På [Azure-portalen](https://portal.azure.com/)väljer du + Skapa en**servicebuss****för resursintegrering** >  **+ Create a resource** > .

1. Ange följande information i fönstret **Skapa namnområde:**

   **Namn**: Namnet på servicebussens namnområde. Namnområdet måste vara unikt i Azure.

   **Prisnivå**: Välj **Basic** i listrutan. Basic-nivån är tillräcklig för den här självstudien.

   **Resursgrupp**: Använd samma resursgrupp som IoT-hubben använder.

   **Plats**: Använd samma plats som IoT-hubben använder.

   ![Skapa ett tjänstbussnamnområde i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Välj **Skapa**. Vänta tills distributionen har slutförts innan du går vidare till nästa steg.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Lägga till en servicebusskö i namnområdet

1. Öppna namnområdet Service Bus. Det enklaste sättet att komma till servicebussnamnområdet är att välja **Resursgrupper** i resursfönstret, välja resursgrupp och sedan välja servicebussnamnområdet i listan över resurser.

1. Välj **+ Kö**i fönstret **Servicebussnamnområde** .

1. Ange ett namn för kön och välj sedan **Skapa**. När kön har skapats stängs **köfönstret Skapa.**

   ![Lägga till en servicebusskö i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Tillbaka i **fönstret Service Bus Namespace** under **Entiteter**väljer **du Köer**. Öppna servicebusskön i listan och välj sedan **principer** > för delad åtkomst **+ Lägg till**.

1. Ange ett namn för principen, markera **Hantera**och välj sedan **Skapa**.

   ![Lägga till en tjänstbussköprincip i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Lägga till en anpassad slutpunkt och routningsregel i IoT-hubben

Lägg till en anpassad slutpunkt för servicebusskön i IoT-hubben och skapa en meddelanderoutningsregel för att dirigera meddelanden som innehåller en temperaturavisering till den slutpunkten, där de kommer att plockas upp av logikappen. Routningsregeln använder en `temperatureAlert = "true"`routningsfråga för att vidarebefordra `temperatureAlert` meddelanden baserat på värdet för den programegenskap som angetts av klientkoden som körs på enheten. Mer information finns i [Fråga om meddelanderoutning baserat på meddelandeegenskaper](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Lägga till en anpassad slutpunkt

1. Öppna din IoT-hubb. Det enklaste sättet att komma till IoT-hubben är att välja **Resursgrupper** från resursfönstret, välja resursgrupp och sedan välja din IoT-hubb i listan över resurser.

1. Under **Meddelande**väljer du **Meddelanderoutning**. Markera fliken **Anpassade slutpunkter** i fönstret **Meddelanderoutning** och välj sedan **+ Lägg till**. Välj **Busskö**i listrutan .

   ![Lägga till en slutpunkt i din IoT-hubb i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Ange följande information i fönstret **Lägg till en servicebuss:**

   **Slutpunktsnamn**: Slutpunktens namn.

   **Servicebussnamnområde:** Välj det namnområde som du skapade.

   **Servicebusskö:** Välj den kö du skapade.

   ![Lägga till en slutpunkt i din IoT-hubb i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Välj **Skapa**. När slutpunkten har skapats går du vidare till nästa steg.

### <a name="add-a-routing-rule"></a>Lägga till en hanteringsregel

1. Tillbaka i **fönstret Meddelanderoutning** väljer du fliken **Rutter** och väljer sedan + **Lägg till**.

1. Ange följande information i fönstret **Lägg till en rutt:**

   **Namn**: Namnet på routningsregeln.

   **Slutpunkt:** Välj den slutpunkt som du skapade.

   **Datakälla**: Välj **Enhetstelemetrimeddelanden**.

   **Routningsfråga** `temperatureAlert = "true"`: Ange .

   ![Lägga till en routningsregel i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Välj **Spara**. Du kan stänga **fönstret Meddelanderoutning.**

## <a name="create-and-configure-a-logic-app"></a>Skapa och konfigurera en logikapp

I föregående avsnitt ställer du in IoT-hubben för att dirigera meddelanden som innehåller en temperaturvarning till din Service Bus-kö. Nu kan du ställa in en logikapp för att övervaka servicebusskön och skicka ett e-postmeddelande när ett meddelande läggs till i kön.

### <a name="create-a-logic-app"></a>Skapa en logikapp

1. Välj Skapa en**resursintegrationslogikapp** > **Logic App** **Create a resource** > .

1. Ange följande information:

   **Namn**: Namnet på logikappen.

   **Resursgrupp**: Använd samma resursgrupp som IoT-hubben använder.

   **Plats**: Använd samma plats som IoT-hubben använder.

   ![Skapa en logikapp i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Välj **Skapa**.

### <a name="configure-the-logic-app-trigger"></a>Konfigurera logic app-utlösaren

1. Öppna logikappen. Det enklaste sättet att komma till logikappen är att välja **Resursgrupper** i resursfönstret, välja resursgrupp och sedan välja logikappen i listan över resurser. När du väljer logikappen öppnas Logic Apps Designer.

1. Bläddra ned till **Mallar** i Logic Apps Designer och välj **Tom Logic App**.

   ![Börja med en tom logikapp i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Välj fliken **Alla** och välj sedan **Servicebuss**.

   ![Välj Service Bus för att börja skapa logikappen i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Under **Utlösare**väljer du **När ett eller flera meddelanden anländer i en kö (slutför automatiskt).**

   ![Välj utlösaren för logikappen i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Skapa en servicebussanslutning.
   1. Ange ett anslutningsnamn och välj namnområdet Service Bus i listan. Nästa skärm öppnas.

      ![Skapa en tjänstbussanslutning för logikappen i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Välj servicebussprincipen (RootManageSharedAccessKey). Välj sedan **Skapa**.

      ![Skapa en tjänstbussanslutning för logikappen i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Välj den kö som du skapade i listrutan för **könamn**på den sista skärmen. Ange `175` för **maximalt antal meddelanden**.

      ![Ange det maximala antalet meddelanden för servicebussanslutningen i logikappen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Välj **Spara** högst upp i Logic Apps Designer för att spara ändringarna.

### <a name="configure-the-logic-app-action"></a>Konfigurera logikappåtgärden

1. Skapa en SMTP-tjänstanslutning.

   1. Välj **Nytt steg**. Välj fliken **Alla** i **Välj en åtgärd.**

   1. Skriv `smtp` i sökrutan, välj **SMTP-tjänsten** i sökresultatet och välj sedan **Skicka e-post**.

      ![Skapa en SMTP-anslutning i logikappen i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Ange SMTP-informationen för postlådan och välj sedan **Skapa**.

      ![Ange SMTP-anslutningsinformation i logikappen i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Hämta SMTP-informationen för [Hotmail/Outlook.com,](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970) [Gmail](https://support.google.com/a/answer/176600?hl=en)och [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Du kan behöva inaktivera TLS/SSL för att upprätta anslutningen. Om så är fallet och du vill återaktivera TLS när anslutningen har upprättats läser du det valfria steget i slutet av det här avsnittet.

   1. Välj **Från**, **Till**, **Ämne** och **Brödtext**i listrutan Lägg **till ny parameter** i steget **Skicka e-post** . Klicka eller tryck var som helst på skärmen för att stänga markeringsrutan.

      ![Välj e-postfält för SMTP-anslutning](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Ange din e-postadress **To**för `High temperature detected` **Från** och Till och för **Ämne** och **Brödtext**. Om **lägg till dynamiskt innehåll från de appar och kopplingar som används i den här flödesdialogrutan** öppnas väljer du **Dölj** för att stänga det. Du använder inte dynamiskt innehåll i den här självstudien.

      ![E-postfält för Ifylla SMTP-anslutning](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Välj **Spara** om du vill spara SMTP-anslutningen.

1. (Valfritt) Om du var tvungen att inaktivera TLS för att upprätta en anslutning till din e-postleverantör och vill återaktivera den gör du så här:

   1. Välj **API-anslutningar**under **Utvecklingsverktyg**i **fönstret Logik.**

   1. Välj SMTP-anslutningen i listan över API-anslutningar.

   1. Välj **Redigera API-anslutning**under **Allmänt**i **fönstret smtp API-anslutning** .

   1. I fönstret **Redigera API-anslutning** väljer du **Aktivera SSL?**, anger lösenordet för ditt e-postkonto igen och väljer **Spara**.

      ![Redigera SMTP API-anslutning i logikappen i Azure-portalen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Logikappen är nu redo att bearbeta temperaturvarningar från servicebusskön och skicka meddelanden till ditt e-postkonto.

## <a name="test-the-logic-app"></a>Testa logikappen

1. Starta klientprogrammet på enheten.

1. Om du använder en fysisk enhet, försiktigt ta med en värmekälla nära värmesensorn tills temperaturen överstiger 30 grader C. Om du använder onlinesimulatorn kommer klientkoden slumpmässigt att mata ut telemetrimeddelanden som överstiger 30 C.

1. Du bör börja ta emot e-postmeddelanden som skickas av logikappen.

   > [!NOTE]
   > Din e-postleverantör kan behöva verifiera avsändarens identitet för att se till att det är du som skickar e-postmeddelandet.

## <a name="next-steps"></a>Nästa steg

Du har skapat en logikapp som ansluter din IoT-hubb och din postlåda för temperaturövervakning och meddelanden.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

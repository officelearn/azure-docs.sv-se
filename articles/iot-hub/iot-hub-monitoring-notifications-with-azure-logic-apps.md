---
title: IoT fjärrövervakning och aviseringar med Azure Logic Apps | Microsoft Docs
description: Använd Azure Logic Apps för övervakning av IoT temperatur på IoT hub och automatiskt skicka e-postmeddelanden till din postlåda för eventuella avvikelser som har identifierats.
author: robinsh
keywords: IOT övervakningsaviseringar, iot, övervakning av iot temperatur
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/19/2019
ms.author: robinsh
ms.openlocfilehash: 26637468f44e12f7ad66f907e0f6be3d907e578f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126280"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT fjärrövervakning och aviseringar med Azure Logic Apps ansluter dina IoT-hubb och postlåda

![Slutpunkt till slutpunkt-diagram](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Med Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) kan hjälpa dig att samordna arbetsflöden i lokala och molnbaserade tjänster, att en eller flera företag, och över olika protokoll. En logikapp börjar med en utlösare, som följs sedan en eller flera åtgärder som kan ordnas med hjälp av inbyggda kontroller, till exempel villkor och Iteratorer. Den här flexibiliteten gör Logic Apps en perfekt IoT-lösning för övervakning IoT-scenarier. Till exempel ankomsten av telemetridata från en enhet i en IoT Hub-slutpunkten kan exempelvis starta logikapparbetsflöden till datalager data i en Azure Storage blob, skicka e-postaviseringar för att varna av data avvikelser, schemalägga ett tekniker besök om en enhet rapporterar ett fel , och så vidare.

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig hur du skapar en logikapp som ansluter din IoT-hubb och postlådan för temperaturövervakning och aviseringar.

Klientkoden körs på din enhet anger en programegenskap `temperatureAlert`på varje telemetri meddelande den skickar till IoT hub. När klienten upptäcker en temperatur över 30 C, egenskapen in till `true`, annars den anger egenskapen till `false`.

I det här avsnittet konfigurerar du routning på IoT hub att skicka meddelanden där `temperatureAlert = true` till en Service Bus-slutpunkt, och du konfigurerar en logikapp som utlöser på meddelanden som inkommer på Service Bus-slutpunkten och skickar du ett e-postmeddelande.

## <a name="what-you-do"></a>Vad du gör

* Skapa ett Service Bus-namnområde och Lägg till en Service Bus-kö.
* Lägga till en anpassad slutpunkt och en regel för vidarebefordran till din IoT hub för att dirigera meddelanden som innehåller en avisering om temperatur till Service Bus-kö.
* Skapa, konfigurera och testa en logikapp som förbrukar meddelanden från Service Bus-kö och skicka e-postaviseringar till en önskad mottagare.

## <a name="what-you-need"></a>Vad du behöver

* Slutför den [Raspberry Pi onlinesimulator](iot-hub-raspberry-pi-web-simulator-get-started.md) självstudien eller någon av enheten självstudiekurser; exempelvis [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Dessa omfattar följande krav:

  * En aktiv Azure-prenumeration.
  * Azure IoT hub i din prenumeration.
  * Ett klientprogram som körs på en enhet som skickar telemetrimeddelanden till din Azure IoT hub.

## <a name="create-service-bus-namespace-and-queue"></a>Skapa Service Bus-namnområde och kö

Skapa ett namnområde och en kö för Service Bus. Senare i det här avsnittet ska skapa du en routningsregel i IoT-hubben till direkta meddelanden som innehåller en avisering om temperatur till Service Bus-kö, där de ska hämtas av en logikapp och utlösa det för att skicka ett e-postmeddelande.

### <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för Service Bus

1. På den [Azure-portalen](https://portal.azure.com/)väljer **+ skapa en resurs** > **integrering** > **Service Bus**.

1. På den **skapa namnområde** fönstret, ange följande information:

   **Namn**: Namnet på service bus-namnområdet. Namnområdet måste vara unikt inom Azure.

   **Prisnivå**: Välj **grundläggande** från den nedrullningsbara listan. Basic-nivån är tillräcklig för den här självstudien.

   **Resursgrupp**: Använd samma resursgrupp som din IoT-hubb använder.

   **Plats**: Använd samma plats som använder din IoT-hubb.

   ![Skapa en service bus-namnområde i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Välj **Skapa**. Vänta tills distributionen har slutförts innan du går vidare till nästa steg.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Lägga till en Service Bus-kö i namnområdet

1. Öppna Service Bus-namnområdet. Det enklaste sättet att komma till Service Bus-namnområdet är att välja **resursgrupper** från resurs-fönstret, Välj din resursgrupp och välj sedan Service Bus-namnområdet från listan över resurser.

1. På den **Service Bus Namespace** väljer **+ kö**.

1. Ange ett namn för kön och välj sedan **skapa**. När kön har skapats visas den **Skapa kö** fönstret stängs.

   ![Lägg till en service bus-kö i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Gå tillbaka till den **Service Bus Namespace** fönstret under **entiteter**väljer **köer**. Öppna Service Bus-kön i listan och välj sedan **principer för delad åtkomst** > **+ Lägg till**.

1. Ange ett namn för principen, kontrollera **hantera**, och välj sedan **skapa**.

   ![Lägg till en service bus-kö-princip i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Lägg till en anpassad slutpunkt och en regel för vidarebefordran till din IoT hub

Lägg till en anpassad slutpunkt för Service Bus-kön till din IoT hub och skapa en hanteringsregel för meddelandet för att dirigera meddelanden som innehåller en temperatur-avisering till denna slutpunkt där de ska hämtas av din logikapp. En routningsregel för använder en routning fråga `temperatureAlert = "true"`, för att vidarebefordra meddelanden baserat på värdet för den `temperatureAlert` programegenskap som angetts av klientkoden körs på enheten. Mer information finns i [Message routing frågan utifrån meddelandeegenskaper](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Lägg till en anpassad slutpunkt

1. Öppna din IoT-hubb. Det enklaste sättet att komma till IoT-hubben är att välja **resursgrupper** från resurs-fönstret, Välj resursgruppen och välj sedan IoT-hubben från listan över resurser.

1. Under **Messaging**väljer **meddelanderoutning**. På den **meddelanderoutning** väljer den **anpassade slutpunkter** fliken och välj sedan **+ Lägg till**. Från listrutan, väljer **Service bus-kö**.

   ![Lägga till en slutpunkt i din IoT-hubb i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. På den **lägga till en service bus-slutpunkt** fönstret anger du följande information:

   **Namnet på slutpunkten**: Namnet på slutpunkten.

   **Service bus-namnområde**: Välj det namnområde som du skapade.

   **Service bus-kö**: Väljer du den kö som du skapade.

   ![Lägga till en slutpunkt i din IoT-hubb i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Välj **Skapa**. När slutpunkten har skapats, kan du gå vidare till nästa steg.

### <a name="add-a-routing-rule"></a>Lägga till en hanteringsregel

1. Gå tillbaka till den **meddelanderoutning** väljer den **vägar** fliken och välj sedan **+ Lägg till**.

1. På den **lägga till en väg** fönstret anger du följande information:

   **Namn**: Namnet på regel för vidarebefordran.

   **Slutpunkt**: Välj vilken slutpunkt som du skapade.

   **Datakälla**: Välj **enhet Telemetrimeddelanden**.

   **Dirigeringsfråga**: Ange `temperatureAlert = "true"`.

   ![Lägg till en regel för vidarebefordran i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Välj **Spara**. Du kan stänga den **meddelanderoutning** fönstret.

## <a name="create-and-configure-a-logic-app"></a>Skapa och konfigurera en Logikapp

I föregående avsnitt ställer du in din IoT-hubb för att dirigera meddelanden som innehåller en avisering om temperatur till Service Bus-kö. Nu kan ställa du in en logikapp för att övervaka Service Bus-kö och skicka ett e-postmeddelande när ett meddelande läggs till i kön.

### <a name="create-a-logic-app"></a>Skapa en logikapp

1. Välj **skapa en resurs** > **integrering** > **Logikapp**.

1. Ange följande information:

   **Namn**: Namnet på logikappen.

   **Resursgrupp**: Använd samma resursgrupp som din IoT-hubb använder.

   **Plats**: Använd samma plats som använder din IoT-hubb.

   ![Skapa en logikapp i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Välj **Skapa**.

### <a name="configure-the-logic-app-trigger"></a>Konfigurera logikappsutlösaren

1. Öppna logikappen. Det enklaste sättet att komma till logikappen är att välja **resursgrupper** från resurs-fönstret, Välj resursgruppen och välj sedan din logikapp från listan över resurser. När du väljer logic app öppnas Logic Apps Designer.

1. Logic Apps Designer, rulla ned till **mallar** och välj **tom Logikapp**.

   ![Börja med en tom logikapp i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Välj den **alla** fliken och välj sedan **Service Bus**.

   ![Välj Service Bus för att börja skapa din logikapp i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Under **utlösare**väljer **när en eller flera meddelanden anländer i en kö (Komplettera automatiskt)**.

   ![Välj utlösaren för din logikapp i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Skapa en service bus-anslutning.
   1. Ange ett namn på anslutningen och välj Service Bus-namnområdet från listan. Nästa skärm öppnas.

      ![Skapa en service bus-anslutning för din logikapp i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Välj service bus-princip (RootManageSharedAccessKey). Välj sedan **skapa**.

      ![Skapa en service bus-anslutning för din logikapp i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. På den sista skärmen för **könamn**, väljer du den kö som du skapade från listrutan. Ange `175` för **maximala meddelandeantalet**.

      ![Ange det maximala meddelandeantalet för service bus-anslutning i din logikapp](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Välj **spara** på menyn högst upp i Logic Apps Designer för att spara dina ändringar.

### <a name="configure-the-logic-app-action"></a>Konfigurera åtgärden till logic app

1. Skapa en anslutning för SMTP-tjänsten.

   1. Välj **Nytt steg**. I **Välj en åtgärd**väljer den **alla** fliken.

   1. Typ `smtp` i sökrutan, Välj den **SMTP** tjänsten i sökresultatet och välj sedan **skicka e-post**.

      ![Skapa en SMTP-anslutning i din logikapp i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Ange SMTP-information för din postlåda och välj sedan **skapa**.

      ![Ange SMTP-anslutningsinformation i din logikapp i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Få den SMTP-informationen för [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en), och [Yahoo e-post](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Du kan behöva inaktivera SSL för att upprätta anslutningen. Om så är fallet och du vill återaktivera SSL när anslutningen har upprättats, finns det valfria steget i slutet av det här avsnittet.

   1. Från den **Lägg till ny parameter** listrutan vid den **skicka e-post** steg, Välj **från**, **till**, **ämne**och **brödtext**. Klicka eller tryck var som helst på skärmen för att Stäng markeringsrutan.

      ![Välj e-postfält för SMTP-anslutning](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Ange din e-postadress för **från** och **till**, och `High temperature detected` för **ämne** och **brödtext**. Om den **Lägg till dynamiskt innehåll från appar och kopplingar som används i det här flödet** dialogruta öppnas, Välj **Dölj** att stänga den. Du använder inte dynamiskt innehåll i den här självstudien.

      ![Fyll SMTP e-Anslutningsfält](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Välj **spara** att spara SMTP-anslutning.

1. (Valfritt) Om du vill inaktivera SSL för att upprätta en anslutning till din e-postleverantör och vill aktivera det igen måste du så här:

   1. På den **logikapp** fönstret under **utvecklingsverktyg**väljer **API-anslutningar**.

   1. Välj SMTP-anslutning från listan över API-anslutningar.

   1. På den **smtp API-anslutningen** fönstret under **Allmänt**väljer **redigera API-anslutningen**.

   1. På den **redigera API-anslutningen** väljer **aktivera SSL?**, anger du lösenordet för ditt e-postkonto igen och välj **spara**.

      ![Redigera SMTP-API-anslutning i din logikapp i Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Logikappen är nu redo att bearbeta temperatur aviseringar från Service Bus-kö och skickar meddelanden till ditt e-postkonto.

## <a name="test-the-logic-app"></a>Testa logikappen

1. Starta klientprogrammet på enheten.

1. Om du använder en fysisk enhet ta noggrant med en källa för den termiska nära termisk sensorn innan den överskrider 30 grader C. Om du använder online simulatorn klientkoden slumpmässigt mata ut telemetrimeddelanden som överskrider 30 C.

1. Du bör börja ta emot e-postmeddelanden som skickas av logikappen.

   > [!NOTE]
   > Din e-post-leverantör kan behöva kontrollera identiteten avsändaren att kontrollera att det är du som skickar e-postmeddelandet.

## <a name="next-steps"></a>Nästa steg

Du skapat har en logikapp som ansluter din IoT-hubb och postlådan för temperaturövervakning och aviseringar.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

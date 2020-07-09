---
title: Azure Event Grid – aktivera diagnostikloggar för ämnen eller domäner
description: Den här artikeln innehåller steg-för-steg-instruktioner om hur du aktiverar diagnostikloggar för ett Azure Event Grid-ämne.
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: 7811c2eef4379b7e3d5ed07dbd0df8e2a52dba85
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114711"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Aktivera diagnostikloggar för Azure Event Grid-ämnen eller domäner
Med diagnostikinställningar kan Event Grid användare fånga och visa **publicerings-och leverans fel** loggar i antingen ett lagrings konto, en händelsehubben eller en Log Analytics arbets yta. Den här artikeln innehåller steg-för-steg-instruktioner för att aktivera de här inställningarna i ett Event Grid ämne.

## <a name="prerequisites"></a>Förutsättningar

- Ett tillhandahållet händelse rutnäts avsnitt
- Ett tillhandahållet mål för att samla in diagnostikloggar. Det kan vara en av följande destinationer på samma plats som Event Grid-ämnet:
    - Azure Storage-konto
    - Händelsehubb
    - Log Analytics-arbetsyta

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>Aktivera diagnostikloggar för ett anpassat ämne

> [!NOTE]
> Följande procedur innehåller stegvisa instruktioner för hur du aktiverar diagnostikloggar för ett ämne. Steg för att aktivera diagnostikloggar för en domän är mycket likartade. I steg 2 navigerar du till Event Grid- **domänen** i Azure Portal.  

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till det event Grid-ämne som du vill aktivera diagnostikloggar för. 
    1. I Sök fältet högst upp söker du efter **Event Grid ämnen**. 
    
        ![Sök efter anpassade ämnen](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. Välj det **ämne** i listan som du vill konfigurera diagnostikinställningar för. 
1. Välj **diagnostikinställningar** under **övervakning** i den vänstra menyn.
1. På sidan **diagnostikinställningar** väljer du **Lägg till ny diagnostisk inställning**. 
    
    ![Knappen Lägg till diagnostisk inställning](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Ange ett **namn** för den diagnostiska inställningen. 
7. Välj alternativen **DeliveryFailures** och **PublishFailures** i avsnittet **logg** . 
    ![Välj felen](./media/enable-diagnostic-logs-topic/log-failures.png)
6. Aktivera en eller flera av avbildnings målen för loggarna och konfigurera dem genom att välja en tidigare skapad avbildnings resurs. 
    - Om du väljer **Arkiv till ett lagrings konto**väljer du **lagrings konto – Konfigurera**och väljer sedan lagrings kontot i din Azure-prenumeration. 

        ![Arkivera till ett Azure Storage-konto](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Om du väljer **Stream till en händelsehubben**väljer du **Event Hub – konfigurera**och väljer sedan Event Hubs namnrymd, händelsehubben och åtkomst principen. 
        ![Strömma till en Event Hub](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Om du väljer **Skicka till Log Analytics**väljer du arbets ytan Log Analytics.
        ![Skicka till Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. Välj **Spara**. Välj sedan **X** i det högra hörnet för att stänga sidan. 
9. Nu bekräftar du på sidan **diagnostikinställningar** och bekräftar att du ser en ny post i tabellen **diagnostikinställningar** . 
    ![Diagnostisk inställning i listan](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Du kan också aktivera insamling av alla mått för ämnet. 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>Aktivera diagnostikloggar för ett system ämne

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till det event Grid-ämne som du vill aktivera diagnostikloggar för. 
    1. I Sök fältet högst upp söker du efter **Event Grid system ämnen**. 
    
        ![Sök efter system avsnitt](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. Välj det **system avsnitt** som du vill konfigurera diagnostikinställningar för. 
    
        ![Avsnittet Välj system](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. Välj **diagnostikinställningar** under **övervakning** i den vänstra menyn och välj sedan **Lägg till diagnostisk inställning**. 

    ![Lägg till diagnostikinställningar – knapp](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. Ange ett **namn** för den diagnostiska inställningen. 
7. Välj **DeliveryFailures** i avsnittet **log** . 
    ![Välj leverans problem](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. Aktivera en eller flera av avbildnings målen för loggarna och konfigurera dem genom att välja en tidigare skapad avbildnings resurs. 
    - Om du väljer **Skicka till Log Analytics**väljer du arbets ytan Log Analytics.
        ![Skicka till Log Analytics](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - Om du väljer **Arkiv till ett lagrings konto**väljer du **lagrings konto – Konfigurera**och väljer sedan lagrings kontot i din Azure-prenumeration. 

        ![Arkivera till ett Azure Storage-konto](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - Om du väljer **Stream till en händelsehubben**väljer du **Event Hub – konfigurera**och väljer sedan Event Hubs namnrymd, händelsehubben och åtkomst principen. 
        ![Strömma till en Event Hub](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. Välj **Spara**. Välj sedan **X** i det högra hörnet för att stänga sidan. 
9. Nu bekräftar du på sidan **diagnostikinställningar** och bekräftar att du ser en ny post i tabellen **diagnostikinställningar** . 
    ![Diagnostisk inställning i listan](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     Du kan också aktivera insamling av alla **Mät värden** för system avsnittet.

    ![System ämne – aktivera alla mått](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Visa diagnostikloggar i Azure Storage 

1. När du aktiverar ett lagrings konto som ett registrerings mål och Event Grid börjar generera diagnostikloggar, bör du se nya behållare med namnet **Insights-logs-deliveryfailures** och Insights- **logs-publishfailures** i lagrings kontot. 

    ![Lagrings behållare för diagnostikloggar](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. När du navigerar genom en av behållarna, kommer du att få en BLOB i JSON-format. Filen innehåller logg poster för antingen ett leverans problem eller ett publicerings haveri. Navigerings Sök vägen representerar **ResourceID** för Event Grid-ämnet och tidsstämpeln (minut nivån) när logg posterna skulle genereras. BLOB/JSON-filen, som kan hämtas, i slutet följer schemat som beskrivs i nästa avsnitt. 

    [![JSON-fil i lagringen ](./media/enable-diagnostic-logs-topic/select-json.png)](./media/enable-diagnostic-logs-topic/select-json.png)
3. Du bör se innehåll i JSON-filen som liknar följande exempel: 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, systemId=17284f7c-0044-46fb-84b7-59fda5776017, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```

## <a name="next-steps"></a>Nästa steg
För logg schema och annan konceptuell information om diagnostikloggar för ämnen eller domäner, se [diagnostikloggar](diagnostic-logs.md).

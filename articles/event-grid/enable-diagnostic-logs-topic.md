---
title: Azure Event Grid – aktivera diagnostikloggar för ett ämne
description: Den här artikeln innehåller steg-för-steg-instruktioner om hur du aktiverar diagnostikloggar för ett Azure Event Grid-ämne.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960508"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Diagnostiska loggar för ett Azure Event Grid-ämne
Med diagnostikinställningar kan Event Grid användare fånga och Visa publicerings-och leverans fel loggar på någon av följande platser: ett Azure Storage-konto, en Event Hub eller en Log Analytics arbets yta. Den här artikeln innehåller steg-för-steg-instruktioner för att aktivera diagnostikloggar för ett event Grid-ämne.

## <a name="prerequisites"></a>Krav

- Ett tillhandahållet händelse rutnäts avsnitt
- Ett tillhandahållet mål för att samla in diagnostikloggar. Den kan vara en av följande destinationer:
    - Azure Storage-konto
    - Händelsehubb
    - Log Analytics-arbetsyta


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Steg för att aktivera diagnostikloggar för ett ämne

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Navigera till det event Grid-ämne som du vill aktivera diagnostikloggar för. 
3. Välj **diagnostikinställningar** under **övervakning** i den vänstra menyn.
4. På sidan **diagnostikinställningar** väljer du **Lägg till ny diagnostisk inställning**. 
    
    ![Knappen Lägg till diagnostisk inställning](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Ange ett **namn** för den diagnostiska inställningen. 

    ![Diagnostikinställningar – namn](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Aktivera en eller flera av avbildnings målen för loggarna och konfigurera dem genom att välja en tidigare skapad avbildnings resurs. 
    - Om du väljer **Arkiv till ett lagrings konto**väljer du **lagrings konto – Konfigurera**och väljer sedan lagrings kontot i din Azure-prenumeration. 

        ![Arkivera till ett Azure Storage-konto](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Om du väljer **Stream till en händelsehubben**väljer du **Event Hub – konfigurera**och väljer sedan Event Hubs namnrymd, händelsehubben och åtkomst principen. 
        ![Stream till en Event Hub-](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Om du väljer **Skicka till Log Analytics**väljer du arbets ytan Log Analytics.
        ![skicka till Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Välj alternativen **DeliveryFailures** och **PublishFailures** i avsnittet **logg** . 
    ![väljer du felen](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Välj **Spara**. Välj **X** i det högra hörnet för att stänga sidan. 
9. Nu bekräftar du på sidan **diagnostikinställningar** och bekräftar att du ser en ny post i tabellen **diagnostikinställningar** . 
    ![diagnostikinställningar i listan](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Du kan också aktivera insamling av alla mått för ämnet. 

## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan du publicera ditt problem i [Stack Overflow-forumet](https://stackoverflow.com/questions/tagged/azure-eventgrid) eller öppna ett [support ärende](https://azure.microsoft.com/support/options/). 

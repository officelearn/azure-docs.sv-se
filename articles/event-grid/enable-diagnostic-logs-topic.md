---
title: Azure Event Grid - Aktivera diagnostikloggar för ett ämne
description: Den här artikeln innehåller steg-för-steg-instruktioner om hur du aktiverar diagnostikloggar för ett Azure-händelserutnätsämne.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960508"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Diagnostikloggar för ett Azure-händelserutnätsämne
Diagnostikinställningar gör det möjligt för Event Grid-användare att samla in och visa publicerings- och leveransfel Loggar på något av följande platser: ett Azure-lagringskonto, en händelsenav eller en Log Analytics-arbetsyta. Den här artikeln innehåller steg-för-steg-instruktioner för att aktivera diagnostikloggar för ett händelserutnätsavsnitt.

## <a name="prerequisites"></a>Krav

- Ett etablerat händelserutnätsavsnitt
- Ett etablerat mål för att samla in diagnostikloggar. Det kan en av följande destinationer:
    - Azure Storage-konto
    - Händelsehubb
    - Log Analytics-arbetsyta


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Steg för att aktivera diagnostikloggar för ett ämne

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till det händelserutnätsämne som du vill aktivera diagnostiklogginställningar för. 
3. Välj **Diagnostikinställningar** under **Övervakning** i den vänstra menyn.
4. På sidan **Diagnostikinställningar** väljer du **Lägg till ny diagnostikinställning**. 
    
    ![Knappen Lägg till diagnostikinställning](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Ange ett **namn** för diagnostikinställningen. 

    ![Diagnostikinställningar - namn](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Aktivera ett eller flera av insamlingsmålen för loggarna och konfigurera dem genom att välja en tidigare skapad insamlingsresurs. 
    - Om du väljer **Arkiv till ett lagringskonto**väljer du **Lagringskonto - Konfigurera**och väljer sedan lagringskontot i din Azure-prenumeration. 

        ![Arkivera till ett Azure-lagringskonto](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Om du väljer **Stream till en händelsehubb**väljer du **Händelsenav - Konfigurera**och väljer sedan namnområdet Event Hubs, händelsenav och åtkomstprincipen. 
        ![Strömma till ett händelsenav](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Om du väljer **Skicka till Logganalys**väljer du arbetsytan Logganalys.
        ![Skicka till Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Välj alternativen **DeliveryFailures** och **PublishFailures** i avsnittet **Log.** 
    ![Välj fel](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Välj **Spara**. Välj **X** i det högra hörnet för att stänga sidan. 
9. Nu, tillbaka på sidan **Diagnostikinställningar,** bekräfta att du ser en ny post i tabellen **Inställningar för diagnostik.** 
    ![Diagnostikinställning i listan](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Du kan också aktivera insamling av alla mått för ämnet. 

## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan du lägga upp ditt problem i [forumet Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) eller öppna en [supportbiljett.](https://azure.microsoft.com/support/options/) 

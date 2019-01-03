---
title: Konfigurera övervakning av aviseringar för Azure Stream Analytics-jobb
description: Den här artikeln beskriver hur du använder Azure-portalen för att konfigurera övervakning och aviseringar för Azure Stream Analytics-jobb.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 727747d84d0db32c73fc1a200bcea7e5c149d24b
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554919"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Konfigurera aviseringar för Azure Stream Analytics-jobb
Du kan ställa in aviseringar för att utlösa en avisering när ett mått når ett villkor som du anger. Du kan till exempel ställa in en avisering om ett villkor som liknar följande:

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

Regler kan ställas in på mått via portalen eller kan konfigureras [programmässigt](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) Åtgärdsloggar data.

## <a name="set-up-alerts-in-the-azure-portal"></a>Konfigurera aviseringar i Azure portal
1. I Azure-portalen öppnar du ett Stream Analytics-jobb som du vill skapa en avisering för. 

2. I den **jobbet** bladet klickar du på den **övervakning** avsnittet.  

3. I den **mått** bladet klickar du på den **Lägg till avisering** kommando.

      ![Azure Stream Analytics aviseringar portalinställningarna](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. Ange ett namn och en beskrivning.

5. Använd väljare för att definiera villkor som aviseringen ska skickas.

6. Ange information om där aviseringen ska gå.

      ![Ställa in en avisering för Azure Streaming Analytics-jobb](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Mer information om hur du konfigurerar aviseringar i Azure-portalen finns i [Varningsaviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  


## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-get-started.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


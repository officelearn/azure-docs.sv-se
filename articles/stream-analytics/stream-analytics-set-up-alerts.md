---
title: Konfigurera övervakning notifieringar för Azure Stream Analytics-jobb
description: Den här artikeln beskriver hur du använder Azure-portalen för att konfigurera övervakning och aviseringar för Azure Stream Analytics-jobb.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/26/2017
ms.openlocfilehash: fb69f1b52e5b17dd6794c896969c29003a929c9b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412118"
---
# <a name="tutorial-set-up-alerts-for-azure-stream-analytics-jobs"></a>Självstudier: Konfigurera aviseringar för Azure Stream Analytics-jobb
Du kan konfigurera aviseringar för att utlösa en avisering när ett mått når ett villkor som du anger. Du kan till exempel skapa en avisering om ett villkor som liknar följande:

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

Regler kan ställas in på mätvärden via portalen eller kan konfigureras [programmässigt](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) över Åtgärdsloggar data.

## <a name="set-up-alerts-in-the-azure-portal"></a>Konfigurera aviseringar i Azure-portalen
1. Öppna Stream Analytics-jobbet som du vill skapa en avisering för i Azure-portalen. 

2. I den **jobbet** bladet, klickar du på den **övervakning** avsnitt.  

3. I den **mått** bladet, klickar du på den **Lägg till avisering** kommando.

      ![Konfigurationen av Azure portal](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. Ange ett namn och en beskrivning.

5. Använd väljare för att definiera de villkor under vilka aviseringen ska skickas.

6. Ange information om aviseringen ska var.

      ![Ställa in en avisering för ett Azure Streaming Analytics-jobb](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Mer information om hur du konfigurerar aviseringar i Azure portal finns [få aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  


## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-get-started.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


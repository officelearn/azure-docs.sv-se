---
title: "Azure säkerhetsdata exportera till SIEM - Pipeline Configuration [förhandsgranskning] | Microsoft Docs"
description: "Den här artikeln dokumenteras producerar för att få Azure security center-loggar till en SIEM"
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: barclayn
ms.openlocfilehash: aef623f047bd7e14cb5bd17fb2a2c18e3c5d42b9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="azure-security-data-export-to-siem--pipeline-configuration-preview"></a>Azure säkerhetsdata exportera till SIEM - Pipeline Configuration [förhandsgranskning]

Det här dokumentet beskriver hur du exporterar data i Azure Security Säkerhetscenter till en SIEM.

Bearbetade händelser som genereras av Azure Security Center har publicerats till Azure [aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), en av loggen typer tillgängliga via Azure-Monitor. Övervakare för Azure erbjuder en konsoliderad pipeline för omdirigering av någon av dina övervakningsdata i ett SIEM-verktyg. Detta görs genom strömning dessa data till en Händelsehubb där den kan sedan ska hämtas till en partner-verktyget.

Aktuell pipe använder den [Azure-övervakning enkel rörledning](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) för att få åtkomst till övervakningsdata från Azure-miljön. På så sätt kan du enkelt konfigurera siem-servrar och övervakningsverktyg för att använda data.

I nästa avsnitt beskrivs hur du konfigurerar data strömmas till en händelsehubb. Anvisningarna förutsätter att du redan har konfigurerats i din Azure-prenumeration för Azure Security Center.

Översikt på hög nivå

![Översikt på hög nivå](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Vad är Azure säkerhetsdata utsätts för SIEM?

I den här förhandsversionen du använda den [säkerhetsaviseringar.](../security-center/security-center-managing-and-responding-alerts.md) Vi kommer förbättra datauppsättningen med säkerhetsrekommendationer i kommande versioner.

## <a name="how-to-setup-the-pipeline"></a>Hur du ställer in pipeline? 

### <a name="create-an-event-hub"></a>Skapa en händelsehubb 

Innan du börjar måste du [skapa ett namnområde för Händelsehubbar](../event-hubs/event-hubs-create.md). Det här namnområdet och Event Hub är mål för alla övervakningsdata.

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Dataströmmen Azure aktivitetsloggen i Händelsehubbar

Se följande artikel [dataströmmen aktivitetsloggen i Händelsehubbar](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)

### <a name="install-a-partner-siem-connector"></a>Installera en partner SIEM-koppling 

Routning övervakningsdata till en Händelsehubb med Azure-Monitor kan du lätt kan integrera med partner SIEM och övervakningsverktyg.

Referera till följande länk för att se en lista över [stöd för Siem](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

## <a name="example-for-querying-data"></a>Exempel för att fråga efter data 

Här är några Splunk frågor som du kan använda för att hämta aviseringsdata:

| **Beskrivning av frågan**                                | **Fråga**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Alla aviseringar                                              | index=main Microsoft.Security/locations/alerts                                                                                         |
| Sammanfatta antal åtgärder med deras namn             | **Aviseringar** index = huvudsakliga sourcetype = ”amal: säkerhet” \| tabell operationName \| stats räkna med operationName                                |
| Få aviseringar information: tid, namn, status, ID och prenumeration | index = huvudsakliga Microsoft.Security/locations/alerts \| tabell \_tid, properties.eventName, tillstånd, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Nästa steg

- [Stöds Siem](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [Strömma aktivitetsloggen till Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [Säkerhetsaviseringar.](../security-center/security-center-managing-and-responding-alerts.md)
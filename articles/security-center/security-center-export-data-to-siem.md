---
title: Azure säkerhetsdata exportera till SIEM - Pipeline-konfiguration | Microsoft Docs
description: Den här artikeln beskrivs ges för att få Azure security center-loggar till en SIEM
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: barclayn
ms.openlocfilehash: 4cc9478197b39198a94ace1cbefab81de3cdb32b
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52306734"
---
# <a name="azure-security-data-export-to-siem-pipeline-configuration"></a>Azure Security dataexport till SIEM-Pipeline-konfiguration

Det här dokumentet beskriver hur du exporterar Azure Security Center säkerhetsdata till en SIEM.

Bearbetade händelser som genereras av Azure Security Center publiceras till Azure [aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), någon av loggen skriver tillgängliga i Azure Monitor. Azure Monitor erbjuder en konsoliderad pipeline för routning någon av dina övervakade data i ett SIEM-verktyg. Detta görs genom att dessa data till en Händelsehubb där det kan sedan hämtas för direktuppspelning i ett partner-verktyg.

Den här pipe använder den [Azure Monitoring enkel rörledning](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) för att få åtkomst till övervakningsdata från Azure-miljön. På så sätt kan du enkelt konfigurera Siem och övervakningsverktyg för att använda data.

I nästa avsnitt beskrivs hur du kan konfigurera data strömmas till en händelsehubb. Förutsätter vi att du redan har konfigurerats i din Azure-prenumeration för Azure Security Center.

Översikt på hög nivå

![Översikt på hög nivå](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Vad är Azure-säkerhetsdata som är exponerade för SIEM?

I den här versionen vi exponerar den [säkerhetsaviseringar.](../security-center/security-center-managing-and-responding-alerts.md) Vi kommer berika datauppsättningen med säkerhetsrekommendationer i kommande versioner.

## <a name="how-to-setup-the-pipeline"></a>Så här konfigurerar du pipelinen

### <a name="create-an-event-hub"></a>Skapa en händelsehubb

Innan du börjar måste du [skapa ett namnområde för Event Hubs](../event-hubs/event-hubs-create.md). Den här namnområde och en Händelsehubb är målet för alla dina övervakningsdata.

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Stream Azure-aktivitetsloggen till Event Hubs

Se följande artikel [strömma aktivitetsloggen till Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)

### <a name="install-a-partner-siem-connector"></a>Installera en partneranslutningsapp 

Routning övervakningsdata till en Event Hub med Azure Monitor kan du lätt kan integrera med partner SIEM och övervakningsverktyg.

Finns på följande länk för att se en lista över [SIEMs som stöds](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

## <a name="example-for-querying-data"></a>Exempel för att fråga efter data 

Här är några Splunk frågor som du kan använda för att hämta aviseringsdata:

| **Beskrivning av fråga**                                | **Fråga**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Alla aviseringar                                              | index = huvudsakliga Microsoft.Security/locations/alerts                                                                                         |
| Sammanfatta antalet åtgärder efter deras namn             | index = huvudsakliga sourcetype = ”amal: säkerhet” \| tabell operationName \| stats antal efter operationName                                |
| Hämta aviseringar info: tid, namn, status, ID och prenumeration | index = huvudsakliga Microsoft.Security/locations/alerts \| tabell \_tid, properties.eventName, tillstånd, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Nästa steg

- [SIEMs som stöds](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [Strömma aktivitetsloggen till Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [Säkerhetsaviseringar.](../security-center/security-center-managing-and-responding-alerts.md)
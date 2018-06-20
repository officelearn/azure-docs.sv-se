---
title: Källor av övervakningsdata i Azure
description: Läs mer om alla övervakning datakällor på Azure idag.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 97eca81a8ddc2726bf5de0f4ad7ba0bdf1df1d05
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220092"
---
# <a name="consume-monitoring-data-from-azure"></a>Använda övervakningsdata från Azure

I Azure-plattformen levererar vi tillsammans övervakningsdata i en enda plats med Azure-Monitor pipeline, men praktiskt taget bekräftar att idag inga övervakningsdata är tillgängliga i denna pipeline ännu. I den här artikeln ska vi sammanfattar de olika sätt som du kan komma åt övervakningsdata från Azure-tjänster.

## <a name="options-for-data-consumption"></a>Alternativ för användning av data

| Datatyp | Kategori | Tjänster som stöds | Åtkomstmetoder |
| --- | --- | --- | --- |
| Azure övervakaren plattform nivå mått | Mått | [Visa lista här](monitoring-supported-metrics.md) | <ul><li>**REST-API:** [Azure övervakaren mått API](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Storage-blob eller händelse hubb:** [diagnostikinställningar](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| Beräkna gäst OS-mått (t.ex. prestandaräknarna) | Mått | [Windows](../virtual-machines-dotnet-diagnostics.md) och virtuella Linux-datorer (v2), [molntjänster](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabell för lagring eller blob:** [Windows eller Linux-Azure-diagnostik](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Händelsehubb:** [Windows Azure-diagnostik](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Anpassade eller ett program mått | Mått | Alla program som instrumenterats med Application Insights | <ul><li>**REST-API:** [Programinsikter REST-API](https://dev.applicationinsights.io/reference)</li></ul> |
| Storage-mätvärden | Mått | Azure Storage | <ul><li>**Tabell för lagring:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Faktureringsinformation | Mått | Alla Azure-tjänster | <ul><li>**REST-API:** [Azure Resursanvändning och RateCard API: er](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Aktivitetslogg | Händelser | Alla Azure-tjänster | <ul><li>**REST-API:** [Azure övervaka händelser API](https://docs.microsoft.com/rest/api/monitor/events)</li><li>**Storage-blob eller händelse hubb:** [Log-profil](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Azure-Monitor diagnostikloggar | Händelser | [Visa lista här](monitoring-diagnostic-logs-schema.md) | <ul><li>**Storage-blob eller händelse hubb:** [diagnostikinställningar](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| Beräkna gäst OS-loggar (t.ex. IIS, ETW, systemloggar) | Händelser | [Windows](../virtual-machines-dotnet-diagnostics.md) och virtuella Linux-datorer (v2), [molntjänster](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabell för lagring eller blob:** [Windows eller Linux-Azure-diagnostik](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Händelsehubb:** [Windows Azure-diagnostik](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Apptjänst loggar | Händelser | App Services | <ul><li>**Fil-, tabell- eller blob storage:** [Web appdiagnostik](../app-service/web-sites-enable-diagnostic-log.md)</li></ul> |
| Storage-loggar | Händelser | Azure Storage | <ul><li>**Tabell för lagring:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Varningsmeddelanden | Händelser | Azure Security Center | <ul><li>**REST-API:** [säkerhetsaviseringar](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Active Directory-rapportering | Händelser | Azure Active Directory | <ul><li>**REST-API:** [Azure Active Directory graph API](../active-directory/active-directory-reporting-api-getting-started.md)</li></ul> |
| Security Center resource status | Status | [Alla stöds resurser](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**REST-API:** [status för säkerhet](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Resource Health | Status | Tjänster som stöds | <ul><li>**REST-API:** [Resource health REST API](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Azure övervaka mått aviseringar | Meddelanden | [Visa lista här](monitoring-supported-metrics.md) | <ul><li>**Webhook:** [Azure mått aviseringar](insights-webhooks-alerts.md)</li></ul> |
| Azure övervaka aktivitetsloggen aviseringar | Meddelanden | Alla Azure-tjänster | <ul><li>**Webhook:** Azure aktivitetsloggen aviseringar</li></ul> |
| Meddelanden om autoskalning | Meddelanden | [Visa lista här](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [Autoskala meddelande webhook nyttolast schemat](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Logga sökfråga varningar | Meddelanden | Log Analytics | <ul><li>**Webhook:** [Webhook åtgärd för log Varningsregler](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md)</li></ul> |
| Application Insights mått aviseringar | Meddelanden | Application Insights | <ul><li>**Webhook:** [Application Insights aviseringar](../application-insights/app-insights-alerts.md)</li></ul> |
| Application Insights webbtester | Meddelanden | Application Insights | <ul><li>**Webhook:** [Application Insights aviseringar](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-Monitor mått](monitoring-overview-metrics.md)
- Lär dig mer om [Azure-aktivitetsloggen](monitoring-overview-activity-logs.md)
- Lär dig mer om [Azure diagnostikloggar](monitoring-overview-of-diagnostic-logs.md)

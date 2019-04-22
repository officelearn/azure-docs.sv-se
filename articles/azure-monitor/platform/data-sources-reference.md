---
title: Källor för övervakningsdata i Azure
description: Läs mer om alla övervakning datakällor tillgängliga på Azure idag.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: c703f735f59a8425c2a14641781f482a6e2d1c78
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58848534"
---
# <a name="consume-monitoring-data-from-azure"></a>Använda övervakningsdata från Azure

I Azure-plattformen samman vi övervakningsdata i en enda plats med Azure Monitor pipeline, men praktiskt taget bekräftar att idag är inte alla övervakningsdata är tillgängliga i denna pipeline ännu. I den här artikeln ska vi sammanfattar de olika sätt som du kan via programmering komma åt övervakningsdata från Azure-tjänster.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="options-for-data-consumption"></a>Alternativ för dataförbrukning

| Datatyp | Kategori | Tjänster som stöds | Åtkomstmetoder |
| --- | --- | --- | --- |
| Azure Monitor på plattformsnivå-mått | Mått | [Se listan här](metrics-supported.md) | <ul><li>**REST-API:** [Azure Monitor mått-API](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Storage blob eller event hub:** [Diagnostikinställningar](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| Compute OS gästmått (t.ex.) prestandaräknare) | Mått | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) och Linux-datorer (v2), [molntjänster](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Storage-tabell eller blob:** [Windows eller Linux Azure-diagnostik](diagnostics-extension-to-storage.md)</li><li>**Händelsehubb:** [Windows Azure diagnostics](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Anpassade eller programmet mått | Mått | Alla program som instrumenteras med Application Insights | <ul><li>**REST-API:** [Application Insights REST API](https://dev.applicationinsights.io/reference)</li></ul> |
| Storage-mått | Mått | Azure Storage | <ul><li>**Storage-tabell:** [Lagringsanalys](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Faktureringsinformation | Mått | Alla Azure-tjänster | <ul><li>**REST-API:** [Azure-Resursanvändning och RateCard-API: er](../../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Aktivitetslogg | Händelser | Alla Azure-tjänster | <ul><li>**REST-API:** [Azure Monitor händelser API](https://docs.microsoft.com/rest/api/monitor/eventcategories)</li><li>**Storage blob eller event hub:** [Loggprofil](activity-logs-overview.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Diagnostikloggar för Azure Monitor | Händelser | [Se listan här](diagnostic-logs-schema.md) | <ul><li>**Storage blob eller event hub:** [Diagnostikinställningar](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| Compute-loggarna för gästoperativsystemet (t.ex.) IIS, ETW, syslogs) | Händelser | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) och Linux-datorer (v2), [molntjänster](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Storage-tabell eller blob:** [Windows eller Linux Azure-diagnostik](diagnostics-extension-to-storage.md)</li><li>**Händelsehubb:** [Windows Azure diagnostics](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| App Service-loggar | Händelser | App Services | <ul><li>**Fil, tabell eller blob-lagring:** [Webbprogramdiagnostik](../../app-service/troubleshoot-diagnostic-logs.md)</li></ul> |
| Storage-loggar | Händelser | Azure Storage | <ul><li>**Storage-tabell:** [Lagringsanalys](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Security Center-aviseringar | Händelser | Azure Security Center | <ul><li>**REST-API:** [Säkerhetsaviseringar](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Active Directory-rapportering | Händelser | Azure Active Directory | <ul><li>**REST-API:** [Azure Active Directory graph API](../../active-directory/reports-monitoring/concept-reporting-api.md)</li></ul> |
| Security Center resursstatus | Status | [Alla resurser](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**REST-API:** [Status för säkerhet](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Resource Health | Status | Tjänster som stöds | <ul><li>**REST-API:** [Resurshälsa REST API](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Azure Monitor-måttaviseringar | Meddelanden | [Se listan här](metrics-supported.md) | <ul><li>**Webhook:** [Azure måttaviseringar](alerts-webhooks.md)</li></ul> |
| Azure Monitor Activity-Log-aviseringar | Meddelanden | Alla Azure-tjänster | <ul><li>**Webhook:** Azure-aktivitetsloggen-aviseringar</li></ul> |
| Meddelanden om autoskalning | Meddelanden | [Se listan här](autoscale-overview.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [Automatisk skalning meddelande webhook-nyttolasten schema](autoscale-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Logga sökfråga aviseringar | Meddelanden | Azure Monitor-loggar | <ul><li>**Webhook:** [Webhook-åtgärd för loggaviseringsregler](alerts-log-webhook.md)</li></ul> |
| Måttaviseringar för Application Insights | Meddelanden | Application Insights | <ul><li>**Webhook:** [Aviseringar för Application Insights](../../azure-monitor/app/alerts.md)</li></ul> |
| Webbtester med Application Insights | Meddelanden | Application Insights | <ul><li>**Webhook:** [Aviseringar för Application Insights](../../azure-monitor/app/alerts.md)</li></ul> |

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor-mått](data-platform.md)
- Läs mer om [Azure-aktivitetsloggen](activity-logs-overview.md)
- Läs mer om [Azure diagnostikloggar](diagnostic-logs-overview.md)


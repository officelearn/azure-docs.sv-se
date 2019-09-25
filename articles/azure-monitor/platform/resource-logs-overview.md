---
title: Översikt över Azures resurs loggar | Microsoft Docs
description: Förstå tjänster och händelse schema som stöds för Azures resurs loggar.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 09/20/2019
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: bfcd2ded96c2679ba9177a760a8b11dc7d2c9a77
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262549"
---
# <a name="azure-resource-logs-overview"></a>Översikt över Azure resurs loggar
Azures resurs loggar är [plattforms loggar](platform-logs-overview.md) som genereras av Azure-resurser som beskriver deras interna åtgärd. Alla resurs loggar delar ett gemensamt schema på högsta nivå med flexibiliteten för varje tjänst för att generera unika egenskaper för sina egna händelser.

> [!NOTE]
> Resurs loggar kallades tidigare för diagnostikloggar.

## <a name="collecting-resource-logs"></a>Samlar in resurs loggar
Resurs loggar genereras automatiskt av Azure-resurser som stöds, men de samlas inte in om du inte konfigurerar dem med hjälp av en [diagnostisk inställning](diagnostic-settings.md). Skapa en diagnostisk inställning för varje Azure-resurs för att vidarebefordra loggarna till följande destinationer:

| Destination | Scenario |
|:---|:---|:---|
| [Log Analytics-arbetsyta](resource-logs-collect-storage.md) | Analysera loggarna med andra övervaknings data och utnyttja Azure Monitor funktioner som logg frågor och logg aviseringar. |
| [Azure Storage](archive-diagnostic-logs.md) | Arkivera loggarna för granskning eller säkerhets kopiering. |
| [Händelsehubb](resource-logs-stream-event-hubs.md) | Strömma loggarna till loggnings-och telemetri system från tredje part.  |

## <a name="compute-resources"></a>Beräkningsresurser
Resurs loggar skiljer sig från gäst loggar på operativ system nivå i Azure Compute-resurser. Beräknings resurser kräver en agent för att samla in loggar och mått från sitt gäst operativ system, inklusive sådana data som händelse loggar, syslog och prestanda räknare. Använd [Diagnostic-tillägget](agents-overview.md#azure-diagnostic-extension) för att dirigera loggdata från virtuella Azure-datorer och [Log Analytics agenten](agents-overview.md#log-analytics-agent) för att samla in loggar och mått från virtuella datorer i Azure, i andra moln och lokalt i en Log Analytics arbets yta. Mer information finns i [källor för övervaknings data för Azure Monitor](data-sources.md) .

## <a name="resource-logs-schema"></a>Schema för resurs loggar
Varje Azure-tjänst har sitt eget schema när resurs loggar skrivs till någon av målen, men alla delar ett schema på högsta nivå i följande tabell. Se [tjänstspecifika scheman](#service-specific-schemas) nedan för länkar till schemat för varje tjänst. 

| Name | Obligatorisk/valfri | Beskrivning |
|---|---|---|
| time | Obligatorisk | Tids stämplingen (UTC) för händelsen. |
| resourceId | Obligatorisk | Resurs-ID för den resurs som har orsakat händelsen. För klient tjänster är detta av formatet/Tenants/Tenant-ID/providers/Provider-Name. |
| tenantId | Krävs för klient loggar | Klient-ID för den Active Directory klient som den här händelsen är kopplad till. Den här egenskapen används bara för loggar på klient nivå, den visas inte i loggar på resurs nivå. |
| operationName | Obligatorisk | Namnet på åtgärden som representeras av den här händelsen. Om händelsen representerar en RBAC-åtgärd är detta namnet på RBAC-åtgärden (t. ex. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Vanligt vis modelleras i form av en Resource Manager-åtgärd, även om de inte är faktiska dokumenterade Resource Manager`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`-åtgärder () |
| operationVersion | Valfritt | Den API-version som är kopplad till åtgärden, om operationName utfördes med hjälp av ett API (t. ex. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Om det inte finns något API som motsvarar den här åtgärden representerar-versionen den åtgärd som är associerad med åtgärden i framtiden. |
| category | Obligatorisk | Händelsens logg kategori. Kategori är den granularitet som du kan använda för att aktivera eller inaktivera loggar för en viss resurs. Egenskaperna som visas i en händelses egenskaps-BLOB är desamma inom en viss logg kategori och resurs typ. Typiska logg kategorier är "granskning", "körning" och "begäran". |
| resultType | Valfritt | Händelsens status. Vanliga värden är startad, pågår, lyckades, misslyckades, aktivt och löst. |
| resultSignature | Valfritt | Händelsens under status. Om den här åtgärden motsvarar ett REST API-anrop är detta HTTP-statuskod för motsvarande REST-anrop. |
| resultDescription | Valfritt | Den statiska text beskrivningen för den här åtgärden, t. ex. "Hämta lagrings fil". |
| durationMs | Valfritt | Åtgärdens varaktighet i millisekunder. |
| callerIpAddress | Valfritt | IP-adressen för anroparen, om åtgärden motsvarar ett API-anrop som kommer från en entitet med en offentligt tillgänglig IP-adress. |
| correlationId | Valfritt | Ett GUID som används för att gruppera samman en uppsättning relaterade händelser. Normalt, om två händelser har samma operationName men två olika status värden (t. ex. "Startade" och "lyckades") delar samma korrelations-ID. Detta kan även representera andra relationer mellan händelser. |
| identity | Valfritt | En JSON-blob som beskriver identiteten för den användare eller det program som utförde åtgärden. Detta inkluderar vanligt vis auktorisering och anspråk/JWT-token från Active Directory. |
| Nivå | Valfritt | Händelsens allvarlighets grad. Måste vara en av information, varning, fel eller kritisk. |
| location | Valfritt | Den region i resursen som avger händelsen, t. ex. "USA, östra" eller "Frankrike, södra" |
| properties | Valfritt | Eventuella utökade egenskaper som är relaterade till den här specifika kategorin av händelser. Alla anpassade/unika egenskaper måste placeras i det här "del B" av schemat. |

## <a name="service-specific-schemas"></a>Tjänstspecifika scheman
Schemat för resurs diagnostiska loggar varierar beroende på resurs typen som definieras av `resourceId` egenskapen `category` och egenskaperna. I följande lista visas alla Azure-tjänster som har stöd för resurs loggar med länkar till tjänsten och det projektspecifika schemat där det är tillgängligt.

| Tjänsten | Schema & dokument |
| --- | --- |
| Azure Active Directory | [Översikt](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), schema för [Gransknings logg](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) och [inloggnings tillägg](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management diagnostikloggar](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Programgateways |[Diagnostikloggning för Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics för Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch diagnostisk loggning](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL diagnostikloggar](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL diagnostikloggar](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Cognitive Services | [Diagnostisk loggning för Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Content Delivery Network | [Azure-diagnostikloggar för CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB loggning](../../cosmos-db/logging.md) |
| Data Factory | [Övervaka data fabriker med hjälp av Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Åtkomst till diagnostikloggar för Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Åtkomst till diagnostikloggar för Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs diagnostikloggar](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Schemat är inte tillgängligt. |
| Azure Firewall | Schemat är inte tillgängligt. |
| IoT-hubb | [IoT Hub åtgärder](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault-loggning](../../key-vault/key-vault-logging.md) |
| Load Balancer |[Logganalys för Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Anpassat Logic Apps B2B-spårningsschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Nätverkssäkerhetsgrupper |[Log Analytics för nätverkssäkerhetsgrupper (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Hantera Azure DDoS Protection standard](../../virtual-network/manage-ddos-protection.md) |
| Dedikerad Power BI | [Diagnostisk loggning för Power BI Embedded i Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Data modell för Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Search |[Aktivera och använda Sök Trafikanalys](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus diagnostikloggar](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database diagnostisk loggning](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Jobb diagnostiska loggar](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager logg schema](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuella nätverk | Schemat är inte tillgängligt. |
| Virtuella nätverksgatewayer | Schemat är inte tillgängligt. |

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om andra Azure Platform-loggar](platform-logs-overview.md) som du kan använda för att övervaka Azure.

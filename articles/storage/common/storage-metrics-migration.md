---
title: Azure Storage metrics migration | Microsoft Docs
description: Lär dig hur du migrerar gamla mått till nya mått som hanteras av Azure-Monitor.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 03/30/2018
ms.author: fryu
ms.openlocfilehash: c64061aee94e8c08a3f6bcae78cffca0b4172d97
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650680"
---
# <a name="azure-storage-metrics-migration"></a>Azure Storage metrics migrering

Justerad med strategin för enhetlig övervakaren användarupplevelse i Azure, integrerar Azure Storage mått för Azure-Monitor-plattformen. Tjänsten gamla mått kommer i framtiden, avslutas med en tidig varning baserat på Azure-princip. Om du förlitar sig på gamla storage-mätvärden, måste du migrera före slutdatumet för tjänsten för att underhålla din mått information.

Den här artikeln visar hur du migrerar från gammal mått till nya statistik.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Förstå gamla mått som hanteras av Azure Storage

Azure Storage samlar in gamla måttvärden och sammanställer och lagrar dem i $Metric tabeller i samma lagringskonto. Du kan använda Azure-portalen för att ställa in ett diagram för övervakning. Du kan också använda Azure Storage SDK: erna för att läsa data från $Metric tabeller som är baserade på schemat. Mer information finns i [Storage Analytics](./storage-analytics.md).

Gamla mått ange kapacitetsdata bara på Azure Blob storage. Gamla mått ger transaktion mått för Blob storage, Table storage, Azure-filer och Queue storage. 

Gamla mått är utformade för i platt schemat. Designen resulterar i värdet noll mått när du inte har trafikmönster utlösa måttet. Till exempel den **ServerTimeoutError** värdet anges till 0 i $Metric tabeller, även om du inte får någon server timeout-fel från live trafiken till ett lagringskonto.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Förstå nya mått som hanteras av Azure-Monitor

För nya storage-mätvärden skickar Azure Storage mått data till Azure-Monitor serverdelen. Azure övervakning ger en enhetlig övervakning miljö, inklusive data från portalen samt datapåfyllning. Mer information kan du referera till detta [artikel](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nya ange kapacitetsdata och transaktionen mått på Blob, tabell, fil, kön och premium-lagring.

Flera dimensionen är en av de funktioner som ger Azure-Monitor. Azure Storage antar design för att definiera nya mått schemat. För stöds dimensioner på mått hittar du information i [Azure Storage-mätvärden i Azure-Monitor](./storage-metrics-in-azure-monitor.md). Flera dimension designen innehåller kostnadseffektivitet på båda bandbredd från införandet och kapacitet från att lagra mått. Därför om trafiken inte utlöses relaterade mått genereras relaterade måttinformationen inte. Till exempel om trafiken inte aktiveras har någon server timeout-fel, Azure-Monitor inte returnerar några data när du frågar värdet för måttet **transaktioner** med dimension **ResponseType** lika med **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Mått mappning mellan gamla mått och nya

Om du har läst mätvärden via programmering måste att införa nya mått schemat i dina program. För att bättre förstå ändringarna, kan du referera till mappningen visas i följande tabell:

**Kapacitet mått**

| Gamla mått | Nytt mått |
| ------------------- | ----------------- |
| **Kapacitet**            | **BlobCapacity** med dimensionen **BlobType** lika med **BlockBlob** eller **PageBlob** |
| **ObjectCount**        | **BlobCount** med dimensionen **BlobType** lika med **BlockBlob** eller **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

Följande mått är nya erbjudanden som inte har stöd för de gamla mått:
* **TableCapacity**
* **TableCount**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **FileCapacity**
* **FileCount**
* **FileShareCount**
* **UsedCapacity**

**Transaktionen mått**

| Gamla mått | Nytt mått |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Transaktioner med dimensionen **ResponseType** lika med **AuthorizationError** |
| **AnonymousClientOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ClientOtherError** |
| **AnonymousClientTimeoutError** | Transaktioner med dimensionen **ResponseType** lika med **ClientTimeoutError** |
| **AnonymousNetworkError** | Transaktioner med dimensionen **ResponseType** lika med **NetworkError** |
| **AnonymousServerOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ServerOtherError** |
| **AnonymousServerTimeoutError** | Transaktioner med dimensionen **ResponseType** lika med **ServerTimeoutError** |
| **AnonymousSuccess** | Transaktioner med dimensionen **ResponseType** lika med **lyckades** |
| **AnonymousThrottlingError** | Transaktioner med dimensionen **ResponseType** lika med **ClientThrottlingError** eller **ServerBusyError** |
| **AuthorizationError** | Transaktioner med dimensionen **ResponseType** lika med **AuthorizationError** |
| **Tillgänglighet** | **Tillgänglighet** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ClientOtherError** |
| **ClientTimeoutError** | Transaktioner med dimensionen **ResponseType** lika med **ClientTimeoutError** |
| **NetworkError** | Transaktioner med dimensionen **ResponseType** lika med **NetworkError** |
| **PercentAuthorizationError** | Transaktioner med dimensionen **ResponseType** lika med **AuthorizationError** |
| **PercentClientOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ClientOtherError** |
| **PercentNetworkError** | Transaktioner med dimensionen **ResponseType** lika med **NetworkError** |
| **PercentServerOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ServerOtherError** |
| **PercentSuccess** | Transaktioner med dimensionen **ResponseType** lika med **lyckades** |
| **PercentThrottlingError** | Transaktioner med dimensionen **ResponseType** lika med **ClientThrottlingError** eller **ServerBusyError** |
| **PercentTimeoutError** | Transaktioner med dimensionen **ResponseType** lika med **ServerTimeoutError** eller **ResponseType** lika med **ClientTimeoutError** |
| **SASAuthorizationError** | Transaktioner med dimensionen **ResponseType** lika med **AuthorizationError** |
| **SASClientOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ClientOtherError** |
| **SASClientTimeoutError** | Transaktioner med dimensionen **ResponseType** lika med **ClientTimeoutError** |
| **SASNetworkError** | Transaktioner med dimensionen **ResponseType** lika med **NetworkError** |
| **SASServerOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ServerOtherError** |
| **SASServerTimeoutError** | Transaktioner med dimensionen **ResponseType** lika med **ServerTimeoutError** |
| **SASSuccess** | Transaktioner med dimensionen **ResponseType** lika med **lyckades** |
| **SASThrottlingError** | Transaktioner med dimensionen **ResponseType** lika med **ClientThrottlingError** eller **ServerBusyError** |
| **ServerOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ServerOtherError** |
| **ServerTimeoutError** | Transaktioner med dimensionen **ResponseType** lika med **ServerTimeoutError** |
| **Lyckades** | Transaktioner med dimensionen **ResponseType** lika med **lyckades** |
| **ThrottlingError** | **Transaktioner** med dimensionen **ResponseType** lika med **ClientThrottlingError** eller **ServerBusyError**|
| **TotalBillableRequests** | **Transaktioner** |
| **TotalEgress** | **Utgående** |
| **TotalIngress** | **Inkommande** |
| **TotalRequests** | **Transaktioner** |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="how-should-i-migrate-existing-alert-rules"></a>Hur får jag migrera befintliga Varningsregler?

Om du har skapat klassiska Varningsregler baserat på gamla storage-mätvärden, måste du skapa nya regler baserat på det nya schemat för mått.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Nya mått data lagras i samma lagringskonto som standard?

Nej. Om du vill arkivera mätvärden till ett lagringskonto, Använd den [Azure övervakaren diagnostiska inställningen API](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Nästa steg

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Storage-mätvärden i Azure-Monitor](./storage-metrics-in-azure-monitor.md)

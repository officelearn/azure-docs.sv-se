---
title: Migrering av Azure Storage-mått | Microsoft Docs
description: Lär dig hur du migrerar gamla mått till nya mått som hanteras av Azure Monitor.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/30/2018
ms.author: fryu
ms.component: common
ms.openlocfilehash: 3f2ebb82f5affa3c41f237edcc039eb6214c7a4c
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649303"
---
# <a name="azure-storage-metrics-migration"></a>Migrering av Azure Storage-mått

I linje med strategi för enhetlig övervakaren användarupplevelse i Azure, integrerar Azure Storage mätvärden för Azure Monitor-plattformen. I framtiden, upphör tjänsten av de gamla mått med en tidig meddelande baserat på Azure policy. Om du förlitar dig på gamla lagringsmått måste du migrera före slutdatumet för tjänsten för att upprätthålla din mått information.

Den här artikeln visar hur du migrerar från den gamla måtten till den nya måtten.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Förstå gamla mått som hanteras av Azure Storage

Azure Storage samlar in gamla måttvärden och aggregerar och lagrar dem i $Metric tabeller inom samma lagringskonto. Du kan använda Azure-portalen för att ställa in en Övervakningsdiagrammet. Du kan också använda Azure Storage SDK: er för att läsa data från $Metric tabeller som baseras på schemat. Mer information finns i [Lagringsanalys](./storage-analytics.md).

Gamla mätvärden är kapacitetsmått som påverkar endast på Azure Blob storage. Gamla mätvärden är transaktionsmått på Blob storage, Table storage, Azure Files och Queue storage.

Gamla mått är utformade i ett fast schema. Designen leder noll måttvärde när du inte har trafikmönster utlösa måttet. Till exempel den **ServerTimeoutError** värdet anges till 0 i $Metric tabeller, även om du inte får någon server timeout-fel från live-trafik till ett lagringskonto.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Förstå nya mått som hanteras av Azure Monitor

För nya lagringsmått genererar måttdata till Azure Monitor-serverdel i Azure Storage. Azure Monitor innehåller en enhetlig övervakningsupplevelse, inklusive data från portalen samt datainmatning. Mer information kan du referera till den här [artikeln](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nya mått ger kapacitet och transaktionsmått utifrån Blob, tabell, fil, kö och premiumlagring.

Måttkommandon är en av de funktioner som Azure Monitor innehåller. Azure Storage antar design för att definiera nya mått schemat. För stöds dimensioner på mått du hittar information i [Azure Storage-mått i Azure Monitor](./storage-metrics-in-azure-monitor.md). Flera dimensionsdesign innehåller kostnadseffektivitet om båda bandbredden från inmatning och kapacitet från att lagra mått. Därför om trafiken inte har utlöst relaterade mått, genereras relaterade måttdata inte. Till exempel om trafiken inte aktiveras har någon server timeout-fel, Azure Monitor inte returnerar några data när du frågar värdet för måttet **transaktioner** med dimensionen **ResponseType** lika med **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Mått mappningen mellan gamla mått och nya mått

Om du har läst mätvärden via programmering behöver att införa nya mått schemat i dina program. För att bättre förstå ändringarna, kan du referera till mappningen som visas i följande tabell:

**Kapacitet mått**

| Gamla mått | Nya mått |
| ------------------- | ----------------- |
| **Kapacitet**            | **BlobCapacity** med dimensionen **BlobType** lika **BlockBlob** eller **PageBlob** |
| **ObjectCount**        | **BlobCount** med dimensionen **BlobType** lika **BlockBlob** eller **PageBlob** |
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

**Transaktionsmått**

| Gamla mått | Nya mått |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Transaktioner med dimensionen **ResponseType** lika **AuthorizationError** - och dimensionstabeller **autentisering** lika **anonym** |
| **AnonymousClientOtherError** | Transaktioner med dimensionen **ResponseType** lika **ClientOtherError** - och dimensionstabeller **autentisering** lika **anonym** |
| **AnonymousClientTimeoutError** | Transaktioner med dimensionen **ResponseType** lika **ClientTimeoutError** - och dimensionstabeller **autentisering** lika **anonym** |
| **AnonymousNetworkError** | Transaktioner med dimensionen **ResponseType** lika **NetworkError** - och dimensionstabeller **autentisering** lika **anonym** |
| **AnonymousServerOtherError** | Transaktioner med dimensionen **ResponseType** lika **ServerOtherError** - och dimensionstabeller **autentisering** lika **anonym** |
| **AnonymousServerTimeoutError** | Transaktioner med dimensionen **ResponseType** lika **ServerTimeoutError** - och dimensionstabeller **autentisering** lika **anonym** |
| **AnonymousSuccess** | Transaktioner med dimensionen **ResponseType** lika **lyckades** - och dimensionstabeller **autentisering** lika **anonym** |
| **AnonymousThrottlingError** | Transaktioner med dimensionen **ResponseType** lika **ClientThrottlingError** eller **ServerBusyError** - och dimensionstabeller **autentisering** lika **anonym** |
| **AuthorizationError** | Transaktioner med dimensionen **ResponseType** lika **AuthorizationError** |
| **Tillgänglighet** | **Tillgänglighet** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Transaktioner med dimensionen **ResponseType** lika **ClientOtherError** |
| **ClientTimeoutError** | Transaktioner med dimensionen **ResponseType** lika **ClientTimeoutError** |
| **NetworkError** | Transaktioner med dimensionen **ResponseType** lika **NetworkError** |
| **PercentAuthorizationError** | Transaktioner med dimensionen **ResponseType** lika **AuthorizationError** |
| **PercentClientOtherError** | Transaktioner med dimensionen **ResponseType** lika **ClientOtherError** |
| **PercentNetworkError** | Transaktioner med dimensionen **ResponseType** lika **NetworkError** |
| **PercentServerOtherError** | Transaktioner med dimensionen **ResponseType** lika **ServerOtherError** |
| **PercentSuccess** | Transaktioner med dimensionen **ResponseType** lika **lyckades** |
| **PercentThrottlingError** | Transaktioner med dimensionen **ResponseType** lika **ClientThrottlingError** eller **ServerBusyError** |
| **PercentTimeoutError** | Transaktioner med dimensionen **ResponseType** lika **ServerTimeoutError** eller **ResponseType** lika **ClientTimeoutError** |
| **SASAuthorizationError** | Transaktioner med dimensionen **ResponseType** lika **AuthorizationError** - och dimensionstabeller **autentisering** lika **SAS** |
| **SASClientOtherError** | Transaktioner med dimensionen **ResponseType** lika **ClientOtherError** - och dimensionstabeller **autentisering** lika **SAS** |
| **SASClientTimeoutError** | Transaktioner med dimensionen **ResponseType** lika **ClientTimeoutError** - och dimensionstabeller **autentisering** lika **SAS** |
| **SASNetworkError** | Transaktioner med dimensionen **ResponseType** lika **NetworkError** - och dimensionstabeller **autentisering** lika **SAS** |
| **SASServerOtherError** | Transaktioner med dimensionen **ResponseType** lika **ServerOtherError** - och dimensionstabeller **autentisering** lika **SAS** |
| **SASServerTimeoutError** | Transaktioner med dimensionen **ResponseType** lika **ServerTimeoutError** - och dimensionstabeller **autentisering** lika **SAS** |
| **SASSuccess** | Transaktioner med dimensionen **ResponseType** lika **lyckades** - och dimensionstabeller **autentisering** lika **SAS** |
| **SASThrottlingError** | Transaktioner med dimensionen **ResponseType** lika **ClientThrottlingError** eller **ServerBusyError** - och dimensionstabeller **autentisering** lika **SAS** |
| **ServerOtherError** | Transaktioner med dimensionen **ResponseType** lika **ServerOtherError** |
| **ServerTimeoutError** | Transaktioner med dimensionen **ResponseType** lika **ServerTimeoutError** |
| **Lyckades** | Transaktioner med dimensionen **ResponseType** lika **lyckades** |
| **ThrottlingError** | **Transaktioner** med dimensionen **ResponseType** lika **ClientThrottlingError** eller **ServerBusyError**|
| **TotalBillableRequests** | **Transaktioner** |
| **TotalEgress** | **Utgående** |
| **TotalIngress** | **Inkommande** |
| **TotalRequests** | **Transaktioner** |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="how-should-i-migrate-existing-alert-rules"></a>Hur ska jag migrerar befintliga Varningsregler?

Om du har skapat klassiska Varningsregler baserat på gamla mätvärden i storage kan behöva du skapa en ny avisering regler baserat på det nya måttet schemat.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Nya mått data lagras i samma lagringskonto som standard?

Nej. Om du vill arkivera måttdata till ett lagringskonto, använda den [diagnostiska inställningen-API i Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Nästa steg

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Storage-mått i Azure Monitor](./storage-metrics-in-azure-monitor.md)

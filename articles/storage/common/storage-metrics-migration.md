---
title: Azure Storage metrics migration | Microsoft Docs
description: Läs mer om hur du migrerar äldre mått till nya mått som hanteras av Azure-Monitor.
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
ms.openlocfilehash: c4dc9b231668315af16c625314c737fee99d672d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="azure-storage-metrics-migration"></a>Azure Storage metrics migrering

Justerade sammanbindande övervakaren upplevelse i Azure-strategi, Azure Storage på-kort mått för Azure-Monitor-plattformen. I framtiden, avslutas tjänsten äldre mått med tidig meddelande utifrån Azure-princip. Om du förlitar sig på äldre storage-mätvärden, måste du migrera före slutdatumet för tjänsten för att underhålla din mått information.

Den här artikeln beskriver hur du migrerar från äldre mått till nya statistik.

## <a name="understand-legacy-metrics-managed-by-azure-storage"></a>Förstå äldre mått som hanteras av Azure Storage

Azure Storage samlar in äldre måttvärden mängder, och lagrar dem i $Metric tabeller i samma lagringskonto. Du kan använda Azure-portalen för att konfigurera övervakning diagram och du kan också använda Azure Storage SDK: er för att läsa data från $Metric tabeller baserat på schemat. Mer information kan du referera till detta [Storage Analytics](./storage-analytics.md).

Äldre mått ange kapacitetsmått för blobben endast och transaktionen mått på Blob-, tabell-, fil- och kön.

Äldre mått är utformade för i platt schemat. Designen resulterar i värdet noll mått när du inte har trafikmönster utlösa måttet. Till exempel **ServerTimeoutError** anges till 0 i $Metric tabeller, även om du inte får någon server timeout-fel från live trafik till lagringskontot.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Förstå nya mått som hanteras av Azure-Monitor

För nya storage-mätvärden skickar Azure Storage mått data till Azure-Monitor-serverdelen. Azure övervakning ger en enhetlig övervakning miljö, inklusive data från portalen samt datapåfyllning. Mer information kan du referera till detta [artikel](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nya ange kapacitetsdata och transaktionen mått på Blob, tabell, fil, kön och premium-lagring.

Flera dimensionen är en funktion som tillhandahålls av Azure-Monitor. Azure Storage antar design för att definiera nya mått schemat. För stöds dimensioner på mått hittar du information i det här [Azure Storage-mätvärden i Azure-Monitor](./storage-metrics-in-azure-monitor.md). Flera dimension designen innehåller kostnadseffektivitet på båda bandbredd från införandet och kapacitet från att lagra mått. Därför om trafiken inte utlöses relaterade mått genereras relaterade måttinformationen inte. Till exempel om trafiken inte aktiveras har någon server timeout-fel, Azure-Monitor returnerar inga data när du frågar värdet för måttet **transaktioner** med dimension **ResponseType** lika med **ServerTimeoutError**.

## <a name="metrics-mapping-between-legacy-metrics-and-new-metrics"></a>Mått mappning mellan äldre mått och nya

Om du har läst mätvärden via programmering måste att införa nya mått schemat i dina program. För att bättre förstå ändringarna, kan du referera till mappningen visas i följande tabell:

**Kapacitet mått**

| Äldre mått | Nytt mått |
| ------------------- | ----------------- |
| Kapacitet            | BlobCapacity med dimension BlobType som är lika med BlockBlob eller PageBlob |
| ObjectCount         | BlobCount med dimension BlobType som är lika med BlockBlob eller PageBlob |
| ContainerCount      | ContainerCount |

Följande mått är nya erbjudanden som inte har stöd för äldre mått:
* TableCapacity
* TableCount
* TableEntityCount
* QueueCapacity
* QueueCount
* QueueMessageCount
* FileCapacity
* FileCount
* FileShareCount
* UsedCapacity

**Transaktionen mått**

| Äldre mått | Nytt mått |
| ------------------- | ----------------- |
| AnonymousAuthorizationError | Transaktioner med dimension ResponseType som är lika med AuthorizationError |
| AnonymousClientOtherError | Transaktioner med dimension ResponseType som är lika med ClientOtherError |
| AnonymousClientTimeoutError | Transaktioner med dimension ResponseType som är lika med ClientTimeoutError |
| AnonymousNetworkError | Transaktioner med dimension ResponseType som är lika med NetworkError |
| AnonymousServerOtherError | Transaktioner med dimension ResponseType som är lika med ServerOtherError |
| AnonymousServerTimeoutError | Transaktioner med dimension ResponseType som är lika med ServerTimeoutError |
| AnonymousSuccess | Transaktioner med dimension ResponseType som är lika med lyckades |
| AnonymousThrottlingError | Transaktioner med dimension ResponseType som är lika med ClientThrottlingError eller ServerBusyError |
| AuthorizationError | Transaktioner med dimension ResponseType som är lika med AuthorizationError |
| Tillgänglighet | Tillgänglighet |
| AverageE2ELatency | SuccessE2ELatency |
| AverageServerLatency | SuccessServerLatency |
| ClientOtherError | Transaktioner med dimension ResponseType som är lika med ClientOtherError |
| ClientTimeoutError | Transaktioner med dimension ResponseType som är lika med ClientTimeoutError |
| NetworkError | Transaktioner med dimension ResponseType som är lika med NetworkError |
| PercentAuthorizationError | Transaktioner med dimension ResponseType som är lika med AuthorizationError |
| PercentClientOtherError | Transaktioner med dimension ResponseType som är lika med ClientOtherError |
| PercentNetworkError | Transaktioner med dimension ResponseType som är lika med NetworkError |
| PercentServerOtherError | Transaktioner med dimension ResponseType som är lika med ServerOtherError |
| PercentSuccess | Transaktioner med dimension ResponseType som är lika med lyckades |
| PercentThrottlingError | Transaktioner med dimension ResponseType som är lika med ClientThrottlingError eller ServerBusyError |
| PercentTimeoutError | Transaktioner med dimension ResponseType som är lika med ServerTimeoutError eller ResponseType som är lika med ClientTimeoutError|
| SASAuthorizationError | Transaktioner med dimension ResponseType som är lika med AuthorizationError |
| SASClientOtherError | Transaktioner med dimension ResponseType som är lika med ClientOtherError |
| SASClientTimeoutError | Transaktioner med dimension ResponseType som är lika med ClientTimeoutError |
| SASNetworkError | Transaktioner med dimension ResponseType som är lika med NetworkError |
| SASServerOtherError | Transaktioner med dimension ResponseType som är lika med ServerOtherError |
| SASServerTimeoutError | Transaktioner med dimension ResponseType som är lika med ServerTimeoutError |
| SASSuccess | Transaktioner med dimension ResponseType som är lika med lyckades |
| SASThrottlingError | Transaktioner med dimension ResponseType som är lika med ClientThrottlingError eller ServerBusyError |
| ServerOtherError | Transaktioner med dimension ResponseType som är lika med ServerOtherError |
| ServerTimeoutError | Transaktioner med dimension ResponseType som är lika med ServerTimeoutError |
| Lyckades | Transaktioner med dimension ResponseType som är lika med lyckades |
| ThrottlingError | Transaktioner med dimension ResponseType som är lika med ClientThrottlingError eller ServerBusyError |
| TotalBillableRequests | Transaktioner |
| TotalEgress | Egress |
| TotalIngress | Ingångshändelser |
| TotalRequests | Transaktioner |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

* Hur får jag migrera befintliga Varningsregler?

S: Om du har skapat klassiska avisering regler baserat på äldre storage-mätvärden, måste du skapa ny avisering regler baserat på nya mått schemat.

* Kommer nya mått data som lagras i samma lagringskonto som standard?

S: Nej. Om du behöver Arkivera mätvärden till storage-konto kan du använda [Diagnostikinställningen i Azure-Monitor](https://azure.microsoft.com/blog/azure-monitor-multiple-diagnostic-settings/)

## <a name="next-steps"></a>Nästa steg

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Storage-mätvärden i Azure-Monitor](./storage-metrics-in-azure-monitor.md)

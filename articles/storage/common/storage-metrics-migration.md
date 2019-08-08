---
title: Azure Storage mått migrering | Microsoft Docs
description: Lär dig hur du migrerar gamla mått till nya mått som hanteras av Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 537369c9466b1083723642ec9e93fcdf25056c5e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855346"
---
# <a name="azure-storage-metrics-migration"></a>Migrering av Azure Storage mått

Med strategin för att förena övervaknings upplevelsen i Azure, Azure Storage integreras måtten för Azure Monitor-plattformen. I framtiden kommer tjänsten för de gamla måtten att avslutas med ett tidigt meddelande baserat på Azure policy. Om du förlitar dig på gamla lagrings mått måste du migrera före tjänstens slutdatum för att kunna underhålla din mått information.

Den här artikeln visar hur du migrerar från gamla mått till nya mått.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Förstå gamla mått som hanteras av Azure Storage

Azure Storage samlar in gamla mått värden och samlar in och lagrar dem i $Metric tabeller inom samma lagrings konto. Du kan använda Azure Portal för att skapa ett övervaknings diagram. Du kan också använda Azure Storage SDK: er för att läsa data från $Metric tabeller som baseras på schemat. Mer information finns i [Lagringsanalys](./storage-analytics.md).

Gamla mått tillhandahåller kapacitets mått endast för Azure Blob Storage. Gamla mått tillhandahåller transaktions mått för Blob Storage, Table Storage, Azure Files och Queue Storage.

Gamla mått är utformade i ett plant schema. Designen resulterar i noll mått värde när du inte har trafik mönstren som utlöser måttet. **ServerTimeoutError** -värdet är till exempel inställt på 0 i $Metric tabeller även om du inte får några timeout-fel för servern från den aktiva trafiken till ett lagrings konto.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Förstå nya mått som hanteras av Azure Monitor

För nya lagrings mått bevarar Azure Storage mått data till Azure Monitor Server delen. Azure Monitor ger en enhetlig övervaknings upplevelse, inklusive data från portalen samt data inmatning. Mer information hittar du i den här [artikeln](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nya mått ger kapacitets mått och transaktions mått för BLOB-, tabell-, fil-, kö-och Premium-lagring.

Flera dimensioner är en av de funktioner som Azure Monitor tillhandahåller. Azure Storage antar designen i att definiera ett nytt mått schema. För mått som stöds för mått kan du hitta information i [Azure Storage mått i Azure Monitor](./storage-metrics-in-azure-monitor.md). Design med flera dimensioner ger kostnads effektivitet på både bandbredd från inhämtning och kapacitet från lagring av mått. Det innebär att om trafiken inte har utlösta relaterade mått genereras inte relaterade mått data. Om din trafik till exempel inte har utlöst några Server tids gräns fel returnerar Azure Monitor inte några data när du frågar värdet för mått **transaktioner** med dimension **ResponseType** lika med **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Mått mappning mellan gamla mått och nya mått

Om du läser mått data program mässigt måste du införa det nya mått schemat i dina program. För att bättre förstå ändringarna kan du referera till mappningen som anges i följande tabell:

**Kapacitets mått**

| Gammalt mått | Nytt mått |
| ------------------- | ----------------- |
| **Kapacitet**            | **BlobCapacity** med dimension **BlobType** lika med **BlockBlob** eller **PageBlob** |
| **ObjectCount**        | **BlobCount** med dimension **BlobType** lika med **BlockBlob** eller **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

Följande mått är nya erbjudanden som de gamla måtten inte stöder:
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

**Transaktions mått**

| Gammalt mått | Nytt mått |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Transaktioner med dimension **ResponseType** som är lika med **AuthorizationError** och dimensions **autentisering** är lika med **Anonym** |
| **AnonymousClientOtherError** | Transaktioner med dimension **ResponseType** som är lika med **ClientOtherError** och dimensions **autentisering** är lika med **Anonym** |
| **AnonymousClientTimeoutError** | Transaktioner med dimension **ResponseType** som är lika med **ClientTimeoutError** och dimensions **autentisering** är lika med **Anonym** |
| **AnonymousNetworkError** | Transaktioner med dimension **ResponseType** som är lika med **NetworkError** och dimensions **autentisering** är lika med **Anonym** |
| **AnonymousServerOtherError** | Transaktioner med dimension **ResponseType** som är lika med **ServerOtherError** och dimensions **autentisering** är lika med **Anonym** |
| **AnonymousServerTimeoutError** | Transaktioner med dimension **ResponseType** som är lika med **ServerTimeoutError** och dimensions **autentisering** är lika med **Anonym** |
| **AnonymousSuccess** | Transaktioner med dimensions **ResponseType** som är lika med **lyckades** och dimensions **autentisering** är lika med **Anonym** |
| **AnonymousThrottlingError** | Transaktioner med dimension **ResponseType** som är lika med **ClientThrottlingError** eller **ServerBusyError** och dimensions **autentisering** är lika med **Anonym** |
| **AuthorizationError** | Transaktioner med dimension **ResponseType** lika med **AuthorizationError** |
| **Tillgänglighet** | **Tillgänglighet** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Transaktioner med dimension **ResponseType** lika med **ClientOtherError** |
| **ClientTimeoutError** | Transaktioner med dimension **ResponseType** lika med **ClientTimeoutError** |
| **NetworkError** | Transaktioner med dimension **ResponseType** lika med **NetworkError** |
| **PercentAuthorizationError** | Transaktioner med dimension **ResponseType** lika med **AuthorizationError** |
| **PercentClientOtherError** | Transaktioner med dimension **ResponseType** lika med **ClientOtherError** |
| **PercentNetworkError** | Transaktioner med dimension **ResponseType** lika med **NetworkError** |
| **PercentServerOtherError** | Transaktioner med dimension **ResponseType** lika med **ServerOtherError** |
| **PercentSuccess** | Transaktioner med dimensions **ResponseType** som är lika med **lyckad** |
| **PercentThrottlingError** | Transaktioner med dimension **ResponseType** lika med **ClientThrottlingError** eller **ServerBusyError** |
| **PercentTimeoutError** | Transaktioner med dimension **ResponseType** lika med **ServerTimeoutError** eller **ResponseType** som är lika med **ClientTimeoutError** |
| **SASAuthorizationError** | Transaktioner med dimension **ResponseType** som är lika med **AuthorizationError** och dimensions **autentisering** är lika med **SAS** |
| **SASClientOtherError** | Transaktioner med dimension **ResponseType** som är lika med **ClientOtherError** och dimensions **autentisering** är lika med **SAS** |
| **SASClientTimeoutError** | Transaktioner med dimension **ResponseType** som är lika med **ClientTimeoutError** och dimensions **autentisering** är lika med **SAS** |
| **SASNetworkError** | Transaktioner med dimension **ResponseType** som är lika med **NetworkError** och dimensions **autentisering** är lika med **SAS** |
| **SASServerOtherError** | Transaktioner med dimension **ResponseType** som är lika med **ServerOtherError** och dimensions **autentisering** är lika med **SAS** |
| **SASServerTimeoutError** | Transaktioner med dimension **ResponseType** som är lika med **ServerTimeoutError** och dimensions **autentisering** är lika med **SAS** |
| **SASSuccess** | Transaktioner med dimensions **ResponseType** som är lika med **lyckades** och dimensions **autentisering** är lika med **SAS** |
| **SASThrottlingError** | Transaktioner med dimension **ResponseType** som är lika med **ClientThrottlingError** eller **ServerBusyError** och dimensions **autentisering** är lika med **SAS** |
| **ServerOtherError** | Transaktioner med dimension **ResponseType** lika med **ServerOtherError** |
| **ServerTimeoutError** | Transaktioner med dimension **ResponseType** lika med **ServerTimeoutError** |
| **Resultatet** | Transaktioner med dimensions **ResponseType** som är lika med **lyckad** |
| **ThrottlingError** | **Transaktioner** med dimension **ResponseType** lika med **ClientThrottlingError** eller **ServerBusyError**|
| **TotalBillableRequests** | **Transaktioner** |
| **TotalEgress** | **Utgående** |
| **Total ingress** | **Inkommande** |
| **TotalRequests** | **Transaktioner** |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="how-should-i-migrate-existing-alert-rules"></a>Hur migrerar jag befintliga aviserings regler?

Om du har skapat klassiska varnings regler baserade på gamla lagrings mått måste du skapa nya varnings regler baserat på det nya mått schemat.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Är nya mått data lagrade i samma lagrings konto som standard?

Nej. Om du vill arkivera mått data till ett lagrings konto använder du [API: et Azure Monitor Diagnostic Setting](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Nästa steg

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Lagrings mått i Azure Monitor](./storage-metrics-in-azure-monitor.md)

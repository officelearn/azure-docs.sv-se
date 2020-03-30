---
title: Migrering av Azure Storage-mått | Microsoft-dokument
description: Lär dig hur du migrerar gamla mått till nya mått som hanteras av Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 537369c9466b1083723642ec9e93fcdf25056c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68855346"
---
# <a name="azure-storage-metrics-migration"></a>Migrering av Azure Storage-mått

Azure Storage är anpassat till strategin att ena övervakningsupplevelsen i Azure och integrerar mått till Azure Monitor-plattformen. I framtiden avslutas tjänsten för de gamla måtten med ett tidigt meddelande baserat på Azure-principen. Om du förlitar dig på gamla lagringsmått måste du migrera före tjänstens slutdatum för att underhålla mätinformationen.

Den här artikeln visar hur du migrerar från de gamla måtten till de nya måtten.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Förstå gamla mått som hanteras av Azure Storage

Azure Storage samlar in gamla måttvärden och sammanställer och lagrar dem i $Metric tabeller i samma lagringskonto. Du kan använda Azure-portalen för att konfigurera ett övervakningsdiagram. Du kan också använda Azure Storage SDK:er för att läsa data från $Metric tabeller som baseras på schemat. Mer information finns i [Storage Analytics](./storage-analytics.md).

Gamla mått ger kapacitetsmått endast på Azure Blob-lagring. Gamla mått ger transaktionsmått för Blob-lagring, Tabelllagring, Azure-filer och kölagring.

Gamla mått är utformade i ett platt schema. Designen resulterar i noll måttvärde när du inte har de trafikmönster som utlöser måttet. **ServerTimeoutError-värdet** är till exempel inställt på 0 i $Metric tabeller även när du inte får några tidsutgångsfel för servern från direkttrafiken till ett lagringskonto.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Förstå nya mått som hanteras av Azure Monitor

För nya lagringsmått avger Azure Storage mätdata till Azure Monitor-backend. Azure Monitor ger en enhetlig övervakningsupplevelse, inklusive data från portalen samt datainmatning. Mer information finns i den här [artikeln](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nya mått ger kapacitetsmått och transaktionsmått för Blob, Tabell, Arkiv, Kö och premiumlagring.

Multi-dimension är en av de funktioner som Azure Monitor tillhandahåller. Azure Storage antar designen för att definiera nya måttschema. För dimensioner som stöds för mått kan du hitta information i [Azure Storage-mått i Azure Monitor](./storage-metrics-in-azure-monitor.md). Design med flera dimensioner ger kostnadseffektivitet på både bandbredd från intag och kapacitet från lagring av mått. Om din trafik inte har utlöst relaterade mått genereras därför inte relaterade måttdata. Om din trafik till exempel inte har utlöst några tidsutgångsfel för servern returnerar Azure Monitor inga data när du frågar värdet för **måtttransaktioner** med **dimensionEn ResponseType** som är lika med **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Mappning mellan gamla och nya mått

Om du läser måttdata programmässigt måste du anta det nya måttschemat i dina program. Om du vill förstå ändringarna bättre kan du läsa mappningen i följande tabell:

**Kapacitetsmått**

| Gammalt mått | Nytt mått |
| ------------------- | ----------------- |
| **Kapacitet**            | **BlobCapacity** med dimensionen **BlobType** lika med **BlockBlob** eller **PageBlob** |
| **ObjectCount (ObjektCount)**        | **BlobCount** med dimensionen **BlobType** lika med **BlockBlob** eller **PageBlob** |
| **ContainerCount (ContainerCount)**      | **ContainerCount (ContainerCount)** |

Följande mått är nya erbjudanden som de gamla måtten inte stöder:
* **TabellKapacitet**
* **TabellCount**
* **TableEntityCount**
* **Kökapacitet**
* **QueueCount (QueueCount)**
* **QueueMessageCount**
* **Filkapacitet**
* **FilCount**
* **Filsharecount**
* **UsedCapacity**

**Transaktionsmått**

| Gammalt mått | Nytt mått |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Transaktioner med dimensionen **ResponseType** lika med **AuthorizationError** och **dimensionsautentisering** lika med **Anonym** |
| **AnonymKlientOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ClientOtherError** och **dimensionsautentisering** lika med **anonym** |
| **AnonymClientTimeoutError** | Transaktioner med dimensionen **ResponseType** lika med **ClientTimeoutError** och **dimensionsautentisering** lika med **anonym** |
| **AnonymousNetworkError** | Transaktioner med dimensionen **ResponseType** lika med **NetworkError** och **dimensionsautentisering** lika med **Anonym** |
| **AnonymServerOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ServerOtherError** och **dimensionsautentisering** lika med **anonym** |
| **AnonymServerTimeoutError** | Transaktioner med dimensionen **ResponseType** lika med **ServerTimeoutError** och **dimensionsautentisering** lika med **anonym** |
| **AnonymSuccess** | Transaktioner med dimensionen **ResponseType** lika **med framgång** och **dimensionsautentisering** lika **med Anonym** |
| **AnonymThrottlingError** | Transaktioner med dimensionen **ResponseType** lika med **ClientThrottlingError** eller **ServerBusyError** och **dimensionsautentisering** lika med **Anonym** |
| **AuthorizationError** | Transaktioner med dimensionen **ResponseType** lika med **AuthorizationError** |
| **Tillgänglighet** | **Tillgänglighet** |
| **AverageE2ELatency** | **FramgångE2ELatency** |
| **AverageServerLatency** | **Lycka Till Svar** |
| **ClientOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ClientOtherError** |
| **KlientTimeoutError** | Transaktioner med dimensionen **ResponseType** lika med **ClientTimeoutError** |
| **Nätverkare** | Transaktioner med dimensionen **ResponseType** lika med **NetworkError** |
| **ProcentAuthorizationError** | Transaktioner med dimensionen **ResponseType** lika med **AuthorizationError** |
| **ProcentKlientOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ClientOtherError** |
| **ProcentNätverkAreror** | Transaktioner med dimensionen **ResponseType** lika med **NetworkError** |
| **ProcentServerOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ServerOtherError** |
| **Procentsug** | Transaktioner med dimensionen **ResponseType** lika **med lyckades** |
| **ProcentBegränsningserror** | Transaktioner med dimensionen **ResponseType** lika med **ClientThrottlingError** eller **ServerBusyError** |
| **ProcentTimeoutError** | Transaktioner med dimensionen **ResponseType** lika med **ServerTimeoutError** eller **ResponseType** lika med **ClientTimeoutError** |
| **SASAuthorizationError** | Transaktioner med dimensionen **ResponseType** lika med **AuthorizationError** och **dimensionsautentisering** som är lika med **SAS** |
| **SASClientOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ClientOtherError** och **dimensionsautentisering** som är lika med **SAS** |
| **SASClientTimeoutError** | Transaktioner med dimensionen **ResponseType** lika med **ClientTimeoutError** och **dimensionsautentisering** som är lika med **SAS** |
| **SASNetworkError** | Transaktioner med dimensionen **ResponseType** lika med **NetworkError** och **dimensionsautentisering** lika med **SAS** |
| **SASServerOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ServerOtherError** och **dimensionsautentisering** som är lika med **SAS** |
| **SASServerTimeoutError** | Transaktioner med dimensionen **ResponseType** lika med **ServerTimeoutError** och **dimensionsautentisering** som är lika med **SAS** |
| **SASSuccess** | Transaktioner med dimensionen **ResponseType** lika **med framgång** och **dimensionsautentisering** som är lika med **SAS** |
| **SASThrottlingError** | Transaktioner med dimensionen **ResponseType** lika med **ClientThrottlingError** eller **ServerBusyError** och **dimensionsautentisering** som är lika med **SAS** |
| **ServerOtherError** | Transaktioner med dimensionen **ResponseType** lika med **ServerOtherError** |
| **ServerTimeoutError** | Transaktioner med dimensionen **ResponseType** lika med **ServerTimeoutError** |
| **Framgång** | Transaktioner med dimensionen **ResponseType** lika **med lyckades** |
| **ThrottlingError** | **Transaktioner** med dimensionen **ResponseType** lika med **ClientThrottlingError** eller **ServerBusyError**|
| **TotaltFakturerbara Begärande** | **Transaktioner** |
| **TotalEgress** | **Utgående** |
| **TotalIngress** | **Inkommande** |
| **TotalRequests** | **Transaktioner** |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="how-should-i-migrate-existing-alert-rules"></a>Hur migrerar jag befintliga varningsregler?

Om du har skapat klassiska varningsregler baserat på gamla lagringsmått måste du skapa nya varningsregler baserat på det nya måttschemat.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Lagras nya måttdata i samma lagringskonto som standard?

Nej. Om du vill arkivera måttdata till ett lagringskonto använder du [API:et för azure monitordiagnostikinställning](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Nästa steg

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Lagringsmått i Azure Monitor](./storage-metrics-in-azure-monitor.md)

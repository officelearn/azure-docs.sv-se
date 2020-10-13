---
title: Flytta från Lagringsanalys mått till Azure Monitor mått | Microsoft Docs
description: Lär dig hur du övergår från Lagringsanalys mått (klassiska mått) till mått i Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: c6a5f69a5a32ed1279b367c93b5246eb77ef0208
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802845"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>Över gång till mått i Azure Monitor

Den **31 augusti 2023** Lagringsanalys Mät värden, även kallade *klassiska mått* , kommer att dras tillbaka. Mer information finns i det [officiella meddelandet](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Om du använder klassiska mått, se till att gå över till mått i Azure Monitor före det datumet. Den här artikeln hjälper dig att göra över gången. 

## <a name="steps-to-complete-the-transition"></a>Steg för att slutföra över gången

För att kunna övergå till mått i Azure Monitor rekommenderar vi följande metod.

1. Lär dig mer om några av de [viktigaste skillnaderna](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor) mellan klassiska mått och mått i Azure Monitor. 

2. Kompilera en lista med klassiska mått som du för närvarande använder.

3. Identifiera [vilka mått i Azure Monitor](#metrics-mapping-between-old-metrics-and-new-metrics) ger samma data som de mått som du för närvarande använder. 
   
4. Skapa [diagram](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal) eller [instrument paneler](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal) för att Visa Mät data.

   > [!NOTE]
   > Mått i Azure Monitor är aktiverade som standard, så det finns ingenting som du behöver göra för att börja samla in mått. Du måste dock skapa diagram eller instrument paneler för att visa dessa mått. 
 
5. Om du har skapat varnings regler som baseras på klassiska lagrings mått ska du [skapa aviserings regler](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview) som baseras på mått i Azure Monitor. 

6. När du har kunnat se alla dina mått i Azure Monitor kan du stänga av den klassiska loggningen. 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>Klassiska mått jämfört med mått i Azure Monitor

I det här avsnittet beskrivs några viktiga skillnader mellan de här två mått plattformarna.

Den största skillnaden är i hur mått hanteras. Klassiska mått hanteras av Azure Storage medan måtten i Azure Monitor hanteras av Azure Monitor. Med klassiska mått kan Azure Storage samla in Mät värden, aggregera dem och sedan lagra dem i tabeller som finns i lagrings kontot. Med mått i Azure Monitor skickar Azure Storage mått data till Azure Monitor Server delen. Azure Monitor ger en enhetlig övervaknings miljö som innehåller data från Azure Portal samt data som matas in. 

Klassiska mått skickas och lagras i ett Azure Storage-konto. Azure Monitor mått kan skickas till flera platser. Ett lagrings konto kan vara en av dessa platser, men det är inte obligatoriskt.  

I mån av stöd för mått tillhandahåller klassiska mått endast **kapacitets** mått för Azure Blob Storage. Mått i Azure Monitor tillhandahåller kapacitets mått för BLOB-, tabell-, fil-, kö-och Premium-lagring. Klassiska mått tillhandahåller **transaktions** mått för BLOB, tabell, Azure-fil och Queue Storage. Mått i Azure Monitor lägga till Premium Storage i listan.

Om aktiviteten i ditt konto inte utlöser ett mått visar klassiska mått värdet noll (0) för det måttet. Mått i Azure Monitor kommer att utelämna data helt och hållet, vilket leder till renare rapporter. Till exempel med klassiska mått, om ingen server tids gräns har rapporter ATS, `ServerTimeoutError` anges värdet i mått tabellen till 0. Azure Monitor returnerar inga data när du frågar värdet för måttet `Transactions` med dimension som `ResponseType` är lika med `ServerTimeoutError` . 

Mer information om mått i Azure Monitor finns i [mått i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>Mappa klassiska mått till mått i Azure Monitor

 Använd de här tabellerna för att identifiera vilka mått i Azure Monitor ger samma data som de mått som du för närvarande använder. 

**Kapacitets mått**

| Klassiskt mått | Mått i Azure Monitor |
| ------------------- | ----------------- |
| `Capacity`            | `BlobCapacity` med dimensionen `BlobType` lika med `BlockBlob` eller `PageBlob` |
| `ObjectCount`        | `BlobCount` med dimensionen `BlobType` lika med `BlockBlob` eller `PageBlob` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> Det finns också flera nya kapacitets mått som inte är tillgängliga som klassiska mått. Om du vill visa den fullständiga listan, se [mått](../common/monitor-storage-reference.md#metrics).

**Transaktionsmått**

| Klassiskt mått | Mått i Azure Monitor |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | Transaktioner med dimensionen `ResponseType` lika med `AuthorizationError` och dimension `Authentication` lika med `Anonymous` |
| `AnonymousClientOtherError` | Transaktioner med dimensionen `ResponseType` lika med `ClientOtherError` och dimension `Authentication` lika med `Anonymous` |
| `AnonymousClientTimeoutError` | Transaktioner med dimensionen `ResponseType` lika med `ClientTimeoutError` och dimension `Authentication` lika med `Anonymous` |
| `AnonymousNetworkError` | Transaktioner med dimensionen `ResponseType` lika med `NetworkError` och dimension `Authentication` lika med `Anonymous` |
| `AnonymousServerOtherError` | Transaktioner med dimensionen `ResponseType` lika med `ServerOtherError` och dimension `Authentication` lika med `Anonymous` |
| `AnonymousServerTimeoutError` | Transaktioner med dimensionen `ResponseType` lika med `ServerTimeoutError` och dimension `Authentication` lika med `Anonymous` |
| `AnonymousSuccess` | Transaktioner med dimensionen `ResponseType` lika med `Success` och dimension `Authentication` lika med `Anonymous` |
| `AnonymousThrottlingError` | Transaktioner med dimensionen `ResponseType` lika med `ClientThrottlingError` eller `ServerBusyError` och dimension `Authentication` lika med `Anonymous` |
| `AuthorizationError` | Transaktioner med dimensionen `ResponseType` lika med `AuthorizationError` |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | Transaktioner med dimensionen `ResponseType` lika med `ClientOtherError` |
| `ClientTimeoutError` | Transaktioner med dimensionen `ResponseType` lika med `ClientTimeoutError` |
| `NetworkError` | Transaktioner med dimensionen `ResponseType` lika med `NetworkError` |
| `PercentAuthorizationError` | Transaktioner med dimensionen `ResponseType` lika med `AuthorizationError` |
| `PercentClientOtherError` | Transaktioner med dimensionen `ResponseType` lika med `ClientOtherError` |
| `PercentNetworkError` | Transaktioner med dimensionen `ResponseType` lika med `NetworkError` |
| `PercentServerOtherError` | Transaktioner med dimensionen `ResponseType` lika med `ServerOtherError` |
| `PercentSuccess` | Transaktioner med dimensionen `ResponseType` lika med `Success` |
| `PercentThrottlingError` | Transaktioner med dimensionen `ResponseType` lika med `ClientThrottlingError` eller `ServerBusyError` |
| `PercentTimeoutError` | Transaktioner med dimensionen `ResponseType` lika med `ServerTimeoutError` eller `ResponseType` lika med `ClientTimeoutError` |
| `SASAuthorizationError` | Transaktioner med dimensionen `ResponseType` lika med `AuthorizationError` och dimension `Authentication` lika med `SAS` |
| `SASClientOtherError` | Transaktioner med dimensionen `ResponseType` lika med `ClientOtherError` och dimension `Authentication` lika med `SAS` |
| `SASClientTimeoutError` | Transaktioner med dimensionen `ResponseType` lika med `ClientTimeoutError` och dimension `Authentication` lika med `SAS` |
| `SASNetworkError` | Transaktioner med dimensionen `ResponseType` lika med `NetworkError` och dimension `Authentication` lika med `SAS` |
| `SASServerOtherError` | Transaktioner med dimensionen `ResponseType` lika med `ServerOtherError` och dimension `Authentication` lika med `SAS` |
| `SASServerTimeoutError` | Transaktioner med dimensionen `ResponseType` lika med `ServerTimeoutError` och dimension `Authentication` lika med `SAS` |
| `SASSuccess` | Transaktioner med dimensionen `ResponseType` lika med `Success` och dimension `Authentication` lika med `SAS` |
| `SASThrottlingError` | Transaktioner med dimensionen `ResponseType` lika med `ClientThrottlingError` eller `ServerBusyError` och dimension `Authentication` lika med `SAS` |
| `ServerOtherError` | Transaktioner med dimensionen `ResponseType` lika med `ServerOtherError` |
| `ServerTimeoutError` | Transaktioner med dimensionen `ResponseType` lika med `ServerTimeoutError` |
| `Success` | Transaktioner med dimensionen `ResponseType` lika med `Success` |
| `ThrottlingError` | `Transactions` med dimensionen `ResponseType` lika med `ClientThrottlingError` eller `ServerBusyError`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>Nästa steg

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)


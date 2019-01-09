---
title: ta med fil
description: ta med fil
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 1/8/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 203168f27e06d58c192bc5009db077461738011a
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54122793"
---
Data factory är en tjänst för flera klienter som har följande standardgränserna på plats för att kontrollera kundprenumerationer skyddas från varandras arbetsbelastningar. Många av gränserna kan enkelt höjas för din prenumeration upp till den maximala gränsen genom att kontakta supporten.

### <a name="version-2"></a>Version 2

| Resurs | Standardgräns | Övre gräns |
| -------- | ------------- | ------------- |
| Datafabriker i en Azure-prenumeration | 50 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Totalt antal enheter (Pipeline, datauppsättningar, utlösare, länkade tjänster, integreringskörningar) i en data factory | 5000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Total CPU-kärnor för Azure-SSIS Integration Runtime(s) under en prenumeration | 256 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Samtidiga pipelinekörningar per data factory (delas mellan alla pipelines i fabriken) | 10 000  | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Högsta antal aktiviteter per pipeline (inklusive inre aktiviteter för behållare) | 40 | 40 |
| Maximalt antal parametrar per pipeline | 50 | 50 |
| ForEach-objekt | 100 000 | 100 000 |
| ForEach-parallellitet | 20 | 50 |
| Tecken per uttryck | 8 192 | 8 192 |
| Minimiintervall för utlösare för rullande fönster | 15 min | 15 min |
| Max Timeout för pipeline-aktivitetskörningar | 7 dagar | 7 dagar |
| Byte per objekt för pipelineobjekt <sup>1</sup> | 200 KB | 200 KB |
| Byte per objekt för datauppsättningen och länkad tjänstobjekt <sup>1</sup> | 100 KB | 2 000 KB |
| Data integration enheter per körningen av kopieringsaktiviteten <sup>3</sup> | 256 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Skriv API-anrop | 2500/timme<br/><br/> Den här gränsen har införts i Azure Resource Manager, inte Azure Data Factory. | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Läs API-anrop | 12 500/timme<br/><br/> Den här gränsen har införts i Azure Resource Manager, inte Azure Data Factory. | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Övervakning av frågor per minut | 1000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Entiteten CRUD-åtgärder per minut | 50 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |


### <a name="version-1"></a>Version 1

| **Resurs** | **Standardgräns** | **Övre gräns** |
| --- | --- | --- |
| Datafabriker i en Azure-prenumeration |50 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Pipelines i en data factory |2500 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Datauppsättningar i en data factory |5000 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Samtidiga sektorer per datauppsättning |10 |10 |
| Byte per objekt för pipelineobjekt <sup>1</sup> |200 KB |200 KB |
| Byte per objekt för datauppsättningen och länkad tjänstobjekt <sup>1</sup> |100 KB |2 000 KB |
| HDInsight-kluster på begäran kärnor inom en prenumeration <sup>2</sup> |60 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Enheter för dataflytt per körningen av kopieringsaktiviteten i molnet <sup>3</sup> |32 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Antal för pipeline-aktivitetskörningar försök |1000 |MaxInt (32-bitars) |

<sup>1</sup> pipeline, datauppsättning och länkad tjänst-objekt som representerar en logisk gruppering av din arbetsbelastning. Gränser för de här objekten inte är relaterade till mängden data som du kan flytta och bearbeta med Azure Data Factory-tjänsten. Data factory är utformat för att skala för att hantera petabyte med data.

<sup>2</sup> på begäran HDInsight-kärnor allokeras utanför den prenumeration som innehåller data factory. Därför är ovan gränsen Data Factory tvingande kärngräns för på begäran HDInsight-kärnor och skiljer sig från kärngräns som är associerade med din Azure-prenumeration.

<sup>3</sup> data Integration enhet (DIU) för v2 eller molnet Data Movement enhet (dmu här) för v1 som används i en moln-till-moln kopieringsåtgärd. Det är ett mått som representerar (en kombination av processor, minne och nätverksresursallokering) en enhet i Data Factory. Du kan uppnå högre kopia dataflöde med hjälp av flera DMUs för vissa scenarier. Referera till [dataenheter integration (V2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) och [dataöverföringsenheter (V1) i molnet](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) avsnittet med information och [sidan med priser för Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) för fakturering de.

<sup>4</sup> the Integration Runtime (IR) är beräkningsinfrastrukturen som används av Azure Data Factory för att tillhandahålla följande funktioner för dataintegrering i olika nätverksmiljöer: dataförflyttning, skicka aktiviteter för att beräkna tjänster, körning av SSIS-paket. Mer information finns i [Integration Runtime-översikt](../articles/data-factory/concepts-integration-runtime.md).

| **Resurs** | **Lägre Standardgräns** | **Lägsta tillåtna värdet** |
| --- | --- | --- |
| Planering intervallet |15 minuter |15 minuter |
| Intervall mellan försök |1 sekund |1 sekund |
| Gör om timeout-värde |1 sekund |1 sekund |

#### <a name="web-service-call-limits"></a>Web tjänstbegränsningar för anrop
Azure Resource Manager har gränser för API-anrop. Du kan göra API-anrop till en kostnad i den [Azure Resource Manager API begränsar](../articles/azure-subscription-service-limits.md#resource-group-limits).

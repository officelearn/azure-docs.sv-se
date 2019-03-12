---
title: ta med fil
description: ta med fil
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 1/10/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 42c1856f30484532e1ace2e84187bcaaacdf4c72
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553832"
---
Azure Data Factory är en multitenant-tjänst som har följande standardgränser på plats för att kontrollera kundprenumerationer skyddas från varandras arbetsbelastningar. Kontakta supporten om du vill höja gränserna upp till maximalt för din prenumeration.

### <a name="version-2"></a>Version 2

| Resurs | Standardgräns | Övre gräns |
| -------- | ------------- | ------------- |
| Datafabriker i en Azure-prenumeration | 50 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totalt antal enheter, till exempel pipelines, datauppsättningar, utlösare, länkade tjänster och integreringskörningar i en data factory | 5 000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Total CPU-kärnor för Azure-SSIS-Integreringskörningar under en prenumeration | 256 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga pipelinekörningar per data factory, som delas mellan alla pipelines i fabriken | 10 000  | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximal aktiviteter per pipeline, som innehåller inre aktiviteter för behållare | 40 | 40 |
| Maximalt antal länkade integreringskörningar som kan skapas mot en enda lokal integration runtime | 20 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximal parametrar per pipeline | 50 | 50 |
| ForEach-objekt | 100 000 | 100 000 |
| ForEach-parallellitet | 20 | 50 |
| Tecken per uttryck | 8 192 | 8 192 |
| Minsta rullande fönster utlösaren intervall | 15 min | 15 min |
| Maximala tidsgränsen för pipeline-aktivitet körs | 7 dagar | 7 dagar |
| Byte per objekt för pipelineobjekt<sup>1</sup> | 200 KB | 200 KB |
| Byte per objekt för datauppsättningen och länkad tjänstobjekt<sup>1</sup> | 100 kB | 2 000 KB |
| Data integration enheter per körningen av kopieringsaktiviteten<sup>3</sup> | 256 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Skriv API-anrop | 2 500/h<br/><br/> Den här gränsen har införts i Azure Resource Manager, inte Azure Data Factory. | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Läs API-anrop | 12 500/h<br/><br/> Den här gränsen har införts i Azure Resource Manager, inte Azure Data Factory. | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Övervakning av frågor per minut | 1,000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Entiteten CRUD-åtgärder per minut | 50 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |


### <a name="version-1"></a>Version 1

| **Resurs** | **Standardgräns** | **Övre gräns** |
| --- | --- | --- |
| Datafabriker i en Azure-prenumeration |50 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Pipelines i en data factory |2,500 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Datauppsättningar i en data factory |5 000 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga sektorer per datauppsättning |10 |10 |
| Byte per objekt för pipelineobjekt<sup>1</sup> |200 KB |200 KB |
| Byte per objekt för data och länkade tjänstobjekt<sup>1</sup> |100 kB |2 000 KB |
| Azure HDInsight-kluster på begäran-kärnor inom en prenumeration<sup>2</sup> |60 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Enheter för dataflytt per körningen av kopieringsaktiviteten i molnet<sup>3</sup> |32 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Antal för pipeline-aktivitetskörningar försök |1,000 |MaxInt (32-bitars) |

<sup>1</sup>pipeline, datauppsättning och länkad tjänst-objekt som representerar en logisk gruppering av din arbetsbelastning. Gränser för de här objekten inte är relaterade till hur mycket du kan flytta och bearbeta data med Azure Data Factory. Data Factory är utformat för att skala för att hantera petabyte med data.

<sup>2</sup>på begäran HDInsight-kärnor allokeras utanför den prenumeration som innehåller data factory. Den föregående gränsen är därför Data Factory-tillämpas kärngräns för på begäran HDInsight-kärnor. Den skiljer sig från gränsen för kärnor som är associerat med din Azure-prenumeration.

<sup>3</sup>dataenheten för integration (DIU) för version 2 eller molnet data movement enhet (dmu här) för version 1 används i en moln-till-moln kopieringsåtgärd. Det är ett mått som representerar en enskild enhet i Data Factory. Den kombinerar de processor, minne och nätverk resursallokeringar. Du kan uppnå högre kopia dataflöde med hjälp av flera DMUs för vissa scenarier. Mer information finns i [integrering enheter (version 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) och [dataöverföringsenheter (version 1) i molnet](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Information om fakturering finns i [priser för Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>4</sup>integration runtime (IR) är beräkningsinfrastrukturen som används av Azure Data Factory för att tillhandahålla funktioner för dataintegrering i olika nätverksmiljöer, till exempel dataflyttning, skicka aktiviteter för att beräkna tjänster, och körning av SSIS-paket. Mer information finns i [översikten över Integration runtime](../articles/data-factory/concepts-integration-runtime.md).

| **Resurs** | **Lägre Standardgräns** | **Lägsta tillåtna värdet** |
| --- | --- | --- |
| Planering intervallet |15 minuter |15 minuter |
| Intervall mellan försök |1 sekund |1 sekund |
| Gör om timeout-värde |1 sekund |1 sekund |

#### <a name="web-service-call-limits"></a>Web tjänstbegränsningar för anrop
Azure Resource Manager har gränser för API-anrop. Du kan göra API-anrop till en kostnad i den [Azure Resource Manager API begränsar](../articles/azure-subscription-service-limits.md#resource-group-limits).

---
title: ta med fil
description: ta med fil
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 5/30/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: ac0747dcff4e74363a58dc9aaf6da4dbd4c6a1c7
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427610"
---
Azure Data Factory är en multitenant-tjänst som har följande standardgränser på plats för att kontrollera kundprenumerationer skyddas från varandras arbetsbelastningar. Kontakta supporten om du vill höja gränserna upp till maximalt för din prenumeration.

### <a name="version-2"></a>Version 2

| Resource | Standardgräns | Övre gräns |
| -------- | ------------- | ------------- |
| Datafabriker i en Azure-prenumeration | 50 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totalt antal enheter, till exempel pipelines, datauppsättningar, utlösare, länkade tjänster och integreringskörningar i en data factory | 5,000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Total CPU-kärnor för Azure-SSIS-Integreringskörningar under en prenumeration | 256 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga pipelinekörningar per data factory, som delas mellan alla pipelines i fabriken | 10 000  | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga externa aktivitetskörningar per prenumeration per [Azure Integration Runtime-region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Externa aktiviteter hanteras på integration runtime men köra på länkade tjänster, inklusive Databricks, lagrad procedur, HDInsights och andra.</small> | 3000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga Pipeline-aktivitet som körs per prenumeration per [Azure Integration Runtime-region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Pipeline-aktiviteter köra på integration runtime, inklusive Lookup, GetMetadata, och ta bort. </small>| 1000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidig redigering-åtgärder per prenumeration per [Azure Integration Runtime-region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Förhandsgranska data inklusive Testa anslutning, bläddra mapplistan och tabelllistan. | 200 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga Integration enheter<sup>1</sup> förbrukning per prenumeration per [Azure Integration Runtime-region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Region-grupp 1<sup>2</sup>: 6000<br>Region-grupp 2<sup>2</sup>: 3000<br>Region-grupp 3<sup>2</sup>: 1500 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximal aktiviteter per pipeline, som innehåller inre aktiviteter för behållare | 40 | 40 |
| Maximalt antal länkade integreringskörningar som kan skapas mot en enda lokal integration runtime | 100 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximal parametrar per pipeline | 50 | 50 |
| ForEach-objekt | 100,000 | 100,000 |
| ForEach-parallellitet | 20 | 50 |
| Tecken per uttryck | 8 192 | 8 192 |
| Minsta rullande fönster utlösaren intervall | 15 min | 15 min |
| Maximala tidsgränsen för pipeline-aktivitet körs | 7 dagar | 7 dagar |
| Byte per objekt för pipelineobjekt<sup>3</sup> | 200 KB | 200 KB |
| Byte per objekt för datauppsättningen och länkad tjänstobjekt<sup>3</sup> | 100 KB | 2 000 KB |
| Integrering enheter<sup>1</sup> per körningen av kopieringsaktiviteten | 256 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Skriv API-anrop | 2 500/h<br/><br/> Den här gränsen har införts i Azure Resource Manager, inte Azure Data Factory. | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Läs API-anrop | 12 500/h<br/><br/> Den här gränsen har införts i Azure Resource Manager, inte Azure Data Factory. | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Övervakning av frågor per minut | 1,000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Entiteten CRUD-åtgärder per minut | 50 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> dataenheten för integration (DIU) används i en moln-till-moln kopieringsåtgärd, mer [integrering enheter (version 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Information om fakturering finns i [priser för Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) är [globalt tillgänglig](https://azure.microsoft.com/global-infrastructure/services/) för att säkerställa dataefterlevnad, effektivitet och minskade nätverk egress kostnader. 

| Region-grupp | Regioner | 
| -------- | ------ |
| Region-grupp 1 | Centrala USA, östra USA, östra usa2, Norra Europa, Västeuropa, västra USA, västra USA 2 |
| Region-grupp 2 | Australien, östra, Australien, sydöstra Australien, södra Brasilien, centrala Indien, Japan östra, Northcentral USA, södra centrala USA, Sydostasien, USA, västra centrala |
| Region-grupp 3 | Kanada, centrala, Asien, Frankrike centrala Korea, centrala, Storbritannien, södra |

<sup>3</sup> pipeline, datauppsättning och länkad tjänst-objekt som representerar en logisk gruppering av din arbetsbelastning. Gränser för de här objekten inte är relaterade till hur mycket du kan flytta och bearbeta data med Azure Data Factory. Data Factory är utformat för att skala för att hantera petabyte med data.

### <a name="version-1"></a>Version 1

| **Resurs** | **Standardgräns** | **Övre gräns** |
| --- | --- | --- |
| Datafabriker i en Azure-prenumeration |50 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Pipelines i en data factory |2,500 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Datauppsättningar i en data factory |5,000 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga sektorer per datauppsättning |10 |10 |
| Byte per objekt för pipelineobjekt<sup>1</sup> |200 KB |200 KB |
| Byte per objekt för data och länkade tjänstobjekt<sup>1</sup> |100 KB |2 000 KB |
| Azure HDInsight-kluster på begäran-kärnor inom en prenumeration<sup>2</sup> |60 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Enheter för dataflytt per körningen av kopieringsaktiviteten i molnet<sup>3</sup> |32 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Antal för pipeline-aktivitetskörningar försök |1,000 |MaxInt (32-bitars) |

<sup>1</sup> pipeline, datauppsättning och länkad tjänst-objekt som representerar en logisk gruppering av din arbetsbelastning. Gränser för de här objekten inte är relaterade till hur mycket du kan flytta och bearbeta data med Azure Data Factory. Data Factory är utformat för att skala för att hantera petabyte med data.

<sup>2</sup> på begäran HDInsight-kärnor allokeras utanför den prenumeration som innehåller data factory. Den föregående gränsen är därför Data Factory-tillämpas kärngräns för på begäran HDInsight-kärnor. Den skiljer sig från gränsen för kärnor som är associerat med din Azure-prenumeration.

<sup>3</sup> cloud data movement enhet (dmu här) för version 1 används i en moln-till-moln kopieringsåtgärd mer [dataöverföringsenheter (version 1) i molnet](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Information om fakturering finns i [priser för Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Resurs** | **Lägre Standardgräns** | **Lägsta tillåtna värdet** |
| --- | --- | --- |
| Planering intervallet |15 minuter |15 minuter |
| Intervall mellan försök |1 sekund |1 sekund |
| Gör om timeout-värde |1 sekund |1 sekund |

#### <a name="web-service-call-limits"></a>Web tjänstbegränsningar för anrop
Azure Resource Manager har gränser för API-anrop. Du kan göra API-anrop till en kostnad i den [Azure Resource Manager API begränsar](../articles/azure-subscription-service-limits.md#resource-group-limits).

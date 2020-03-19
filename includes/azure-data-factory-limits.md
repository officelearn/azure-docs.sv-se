---
title: ta med fil
description: ta med fil
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 01/08/2020
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 217e48ab21439f03e0b52c894c9aace5b51b1502
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086148"
---
Azure Data Factory är en tjänst för flera klientorganisationer som har följande standardgränser på plats för att se till att kundprenumerationer skyddas från varandras arbetsbelastningar. Kontakta supporten om du vill höja gränserna upp till maximum för prenumerationen.

### <a name="version-2"></a>Version 2

| Resurs | Standardgräns | Övre gräns |
| -------- | ------------- | ------------- |
| Datafabriker i en Azure-prenumeration | 800 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totalt antal entiteter, till exempel pipelines, datauppsättningar, utlösare, länkade tjänster och integreringskörningar, i en datafabrik | 5 000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totalt antal CPU-kärnor för Azure-SSIS Integration Runtime under en prenumeration | 256 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga pipelinekörningar per datafabrik som delas bland alla pipelines i fabriken | 10 000  | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga externa aktivitetskörningar per prenumeration per [Azure Integration Runtime-region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Externa aktiviteter hanteras på integreringskörning men körs på länkade tjänster, inklusive Databricks, lagrad procedur, HDInsights, webb med flera.</small> | 3000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga pipeline-aktivitetskörningar per prenumeration per [Azure Integration Runtime-region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Pipeline-aktiviteter körs på integreringskörning, inklusive Lookup, GetMetadata och Delete. </small>| 1000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga redigeringsåtgärder per prenumeration per [Azure Integration Runtime-region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Inklusive testanslutning, bläddra i mapplista och tabellista, förhandsgranska data. | 200 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga dataintegreringsenheter<sup>1</sup> förbrukning per prenumeration per [Azure Integration Runtime region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Regiongrupp 1<sup>2</sup>: 6000<br>Regiongrupp 2<sup>2</sup>: 3000<br>Regiongrupp 3<sup>2</sup>: 1500 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximalt antal aktiviteter per pipeline, som inkluderar inre aktiviteter för containrar | 40 | 40 |
| Maximalt antal länkade integreringskörningar som kan skapas mot en enda lokalt installerad integrationskörning | 100 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximalt antal parametrar per pipeline | 50 | 50 |
| ForEach-objekt | 100 000 | 100 000 |
| ForEach-parallelitet | 20 | 50 |
| Maximalt antal köade körningar per pipeline | 100 | 100 |
| Tecken per uttryck | 8 192 | 8 192 |
| Minsta intervall för utlösare för rullande fönster | 15 min | 15 min |
| Maximal tidsgräns för pipeline-aktivitetskörningar | 7 dagar | 7 dagar |
| Byte per objekt for pipeline-objekt<sup>3</sup> | 200 KB | 200 KB |
| Byte per objekt for datauppsättningsobjekt och länkad tjänst-objekt<sup>3</sup> | 100 kB | 2 000 kB |
| Dataintegreringsenheter<sup>1</sup> per kopieringsaktivitetskörning | 256 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API-anrop för skrivning | 1 200/tim<br/><br/> Gränsen tillämpas av Azure Resource Manager, inte Azure Data Factory. | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API-anrop för läsning | 12 500/tim<br/><br/> Gränsen tillämpas av Azure Resource Manager, inte Azure Data Factory. | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Övervakningsfrågor per minut | 1,000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Entitets-CRUD-åtgärder per minut | 50 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximal tid för felsökningssession för dataflöde | 8 timmar | 8 timmar |
| Samtidigt antal dataflöden per fabrik | 50 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidigt antal felsökningssessioner för dataflöde per användare per fabrik | 3 | 3 |
| Azure IR TTL-gräns för dataflöde | 4 timmar | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> Dataintegreringsenheten (DIU) används i en moln-till-moln-kopieringsåtgärd. Mer information finns i [Dataintegreringsenheter (version 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Information om fakturering finns i [prissättningen för Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) är [globalt tillgängligt](https://azure.microsoft.com/global-infrastructure/services/) för att säkerställa dataefterlevnad, effektivitet och minskade kostnader för nätverksegress. 

| Regiongrupp | Regioner | 
| -------- | ------ |
| Regiongrupp 1 | USA, centrala; USA, östra; USA, östra 2; Europa, norra; Europa, västra; USA, västra; USA, västra 2 |
| Regiongrupp 2 | Australien, östra; Australien, sydöstra; Brasilien, södra; Indien, centrala; Japan, östra; USA, norra centrala; USA södra centrala; Asien, sydöstra; USA, västra centrala |
| Regiongrupp 3 | Kanada, centrala; Asien, östra; Frankrike, centrala; Sydkorea, centrala; Storbritannien, södra |

<sup>3</sup> Pipeline-, datauppsättnings- och länkad tjänst-objekt representerar en logisk gruppering av din arbetsbelastning. Gränser för dessa objekt har inget samband med mängden data du kan flytta och bearbeta med Azure Data Factory. Data Factory är utformad att skalas för att hantera petabyte av data.

### <a name="version-1"></a>Version 1

| **Resurs** | **Standardgräns** | **Övre gräns** |
| --- | --- | --- |
| Pipelines i en datafabrik |2 500 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Datauppsättningar i en fabrik |5 000 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga sektorer per datauppsättning |10 |10 |
| Byte per objekt for pipeline-objekt<sup>1</sup> |200 KB |200 KB |
| Byte per objekt for datauppsättningsobjekt och länkad tjänst-objekt<sup>1</sup> |100 kB |2 000 kB |
| Azure HDInsight-klusterkärnor på begäran i en prenumeration<sup>2</sup> |60 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Molndataflyttenheter per kopieringsaktivitetskörning<sup>3</sup> |32 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Antal återförsök för pipeline-aktivitetskörningar |1,000 |MaxInt (32 bitar) |

<sup>1</sup> Pipeline-, datauppsättnings- och länkad tjänst-objekt representerar en logisk gruppering av din arbetsbelastning. Gränser för dessa objekt har inget samband med mängden data du kan flytta och bearbeta med Azure Data Factory. Data Factory är utformad att skalas för att hantera petabyte av data.

<sup>2</sup> HDInsight-kärnor på begäran har allokerats ut ur prenumerationen som innehåller datafabriken. Därför är den föregående gränsen den Data Factory-tillämpade kärngränsen för HDInsight-kärnor på begäran. Den skiljer sig från kärngränsen som är kopplad till din Azure-prenumeration.

<sup>3</sup> Molndataflyttenheten (DMU) för version 1 används i en moln-till-moln-kopieringsåtgärd. Mer information finns i [Molndataflyttenheter (version 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Information om fakturering finns i [prissättningen för Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Resurs** | **Nedre standardgräns** | **Minsta gräns** |
| --- | --- | --- |
| Schemaintervall |15 minuter |15 minuter |
| Intervall mellan försök |1 sekund |1 sekund |
| Tidsgränsvärde för försök |1 sekund |1 sekund |

#### <a name="web-service-call-limits"></a>Gränser för webbtjänstanrop
Azure Resource Manager har gränser för API-anrop. Du kan göra API-anrop inom [Azure Resource Manager API-gränserna](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).

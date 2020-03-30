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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086148"
---
Azure Data Factory är en tjänst med flera följare som har följande standardgränser för att se till att kundprenumerationer skyddas från varandras arbetsbelastningar. Om du vill höja gränserna upp till det högsta för din prenumeration kontaktar du supporten.

### <a name="version-2"></a>Version 2

| Resurs | Standardgräns | Övre gräns |
| -------- | ------------- | ------------- |
| Datafabriker i en Azure-prenumeration | 800 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totalt antal entiteter, till exempel pipelines, datauppsättningar, utlösare, länkade tjänster och integrationskörningar, inom en datafabrik | 5 000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totalt antal CPU-kärnor för Azure-SSIS Integration Runtimes under en prenumeration | 256 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidig pipeline körs per data fabrik som delas mellan alla rörledningar i fabriken | 10 000  | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidig extern aktivitet körs per prenumeration per [Azure Integration Runtime-region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Externa aktiviteter hanteras på integrationskörning men körs på länkade tjänster, inklusive Databricks, lagrad procedur, HDInsights, Webb och andra.</small> | 3000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidig pipeline-aktivitet körs per prenumeration per [Azure Integration Runtime-region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Pipeline-aktiviteter körs på integrationskörning, inklusive Uppslag, GetMetadata och Ta bort.</small>| 1000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga redigeringsåtgärder per prenumeration per [Azure Integration Runtime-region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Inklusive testanslutning, bläddra i mapplista och tabelllista, förhandsgranska data. | 200 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidig dataintegrationsenheter<sup>1</sup> förbrukning per prenumeration per [Azure Integration Runtime-region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Regiongrupp 1<sup>2</sup>: 6000<br>Regiongrupp 2<sup>2</sup>: 3000<br>Regiongrupp 3<sup>2</sup>: 1500 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximala aktiviteter per rörledning, vilket inkluderar inre aktiviteter för containrar | 40 | 40 |
| Maximalt antal länkade integrationskörningar som kan skapas mot en enda självvärd integreringskörning | 100 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximala parametrar per rörledning | 50 | 50 |
| ForEach-objekt | 100 000 | 100 000 |
| ForEach parallellism | 20 | 50 |
| Maximala köade körningar per pipeline | 100 | 100 |
| Tecken per uttryck | 8 192 | 8 192 |
| Minsta utlösande intervall för tumlande fönster | 15 min | 15 min |
| Maximal timeout för pipeline-aktivitetskörningar | 7 dagar | 7 dagar |
| Byte per objekt för pipeline-objekt<sup>3</sup> | 200 KB | 200 KB |
| Byte per objekt för datauppsättning och länkade tjänstobjekt<sup>3</sup> | 100 kB | 2 000 KB |
| Dataintegrationsenheterna<sup>1</sup> per kopieringsaktivitetskörning | 256 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Skriv API-anrop | 1 200/h<br/><br/> Den här gränsen anges av Azure Resource Manager, inte Azure Data Factory. | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Läsa API-anrop | 12 500/h<br/><br/> Den här gränsen anges av Azure Resource Manager, inte Azure Data Factory. | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Övervaka frågor per minut | 1,000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| CRUD-transaktioner per minut | 50 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximal tid för felsökningssession för dataflöde | 8 timmar | 8 timmar |
| Samtidig antal dataflöden per fabrik | 50 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga antal felsökningssessioner för dataflöde per användare och fabrik | 3 | 3 |
| Azure IR-gräns för Dataflöde | 4 timmar | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> Dataintegrationsenheten (DIU) används i en moln-till-moln-kopiering, läs mer från [dataintegrationsenheter (version 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Information om fakturering finns i [Azure Data Factory-priser](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) är globalt [tillgängligt](https://azure.microsoft.com/global-infrastructure/services/) för att säkerställa dataefterlevnad, effektivitet och minskade kostnader för nätverksutgående. 

| Regiongrupp | Regioner | 
| -------- | ------ |
| Regiongrupp 1 | Centrala USA, Östra USA, Östra USA2, Nordeuropa, Västeuropa, Västra USA, Västra USA 2 |
| Regiongrupp 2 | Östra Australien, Sydöstra Australien, Södra Brasilien, Centrala Indien, Östra Japan, Norra USA, Sydcentrala USA, Sydostasien, Västra centrala USA |
| Regiongrupp 3 | Kanada Central, Östasien, Frankrike Central, Korea Central, Storbritannien Syd |

<sup>3</sup> Pipeline-, datauppsättnings- och länkade tjänstobjekt representerar en logisk gruppering av din arbetsbelastning. Begränsningar för dessa objekt relaterar inte till mängden data som du kan flytta och bearbeta med Azure Data Factory. Data Factory är utformad för att skala för att hantera petabyte data.

### <a name="version-1"></a>Version 1

| **Resurs** | **Standardgräns** | **Högsta gräns** |
| --- | --- | --- |
| Pipelines inom en datafabrik |2 500 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Datauppsättningar i en datafabrik |5 000 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga segment per datauppsättning |10 |10 |
| Byte per objekt för pipeline-objekt<sup>1</sup> |200 KB |200 KB |
| Byte per objekt för datauppsättning och länkade tjänstobjekt<sup>1</sup> |100 kB |2 000 KB |
| Azure HDInsight klusterkärnor på begäran inom en prenumeration<sup>2</sup> |60 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Molndatarörelseenheter per kopieringsaktivitet körs<sup>3</sup> |32 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Antal försök igen för pipeline-aktivitetskörningar |1,000 |MaxInt (32 bitar) |

<sup>1</sup> Pipeline-, datauppsättnings- och länkade tjänstobjekt representerar en logisk gruppering av din arbetsbelastning. Begränsningar för dessa objekt relaterar inte till mängden data som du kan flytta och bearbeta med Azure Data Factory. Data Factory är utformad för att skala för att hantera petabyte data.

<sup>2</sup> HDInsight-kärnor på begäran fördelas från prenumerationen som innehåller datafabriken. Som ett resultat är den tidigare gränsen den datafabriksdrivna kärngränsen för HDInsight-kärnor på begäran. Det skiljer sig från kärngränsen som är associerad med din Azure-prenumeration.

<sup>3</sup> Cloud data movement unit (DMU) för version 1 används i en moln-till-moln-kopiering, läs mer från [Cloud dataförflyttningsenheter (version 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Information om fakturering finns i [Azure Data Factory-priser](https://azure.microsoft.com/pricing/details/data-factory/).

| **Resurs** | **Lägsta standardgräns** | **Minimigräns** |
| --- | --- | --- |
| Schemaläggningsintervall |15 minuter |15 minuter |
| Intervall mellan försök till försök till nytt |1 sekund |1 sekund |
| Timeout-värde för återförsök |1 sekund |1 sekund |

#### <a name="web-service-call-limits"></a>Begränsningar för webbtjänstsamtal
Azure Resource Manager har begränsningar för API-anrop. Du kan ringa API-anrop med en hastighet inom [Azure Resource Manager API-gränser](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).

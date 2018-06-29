---
title: ta med fil
description: ta med fil
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 06/20/2018
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 43408ebc65d4acf581b612e8ecfb9d00679cc078
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37066109"
---
Datafabriken är en tjänst med flera klienter som har följande standardgränser för att kontrollera kundprenumerationer skyddas från varandras arbetsbelastningar. Många av gränserna som kan enkelt höjas för din prenumeration upp till den maximala gränsen genom att kontakta supporten.

### <a name="version-2"></a>Version 2

| Resurs | Standardgräns | Övre gräns |
| -------- | ------------- | ------------- |
| Datafabriker i en Azure-prenumeration | 50 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Totalt antal enheter (Pipeline, datauppsättningar, utlösare, länkade tjänster, Integration körningar) i en datafabrik | 5000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Totalt antal processorkärnor för Azure-SSIS-integrering Runtime(s) under en prenumeration | 100 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Samtidiga pipeline körs per pipeline | 100 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Samtidiga pipeline körs per data factory | 10 000  | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Högsta antal aktiviteter per pipeline (inklusive inre aktiviteter för behållare) | 40 | 40 |
| Maximalt antal parametrar per pipeline | 50 | 50 |
| ForEach-objekt | 100,000 | 100,000 |
| ForEach parallellitet | 20 | 50 |
| Tecken per uttryck | 8 192 | 8 192 |
| Minimiintervall för rullande fönster utlösare | 15 min | 15 min |
| Maximal Timeout för pipeline-aktiviteten körs | 7 dagar | 7 dagar |
| Byte per objekt för pipeline-objekt <sup>1</sup> | 200 KB | 200 KB |
| Byte per objekt för datauppsättningen och länkade tjänstobjekt <sup>1</sup> | 100 KB | 2000 KB |
| Data integration enheter per kopieringsaktiviteten kör <sup>3</sup> | 256 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Skriva API-anrop | 2500/timme<br/><br/> Den här gränsen har införts av Azure Resource Manager kan inte Azure Data Factory. | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Läs API-anrop | 12 500/timme<br/><br/> Den här gränsen har införts av Azure Resource Manager kan inte Azure Data Factory. | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |


### <a name="version-1"></a>Version 1

| **Resurs** | **Standardgräns** | **Övre gräns** |
| --- | --- | --- |
| Datafabriker i en Azure-prenumeration |50 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Pipelines inom en datafabrik |2500 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Datauppsättningar inom en datafabrik |5000 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Samtidiga segment per dataset |10 |10 |
| Byte per objekt för pipeline-objekt <sup>1</sup> |200 KB |200 KB |
| Byte per objekt för datauppsättningen och länkade tjänstobjekt <sup>1</sup> |100 KB |2000 KB |
| HDInsight-kluster på begäran kärnor inom en prenumeration <sup>2</sup> |60 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Molnet data movement enheter per kopieringsaktiviteten kör <sup>3</sup> |32 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Antal för pipeline aktivitetskörningar försök |1000 |MaxInt (32-bitars) |

<sup>1</sup> pipeline, datamängd och länkade tjänstobjekt representerar en logisk gruppering av din arbetsbelastning. Begränsningar för dessa objekt inte är relaterade till mängden data som du kan flytta och bearbeta med Azure Data Factory-tjänsten. Datafabriken är utformat för att skala för att hantera petabyte med data.

<sup>2</sup> kärnor på begäran HDInsight tilldelas utanför den prenumeration som innehåller data factory. Ovanstående gränsen är därför Data Factory tvingande core gränsen för på begäran HDInsight kärnor och skiljer sig från gränsen core som är associerade med din Azure-prenumeration.

<sup>3</sup> data Integration enhet (DIU) för v2 eller molnet Data Movement enhet (dmu här) för v1 används i ett moln-to-cloud kopieringsåtgärden. Det är ett mått som representerar en enhet i Data Factory styrka (en kombination av CPU, minne och nätverksresursallokering). Du kan uppnå högre kopiera genomströmning genom att använda flera DMUs för vissa scenarier. Referera till [integrering enheter (V2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) och [molnet data movement enheter (V1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) avsnittet information och [sida med priser för Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) för fakturering konsekvenserna.

<sup>4</sup> i Integration Runtime (IR) är beräkningsinfrastrukturen som används av Azure Data Factory för att tillhandahålla följande funktioner för integration av data över olika nätverksmiljöer: dataflyttning sändning av aktiviteter för att beräkna tjänster körning av SSIS-paket. Mer information finns i [integrering Runtime översikt](../articles/data-factory/concepts-integration-runtime.md).

| **Resurs** | **Standard nedre gräns** | **Minimigräns** |
| --- | --- | --- |
| Planering intervallet |15 minuter |15 minuter |
| Intervall mellan försök |1 sekund |1 sekund |
| Gör timeout-värde |1 sekund |1 sekund |

#### <a name="web-service-call-limits"></a>Web service anropet gränser
Azure Resource Manager har begränsningar för API-anrop. Du kan göra API-anrop med en hastighet inom den [Azure Resource Manager API begränsar](../articles/azure-subscription-service-limits.md#resource-group-limits).

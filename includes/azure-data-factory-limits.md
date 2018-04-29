---
title: ta med fil
description: ta med fil
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 04/18/2018
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 7f74f100dca5dfe83ef88ca20f69a1b7af38b333
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
Datafabriken är en tjänst med flera klienter som har följande standardgränser för att kontrollera kundprenumerationer skyddas från varandras arbetsbelastningar. Många av gränserna som kan enkelt höjas för din prenumeration upp till den maximala gränsen genom att kontakta supporten.

### <a name="version-2"></a>Version 2

| Resurs | Standardgräns | Övre gräns | 
| -------- | ------------- | ------------- | 
| datafabriker i en Azure-prenumeration | 50 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| pipelines inom en datafabrik | 2500 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| datauppsättningar inom en datafabrik | 2500 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Utlösare i en datafabrik | 2500 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Länkade tjänster i en datafabrik | 2500 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Integration körningar inom en datafabrik <sup>4</sup> | 2500 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Totalt antal processorkärnor för Azure-SSIS-integrering Runtime(s) under en prenumeration | 100 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Samtidiga pipeline körs per pipeline | 20 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Högsta antal aktiviteter per pipeline | 20 | 30 |
| Maximalt antal parametrar per pipeline | 20 | 30 |
| Byte per objekt för pipeline-objekt <sup>1</sup> | 200 KB | 200 KB |
| Byte per objekt för datauppsättningen och länkade tjänstobjekt <sup>1</sup> | 100 KB | 2000 KB |
| Molnet data movement enheter <sup>3</sup> | 256 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Antal för pipeline aktivitetskörningar försök | 1 day(timeout) | 1 dag (timeout) |
| Skriva API-anrop | 2500/timme<br/><br/> Den här gränsen har införts av Azure Resource Manager kan inte Azure Data Factory. | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Läs API-anrop | 12 500/timme<br/><br/> Den här gränsen har införts av Azure Resource Manager kan inte Azure Data Factory. | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |


### <a name="version-1"></a>Version 1

| **Resurs** | **Standardgräns** | **Övre gräns** |
| --- | --- | --- |
| datafabriker i en Azure-prenumeration |50 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| pipelines inom en datafabrik |2500 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| datauppsättningar inom en datafabrik |5000 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| samtidiga segment per dataset |10 |10 |
| Byte per objekt för pipeline-objekt <sup>1</sup> |200 KB |200 KB |
| Byte per objekt för datauppsättningen och länkade tjänstobjekt <sup>1</sup> |100 KB |2000 KB |
| HDInsight-kluster på begäran kärnor inom en prenumeration <sup>2</sup> |60 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Molnet data movement enheter <sup>3</sup> |32 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Antal för pipeline aktivitetskörningar försök |1000 |MaxInt (32-bitars) |

<sup>1</sup> pipeline, datamängd och länkade tjänstobjekt representerar en logisk gruppering av din arbetsbelastning. Begränsningar för dessa objekt inte är relaterade till mängden data som du kan flytta och bearbeta med Azure Data Factory-tjänsten. Datafabriken är utformat för att skala för att hantera petabyte med data.

<sup>2</sup> kärnor på begäran HDInsight tilldelas utanför den prenumeration som innehåller data factory. Ovanstående gränsen är därför Data Factory tvingande core gränsen för på begäran HDInsight kärnor och skiljer sig från gränsen core som är associerade med din Azure-prenumeration.

<sup>3</sup> moln data movement enhet (dmu här) används i en moln-to-cloud kopieringsåtgärd. Det är ett mått som representerar en enhet i Data Factory styrka (en kombination av CPU, minne och nätverksresursallokering). Du kan uppnå högre kopiera genomströmning genom att använda flera DMUs för vissa scenarier. Referera till [molnet data movement enheter (V2)](../articles/data-factory/copy-activity-performance.md#cloud-data-movement-units) och [molnet data movement enheter (V1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) avsnittet detaljer.

<sup>4</sup> i Integration Runtime (IR) är beräkningsinfrastrukturen som används av Azure Data Factory för att tillhandahålla följande funktioner för integration av data över olika nätverksmiljöer: dataflyttning sändning av aktiviteter för att beräkna tjänster körning av SSIS-paket. Mer information finns i [integrering Runtime översikt](../articles/data-factory/concepts-integration-runtime.md).

| **Resurs** | **Standard nedre gräns** | **Minimigräns** |
| --- | --- | --- |
| Planering intervallet |15 minuter |15 minuter |
| Intervall mellan försök |1 sekund |1 sekund |
| Gör timeout-värde |1 sekund |1 sekund |

#### <a name="web-service-call-limits"></a>Web service anropet gränser
Azure Resource Manager har begränsningar för API-anrop. Du kan göra API-anrop med en hastighet inom den [Azure Resource Manager API begränsar](../articles/azure-subscription-service-limits.md#resource-group-limits).

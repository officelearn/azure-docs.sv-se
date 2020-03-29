---
title: Förstå Azure Data Factory-priser via exempel
description: I den här artikeln beskrivs och demonstreras azure data factory-prismodellen med detaljerade exempel
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: ee5acc97e4b05a0e93f4ceee8c04b400da211b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76769497"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Förstå priser på datafabriker genom exempel

Den här artikeln förklarar och demonstrerar Azure Data Factory-prismodellen med detaljerade exempel.

> [!NOTE]
> De priser som används i dessa exempel nedan är hypotetiska och är inte avsedda att innebära faktisk prissättning.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Kopiera data från AWS S3 till Azure Blob-lagring varje timme

I det här fallet vill du kopiera data från AWS S3 till Azure Blob storage enligt ett timschema.

För att kunna utföra scenariot måste du skapa en pipeline med följande objekt:

1. En kopieringsaktivitet med indatauppsättning för de data som ska kopieras från AWS S3.

2. En utdatauppsättning för data på Azure Storage.

3. En schemautlösare som ska köra pipelinen varje timme.

   ![Scenario1](media/pricing-concepts/scenario1.png)

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 2 Läs/skriv entitet  |
| Skapa datauppsättningar | 4 Läs/skriv-entiteter (2 för skapande av datauppsättning, 2 för länkade tjänstreferenser) |
| Skapa pipeline | 3 Läs/skriv-entiteter (1 för skapande av pipeline, 2 för datauppsättningsreferenser) |
| Hämta pipeline | 1 Läs/skriv entitet |
| Kör pipeline | 2 Aktivitetskörningar (1 för utlösarkörning, 1 för aktivitetskörningar) |
| Kopiera dataantagande: körningstid = 10 min | 10 \* 4 Azure Integration Runtime (standard-DIU-inställning = 4) Mer information om dataintegrationsenheter och optimering av kopieringsprestanda finns i [den här artikeln](copy-activity-performance.md) |
| Övervaka pipelineantagande: Endast 1 körning inträffade | 2 Övervakningskörningsposter som gjorts om (1 för pipelinekörning, 1 för aktivitetskörning) |

**Totalt scenario prissättning: $ 0,16811**

- Data Fabriksoperationer = **$0.0001**
  - Läs/skriv = 10\*00001 = $0.0001 [1 R/W = $0.50/50000 = 0.00001]
  - Övervakning =\*2 000005 = $0.00001 [1 Övervakning = $0.25/50000 = 0.000005]
- Körning av &amp; pipelineorkestrering = **$0.168**
  - Aktivitetskörningar =\*001 2 = 0,002 [1 körning = $1/1000 = 0.001]
  - Data movement aktiviteter = $0.166 (Proportionellt för 10 minuters körningstid. $0.25/timme på Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Kopiera data och transformera med Azure Databricks varje timme

I det här fallet vill du kopiera data från AWS S3 till Azure Blob storage och omvandla data med Azure Databricks på ett timschema.

För att kunna utföra scenariot måste du skapa en pipeline med följande objekt:

1. En kopieringsaktivitet med indatauppsättning för de data som ska kopieras från AWS S3 och en utdatauppsättning för data på Azure-lagring.
2. En Azure Databricks-aktivitet för dataomvandlingen.
3. En schemautlösare för att köra pipelinen varje timme.

![Scenario2](media/pricing-concepts/scenario2.png)

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 3 Läs/skriv entitet  |
| Skapa datauppsättningar | 4 Läs/skriv-entiteter (2 för skapande av datauppsättning, 2 för länkade tjänstreferenser) |
| Skapa pipeline | 3 Läs/skriv-entiteter (1 för skapande av pipeline, 2 för datauppsättningsreferenser) |
| Hämta pipeline | 1 Läs/skriv entitet |
| Kör pipeline | 3 Aktivitetskörningar (1 för utlösarkörning, 2 för aktivitetskörningar) |
| Kopiera dataantagande: körningstid = 10 min | 10 \* 4 Azure Integration Runtime (standard-DIU-inställning = 4) Mer information om dataintegrationsenheter och optimering av kopieringsprestanda finns i [den här artikeln](copy-activity-performance.md) |
| Övervaka pipelineantagande: Endast 1 körning inträffade | 3 Övervakningskörningsposter har gjorts om (1 för pipelinekörning, 2 för aktivitetskörning) |
| Kör Databricks aktivitet Antagande: exekveringstid = 10 min | 10 min extern pipeline-aktivitetskörning |

**Totalt scenario prissättning: $ 0,16916**

- Data Fabriksoperationer = **$0.00012**
  - Läs/skriv = 11\*00001 = $0.00011 [1 R/W = $0.50/50000 = 0.00001]
  - Övervakning =\*3 000005 = $0.00001 [1 Övervakning = $0.25/50000 = 0.000005]
- Genomförande av &amp; pipelineorkestrering = **$0.16904**
  - Aktivitetskörningar =\*001 3 = 0,003 [1 körning = $1/1000 = 0.001]
  - Data movement aktiviteter = $0.166 (Proportionellt för 10 minuters körningstid. $0.25/timme på Azure Integration Runtime)
  - Extern pipelineaktivitet = $0.000041 (Proportionellt för 10 minuters körningstid. $0.00025/timme på Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Kopiera data och omforma med dynamiska parametrar varje timme

I det här fallet vill du kopiera data från AWS S3 till Azure Blob storage och omvandla med Azure Databricks (med dynamiska parametrar i skriptet) på ett timschema.

För att kunna utföra scenariot måste du skapa en pipeline med följande objekt:

1. En kopieringsaktivitet med indatauppsättning för de data som ska kopieras från AWS S3, en utdatauppsättning för data på Azure-lagring.
2. En uppslagsaktivitet för att skicka parametrar dynamiskt till omvandlingsskriptet.
3. En Azure Databricks-aktivitet för dataomvandlingen.
4. En schemautlösare för att köra pipelinen varje timme.

![Scenario3](media/pricing-concepts/scenario3.png)

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 3 Läs/skriv entitet  |
| Skapa datauppsättningar | 4 Läs/skriv-entiteter (2 för skapande av datauppsättning, 2 för länkade tjänstreferenser) |
| Skapa pipeline | 3 Läs/skriv-entiteter (1 för skapande av pipeline, 2 för datauppsättningsreferenser) |
| Hämta pipeline | 1 Läs/skriv entitet |
| Kör pipeline | 4 Aktivitetskörningar (1 för utlösarkörning, 3 för aktivitetskörningar) |
| Kopiera dataantagande: körningstid = 10 min | 10 \* 4 Azure Integration Runtime (standard-DIU-inställning = 4) Mer information om dataintegrationsenheter och optimering av kopieringsprestanda finns i [den här artikeln](copy-activity-performance.md) |
| Övervaka pipelineantagande: Endast 1 körning inträffade | 4 Övervakningskörningsposter som gjorts om (1 för pipelinekörning, 3 för aktivitetskörning) |
| Antagande om uppslagsaktivitet: körningstid = 1 min | 1 min körning av pipelineaktivitet |
| Kör Databricks aktivitet Antagande: exekveringstid = 10 min | 10 min körning av extern pipelineaktivitet |

**Totalt scenario prissättning: $ 0,17020**

- Data Fabriksoperationer = **$0.00013**
  - Läs/skriv = 11\*00001 = $0.00011 [1 R/W = $0.50/50000 = 0.00001]
  - Övervakning =\*4 000005 = $0.00002 [1 Övervakning = $0.25/50000 = 0.000005]
- Genomförande av &amp; pipelineorkestrering = **$0.17007**
  - Aktivitetskörningar =\*001 4 = 0,004 [1 körning = $1/1000 = 0.001]
  - Data movement aktiviteter = $0.166 (Proportionellt för 10 minuters körningstid. $0.25/timme på Azure Integration Runtime)
  - Pipeline Aktivitet = $0.00003 (Proportionellt för 1 minuts körningstid. $0.002/timme på Azure Integration Runtime)
  - Extern pipelineaktivitet = $0.000041 (Proportionellt för 10 minuters körningstid. $0.00025/timme på Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Använda felsökning av dataflöde för en normal arbetsdag

Som datatekniker ansvarar du för att utforma, skapa och testa kartläggningsdataflöden varje dag. Du loggar in i ADF-användargränssnittet på morgonen och aktiverar felsökningsläget för dataflöden. Standard-TTL för felsökningssessioner är 60 minuter. Du arbetar hela dagen i 8 timmar, så din Felsökningssession upphör aldrig att gälla. Därför kommer din avgift för dagen att vara:

**8 (timmar) x 8 (beräkningsoptimerade kärnor) x $0.193 = $12.35**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Omvandla data i blob-arkivet med mappningsdataflöden

I det här fallet vill du omvandla data i Blob Store visuellt i ADF-mappningsdataflöden enligt ett timschema.

För att kunna utföra scenariot måste du skapa en pipeline med följande objekt:

1. En dataflödesaktivitet med omvandlingslogiken.

2. En indatauppsättning för data på Azure Storage.

3. En utdatauppsättning för data på Azure Storage.

4. En schemautlösare som ska köra pipelinen varje timme.

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 2 Läs/skriv entitet  |
| Skapa datauppsättningar | 4 Läs/skriv-entiteter (2 för skapande av datauppsättning, 2 för länkade tjänstreferenser) |
| Skapa pipeline | 3 Läs/skriv-entiteter (1 för skapande av pipeline, 2 för datauppsättningsreferenser) |
| Hämta pipeline | 1 Läs/skriv entitet |
| Kör pipeline | 2 Aktivitetskörningar (1 för utlösarkörning, 1 för aktivitetskörningar) |
| Dataflödesantaganden: exekveringstid = 10 min + 10 min TTL | 10 \* 16 kärnor av General Compute med TTL av 10 |
| Övervaka pipelineantagande: Endast 1 körning inträffade | 2 Övervakningskörningsposter som gjorts om (1 för pipelinekörning, 1 för aktivitetskörning) |

**Totalt scenario prissättning: $ 1,4631**

- Data Fabriksoperationer = **$0.0001**
  - Läs/skriv = 10\*00001 = $0.0001 [1 R/W = $0.50/50000 = 0.00001]
  - Övervakning =\*2 000005 = $0.00001 [1 Övervakning = $0.25/50000 = 0.000005]
- Körning av &amp; pipelineorkestrering = **$1.463**
  - Aktivitetskörningar =\*001 2 = 0,002 [1 körning = $1/1000 = 0.001]
  - Dataflödesaktiviteter = $1.461 proportionellt i 20 minuter (10 min körningstid + 10 minuter TTL). $0.274/hour på Azure Integration Runtime med 16 kärnor allmän beräkning

## <a name="next-steps"></a>Nästa steg

Nu när du förstår prissättningen för Azure Data Factory kan du komma igång!

- [Skapa en datafabrik med hjälp av Azure Data Factory UI](quickstart-create-data-factory-portal.md)

- [Introduktion till Azure Data Factory](introduction.md)

- [Visuell redigering i Azure Data Factory](author-visually.md)

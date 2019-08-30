---
title: Förstå Azure Data Factory priser via exempel | Microsoft Docs
description: Den här artikeln beskriver och demonstrerar Azure Data Factory pris modell med detaljerade exempel
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/25/2018
ms.openlocfilehash: f08dea90e7700082b6eeb708b576451060f81255
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140949"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Förstå Data Factory priser via exempel

I den här artikeln beskrivs och demonstreras den Azure Data Factory pris modellen med detaljerade exempel.

> [!NOTE]
> Priserna som används i de här exemplen nedan är hypotetiska och är inte avsedda att innebära den faktiska prissättningen.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Kopiera data från AWS S3 till Azure Blob Storage per timme

I det här scenariot vill du kopiera data från AWS S3 till Azure Blob Storage enligt ett Tim schema.

För att uppnå scenariot måste du skapa en pipeline med följande objekt:

1. En kopierings aktivitet med en indata-datauppsättning för de data som ska kopieras från AWS S3.

2. En data uppsättning för data i Azure Storage.

3. En schema utlösare för att köra pipelinen varje timme.

   ![Scenario1](media/pricing-concepts/scenario1.png)

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 2 Läs/skriv-entitet  |
| Skapa data uppsättningar | 4 Läs-och skrivbara entiteter (2 för skapande av data uppsättning, 2 för länkade tjänst referenser) |
| Skapa pipeline | 3 Läs-och skrivbara entiteter (1 för skapande av pipeline, 2 för data uppsättnings referenser) |
| Hämta pipeline | 1 Läs-/skriv entitet |
| Kör pipeline | 2 aktivitets körningar (1 för körnings körning, 1 för aktivitets körningar) |
| Kopiera data antagande: körnings tid = 10 min | 10 \* 4 Azure integration Runtime (standard DIU-inställning = 4) mer information om data integrerings enheter och optimering av kopierings prestanda finns i [den här artikeln](copy-activity-performance.md) |
| Övervaka pipeline-antagande: Endast 1 körning inträffade | 2 nya försök att köra poster (1 för pipeline-körning, 1 för aktivitets körning) |

**Pris för total scenario: $0,16811**

- Data Factory åtgärder = **$0,0001**
  - Läs/Skriv = 10\*00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Övervakning = 2\*000005 = $0,00001 [1 övervakning = $0,25/50000 = 0,000005]
- &amp; Körning av pipeline-dirigering = **$0,168**
  - Aktivitet körs = 001\*2 = 0,002 [1 körning = $1/1000 = 0,001]
  - Data förflyttnings aktiviteter = $0,166 (beräknat i 10 minuters körnings tid. $0,25/timme på Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Kopiera data och transformera med Azure Databricks varje timme

I det här scenariot vill du kopiera data från AWS S3 till Azure Blob Storage och transformera data med Azure Databricks per tim schema.

För att uppnå scenariot måste du skapa en pipeline med följande objekt:

1. En kopierings aktivitet med en indatamängd för data som ska kopieras från AWS S3 och en data uppsättning för data i Azure Storage.
2. En Azure Databricks-aktivitet för data omvandlingen.
3. En schema utlösare för att köra pipelinen varje timme.

![Scenario2](media/pricing-concepts/scenario2.png)

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 3 Läs/skriv-entitet  |
| Skapa data uppsättningar | 4 Läs-och skrivbara entiteter (2 för skapande av data uppsättning, 2 för länkade tjänst referenser) |
| Skapa pipeline | 3 Läs-och skrivbara entiteter (1 för skapande av pipeline, 2 för data uppsättnings referenser) |
| Hämta pipeline | 1 Läs-/skriv entitet |
| Kör pipeline | 3 aktivitets körningar (1 för körnings körning, 2 för aktivitets körningar) |
| Kopiera data antagande: körnings tid = 10 min | 10 \* 4 Azure integration Runtime (standard DIU-inställning = 4) mer information om data integrerings enheter och optimering av kopierings prestanda finns i [den här artikeln](copy-activity-performance.md) |
| Övervaka pipeline-antagande: Endast 1 körning inträffade | 3 försök att köra poster för övervakning (1 för pipeline-körning, 2 för aktivitets körning) |
| Kör Databricks Activity Assumptions: körnings tid = 10 min | 10 min externa pipeline-aktivitets körning |

**Pris för total scenario: $0,16916**

- Data Factory åtgärder = **$0,00012**
  - Läs/skriv = 11\*00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Övervakning = 3\*000005 = $0,00001 [1 övervakning = $0,25/50000 = 0,000005]
- &amp; Körning av pipeline-dirigering = **$0,16904**
  - Aktivitet körs = 001\*3 = 0,003 [1 körning = $1/1000 = 0,001]
  - Data förflyttnings aktiviteter = $0,166 (beräknat i 10 minuters körnings tid. $0,25/timme på Azure Integration Runtime)
  - Extern pipeline-aktivitet = $0,000041 (beräknat i 10 minuters körnings tid. $0.00025/timme på Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Kopiera data och transformera med dynamiska parametrar varje timme

I det här scenariot vill du kopiera data från AWS S3 till Azure Blob Storage och transformera med Azure Databricks (med dynamiska parametrar i skriptet) enligt ett Tim schema.

För att uppnå scenariot måste du skapa en pipeline med följande objekt:

1. En kopierings aktivitet med en indatamängd för data som ska kopieras från AWS S3, en data uppsättning för data i Azure Storage.
2. En söknings aktivitet för att skicka parametrar dynamiskt till omvandlings skriptet.
3. En Azure Databricks-aktivitet för data omvandlingen.
4. En schema utlösare för att köra pipelinen varje timme.

![Scenario3](media/pricing-concepts/scenario3.png)

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 3 Läs/skriv-entitet  |
| Skapa data uppsättningar | 4 Läs-och skrivbara entiteter (2 för skapande av data uppsättning, 2 för länkade tjänst referenser) |
| Skapa pipeline | 3 Läs-och skrivbara entiteter (1 för skapande av pipeline, 2 för data uppsättnings referenser) |
| Hämta pipeline | 1 Läs-/skriv entitet |
| Kör pipeline | 4 aktivitets körningar (1 för körning av Utlös punkt, 3 för aktivitets körningar) |
| Kopiera data antagande: körnings tid = 10 min | 10 \* 4 Azure integration Runtime (standard DIU-inställning = 4) mer information om data integrerings enheter och optimering av kopierings prestanda finns i [den här artikeln](copy-activity-performance.md) |
| Övervaka pipeline-antagande: Endast 1 körning inträffade | 4 försök att köra poster för övervakning (1 för pipeline-körning, 3 för aktivitets körning) |
| Kör söknings aktivitets antagande: körnings tid = 1 min | 1 min körning av pipeline-aktivitet |
| Kör Databricks Activity Assumptions: körnings tid = 10 min | 10 min externa pipeline-aktivitets körning |

**Pris för total scenario: $0,17020**

- Data Factory åtgärder = **$0,00013**
  - Läs/skriv = 11\*00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Övervakning = 4\*000005 = $0,00002 [1 övervakning = $0,25/50000 = 0,000005]
- &amp; Körning av pipeline-dirigering = **$0,17007**
  - Aktivitet körs = 001\*4 = 0,004 [1 körning = $1/1000 = 0,001]
  - Data förflyttnings aktiviteter = $0,166 (beräknat i 10 minuters körnings tid. $0,25/timme på Azure Integration Runtime)
  - Pipeline-aktivitet = $0,00003 (beräknas för 1 minuters körnings tid. $0.002/timme på Azure Integration Runtime)
  - Extern pipeline-aktivitet = $0,000041 (beräknat i 10 minuters körnings tid. $0.00025/timme på Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday-preview-pricing"></a>Använda fel sökning av data flöde för en normal arbets dag (för hands versions prissättning)

Som data tekniker ansvarar du för att utforma, skapa och testa mappnings data flöden varje dag. Du loggar in på ADF-gränssnittet i morgon och aktiverar fel söknings läge för data flöden. Standard-TTL för debug-sessioner är 60 minuter. Du arbetar under en dag i 10 timmar så att din Felsök-session aldrig upphör att gälla. Avgiften för dagen blir därför:

**10 (timmar) x 8 (kärnor) x $0,112 = $8,96**

## <a name="transform-data-in-blob-store-with-mapping-data-flows-preview-pricing"></a>Transformera data i BLOB Store med mappnings data flöden (pris för för hands version)

I det här scenariot vill du transformera data i BLOB Store visuellt i data flöden för ADF-mappning enligt ett Tim schema.

För att uppnå scenariot måste du skapa en pipeline med följande objekt:

1. En data flödes aktivitet med omvandlings logiken.

2. En data uppsättning för data i Azure Storage.

3. En data uppsättning för data i Azure Storage.

4. En schema utlösare för att köra pipelinen varje timme.

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 2 Läs/skriv-entitet  |
| Skapa data uppsättningar | 4 Läs-och skrivbara entiteter (2 för skapande av data uppsättning, 2 för länkade tjänst referenser) |
| Skapa pipeline | 3 Läs-och skrivbara entiteter (1 för skapande av pipeline, 2 för data uppsättnings referenser) |
| Hämta pipeline | 1 Läs-/skriv entitet |
| Kör pipeline | 2 aktivitets körningar (1 för körnings körning, 1 för aktivitets körningar) |
| Antaganden för data flöde: körnings tid = 10 min + 10 min TTL | 10 \* 8 kärnor i allmän beräkning med TTL på 10 |
| Övervaka pipeline-antagande: Endast 1 körning inträffade | 2 nya försök att köra poster (1 för pipeline-körning, 1 för aktivitets körning) |

**Pris för total scenario: $0,3011**

- Data Factory åtgärder = **$0,0001**
  - Läs/Skriv = 10\*00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Övervakning = 2\*000005 = $0,00001 [1 övervakning = $0,25/50000 = 0,000005]
- &amp; Körning av pipeline-dirigering = **$0,301**
  - Aktivitet körs = 001\*2 = 0,002 [1 körning = $1/1000 = 0,001]
  - Data flödes aktiviteter = $0,299 beräknat i 20 minuter (10 minuter körnings tid + 10 minuter TTL). $0.112/timme på Azure Integration Runtime med 8 kärnor allmän beräkning

## <a name="next-steps"></a>Nästa steg

Nu när du förstår priserna för Azure Data Factory kan du komma igång!

- [Skapa en data fabrik med hjälp av Azure Data Factory gränssnittet](quickstart-create-data-factory-portal.md)

- [Introduktion till Azure Data Factory](introduction.md)

- [Visuell redigering i Azure Data Factory](author-visually.md)

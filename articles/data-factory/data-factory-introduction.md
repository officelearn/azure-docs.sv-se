---
title: "Introduktion till Data Factory, en dataintegreringstjänst | Microsoft Docs"
description: "Detta är vad Azure Data Factory är: en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data."
keywords: dataintegrering, molnbaserad dataintegrering, azure data factory
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/22/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 0175f2877a59db214a38bfe27eca74748518a498


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Introduktion till Azure Data Factory – en dataintegreringstjänst i molnet
## <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory?
Data Factory är en molnbaserad dataintegreringstjänst som samordnar och automatiserar **förflyttning** och **transformering** av data. Du kan skapa dataintegrationslösningar med hjälp av tjänsten Data Factory som kan föra in data från olika datakällor, transformera/bearbeta data och publicera resultatdata till datalager.

Med Data Factory-tjänsten kan du skapa datapipelines som flyttar och transformerar data och kör pipelines enligt ett angivet schema (varje timme, varje dag, varje vecka osv.). Den innehåller också omfattande visualiseringar för att visa härkomst och beroenden mellan dina datapipelines och övervaka alla datapipelines från en enda enhetlig vy för att enkelt hitta problem och konfigurera övervakningsaviseringar.

![Diagram: Data Factory-översikt, en dataintegreringstjänst](./media/data-factory-introduction/what-is-azure-data-factory.png)
**Figur 1.** För in data från olika datakällor, förbered, transformera och analysera data, och publicera sedan data som färdiga att använda.

## <a name="pipelines-and-activities"></a>Pipelines och aktiviteter
I en Data Factory-lösning skapar du en eller flera data**pipelines**. En pipeline är en logisk gruppering av aktiviteter. De används för att gruppera aktiviteter till en enhet som tillsammans utför en aktivitet.

**Aktiviteter** definierar de åtgärder som ska utföras på dina data. Du kan till exempel använda en kopieringsaktivitet för att kopiera data från ett datalager till ett annat. På samma sätt kan du använda en Hive-aktivitet som kör en Hive-fråga på ett Azure HDInsight-kluster för att transformera eller analysera dina data. Data Factory stöder två typer av aktiviteter: aktiviteter för dataförflyttning och datatransformering.

## <a name="data-movement-activities"></a>Dataförflyttningsaktiviteter
[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information.

## <a name="data-transformation-activities"></a>Datatransformeringsaktiviteter
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Se artikeln [Datatransformeringsaktiviteter](data-factory-data-transformation-activities.md) för mer information.

Om du behöver flytta data till/från ett datalager som Kopiera aktivitet inte stöder, eller transformera data med egen logik, kan du skapa en **anpassad .NET-aktivitet**. Mer information om att skapa och använda en anpassad aktivitet finns i [Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)](data-factory-use-custom-activities.md).

## <a name="linked-services"></a>Länkade tjänster
Länkade tjänster definierar den information som behövs för att Data Factory ska kunna ansluta till externa resurser (exempel: Azure Storage, lokal SQL Server, Azure HDInsight). Länkade tjänster används för två syften i Data Factory:

* Att representera ett **datalager** inklusive, men inte begränsat till, en lokal SQL Server, Oracle-databas, filresurs eller ett Azure Blob Storage-konto. I avsnittet [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) finns en lista över datalager som stöds.
* Så här visar du en **beräkningsresurs** som kan vara värd för körningen av en aktivitet. HDInsightHive-aktiviteten körs till exempel på ett HDInsight Hadoop-kluster. Se avsnittet [Datatransformeringsaktiviteter](data-factory-data-transformation-activities.md), för en lista över compute-miljöer som stöds.

## <a name="datasets"></a>Datauppsättningar
Länkade tjänster länkar datalager till en Azure-datafabrik. Datauppsättningar representerar datastrukturer i datalager. Till exempel innehåller en länkad Azure Storage-tjänst anslutningsinformation för Data Factory för att ansluta till ett Azure Storage-konto. En Azure Blob-datauppsättning anger blobbehållaren och -mappen i Azure Blob Storage som pipelinen ska läsa data från. På samma sätt anger en länkad Azure SQL-tjänst anslutningsinformation för en Azure SQL Database och en Azure SQL-datauppsättning anger tabellen som innehåller data.   

## <a name="relationship-between-data-factory-entities"></a>Förhållande mellan Data Factory-enheter
Data Factory har några viktiga entiteter som samverkar för att definiera indata och utdata, bearbeta händelser, samt de scheman och resurser som krävs för att köra det önskade dataflödet.

![Diagram: Data Factory, en molnbaserad dataintegreringstjänst – nyckelbegrepp](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figur 2.** Relationer mellan datauppsättning, aktivitet, pipeline och länkad tjänst

Med de fyra enkla länkade tjänsterna, datauppsättningarna, aktiviteterna och pipelines är du redo att sätta igång! Du kan [skapa din första pipeline](data-factory-build-your-first-pipeline.md).

## <a name="supported-regions"></a>Regioner som stöds
För närvarande kan du skapa datafabriker i regionerna **västra USA**, **östra USA** och **Norra Europa**. Dock kan en datafabrik ha åtkomst till datalager och beräkna tjänster i andra Azure-regioner för att flytta data mellan datalager eller bearbeta data med hjälp av beräkningstjänster.

Azure Data Factory lagrar inte själv några data. Du kan använda den för att skapa datadrivna flöden som samordnar flödet av data mellan [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) och bearbetning av data med hjälp av [beräkningstjänster](data-factory-compute-linked-services.md) i andra regioner eller i en lokal miljö. Du kan också [övervaka och hantera arbetsflöden](data-factory-monitor-manage-pipelines.md) med både program- och användargränssnittsmetoder.

Även om Azure Data Factory bara finns i regionerna **västra USA**, **östra USA** och **norra Europa**, är dataförflyttning i Data Factory tillgängligt [globalt](data-factory-data-movement-activities.md#global) i flera regioner. Om ett datalager finns bakom en brandvägg kan en [gateway för datahantering](data-factory-move-data-between-onprem-and-cloud.md) som installerats i din lokala miljö flytta datan i stället.

Exempelvis kan vi anta att dina beräkningsmiljöer, som t.ex. Azure HDInsight-kluster och Azure Machine Learning, körs utanför Västeuropas region. Du kan skapa och använda en Azure Data Factory-instans i norra Europa och använda den för att schemalägga jobb i dina beräkningsmiljöer i Västeuropa. Det tar några millisekunder för Data Factory att utlösa jobbet i din beräkningsmiljö, men den tid det tar för att köra jobbet ändras inte.

Vi planerar att ha Azure Data Factory i varje geografisk region som stöds av Azure i framtiden.

## <a name="next-steps"></a>Nästa steg
Följ de stegvisa anvisningarna i följande självstudier om du vill lära dig hur man skapar datafabriker med datapipeliner.

| Självstudier | Beskrivning |
| --- | --- |
| [Skapa en datapipeline som bearbetar data via ett Hadoop-kluster](data-factory-build-your-first-pipeline.md) |I de här självstudierna skapar du din första Azure Data Factory med en datapipeline som **bearbetar data** genom att köra Hive-skriptet på ett Azure HDInsight-kluster (Hadoop). |
| [Skapa en datapipeline som flyttar data mellan två molndatalager](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |I de här självstudierna skapar du en datafabrik med en pipeline som **flyttar data** från Blob Storage till SQL Database. |
| [Skapa en datapipeline som flyttar data mellan ett lokalt datalager och ett molndatalager med hjälp av Gateway för datahantering](data-factory-move-data-between-onprem-and-cloud.md) |I de här självstudierna skapar du en datafabrik med en pipeline som **flyttar data** från en **lokal** SQL Server-databas till en Azure-blob. Som en del av den här genomgången kommer du att installera och konfigurera Data Management Gateway på din dator. |



<!--HONumber=Nov16_HO2-->



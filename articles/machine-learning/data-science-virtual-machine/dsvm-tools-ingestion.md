---
title: "Data vetenskap virtuella data införandet verktyg - Azure | Microsoft Docs"
description: "Data vetenskap virtuella data införandet verktyg"
keywords: "datavetenskap verktyg, datavetenskap virtuell dator, verktyg för datavetenskap, datavetenskap för linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 8526e949aee2935824a03a0972d9e45c71d6601b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Data vetenskap virtuella data införandet verktyg

En av de första tekniska stegen i en datavetenskap eller AI-projekt är att identifiera datauppsättningarna som ska användas och sätta dem i din miljö för analytics. Den virtuella datorn på vetenskap (DSVM) tillhandahåller verktyg och bibliotek hämta data från olika källor i en analysdata lagring lokalt på DSVM eller i en dataplattform i molnet eller lokalt. 

Här följer vissa data movement verktyg som vi har samlat på DSVM. 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett verktyg för att kopiera data från Azure storage BLOB till Azure Data Lake Store. Det kan också kopiera data mellan två Azure Data Lake Store-konton.      |
| Stöds DSVM versioner      | Windows      |
| Vanliga användningsområden      | Importera flera blobbar från Azure-lagring till Azure Data Lake Store.      |
|  Hur du använder / kör den?    |   Öppna en kommandotolk, Skriv `adlcopy` kan få hjälp.    |
| Länkar till exempel      | [Använda AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Relaterade verktyg på DSVM      | AzCopy, Azure kommandoraden     |

## <a name="azure-command-line"></a>Azure kommandoraden

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett hanteringsverktyg för Azure. Den innehåller också kommandot verb för att flytta data från Azure data plattformar, som Azure storage-blobbar, lagring av Azure Data Lake     |
| Stöds DSVM versioner      | Windows, Linux     |
| Vanliga användningsområden      | Import, export av data till och från Azure storage, Azure Data Lake Store      |
|  Hur du använder / kör den?    |   Öppna en kommandotolk, Skriv `az` kan få hjälp.    |
| Länkar till exempel      | [Använda Azure CLI](https://docs.microsoft.com/cli/azure)     |
| Relaterade verktyg på DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett verktyg för att kopiera data till och från lokala filer, Azure storage-blobbar, filer och tabeller.      |
| Stöds DSVM versioner      | Windows      |
| Vanliga användningsområden      | Kopiera filer till blob storage, kopierar blobar mellan konton.      |
|  Hur du använder / kör den?    |   Öppna en kommandotolk, Skriv `azcopy` kan få hjälp.    |
| Länkar till exempel      | [AzCopy i Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Relaterade verktyg på DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB datamigreringsverktyget

|    |           |
| ------------- | ------------- |
| Vad är det?   | Verktyg för att importera data från olika källor, inklusive JSON-filer, CSV-filer, SQL, MongoDB, Azure Table storage, Amazon DynamoDB och Azure SQL DB-API Cosmos-samlingar i Azure Cosmos DB.      |
| Stöds DSVM versioner      | Windows      |
| Vanliga användningsområden      | Importera filer från en virtuell dator till CosmosDB, importera data från Azure-tabellagring till CosmosDB eller importera data från en SQL Server-databas till CosmosDB.     |
|  Hur du använder / kör den?    |   För att använda kommandoraden version, öppna en kommandotolk, Skriv `dt`. Att använda GUI-verktyg, öppna Kommandotolken, Skriv `dtui`.    |
| Länkar till exempel      | [CosmosDB importera Data](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Relaterade verktyg på DSVM      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Vad är det?   | SQL Server för att kopiera data mellan SQL Server och en datafil.      |
| Stöds DSVM versioner      | Windows      |
| Vanliga användningsområden      | Importera en CSV-fil i en SQL Server-tabell, exportera en tabell i SQL Server till en fil.      |
|  Hur du använder / kör den?    |   Öppna en kommandotolk, Skriv `bcp` kan få hjälp.    |
| Länkar till exempel      | [Bulk Copy-verktyget](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Relaterade verktyg på DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett verktyg för att montera en Azure blob-behållare i Linux-filsystem.      |
| Stöds DSVM versioner      | Linux      |
| Vanliga användningsområden      | Läsning och skrivning till blobbar i en behållare      |
|  Hur du använder / kör den?    |   Kör _blobfuse_ i en terminal.    |
| Länkar till exempel      | [blobfuse på GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Relaterade verktyg på DSVM      | Azure kommandoraden      |


## <a name="microsoft-data-management-gateway"></a>Microsoft Data Management Gateway

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett verktyg för att ansluta lokalt datakällor molntjänster för användning.      |
| Stöds DSVM versioner      | Windows      |
| Vanliga användningsområden      | Ansluter en virtuell dator till en lokal datakälla.      |
|  Hur du använder / kör den?    |   Starta ”Microsoft Data Management Gateway” från Start-menyn.    |
| Länkar till exempel      | [Gateway för datahantering](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Relaterade verktyg på DSVM      | AzCopy, AdlCopy, bcp    |

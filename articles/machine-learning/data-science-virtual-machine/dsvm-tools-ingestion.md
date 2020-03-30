---
title: Verktyg för datainhämtning
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig mer om de verktyg och verktyg för datainmatning som är förinstallerade på den virtuella datorn för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d86858f8d7f09628457b718ca3c481934d720081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270061"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Data Science Virtual Machine datainmatningsverktyg

Som ett av de första tekniska stegen i ett datavetenskap eller AI-projekt måste du identifiera de datauppsättningar som ska användas och föra in dem i din analysmiljö. Data Science Virtual Machine (DSVM) tillhandahåller verktyg och bibliotek för att föra in data från olika källor i analytisk datalagring lokalt på DSVM eller till en dataplattform antingen i molnet eller lokalt.

Här är några datarörelseverktyg som är tillgängliga i DSVM.

## <a name="adlcopy"></a>AdlCopy (AdlCopy)

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Ett verktyg för att kopiera data från Azure Blob-lagring till Azure Data Lake Store. Det kan också kopiera data mellan två Azure Data Lake Store-konton.      |
| DSVM-versioner som stöds      | Windows      |
| Typiska användningsområden      | Importera flera blobbar från Azure Blob-lagring till Azure Data Lake Store.      |
|  Hur man använder / köra det?    |   Öppna en kommandotolk och skriv `adlcopy` för att få hjälp.    |
| Länkar till exempel      | [Använda AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Relaterade verktyg på DSVM      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Ett hanteringsverktyg för Azure. Den innehåller också kommandovert för att flytta data från Azure-dataplattformar som Azure Blob storage och Azure Data Lake Store.     |
| DSVM-versioner som stöds      | Windows, Linux     |
| Typiska användningsområden      | Importera och exportera data till och från Azure Storage och Azure Data Lake Store.      |
|  Hur man använder / köra det?    |   Öppna en kommandotolk och skriv `az` för att få hjälp.    |
| Länkar till exempel      | [Använda Azure CLI](https://docs.microsoft.com/cli/azure)     |
| Relaterade verktyg på DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Ett verktyg för att kopiera data till och från lokala filer, Azure Blob-lagring, filer och tabeller.      |
| DSVM-versioner som stöds      | Windows      |
| Typiska användningsområden      | Kopiera filer till Azure Blob-lagring och kopiering av blobbar mellan konton.      |
|  Hur man använder / köra det?    |   Öppna en kommandotolk och skriv `azcopy` för att få hjälp.    |
| Länkar till exempel      | [AzCopy i Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Relaterade verktyg på DSVM      | AdlCopy (AdlCopy)     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Verktyget Azure Cosmos DB-datamigrering

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Verktyg för att importera data från olika källor till Azure Cosmos DB, en NoSQL-databas i molnet. Dessa källor inkluderar JSON-filer, CSV-filer, SQL, MongoDB, Azure Table storage, Amazon DynamoDB och Azure Cosmos DB SQL API-samlingar.      |
| DSVM-versioner som stöds      | Windows      |
| Typiska användningsområden      | Importera filer från en virtuell dator till CosmosDB, importera data från Azure-tabelllagring till CosmosDB och importera data från en Microsoft SQL Server-databas till CosmosDB.     |
|  Hur man använder / köra det?    |   Om du vill använda kommandoradsversionen öppnar `dt`du en kommandotolk och skriver . Om du vill använda GUI-verktyget `dtui`öppnar du en kommandotolk och skriver .    |
| Länkar till exempel      | [Importdata för CosmosDB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Relaterade verktyg på DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Grafiskt användargränssnitt för att interagera med filer som lagras i Azure-molnet. |
| DSVM-versioner som stöds      | Windows      |
| Typiska användningsområden      | Importera och exportera data från DSVM.    |
|  Hur man använder / köra det?    | Sök efter "Azure Storage Explorer" på Start-menyn. |
| Länkar till exempel      | [Utforskaren för Azure Storage](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Vad är detta?   | SQL Server-verktyg för att kopiera data mellan SQL Server och en datafil.      |
| DSVM-versioner som stöds      | Windows      |
| Typiska användningsområden      | Importera en CSV-fil till en SQL Server-tabell och exportera en SQL Server-tabell till en fil.      |
|  Hur man använder / köra det?    |   Öppna en kommandotolk och skriv `bcp` för att få hjälp.    |
| Länkar till exempel      | [bcp-verktyget](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Relaterade verktyg på DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Ett verktyg för att montera en Azure Blob-lagringsbehållare i Linux-filsystemet.      |
| DSVM-versioner som stöds      | Linux      |
| Typiska användningsområden      | Läsa och skriva till blobbar i en behållare.      |
|  Hur man använder och kör det?    |   Kör _blus_ på en terminal.    |
| Länkar till exempel      | [blobfuse på GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Relaterade verktyg på DSVM      | Azure CLI      |

---
title: Verktyg för datainhämtning
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig mer om de verktyg och verktyg för data inmatning som är förinstallerade på Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d86858f8d7f09628457b718ca3c481934d720081
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270061"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Data Science Virtual Machine verktyg för data inmatning

Som ett av de första tekniska stegen i ett data vetenskaps-eller AI-projekt måste du identifiera de data uppsättningar som ska användas och ta dem i din analys miljö. Data Science Virtual Machine (DSVM) innehåller verktyg och bibliotek för att hämta data från olika källor till analys data lagring lokalt på DSVM eller till en data plattform antingen i molnet eller lokalt.

Här följer några data flytt verktyg som är tillgängliga i DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Ett verktyg för att kopiera data från Azure Blob Storage till Azure Data Lake Store. Det kan också kopiera data mellan två Azure Data Lake Store-konton.      |
| DSVM-versioner som stöds      | Windows      |
| Vanliga användnings områden      | Importera flera blobbar från Azure Blob Storage till Azure Data Lake Store.      |
|  Hur använder man/kör den?    |   Öppna en kommando tolk och skriv `adlcopy` för att få hjälp.    |
| Länkar till exempel      | [Använda AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Relaterade verktyg på DSVM      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Ett hanterings verktyg för Azure. Det innehåller också kommando verb för att flytta data från Azure-dataplattformar som Azure Blob Storage och Azure Data Lake Store.     |
| DSVM-versioner som stöds      | Windows, Linux     |
| Vanliga användnings områden      | Importera och exportera data till och från Azure Storage och Azure Data Lake Store.      |
|  Hur använder man/kör den?    |   Öppna en kommando tolk och skriv `az` för att få hjälp.    |
| Länkar till exempel      | [Använda Azure CLI](https://docs.microsoft.com/cli/azure)     |
| Relaterade verktyg på DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Ett verktyg för att kopiera data till och från lokala filer, Azure Blob Storage, filer och tabeller.      |
| DSVM-versioner som stöds      | Windows      |
| Vanliga användnings områden      | Kopiera filer till Azure Blob Storage och kopiera blobbar mellan konton.      |
|  Hur använder man/kör den?    |   Öppna en kommando tolk och skriv `azcopy` för att få hjälp.    |
| Länkar till exempel      | [AzCopy i Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Relaterade verktyg på DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Verktyget Azure Cosmos DB datamigrering

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Verktyg för att importera data från olika källor till Azure Cosmos DB en NoSQL-databas i molnet. Dessa källor innehåller JSON-filer, CSV-filer, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB och Azure Cosmos DB SQL API-samlingar.      |
| DSVM-versioner som stöds      | Windows      |
| Vanliga användnings områden      | Importera filer från en virtuell dator till CosmosDB, importera data från Azure Table Storage till CosmosDB och importera data från en Microsoft SQL Server databas till CosmosDB.     |
|  Hur använder man/kör den?    |   Om du vill använda kommando rads versionen öppnar du en kommando tolk och skriver `dt`. Om du vill använda GUI-verktyget öppnar du en kommando tolk `dtui`och skriver.    |
| Länkar till exempel      | [Importera data för CosmosDB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Relaterade verktyg på DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Grafiskt användar gränssnitt för att interagera med filer som lagras i Azure-molnet. |
| DSVM-versioner som stöds      | Windows      |
| Vanliga användnings områden      | Importera och exportera data från DSVM.    |
|  Hur använder man/kör den?    | Sök efter "Azure Storage Explorer" på Start menyn. |
| Länkar till exempel      | [Azure Lagringsutforskaren](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Vad är detta?   | SQL Server verktyget för att kopiera data mellan SQL Server och en datafil.      |
| DSVM-versioner som stöds      | Windows      |
| Vanliga användnings områden      | Importera en CSV-fil till en SQL Server tabell och exportera en SQL Server tabell till en fil.      |
|  Hur använder man/kör den?    |   Öppna en kommando tolk och skriv `bcp` för att få hjälp.    |
| Länkar till exempel      | [BCP-verktyg](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Relaterade verktyg på DSVM      | SQL Server, SQLCMD      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Ett verktyg för att montera en Azure Blob Storage-behållare i Linux-filsystemet.      |
| DSVM-versioner som stöds      | Linux      |
| Vanliga användnings områden      | Läsning och skrivning till blobbar i en behållare.      |
|  Hur använder och kör jag det?    |   Kör _blobfuse_ på en Terminal.    |
| Länkar till exempel      | [blobfuse på GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Relaterade verktyg på DSVM      | Azure CLI      |

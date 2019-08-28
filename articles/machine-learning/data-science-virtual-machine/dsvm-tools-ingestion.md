---
title: Data Science Virtual Machine verktyg för datainhämtning – Azure | Microsoft Docs
description: Lär dig mer om de verktyg och verktyg för data inmatning som är förinstallerade på Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: 58bfab43d39f050e19687c30a61e05892fffc3f2
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060608"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Data Science Virtual Machine verktyg för datainhämtning

Som ett av de första tekniska stegen i ett data vetenskaps-eller AI-projekt måste du identifiera de data uppsättningar som ska användas och ta dem i din analys miljö. Data Science Virtual Machine (DSVM) innehåller verktyg och bibliotek för att hämta data från olika källor till analys data lagring lokalt på DSVM eller till en data plattform antingen i molnet eller lokalt.

Här följer några data flytt verktyg som är tillgängliga i DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett verktyg för att kopiera data från Azure Blob Storage till Azure Data Lake Store. Det kan också kopiera data mellan två Azure Data Lake Store-konton.      |
| DSVM-versioner som stöds      | Windows      |
| Vanliga användningsområden      | Importera flera blobbar från Azure Blob Storage till Azure Data Lake Store.      |
|  Hur du använder / köra den?    |   Öppna en kommando tolk och skriv `adlcopy` för att få hjälp.    |
| Innehåller länkar till exempel      | [Använda AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Relaterade verktyg på DSVM      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett hanteringsverktyg för Azure. Det innehåller också kommando verb för att flytta data från Azure-dataplattformar som Azure Blob Storage och Azure Data Lake Store.     |
| DSVM-versioner som stöds      | Windows, Linux     |
| Vanliga användningsområden      | Importera och exportera data till och från Azure Storage och Azure Data Lake Store.      |
|  Hur du använder / köra den?    |   Öppna en kommando tolk och skriv `az` för att få hjälp.    |
| Innehåller länkar till exempel      | [Använda Azure CLI](https://docs.microsoft.com/cli/azure)     |
| Relaterade verktyg på DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett verktyg för att kopiera data till och från lokala filer, Azure Blob Storage, filer och tabeller.      |
| DSVM-versioner som stöds      | Windows      |
| Vanliga användningsområden      | Kopiera filer till Azure Blob Storage och kopiera blobbar mellan konton.      |
|  Hur du använder / köra den?    |   Öppna en kommando tolk och skriv `azcopy` för att få hjälp.    |
| Innehåller länkar till exempel      | [AzCopy i Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Relaterade verktyg på DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB datamigreringsverktyget

|    |           |
| ------------- | ------------- |
| Vad är det?   | Verktyg för att importera data från olika källor till Azure Cosmos DB. Dessa källor innehåller JSON-filer, CSV-filer, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB och Azure Cosmos DB SQL API-samlingar.      |
| DSVM-versioner som stöds      | Windows      |
| Vanliga användningsområden      | Importera filer från en virtuell dator till CosmosDB, importera data från Azure Table Storage till CosmosDB och importera data från en Microsoft SQL Server databas till CosmosDB.     |
|  Hur du använder / köra den?    |   Om du vill använda kommando rads versionen öppnar du en kommando tolk och skriver `dt`. Om du vill använda GUI-verktyget öppnar du en kommando tolk `dtui`och skriver.    |
| Innehåller länkar till exempel      | [Importera data för CosmosDB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Relaterade verktyg på DSVM      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Vad är det?   | SQL Server-verktyg för att kopiera data mellan SQL Server och en datafil.      |
| DSVM-versioner som stöds      | Windows      |
| Vanliga användningsområden      | Importera en CSV-fil till en SQL Server tabell och exportera en SQL Server tabell till en fil.      |
|  Hur du använder / köra den?    |   Öppna en kommando tolk och skriv `bcp` för att få hjälp.    |
| Innehåller länkar till exempel      | [BCP-verktyg](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Relaterade verktyg på DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>Blobfuse

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett verktyg för att montera en Azure Blob Storage-behållare i Linux-filsystemet.      |
| DSVM-versioner som stöds      | Linux      |
| Vanliga användningsområden      | Läsning och skrivning till blobbar i en behållare.      |
|  Hur använder och kör jag det?    |   Kör _blobfuse_ i en terminal.    |
| Innehåller länkar till exempel      | [blobfuse på GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Relaterade verktyg på DSVM      | Azure CLI      |


## <a name="microsoft-data-management-gateway"></a>Microsoft Data Management Gateway

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett verktyg för att ansluta lokala datakällor molnbaserade tjänster för konsumtion.      |
| DSVM-versioner som stöds      | Windows      |
| Vanliga användningsområden      | Ansluta en virtuell dator till en lokal datakälla.      |
|  Hur du använder / köra den?    |   Öppna Microsoft Data Management Gateway på Start menyn.    |
| Innehåller länkar till exempel      | [Gateway för datahantering](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Relaterade verktyg på DSVM      | AzCopy, AdlCopy, bcp    |

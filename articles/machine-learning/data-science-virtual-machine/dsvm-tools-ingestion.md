---
title: Data Science Virtual Machine verktyg för datainhämtning – Azure | Microsoft Docs
description: Läs om verktyg för datainhämtning och verktyg som redan är installerat på den virtuella datorn för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: gopitk
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
ms.author: gokuma
ms.openlocfilehash: 92ff5d21fc30d8fcafe97a2b452ff157a2cd5f86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60502237"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Data Science Virtual Machine verktyg för datainhämtning

En av de första tekniska stegen i en datavetenskap eller AI-projekt är att identifiera datauppsättningarna som ska användas och anpassa dem till din analytics-miljö. Den virtuella datorn på datavetenskap (DSVM) erbjuder verktyg och bibliotek att importera data från olika källor till en analysdata storage lokalt på DSVM eller i en plattform för företagsdata i molnet eller lokalt. 

Här är några verktyg för flytt av data som har vi lagt till på DSVM. 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett verktyg för att kopiera data från Azure storage BLOB till Azure Data Lake Store. Det kan också kopiera data mellan två Azure Data Lake Store-konton.      |
| Stöds DSVM-versioner      | Windows      |
| Vanliga användningsområden      | Importera flera blobbar från Azure storage till Azure Data Lake Store.      |
|  Hur du använder / köra den?    |   Öppna en kommandotolk, Skriv `adlcopy` att få hjälp.    |
| Innehåller länkar till exempel      | [Använda AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Relaterade verktyg på DSVM      | AzCopy, Azure-kommandorad     |

## <a name="azure-command-line"></a>Azure-kommandorad

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett hanteringsverktyg för Azure. Den innehåller också kommandot verb för att flytta data från Azure-Dataplattformar som Azure storage BLOB, Azure Data Lake Storage     |
| Stöds DSVM-versioner      | Windows, Linux     |
| Vanliga användningsområden      | Importera, exportera data till och från Azure storage, Azure Data Lake Store      |
|  Hur du använder / köra den?    |   Öppna en kommandotolk, Skriv `az` att få hjälp.    |
| Innehåller länkar till exempel      | [Använda Azure CLI](https://docs.microsoft.com/cli/azure)     |
| Relaterade verktyg på DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett verktyg för att kopiera data till och från lokala filer, Azure storage-blobbar, filer och tabeller.      |
| Stöds DSVM-versioner      | Windows      |
| Vanliga användningsområden      | Kopiera filer till blob storage, kopiera blobar mellan konton.      |
|  Hur du använder / köra den?    |   Öppna en kommandotolk, Skriv `azcopy` att få hjälp.    |
| Innehåller länkar till exempel      | [AzCopy i Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Relaterade verktyg på DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB datamigreringsverktyget

|    |           |
| ------------- | ------------- |
| Vad är det?   | Verktyg för att importera data från olika källor, inklusive JSON-filer, CSV-filer, SQL, MongoDB, Azure Table storage, Amazon DynamoDB och Azure Cosmos DB SQL API-samlingar till Azure Cosmos DB.      |
| Stöds DSVM-versioner      | Windows      |
| Vanliga användningsområden      | Importera filer från en virtuell dator till CosmosDB, importera data från Azure table storage till CosmosDB eller importera data från en SQL Server-databas till CosmosDB.     |
|  Hur du använder / köra den?    |   För att använda kommandoraden version, öppna en kommandotolk, skriv sedan `dt`. Att använda GUI-verktyget, öppna en kommandotolk, Skriv `dtui`.    |
| Innehåller länkar till exempel      | [CosmosDB importera Data](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Relaterade verktyg på DSVM      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Vad är det?   | SQL Server-verktyg för att kopiera data mellan SQL Server och en datafil.      |
| Stöds DSVM-versioner      | Windows      |
| Vanliga användningsområden      | Importera en CSV-fil i en SQL Server-tabell, exportera en SQL Server-tabell till en fil.      |
|  Hur du använder / köra den?    |   Öppna en kommandotolk, Skriv `bcp` att få hjälp.    |
| Innehåller länkar till exempel      | [Bulk Copy-verktyget](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Relaterade verktyg på DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>Blobfuse

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett verktyg för att montera en Azure blob-behållare i Linux-filsystem.      |
| Stöds DSVM-versioner      | Linux      |
| Vanliga användningsområden      | Läsning och skrivning till blobarna i en behållare      |
|  Hur du använder / köra den?    |   Kör _blobfuse_ i en terminal.    |
| Innehåller länkar till exempel      | [blobfuse på GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Relaterade verktyg på DSVM      | Azure-kommandorad      |


## <a name="microsoft-data-management-gateway"></a>Microsoft Data Management Gateway

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ett verktyg för att ansluta lokala datakällor molnbaserade tjänster för konsumtion.      |
| Stöds DSVM-versioner      | Windows      |
| Vanliga användningsområden      | Ansluta en virtuell dator till en lokal datakälla.      |
|  Hur du använder / köra den?    |   Starta ”Microsoft Data Management Gateway” från Start-menyn.    |
| Innehåller länkar till exempel      | [Gateway för datahantering](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Relaterade verktyg på DSVM      | AzCopy, AdlCopy, bcp    |

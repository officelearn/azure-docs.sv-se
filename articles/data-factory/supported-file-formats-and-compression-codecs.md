---
title: Fil format som stöds i Azure Data Factory
description: Det här avsnittet beskriver filformat och komprimering koder som stöds av filbaserade anslutningar i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 6855eea5939419c9a0a867de4e0621b4d4ae02b9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439576"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Filformat som stöds och komprimering codec-enheter i Azure Data Factory

*Den här artikeln gäller följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Du kan använda [kopierings aktiviteten](copy-activity-overview.md) för att kopiera filer som är mellan två filbaserade data lager, i vilket fall data kopieras effektivt utan någon serialisering eller deserialisering. 

Dessutom kan du också tolka eller generera filer av ett angivet format. Du kan till exempel göra följande:

* Kopiera data från en lokal SQL Server databas och skriv till Azure Data Lake Storage Gen2 i Parquet-format.
* Kopiera filer i text format (CSV) från ett lokalt fil system och skriva till Azure Blob Storage i Avro-format.
* Kopiera zippade filer från ett lokalt fil system, expandera dem och skriv sedan de extraherade filerna till Azure Data Lake Storage Gen2.
* Kopiera data i formatet GZIP-komprimerad text (CSV) från Azure Blob Storage och skriv den till Azure SQL Database.
* Många fler aktiviteter som kräver serialisering/deserialisering eller komprimering/expandering.

## <a name="next-steps"></a>Nästa steg

Se de andra artiklarna i Kopieringsaktiviteten:

- [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
- [Kopiera aktivitets prestanda](copy-activity-performance.md)

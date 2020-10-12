---
title: Fil format som stöds av kopierings aktivitet i Azure Data Factory
description: I det här avsnittet beskrivs de fil format och komprimerings koder som stöds av kopierings aktiviteten i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.openlocfilehash: c044208699bf5bebb6383cfef00bf53b744369d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86522455"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory"></a>Fil format och komprimerings-codec som stöds av kopierings aktiviteten i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Den här artikeln gäller följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Du kan använda [kopierings aktiviteten](copy-activity-overview.md) för att kopiera filer som är mellan två filbaserade data lager, i vilket fall data kopieras effektivt utan någon serialisering eller deserialisering. 

Dessutom kan du också tolka eller generera filer av ett angivet format. Du kan till exempel göra följande:

* Kopiera data från en SQL Server-databas och skriva till Azure Data Lake Storage Gen2 i Parquet-format.
* Kopiera filer i text format (CSV) från ett lokalt fil system och skriva till Azure Blob Storage i Avro-format.
* Kopiera zippade filer från ett lokalt fil system, expandera dem och skriv sedan de extraherade filerna till Azure Data Lake Storage Gen2.
* Kopiera data i formatet GZIP-komprimerad text (CSV) från Azure Blob Storage och skriv den till Azure SQL Database.
* Många fler aktiviteter som kräver serialisering/deserialisering eller komprimering/expandering.

## <a name="next-steps"></a>Nästa steg

Se andra artiklar om kopierings aktiviteter:

- [Översikt över kopieringsaktivitet](copy-activity-overview.md)
- [Kopiera aktivitetsprestanda](copy-activity-performance.md)

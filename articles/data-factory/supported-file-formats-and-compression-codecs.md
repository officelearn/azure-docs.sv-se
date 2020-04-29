---
title: Fil format som stöds i Azure Data Factory
description: I det här avsnittet beskrivs de fil format och komprimerings koder som stöds av filbaserade kopplingar i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: dbcfad3dd3db9f5c9431e07d85d77a77a10283c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419042"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Fil format och komprimerings-codecar som stöds i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

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

Se andra artiklar om kopierings aktiviteter:

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Kopiera aktivitetsprestanda](copy-activity-performance.md)

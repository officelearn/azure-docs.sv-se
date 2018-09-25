---
title: Datakällor som är tillgängliga med Azure Machine Learning förberedelse av data som stöds | Microsoft Docs
description: Det här dokumentet innehåller en fullständig lista över stödda datakällor som är tillgängliga för förberedelse av data i Azure Machine Learning.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: c4686bf01031e30bd9dc94f42d80da524db82931
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969265"
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>Datakällor som stöds för förberedelse av data i Azure Machine Learning 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Den här artikeln beskriver de datakällor som för närvarande stöds för förberedelse av data i Azure Machine Learning.

Datakällor som stöds för den här versionen är som följer.

## <a name="types"></a>Typer 

### <a name="sql-server"></a>SQL Server
Läsa från en lokal SQLServer eller Azure SQL-databas.

#### <a name="options"></a>Alternativ
- Serveradress
- Lita på servern (även om certifikatet på servern inte är giltigt. Använd med försiktighet)
- Autentiseringstyp (Windows, Server)
- Användarnamn
- Lösenord
- Databasen för att ansluta till
- SQL-fråga

#### <a name="notes"></a>Anteckningar
- SQL-variant kolumner stöds inte
- Time-kolumn omvandlas till datetime genom att lägga till tid från databasen hittills 1970/1/1
- När den körs på Spark-kluster, alla data som är relaterade kolumner (datum, datetime, datetime2, datetimeoffset) ska utvärdera felaktiga värden för datum innan 1583
- Värdena i de kolumner som decimaltal kan förlora precision på grund av omvandling till decimal

### <a name="directory-vs-file"></a>Directory jämfört med filen
Välj en enda fil och läsa in förberedelse av data. Filtypen analyseras för att fastställa standardparametrar för filen anslutningen visas på nästa skärm.

Välj en katalog eller en uppsättning filer i en katalog (filväljaren är Välj flera). Med antingen metoden filerna är skrivskyddade i som ett enda dataflöde och läggs till varandra, med rubriker bort om det behövs.

Filtyper som stöds är:
- Avgränsad (.csv, TSV, .txt, osv.)
- Fast bredd
- Oformaterad text
- JSON-fil

### <a name="csv-file"></a>CSV-fil
Läs en fil med kommatecken avgränsade värden från lagringen.

#### <a name="options"></a>Alternativ
- Avgränsare
- Kommentar
- Rubriker
- Decimaltecken
- Filkodning
- Rader att hoppa över

### <a name="tsv-file"></a>TSV-fil
Läs en fil med fliken avgränsade värden från lagringen.

#### <a name="options"></a>Alternativ
- Kommentar
- Rubriker
- Filkodning
- Rader att hoppa över

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Läsa ett Excel-fil, ett blad i taget genom att ange namn eller en siffra.

#### <a name="options"></a>Alternativ
- Arknamnet eller en siffra
- Rubriker
- Rader att hoppa över

### <a name="json-file"></a>JSON-fil
Läs en JSON-fil från lagringen. Filen förenklas ”” vid läsning.

#### <a name="options"></a>Alternativ
- Ingen

### <a name="parquet"></a>Parquet
Läs en Parquet-datamängd, antingen en enskild fil eller en mapp.

Parquet eftersom ett format som kan ta olika former i lagring. En enda .parquet fil används ibland för mindre datamängder. Olika Python-biblioteken stöder läsning eller skrivning till enkel .parquet filer. För tillfället är förberedelse av data i Azure Machine Learning förlitar sig på PyArrow Python-bibliotek för att läsa Parquet under lokal interaktiva användning. Det stöder enkel .parquet filer (så länge som de har skrivits som sådana och inte som en del av en större mängd data), samt Parquet-datauppsättningar.

En Parquet-datauppsättning är en samling av fler än en .parquet-fil som representerar en mindre partition av en större mängd data. Datauppsättningar finns vanligtvis i en mapp och är parquet format för standardutdata för plattformar som Spark och Hive.

>[!NOTE]
>När du läser Parquet-data som finns i en mapp med flera .parquet filer, är det säkrast att välja katalog för att läsningen ska och **Parquet datauppsättning** alternativet. Detta gör PyArrow läsa hela mappen i stället för enskilda filer. Detta säkerställer att stöd för att läsa mer komplicerad sätt för att lagra Parquet på disk, till exempel mappen partitionering.

Skala ut körning förlitar sig på Spark's Parquet läser funktioner och stöd för enskilda filer och mappar, liknar lokal interaktiva användning.

#### <a name="options"></a>Alternativ
- Parquet-datauppsättningen. Det här alternativet avgör om förberedelse av data i Azure Machine Learning expanderar en viss katalog och försöker läsa varje fil individuellt (de omarkerade läget), eller om den behandlar katalogen som hela datauppsättningen (det valda läget). Med det valda läget väljer PyArrow du det bästa sättet att tolka filerna.


## <a name="locations"></a>Platser
### <a name="local"></a>Lokal
En lokal hårddisk eller en lagringsplats för mappade nätverksenheter.

### <a name="sql-server"></a>SQL Server
En lokal SQL Server eller Azure SQL-databas.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob-lagring, vilket kräver en Azure-prenumeration.


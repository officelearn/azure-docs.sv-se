---
title: "Datakällor som är tillgängliga med Azure Machine Learning förberedelse av data som stöds | Microsoft Docs"
description: "Det här dokumentet innehåller en fullständig lista över datakällor som stöds tillgänglig för Azure Machine Learning förberedelse av data."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 7b42080ea4bf9a9e49f2695ab8746d9ead7348bd
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>Datakällor som stöds för Azure Machine Learning förberedelse av data 
Den här artikeln beskrivs datakällor som för närvarande stöds för Azure Machine Learning förberedelse av data.

Datakällor som stöds för den här versionen är som följer.

## <a name="types"></a>Typer 

### <a name="sql-server"></a>SQL Server
Läsa från lokala SQLServer- eller Azure SQL-databas.

#### <a name="options"></a>Alternativ
- Serveradress
- Lita på servern (även om certifikatet på servern som inte är giltigt. Använd med försiktighet)
- Autentiseringstyp (Windows, Server)
- Användarnamn
- Lösenord
- Databasen för att ansluta till
- SQL-fråga

#### <a name="notes"></a>Anteckningar
- SQL-variant-kolumner stöds inte
- Time-kolumn konverteras till datetime genom att lägga till tid från databasen till datum 1970/1/1
- När den körs på Spark-kluster, alla data som är relaterade kolumner (datum, datetime, datetime2, datetimeoffset) kommer att utvärdera felaktiga värden för datum före 1583
- Värden i decimal kolumner kan förlora precision på grund av konvertering till decimal

### <a name="directory-vs-file"></a>Directory kontra fil
Välj en fil och läsa in förberedelse av data. Filtypen analyseras för att fastställa standardparametrar för filen anslutningen visas på nästa skärm.

Välj en katalog eller en uppsättning filer i en katalog (filväljaren är multiselect). Filerna är skrivskyddade i som ett enskilt dataflöde med antingen metoden och läggs till varandra med sidhuvuden bort om det behövs.

Filtyper som stöds är:
- Avgränsade (.csv, TSV, .txt, etc.)
- Fast bredd
- Oformaterad text
- JSON-fil

### <a name="csv-file"></a>CSV-fil
Läs en CSV-värdefil från lagringsplatsen.

#### <a name="options"></a>Alternativ
- Avgränsare
- Kommentar
- Sidhuvuden
- Decimaltecken
- Filkodning
- Rader ska hoppas över

### <a name="tsv-file"></a>TVS fil
Läs en fliken-värdefil från lagringsplatsen.

#### <a name="options"></a>Alternativ
- Kommentar
- Sidhuvuden
- Filkodning
- Rader ska hoppas över

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Läs en Excel-fil, ett blad i taget genom att ange namn eller en siffra.

#### <a name="options"></a>Alternativ
- Namn eller en siffra
- Sidhuvuden
- Rader ska hoppas över

### <a name="json-file"></a>JSON-fil
Läs en JSON-fil från lagringsplatsen. Filen förenklas ”” vid läsning.

#### <a name="options"></a>Alternativ
- Ingen

### <a name="parquet"></a>Parkettgolv
Läs en parkettgolv datamängd, antingen en enskild fil eller en mapp.

Parkettgolv som ett format som kan ha olika format i lagringen. En enda .parquet-fil används ibland för mindre datamängder. Olika Python-bibliotek stöd för läsning eller skrivning till enkel .parquet filer. För tillfället beroende förberedelse av Azure Machine Learning data PyArrow Python-bibliotek för att läsa parkettgolv under lokala interaktiv användning. Den stöder enkel .parquet filer (så länge de skrivs som sådana och inte som en del av en större mängd data), samt parkettgolv datauppsättningar.

En datauppsättning för parkettgolv är en samling av fler än en .parquet-fil, som representerar en mindre partition av en större mängd data. Uppgifter som finns vanligtvis i en mapp och är standardformatet för parkettgolv utdata för plattformar, till exempel Spark och Hive.

>[!NOTE]
>När du läser parkettgolv data i en mapp med flera .parquet filer är det säkraste och välj katalogen för att läsa och **parkettgolv datauppsättning** alternativet. Detta gör PyArrow läsa hela mappen i stället för enskilda filer. Detta säkerställer att stöd för att läsa mer komplicerad sätt att lagra parkettgolv på disk, till exempel mappen partitionering.

Skalbar körning beroende Sparks parkettgolv läsning av funktioner och stöd för enskilda filer och mappar, liknar lokal interaktiv användning.

#### <a name="options"></a>Alternativ
- Parkettgolv datauppsättning. Det här alternativet avgör om Azure Machine Learning förberedelse av data expanderar en viss katalog och försöker läsa varje fil individuellt (omarkerat läget), eller om det behandlar katalogen som hela datauppsättningen (valda läge). Med det markerade läget väljer PyArrow det bästa sättet att tolka filerna.


## <a name="locations"></a>Platser
### <a name="local"></a>Lokal
En lokal hårddisk eller en lagringsplats för mappade nätverksenheter.

### <a name="sql-server"></a>SQL Server
Lokal SQL Server, eller Azure SQL-databas.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob storage, vilket kräver en Azure-prenumeration.


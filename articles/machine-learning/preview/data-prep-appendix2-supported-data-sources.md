---
title: "Datakällor som är tillgängliga med Azure Machine Learning förberedelse av Data som stöds | Microsoft Docs"
description: "Det här dokumentet innehåller en fullständig lista över stöds datakällor för Azure Machine Learning förberedelse av Data"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 1ef4c5c33d98cfeb566e8fe23bda9e0d3f041781
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="supported-data-sources-for-this-release"></a>Stöds datakällor för den här versionen 
Följande dokument innehåller listan över datakällor som stöds för närvarande i Azure Machine Learning förberedelse av Data.

Stöds datakällorna för den här versionen är som följer.

## <a name="types"></a>Typer 
### <a name="directory-versus-file"></a>Directory jämfört med filen
*Filer och kataloger*: Välj en enda fil och läsa in förberedelse av Data. Filtypen analyseras för att fastställa standardparametrar för fil-anslutningen som finns på nästa skärm. Välj en katalog eller en uppsättning filer i en katalog (filväljaren är multiselect). Antingen metoden leder till att filerna som läses som ett enskilt dataflöde med de filer som läggs till varandra (med rubriker bort om det behövs).

Filtyperna är följande:
- Avgränsade (.csv, TSV, .txt och så vidare) 
- Fast bredd
- Oformaterad text
- JSON-fil

### <a name="csv-file"></a>CSV-fil
Läser en CSV-fil från lagringsplatsen.

#### <a name="options"></a>Alternativ
- avgränsare
- Kommentar
- Rubriker
- Decimaltecken
- Filkodning
- Rader ska hoppas över

### <a name="tsv-file"></a>TVS fil
Läser en fil för TVS värden från lagringsplatsen.

#### <a name="options"></a>Alternativ
- Kommentar
- Rubriker
- Filkodning
- Rader ska hoppas över

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Läser en Excel-fil, ett blad i taget, genom att ange namn eller en siffra.

#### <a name="options"></a>Alternativ
- Blad namn eller nummer
- Rubriker
- Rader ska hoppas över

### <a name="json-file"></a>JSON-fil
Läs en JSON-fil från lagringsplatsen. Observera att filen ”förenklas” vid läsning.

#### <a name="options"></a>Alternativ
Ingen

### <a name="parquet"></a>Parkettgolv
Läs en parkettgolv dataset, antingen en enskild fil eller mapp.

Parkettgolv som ett format som kan ha olika format i lagringen. En enda .parquet-fil används ibland för mindre datamängder. Olika Python-bibliotek stöd för läsning eller skrivning till enkel .parquet fil. För närvarande använder Azure Machine Learning arbetsstationen PyArrow Python-bibliotek för att läsa parkettgolv under lokala interaktiv användning. Den stöder enkel .parquet-filer (förutsatt att de har skrivits därför inte som en del av en större datamängd). Det stöder också parkettgolv datauppsättningar. 

En parkettgolv dataset är en samling av fler än en .parquet-fil, som representerar en mindre partition på en större datamängd. Datauppsättningar som vanligtvis finns i en mapp. De är standardformatet för parkettgolv utdata för vanliga plattformar, till exempel Spark och Hive.

>[!NOTE]
>När du läser parkettgolv data i en mapp med flera .parquet filer är det säkraste och välj katalogen för läsning och skalstreck den **parkettgolv Dataset** alternativet. Detta gör PyArrow läsa hela mappen i stället för enskilda filer. Detta säkerställer att stöd för att läsa mer komplicerad sätt att lagra parkettgolv på disken (till exempel mappen partitioneras.)

Skalbar körning beroende Sparks parkettgolv läsning av funktioner och stöd för enskilda filer och mappar.

#### <a name="options"></a>Alternativ
*Parkettgolv dataset*: det här alternativet avgör om Azure Machine Learning arbetsstationen använder den webbanvändarens eller ticked läge. Webbanvändarens läge utökar en viss katalog och försöker läsa varje fil i den individuellt. Ticked läge behandlar katalogen som hela datauppsättningen och låter PyArrow ta reda på det bästa sättet att tolka filerna.


## <a name="locations"></a>Platser
### <a name="local"></a>Lokal
Hårddisken eller mappade nätverksenheter lagringsplats.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Kräver en Azure-prenumeration.


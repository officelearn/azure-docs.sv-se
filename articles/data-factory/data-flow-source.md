---
title: Källomvandling vid mappning av dataflöde
description: Lär dig hur du ställer in en källomvandling vid mappning av dataflöde.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: e4f1369aa850a244128da470aee81b2efc2e09be
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413196"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Källomvandling vid mappning av dataflöde 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

En källomvandling konfigurerar datakällan för dataflödet. När du utformar dataflöden kommer ditt första steg alltid att konfigurera en källomvandling. Om du vill lägga till en källa klickar du på rutan **Lägg till källa** på dataflödesarbetsytan.

Varje dataflöde kräver minst en källomvandling, men du kan lägga till så många källor som behövs för att slutföra dina dataomvandlingar. Du kan gå med i dessa källor tillsammans med en koppling, sökning eller en unionstransformation.

Varje källomvandling är associerad med exakt en Data Factory-datauppsättning. Datauppsättningen definierar formen och platsen för de data som du vill skriva till eller läsa från. Om du använder en filbaserad datauppsättning kan du använda jokertecken och fillistor i källan för att arbeta med mer än en fil i taget.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>Källkopplingar som stöds vid mappning av dataflöde

Mapping Data Flow följer en extrahera, läsa in, transformera (ELT) metod och fungerar med *mellanlagringsdatauppsättningar* som alla finns i Azure. För närvarande kan följande datauppsättningar användas i en källomvandling:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Text, Parkett)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Text, Parkett)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Text, Parkett)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Inställningar som är specifika för dessa kopplingar finns på fliken **Källalternativ.** Information om dessa inställningar finns i anslutningsdokumentationen. 

Azure Data Factory har åtkomst till över [90 inbyggda kopplingar](connector-overview.md). Om du vill inkludera data från de andra källorna i dataflödet använder du kopieringsaktiviteten för att läsa in dessa data i ett av de mellanlagringsområden som stöds.

## <a name="source-settings"></a>Källinställningar

När du har lagt till en källa konfigurerar du via fliken **Källinställningar.** Här kan du välja eller skapa den datauppsättning din källpunkter på. Du kan också välja schema- och samplingsalternativ för dina data.

![Fliken Källinställningar](media/data-flow/source1.png "Fliken Källinställningar")

**Testanslutning:** Testa om dataflödets gnisttjänst kan ansluta till den länkade tjänst som används i källdatauppsättningen. Felsökningsläget måste vara aktiverat för att den här funktionen ska kunna aktiveras.

**Schemadrift:** [Schema Drift](concepts-data-flow-schema-drift.md) är datafabrikens möjlighet att internt hantera flexibla scheman i dina dataflöden utan att uttryckligen behöva definiera kolumnändringar.

* Markera rutan **Tillåt schemadrift** om källkolumnerna ändras ofta. Med den här inställningen kan alla inkommande källfält flöda genom omvandlingarna till diskhon.

* Om du väljer **Infer-bortreskade kolumntyper** instrueras datafabriken att identifiera och definiera datatyper för varje ny kolumn som upptäcks. När den här funktionen är inaktiverad kommer alla borttrakade kolumner att vara av typen sträng.

**Validera schema:** Om validera schema är markerat, kommer dataflödet misslyckas med att köras om den inkommande källdata inte matchar det definierade schemat för datauppsättningen.

**Hoppa över antal rader:** Fältet hoppa över radantal anger hur många rader som ska ignoreras i början av datauppsättningen.

**Provtagning:** Aktivera sampling för att begränsa antalet rader från källan. Använd den här inställningen när du testar eller exempeldata från källan för felsökning.

**Rader med flera rader:** Markera rader med flera rader om källtextfilen innehåller strängvärden som sträcker sig över flera rader, dvs. Den här inställningen är endast tillgänglig i AvimitedText-datauppsättningar.

Om du vill verifiera att källan har konfigurerats korrekt aktiverar du felsökningsläget och hämtar en förhandsgranskning av data. Mer information finns i [Felsökningsläge](concepts-data-flow-debug-mode.md).

> [!NOTE]
> När felsökningsläget är aktiverat skriver radgränskonfigurationen i felsökningsinställningar över samplingsinställningen i källan under förhandsgranskningen av data.

## <a name="projection"></a>Projektion

Precis som scheman i datauppsättningar definierar projektionen i en källa datakolumnerna, typerna och formaten från källdata. För de flesta datauppsättningstyper som SQL och Parkett är projektionen i en källa fast för att återspegla schemat som definierats i en datauppsättning. När källfilerna inte är starkt maskinskrivna (till exempel platta CSV-filer i stället för parettfiler) kan du definiera datatyperna för varje fält i källomvandlingen.

![Inställningar på fliken Projektion](media/data-flow/source3.png "Projektion")

Om textfilen inte har något definierat schema väljer du **Identifiera datatyp** så att Data Factory tar prov på och drar slutsatser om datatyperna. Välj **Definiera standardformat** om du vill automatiskt definiera standarddataformaten.

**Återställ schemat** återställer projektionen till vad som definieras i den refererade datauppsättningen.

Du kan ändra kolumndatatyperna i en nedströms härledd kolumnomvandling. Använd en markera omformning för att ändra kolumnnamnen.

### <a name="import-schema"></a>Importera schema

Med knappen **Importera schema** på fliken **Projektion** kan du använda ett aktivt felsökningskluster för att skapa en schemaprojektion. Om du importerar schemat här åsidosätts projektionen som definierats i datauppsättningen om du importerar schemat här. Datauppsättningsobjektet ändras inte.

Detta är användbart i datauppsättningar som Avro och CosmosDB som stöder komplexa datastrukturer inte kräver schemadefinitioner för att finnas i datauppsättningen.

## <a name="optimize-the-source-transformation"></a>Optimera källomvandlingen

På fliken **Optimera** för källomvandlingen kan du se en **källpartitionstyp.** Det här alternativet är bara tillgängligt när källan är Azure SQL Database. Detta beror på att Data Factory försöker göra anslutningar parallella för att köra stora frågor mot SQL Database-källan.

![Inställningar för källpartition](media/data-flow/sourcepart3.png "Partitionering")

Du behöver inte partitionera data på SQL Database-källan, men partitioner är användbara för stora frågor. Du kan basera partitionen på en kolumn eller en fråga.

### <a name="use-a-column-to-partition-data"></a>Använda en kolumn för att partitionera data

Välj en kolumn som du vill partitionera på i källtabellen. Ange också antalet partitioner.

### <a name="use-a-query-to-partition-data"></a>Använda en fråga för att partitionera data

Du kan välja att partitionera anslutningarna baserat på en fråga. Ange innehållet i ett WHERE-predikat. Ange till exempel år > 1980.

Mer information om optimering inom mappningsdataflödet finns på [fliken Optimera](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Nästa steg

Börja bygga en [härledd kolumnomvandling](data-flow-derived-column.md) och en [utvald omvandling](data-flow-select.md).

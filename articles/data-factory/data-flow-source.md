---
title: Käll omvandling i data flöde för mappning
description: Lär dig hur du konfigurerar en käll omvandling i mappnings data flödet.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 7a438a52ab69810ecf49319c148f817da974ea61
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440225"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Käll omvandling i data flöde för mappning 

En käll omvandling konfigurerar data källan för data flödet. När du skapar data flöden kommer ditt första steg alltid att konfigurera en käll omvandling. Om du vill lägga till en källa klickar du på rutan **Lägg till källa** i arbets ytan data flöde.

Varje data flöde kräver minst en käll omvandling, men du kan lägga till så många källor som behövs för att slutföra dina data transformationer. Du kan koppla ihop dessa källor med en JOIN-, lookup-eller union-omvandling.

Varje käll omvandling är associerad med exakt en Data Factory data uppsättning. Data uppsättningen definierar formen och platsen för de data som du vill skriva till eller läsa från. Om du använder en filbaserad data uppsättning kan du använda jokertecken och fil listor i din källa för att arbeta med fler än en fil i taget.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>Käll anslutningar som stöds i mappnings data flödet

Genom att mappa data flödet följer du metoden extrahera, läsa in, transformera (ELT) och arbetar med *mellanlagring* av data uppsättningar som är alla i Azure. För närvarande kan följande data uppsättningar användas i en käll omvandling:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Synapse-analys](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure-CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Inställningar som är aktuella för dessa anslutningar finns på fliken **käll alternativ** . information om de här inställningarna finns i anslutnings dokumentationen. 

Azure Data Factory har åtkomst till över [90 inbyggda anslutningar](connector-overview.md). Om du vill ta med data från de andra källorna i ditt data flöde använder du kopierings aktiviteten för att läsa in dessa data till något av de mellanliggande mellanlagrings områdena.

## <a name="source-settings"></a>Källinställningar

När du har lagt till en källa konfigurerar du via fliken **käll inställningar** . Här kan du välja eller skapa den data uppsättning som din käll pekar på. Du kan också välja schema-och samplings alternativ för dina data.

![Fliken käll inställningar](media/data-flow/source1.png "Fliken käll inställningar")

**Schema avvikelse:** [schema avvikelse](concepts-data-flow-schema-drift.md) är data fabriks möjlighet att hantera flexibla scheman i dina data flöden utan att uttryckligen definiera kolumn ändringar.

* Markera kryss rutan **Tillåt schema avvikelse** om käll kolumnerna ska ändras ofta. Med den här inställningen kan alla inkommande källfält flöda genom omvandlingarna till mottagaren.

* Om du väljer **härledda kolumn typer** instrueras Data Factory att identifiera och definiera data typer för varje ny kolumn som identifieras. När den här funktionen är inaktive rad blir alla nedsatta kolumner av typen sträng.

**Verifiera schema:** Om verifiera schema har valts kan data flödet inte köras om inkommande källdata inte matchar det definierade schemat för data uppsättningen.

**Hoppa över linje antal:** Fältet slopa rad antal anger hur många rader som ska ignoreras i början av data uppsättningen.

**Sampling:** Aktivera sampling för att begränsa antalet rader från källan. Använd den här inställningen när du testar eller samplar data från källan för fel sökning.

**Flera rader:** Välj flera rader om käll text filen innehåller sträng värden som sträcker sig över flera rader, d.v.s. newlines inuti ett värde. Den här inställningen är endast tillgänglig i DelimitedText-datauppsättningar.

Verifiera att källan är korrekt konfigurerad genom att aktivera fel söknings läge och hämta en data förhands granskning. Mer information finns i [fel söknings läge](concepts-data-flow-debug-mode.md).

> [!NOTE]
> När fel söknings läget är aktiverat skriver rad begränsnings konfigurationen i fel söknings inställningarna över insamlings inställningen i källan under för hands versionen av data.

## <a name="projection"></a>Projektion

Som scheman i data uppsättningar definierar projektionen i en källa data kolumnerna, typerna och formaten från käll data. För de flesta typer av data uppsättningar, till exempel SQL och Parquet, åtgärdas projektionen i en källa för att återspegla det schema som definierats i en data uppsättning. När källfilerna inte är starkt skrivna (till exempel enkla CSV-filer i stället för Parquet-filer) kan du definiera data typerna för varje fält i käll omvandlingen.

![Inställningar på fliken projektion](media/data-flow/source3.png "Projektion")

Om text filen inte har något definierat schema väljer du **identifiera data typ** så att Data Factory kan sampla och härleda data typerna. Välj **definiera standardformat** för att automatiskt identifiera standard data formaten. 

Du kan ändra kolumn data typerna i en nedströms härledd kolumn-omvandling. Använd en SELECT-omvandling för att ändra kolumn namnen.

### <a name="import-schema"></a>Importera schema

Data uppsättningar som Avro och CosmosDB som stöder komplexa data strukturer kräver inte att schema definitioner finns i data uppsättningen. Därför kan du klicka på knappen **Importera schema** på fliken **projektion** för dessa typer av källor.

## <a name="optimize-the-source-transformation"></a>Optimera käll omvandlingen

På fliken **optimera** för käll omvandling kan du se en typ av **Källtyp** . Det här alternativet är bara tillgängligt när din källa är Azure SQL Database. Detta beror på att Data Factory försöker göra anslutningar parallella för att köra stora frågor mot din SQL Database källa.

![Käll partitions inställningar](media/data-flow/sourcepart3.png "partitionering")

Du behöver inte partitionera data på din SQL Database källa, men partitioner är användbara för stora frågor. Du kan basera partitionen på en kolumn eller en fråga.

### <a name="use-a-column-to-partition-data"></a>Använda en kolumn för att partitionera data

Välj en kolumn att partitionera på i käll tabellen. Ange också antalet partitioner.

### <a name="use-a-query-to-partition-data"></a>Använd en fråga för att partitionera data

Du kan välja att partitionera anslutningarna baserat på en fråga. Ange innehållet i ett WHERE-predikat. Ange till exempel Year > 1980.

Mer information om optimering i data flödet för mappning finns på [fliken optimera](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Nästa steg

Börja skapa en [omvandling med härledd kolumn](data-flow-derived-column.md) och en [Välj omvandling](data-flow-select.md).

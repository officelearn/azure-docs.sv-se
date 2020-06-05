---
title: Käll omvandling i data flöde för mappning
description: Lär dig hur du konfigurerar en käll omvandling i mappnings data flödet.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/03/2020
ms.openlocfilehash: 6da0c56e11b8531192ba77d8f0c27fa16eea5de2
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433301"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Käll omvandling i data flöde för mappning 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En käll omvandling konfigurerar data källan för data flödet. När du skapar data flöden kommer ditt första steg alltid att konfigurera en käll omvandling. Om du vill lägga till en källa klickar du på rutan **Lägg till källa** i arbets ytan data flöde.

Varje data flöde kräver minst en käll omvandling, men du kan lägga till så många källor som behövs för att slutföra dina data transformationer. Du kan koppla ihop dessa källor med en JOIN-, lookup-eller union-omvandling.

Varje käll omvandling är associerad med exakt en Data Factory data uppsättning. Data uppsättningen definierar formen och platsen för de data som du vill skriva till eller läsa från. Om du använder en filbaserad data uppsättning kan du använda jokertecken och fil listor i din källa för att arbeta med fler än en fil i taget.

## <a name="inline-datasets"></a>Infogade data uppsättningar

Det första beslut du fattar när du skapar en käll omvandling är om din käll information definieras i ett DataSet-objekt eller i käll omvandlingen. De flesta format är bara tillgängliga i den ena eller den andra. Referera till lämpligt kopplings dokument för att lära dig hur du använder en speciell anslutning.

När ett format stöds för både infogade och i ett data uppsättnings objekt, finns det fördelar med båda. Data uppsättnings objekt är återanvändbara entiteter som kan utnyttjas i andra data flöden och aktiviteter som kopiering. Dessa är särskilt användbara när du använder ett strikt schema. Data uppsättningar är inte baserade i Spark och ibland kan du behöva åsidosätta vissa inställningar eller schema projektion i käll omvandlingen.

Infogade data uppsättningar rekommenderas när du använder flexibla scheman, ensidiga käll instanser eller parameter källor. Om källan är mycket parametriserad kan du använda data uppsättningar i rad för att inte skapa ett "dummy"-objekt. Infogade data uppsättningar baseras i Spark och deras egenskaper är inbyggda i data flödet.

Om du vill använda en infogad data uppsättning väljer du det önskade formatet i väljaren för **käll typ** . I stället för att välja en käll data uppsättning väljer du den länkade tjänst som du vill ansluta till.

![Infogad data uppsättning](media/data-flow/inline-selector.png "Infogad data uppsättning")

### <a name="supported-inline-dataset-formats"></a>Infogade data uppsättnings format som stöds

För närvarande är den enda tillgängliga uppsättningen med data uppsättnings format den [gemensamma data modellen](format-common-data-model.md#source-properties) läst från [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md).

## <a name="supported-source-datasets-in-mapping-data-flow"></a>Käll data uppsättningar som stöds i mappnings data flödet

Genom att mappa data flödet följer du metoden extrahera, läsa in, transformera (ELT) och arbetar med *mellanlagring* av data uppsättningar som är alla i Azure. För närvarande kan följande data uppsättningar användas i en käll omvandling:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure-CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Inställningar som är aktuella för dessa anslutningar finns på fliken **käll alternativ** . information om de här inställningarna finns i anslutnings dokumentationen. 

Azure Data Factory har åtkomst till över [90 inbyggda anslutningsprogram](connector-overview.md). Om du vill ta med data från de andra källorna i ditt data flöde använder du kopierings aktiviteten för att läsa in dessa data till något av de mellanliggande mellanlagrings områdena.

## <a name="source-settings"></a>Källinställningar

När du har lagt till en källa konfigurerar du via fliken **käll inställningar** . Här kan du välja eller skapa den data uppsättning som din käll pekar på. Du kan också välja schema-och samplings alternativ för dina data.

![Fliken käll inställningar](media/data-flow/source1.png "Fliken käll inställningar")

**Testa anslutning:** Testa om data flödet Spark-tjänsten kan ansluta till den länkade tjänsten som används i din käll data uppsättning. Fel söknings läge måste vara aktiverat för att den här funktionen ska vara aktive rad.

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

**Återställ schema** återställer projektionen till det som definieras i den refererade data uppsättningen.

Du kan ändra kolumn data typerna i en nedströms härledd kolumn-omvandling. Använd en SELECT-omvandling för att ändra kolumn namnen.

### <a name="import-schema"></a>Importera schema

Knappen **Importera schema** på fliken **projektion** gör att du kan använda ett aktivt fel söknings kluster för att skapa en schema projektion. När du importerar schemat i varje källtyp åsidosätts den projektion som definierats i data uppsättningen. Dataset-objektet kommer inte att ändras.

Detta är användbart i data uppsättningar som Avro och CosmosDB som stöder komplexa data strukturer kräver inte att schema definitioner finns i data uppsättningen.

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

---
title: Omvandling av mottagare i data flöde för mappning
description: Lär dig hur du konfigurerar en Sink-omvandling i mappnings data flödet.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 1c65a456270cdca345504c07b927a7ef7e1f725b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440269"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Omvandling av mottagare i data flöde för mappning

När du har transformerat dina data kan du sinka data till en mål data uppsättning. Varje data flöde kräver minst en Sink-omvandling, men du kan skriva till så många handfat som behövs för att slutföra ditt omvandlings flöde. Om du vill skriva till ytterligare mottagare skapar du nya strömmar via nya grenar och villkorliga delningar.

Varje Sink-omvandling är associerad med exakt en Data Factory data uppsättning. Data uppsättningen definierar formen och platsen för de data som du vill skriva till.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Mottagar kopplingar som stöds i mappnings data flödet

För närvarande kan följande data uppsättningar användas i en Sink-omvandling:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Synapse-analys](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure-CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Inställningar som är aktuella för dessa anslutningar finns på fliken **Inställningar** . information om de här inställningarna finns i anslutnings dokumentationen. 

Azure Data Factory har åtkomst till över [90 inbyggda anslutningar](connector-overview.md). Om du vill skriva data till de andra källorna från ditt data flöde kan du använda kopierings aktiviteten för att läsa in data från ett av de mellanliggande mellanliggande områdena när ditt data flöde har slutförts.

## <a name="sink-settings"></a>Mottagar inställningar

När du har lagt till en mottagare konfigurerar du via fliken **mottagare** . Här kan du välja eller skapa data uppsättningen som din Sink skriver till 

![Mottagar inställningar](media/data-flow/sink-settings.png "Mottagar inställningar")

**Schema avvikelse:** [schema avvikelse](concepts-data-flow-schema-drift.md) är data fabriks möjlighet att hantera flexibla scheman i dina data flöden utan att uttryckligen definiera kolumn ändringar. Aktivera **Tillåt schema avvikelse** för att skriva ytterligare kolumner ovanpå vad som definieras i Datasink-dataschemat.

**Verifiera schema:** Om verifiera schema väljs, Miss kommer data flödet om någon kolumn i det definierade schemat för data uppsättningen inte hittas.

## <a name="field-mapping"></a>Fältmappning

På samma sätt som i en SELECT-omvandling, på fliken **mappning** för mottagaren, kan du bestämma vilka inkommande kolumner som ska skrivas. Som standard mappas alla inmatade kolumner, inklusive ingångna kolumner. Detta kallas för **automatisk mappning**.

När du inaktiverar automatisk mappning har du möjlighet att lägga till antingen fasta kolumnbaserade mappningar eller regelbaserade mappningar. Med hjälp av regelbaserade mappningar kan du skriva uttryck med mönster matchning medan en fast mappning mappar logiska och fysiska kolumn namn. Mer information om regelbaserade mappning finns [i kolumn mönster i mappa data flöde](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="data-preview-in-sink"></a>Förhands granskning av data i mottagare

När du hämtar en data förhands granskning i ett fel söknings kluster kommer inga data att skrivas till din mottagare. En ögonblicks bild av hur data ser ut kommer att returneras, men inget skrivs till ditt mål. Om du vill testa att skriva data i din mottagare kan du köra en pipeline-felsökning från pipeline-arbetsytan.

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ditt data flöde lägger du till en [data flödes aktivitet i din pipeline](concepts-data-flow-overview.md).

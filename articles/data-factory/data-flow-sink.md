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
ms.date: 06/03/2020
ms.openlocfilehash: 2c57ddd88046044cccd13b0ade23144cd5649455
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433313"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Omvandling av mottagare i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

När du har transformerat dina data kan du sinka data till en mål data uppsättning. Varje data flöde kräver minst en Sink-omvandling, men du kan skriva till så många handfat som behövs för att slutföra ditt omvandlings flöde. Om du vill skriva till ytterligare mottagare skapar du nya strömmar via nya grenar och villkorliga delningar.

Varje Sink-omvandling är associerad med exakt en Data Factory data uppsättning. Data uppsättningen definierar formen och platsen för de data som du vill skriva till.

## <a name="inline-datasets"></a>Infogade data uppsättningar

När du skapar en Sink-omvandling väljer du om mottagar informationen definieras i ett DataSet-objekt eller i omvandling av mottagare. De flesta format är bara tillgängliga i den ena eller den andra. Referera till lämpligt kopplings dokument för att lära dig hur du använder en speciell anslutning.

När ett format stöds för både infogade och i ett data uppsättnings objekt, finns det fördelar med båda. Data uppsättnings objekt är återanvändbara entiteter som kan utnyttjas i andra data flöden och aktiviteter som kopiering. Dessa är särskilt användbara när du använder ett strikt schema. Data uppsättningar är inte baserade i Spark och ibland kan du behöva åsidosätta vissa inställningar eller schema projektion i Sink-omvandlingen.

Infogade data uppsättningar rekommenderas när du använder flexibla scheman, ett-off-Sink-instanser eller parameterstyrda mottagare. Om din mottagare är mycket parametriserad kan du inte skapa ett "dummy"-objekt i data uppsättningar i rad. Infogade data uppsättningar baseras i Spark och deras egenskaper är inbyggda i data flödet.

Om du vill använda en infogad data uppsättning väljer du önskat format i typ väljaren för **mottagare** . I stället för att välja en data uppsättning för mottagare väljer du den länkade tjänst som du vill ansluta till.

![Infogad data uppsättning](media/data-flow/inline-selector.png "Infogad data uppsättning")

### <a name="supported-inline-dataset-formats"></a>Infogade data uppsättnings format som stöds

För närvarande är den enda tillgängliga uppsättningen med data uppsättnings format den [gemensamma data modellen](format-common-data-model.md#sink-properties) läst från [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md).

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Mottagar kopplingar som stöds i mappnings data flödet

För närvarande kan följande data uppsättningar användas i en Sink-omvandling:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, text, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure-CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Inställningar som är aktuella för dessa anslutningar finns på fliken **Inställningar** . information om de här inställningarna finns i anslutnings dokumentationen. 

Azure Data Factory har åtkomst till över [90 inbyggda anslutningsprogram](connector-overview.md). Om du vill skriva data till de andra kopplingarna från ditt data flöde använder du kopierings aktiviteten för att läsa in data från ett av de mellanliggande mellanliggande områdena när ditt data flöde har slutförts.

## <a name="sink-settings"></a>Mottagar inställningar

När du har lagt till en mottagare konfigurerar du via fliken **mottagare** . Här kan du välja eller skapa den data uppsättning som din Sink skriver till. Nedan visas en video som förklarar ett antal olika Sink-alternativ för text avgränsade filtyper:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tf7T]

![Mottagar inställningar](media/data-flow/sink-settings.png "Mottagar inställningar")

**Schema avvikelse:** [schema avvikelse](concepts-data-flow-schema-drift.md) är data fabriks möjlighet att hantera flexibla scheman i dina data flöden utan att uttryckligen definiera kolumn ändringar. Aktivera **Tillåt schema avvikelse** för att skriva ytterligare kolumner ovanpå vad som definieras i Datasink-dataschemat.

**Verifiera schema:** Om verifiera schema väljs, Miss kommer data flödet om någon kolumn i det inkommande käll schemat inte hittas i käll projektionen, eller om data typerna inte matchar. Använd den här inställningen för att genomdriva att källdata uppfyller kontraktet för din definierade projektion. Det är mycket användbart i scenarier med databas källan för att signalera att kolumn namn eller typer har ändrats.

## <a name="field-mapping"></a>Fältmappning

På samma sätt som i en SELECT-omvandling, på fliken **mappning** för mottagaren, kan du bestämma vilka inkommande kolumner som ska skrivas. Som standard mappas alla inmatade kolumner, inklusive ingångna kolumner. Detta kallas för **automatisk mappning**.

När du inaktiverar automatisk mappning har du möjlighet att lägga till antingen fasta kolumnbaserade mappningar eller regelbaserade mappningar. Med hjälp av regelbaserade mappningar kan du skriva uttryck med mönster matchning medan en fast mappning mappar logiska och fysiska kolumn namn. Mer information om regelbaserade mappning finns [i kolumn mönster i mappa data flöde](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Beställning av anpassad mottagare

Som standard skrivs data till flera handfat i en icke deterministisk ordning. Körnings motorn skriver data parallellt eftersom omvandlings logiken har slutförts och mottagar ordningen kan variera varje gång. Om du vill ange och exakt den exakta Sink-ordningen aktiverar du **anpassad sortering av mottagare** på fliken Allmänt i data flödet. När den är aktive rad skrivs Sinks sekventiellt i stigande ordning.

![Beställning av anpassad mottagare](media/data-flow/custom-sink-ordering.png "Beställning av anpassad mottagare")

## <a name="data-preview-in-sink"></a>Förhands granskning av data i mottagare

När du hämtar en data förhands granskning i ett fel söknings kluster kommer inga data att skrivas till din mottagare. En ögonblicks bild av hur data ser ut kommer att returneras, men inget skrivs till ditt mål. Om du vill testa att skriva data i din mottagare kan du köra en pipeline-felsökning från pipeline-arbetsytan.

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ditt data flöde lägger du till en [data flödes aktivitet i din pipeline](concepts-data-flow-overview.md).

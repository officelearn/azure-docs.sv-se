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
ms.date: 09/27/2020
ms.openlocfilehash: 06d70012756694dca1fad8fa90db0293bb106bf9
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828138"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Omvandling av mottagare i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

När du har slutfört transformeringen av dina data skriver du den i ett mål lager med hjälp av Sink-omvandlingen. Varje data flöde kräver minst en Sink-omvandling, men du kan skriva till så många handfat som behövs för att slutföra ditt omvandlings flöde. Om du vill skriva till ytterligare mottagare skapar du nya strömmar via nya grenar och villkorliga delningar.

Varje Sink-omvandling är associerad med exakt ett Azure Data Factory DataSet-objekt eller en länkad tjänst. Omvandlingen av mottagare bestämmer formen och platsen för de data som du vill skriva till.

## <a name="inline-datasets"></a>Infogade data uppsättningar

När du skapar en Sink-omvandling väljer du om mottagar informationen definieras i ett DataSet-objekt eller i omvandling av mottagare. De flesta format är bara tillgängliga i den ena eller den andra. Referera till lämpligt kopplings dokument för att lära dig hur du använder en speciell anslutning.

När ett format stöds för både infogade och i ett data uppsättnings objekt, finns det fördelar med båda. Data uppsättnings objekt är återanvändbara entiteter som kan utnyttjas i andra data flöden och aktiviteter som kopiering. Dessa är särskilt användbara när du använder ett strikt schema. Data uppsättningar är inte baserade i Spark och ibland kan du behöva åsidosätta vissa inställningar eller schema projektion i Sink-omvandlingen.

Infogade data uppsättningar rekommenderas när du använder flexibla scheman, ett-off-Sink-instanser eller parameterstyrda mottagare. Om din mottagare är mycket parametriserad kan du inte skapa ett "dummy"-objekt i data uppsättningar i rad. Infogade data uppsättningar baseras i Spark och deras egenskaper är inbyggda i data flödet.

Om du vill använda en infogad data uppsättning väljer du önskat format i typ väljaren för **mottagare** . I stället för att välja en data uppsättning för mottagare väljer du den länkade tjänst som du vill ansluta till.

![Infogad data uppsättning](media/data-flow/inline-selector.png "Infogad data uppsättning")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Mottagar typer som stöds

Genom att mappa data flödet följer du metoden extrahera, läsa in, transformera (ELT) och arbetar med *mellanlagring* av data uppsättningar som är alla i Azure. För närvarande kan följande data uppsättningar användas i en käll omvandling:

| Anslutningsprogram | Format | Data uppsättning/intern |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Avgränsad text](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (för hands version)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Avgränsad text](format-delimited-text.md#mapping-data-flow-properties) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Avgränsad text](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (för hands version)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Common data Model (för hands version)](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure-CosmosDB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Inställningar som är aktuella för dessa anslutningar finns på fliken **Inställningar** . Skript exempel för information och data flöde i dessa inställningar finns i anslutnings dokumentationen. 

Azure Data Factory har åtkomst till över [90 inbyggda anslutningsprogram](connector-overview.md). Om du vill skriva data till de andra källorna från ditt data flöde använder du kopierings aktiviteten för att läsa in data från en mottagare som stöds.

## <a name="sink-settings"></a>Mottagar inställningar

När du har lagt till en mottagare konfigurerar du via fliken **mottagare** . Här kan du välja eller skapa den data uppsättning som din Sink skriver till. Utvecklings värden för data uppsättnings parametrar kan konfigureras i [fel söknings inställningar](concepts-data-flow-debug-mode.md) (du måste aktivera fel söknings läge).

Nedan visas en video som förklarar ett antal olika Sink-alternativ för text avgränsade filtyper:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

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

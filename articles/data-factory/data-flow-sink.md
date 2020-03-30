---
title: Sink-omvandling vid mappning av dataflöde
description: Lär dig hur du konfigurerar en sink-omformning vid mappning av dataflöde.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: a0b9d424c1995fba075c05ffe5058e297d764775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531268"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Sink-omvandling vid mappning av dataflöde

När du har omvandlat dina data kan du sänka data till en måldatauppsättning. Varje dataflöde kräver minst en sink-omformning, men du kan skriva till så många sänkor som behövs för att slutföra omvandlingsflödet. Om du vill skriva till ytterligare sänkor skapar du nya strömmar via nya grenar och villkorsstyrda delningar.

Varje sinkomvandling är associerad med exakt en Data Factory-datauppsättning. Datauppsättningen definierar formen och platsen för de data som du vill skriva till.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Sink-kopplingar som stöds vid mappning av dataflöde som stöds

För närvarande kan följande datauppsättningar användas i en sink transformation:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Text, Parkett)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Text, Parkett)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Text, Parkett)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Inställningar som är specifika för dessa kopplingar finns på fliken **Inställningar.** Information om dessa inställningar finns i anslutningsdokumentationen. 

Azure Data Factory har åtkomst till över [90 inbyggda kopplingar](connector-overview.md). Om du vill skriva data till de andra källorna från dataflödet använder du kopieringsaktiviteten för att läsa in dessa data från ett av de mellanlagringsområden som stöds efter att dataflödet har slutförts.

## <a name="sink-settings"></a>Sink-inställningar

När du har lagt till en diskbänk konfigurerar du via fliken **Sink.** Här kan du välja eller skapa datauppsättningen som diskbänken skriver till 

![Sink-inställningar](media/data-flow/sink-settings.png "Inställningar för diskbänk")

**Schemadrift:** [Schema Drift](concepts-data-flow-schema-drift.md) är datafabrikens möjlighet att internt hantera flexibla scheman i dina dataflöden utan att uttryckligen behöva definiera kolumnändringar. Aktivera **Tillåt schemaavdrift** för att skriva ytterligare kolumner ovanpå vad som definieras i sink-dataschemat.

**Validera schema:** Om validera schema är markerat misslyckas dataflödet om någon kolumn i det inkommande källschemat inte hittas i källprojektionen, eller om datatyperna inte matchar. Använd den här inställningen om du vill framtvinga att källdata uppfyller kontraktet för den definierade projektionen. Det är mycket användbart i databaskälla scenarier för att signalera att kolumnnamn eller typer har ändrats.

## <a name="field-mapping"></a>Fältmappning

I likhet med en Select-omformning kan du bestämma vilka inkommande kolumner som ska skrivas på fliken **Mappning** i diskhon. Som standard mappas alla indatakolumner, inklusive borttrollade kolumner. Detta kallas **Automatisk mappning**.

När du inaktiverar automatisk mappning kan du välja att lägga till antingen fasta kolumnbaserade mappningar eller regelbaserade mappningar. Med regelbaserade mappningar kan du skriva uttryck med mönstermatchning medan den fasta mappningen kartlägger logiska och fysiska kolumnnamn. Mer information om regelbaserad mappning finns [i kolumnmönster i mappning av dataflöde](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Anpassad sinkordning

Som standard skrivs data till flera sänkor i en icke-deterministisk ordning. Körningsmotorn skriver data parallellt när omvandlingslogiken har slutförts och sinkordningen kan variera varje körning. Om du vill ange och exakt sinkordning aktiverar du **Anpassad sinkordning** på fliken Allmänt i dataflödet. När det är aktiverat skrivs sänkor sekventiellt i ökande ordning.

![Anpassad sinkordning](media/data-flow/custom-sink-ordering.png "Anpassad sinkordning")

## <a name="data-preview-in-sink"></a>Förhandsgranskning av data i diskbänk

När du hämtar en dataförhandsvisning i ett felsökningskluster skrivs inga data till diskhon. En ögonblicksbild av hur data ser ut kommer att returneras, men ingenting kommer att skrivas till din destination. Om du vill testa att skriva data i diskhon kör du en pipeline-felsökning från pipeline-arbetsytan.

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat dataflödet lägger du till en [dataflödesaktivitet i pipelinen](concepts-data-flow-overview.md).

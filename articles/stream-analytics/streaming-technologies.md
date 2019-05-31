---
title: Välj en analys i realtid och streaming bearbetningsteknik i Azure
description: Läs mer om hur du väljer rätt realtidsanalys och bearbetning av strömningsteknik att skapa ditt program på Azure.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 85d6ed80da93f90e6dc0feaee7081ee3f36f1bf9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242696"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Välj en analys i realtid och streaming bearbetningsteknik i Azure

Det finns flera tjänster som är tillgängliga för analys i realtid och strömmande bearbetning på Azure. Den här artikeln innehåller information som du måste bestämma vilken teknik som är bäst för ditt program.

## <a name="when-to-use-azure-stream-analytics"></a>När du använder Azure Stream Analytics

Azure Stream Analytics är den rekommendera tjänsten för stream analytics på Azure. Den är avsedd för en mängd olika scenarier som inkluderar men inte begränsat till:

* Instrumentpaneler för datavisualisering
* I realtid [aviseringar](stream-analytics-set-up-alerts.md) från temporal och spatial mönster eller avvikelser
* Extrahera, transformera, läsa in (ETL)
* [Mönstret händelsekällor](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Att lägga till ett Azure Stream Analytics jobb till ditt program är det snabbaste sättet att få streaming analytics upp och som körs i Azure med SQL-språk du redan kan. Azure Stream Analytics är en tjänst, så att du inte behöver lägga ned tid hantera kluster och du inte behöver bekymra dig om driftstopp med ett serviceavtal på 99,9% på jobbnivån. Debiteringen görs också på jobbnivån att start-kostnader med låg (en Streaming Unit), men skalbar (upp till 192 enheter för strömning). Det är mycket mer kostnadseffektivt att köra några Stream Analytics-jobb än att kör och underhåller ett kluster.

Azure Stream Analytics har en omfattande produkten. Du kan omedelbart dra nytta av följande funktioner utan ytterligare inställningar:

* Temporala operatorer är inbyggda, till exempel [fönsteraggregeringar](stream-analytics-window-functions.md), temporal kopplingar och temporala analysfunktioner.
* Intern Azure [inkommande](stream-analytics-add-inputs.md) och [utdata](stream-analytics-define-outputs.md) nätverkskort
* Stöd för långsam ändra [referensdata](stream-analytics-use-reference-data.md) (även kallat en uppslagstabeller), inklusive sammankoppling med geospatiala referensdata för geofencing.
* Integrerade lösningar, till exempel [Avvikelseidentifiering](stream-analytics-machine-learning-anomaly-detection.md)
* Flera tidsfönster i samma fråga
* Möjlighet att skapa flera temporala operatorer i godtyckliga sekvenser.
* Under 100 ms inklusive svarstid slutpunkt till slutpunkt från indata anländer till Event Hubs, att mata ut startsida i Event Hubs, nätverk fördröjningen från och till Event Hubs med varaktigt högt dataflöde

## <a name="when-to-use-other-technologies"></a>När du ska använda andra tekniker

### <a name="you-need-to-input-from-or-output-to-kafka"></a>Du behöver indata från eller utdata till Kafka

Azure Stream Analytics inte har ett Apache Kafka-indata eller utdata nätverkskort. Om du har händelser som hamnar i eller skicka till Kafka och du inte har ett krav att köra dina egna Kafka-kluster, kan du fortsätta att använda Stream Analytics genom att skicka händelser till Event Hubs med Event Hubs Kafka-API: N utan att ändra avsändaren händelse. Om du behöver att köra dina egna Kafka-kluster, kan du använda Spark Structured Streaming, som har fullt stöd för [Azure Databricks](../azure-databricks/index.yml), eller för Storm på [Azure HDInsight](../hdinsight/storm/apache-storm-tutorial-get-started-linux.md).

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Vill du skriva UDF och UDA anpassade deserializers i ett annat språk än JavaScript ellerC#

Azure Stream Analytics stöder användardefinierade funktioner (UDF) eller användardefinierade aggregeringar (UDA) i JavaScript för molnjobb och C# för IoT Edge-jobb. C#användardefinierade deserializers stöds också. Om du vill implementera en deserialiserare, en UDF eller en UDA på andra språk, till exempel Java eller Python, kan du använda Spark Structured Streaming. Du kan också köra Event Hubs **EventProcessorHost** på dina egna virtuella datorer att göra godtyckliga strömmande bearbetning.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Din lösning är i en miljö med flera moln eller på plats

Azure Stream Analytics är Microsofts upphovsrättsskyddad teknik och är bara tillgängligt på Azure. Om du behöver din lösning kan flyttas mellan moln eller på plats kan du tekniker för öppen källkod som Spark Structured Streaming eller Storm.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
* [Skapa ett Stream Analytics-jobb med hjälp av Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Skapa ett Stream Analytics-jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md)
* [Skapa ett Stream Analytics-jobb med hjälp av Visual Studio Code](quick-create-vs-code.md)
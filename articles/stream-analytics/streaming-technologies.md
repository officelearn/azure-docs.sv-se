---
title: Välj en bearbetningslösning i realtid och stream på Azure
description: Lär dig mer om hur du väljer rätt teknik för analys- och strömning i realtid för att skapa ditt program på Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 2146b1bd782aba5d98729a2d37d956744e469ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75860256"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Välj en teknik för analys- och streamingbearbetning i realtid på Azure

Det finns flera tjänster tillgängliga för analys- och direktuppspelningsbearbetning i realtid på Azure. Den här artikeln innehåller den information du behöver för att avgöra vilken teknik som passar bäst för ditt program.

## <a name="when-to-use-azure-stream-analytics"></a>När ska Azure Stream Analytics användas

Azure Stream Analytics är den rekommenderade tjänsten för dataflödesanalys på Azure. Den är avsedd för en mängd olika scenarier som inkluderar men inte är begränsade till:

* Instrumentpaneler för datavisualisering
* Varningar i realtid från [tidsmässiga](stream-analytics-set-up-alerts.md) och rumsliga mönster eller avvikelser
* Extrahera, transformera, läsa in (ETL)
* [Mönster för händelseinköp](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Att lägga till ett Azure Stream Analytics-jobb i ditt program är det snabbaste sättet att få igång direktuppspelningsanalys i Azure med det SQL-språk du redan känner till. Azure Stream Analytics är en jobbtjänst, så du behöver inte spendera tid på att hantera kluster och du behöver inte oroa dig för driftstopp med ett serviceavtal på 99,9 % på jobbnivå. Fakturering sker också på jobbnivå vilket gör startkostnaderna låga (en streamingenhet), men skalbar (upp till 192 streamingenheter). Det är mycket mer kostnadseffektivt att köra några Stream Analytics-jobb än att köra och underhålla ett kluster.

Azure Stream Analytics har en omfattande out-of-the-box-upplevelse. Du kan omedelbart dra nytta av följande funktioner utan ytterligare inställningar:

* Inbyggda temporaloperatorer, till exempel [fönsterade aggregat,](stream-analytics-window-functions.md)temporala kopplingar och temporala analytiska funktioner.
* Inbyggda [Azure-in-](stream-analytics-add-inputs.md) och [utdatakort](stream-analytics-define-outputs.md)
* Stöd för långsamma [referensdata](stream-analytics-use-reference-data.md) (kallas även uppslagstabeller), inklusive att gå med geospatiala referensdata för geofencing.
* Integrerade lösningar, till exempel [avvikelseidentifiering](stream-analytics-machine-learning-anomaly-detection.md)
* Flera tidsfönster i samma fråga
* Möjlighet att komponera flera temporala operatorer i godtyckliga sekvenser.
* Under svarstid från 100 ms från slutpunkt till slutpunkt från indata som anländer till eventhubbar till utdatalandning i eventhubbar, inklusive nätverksförströjningen från och till eventhubbar, med ihållande högt dataflöde

## <a name="when-to-use-other-technologies"></a>När ska andra tekniker användas

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Du vill skriva UDF: er, UDAs och anpassade deserializers på ett annat språk än JavaScript eller C #

Azure Stream Analytics stöder användardefinierade funktioner (UDF) eller användardefinierade aggregat (UDA) i JavaScript för molnjobb och C# för IoT Edge-jobb. C# användardefinierade deserializers stöds också. Om du vill implementera en deserializer, en UDF eller en UDA på andra språk, till exempel Java eller Python, kan du använda Spark Structured Streaming. Du kan också köra Event Hubs **EventProcessorHost** på dina egna virtuella datorer för att göra godtycklig strömningsbearbetning.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Din lösning finns i en miljö med flera moln eller lokalt

Azure Stream Analytics är Microsofts egenutvecklade teknik och är endast tillgänglig på Azure. Om du behöver din lösning för att vara bärbar över moln eller lokalt bör du överväga tekniker med öppen källkod, till exempel Spark Structured Streaming eller Storm.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
* [Skapa ett Stream Analytics-jobb med hjälp av Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Skapa ett Stream Analytics-jobb med Visual Studio](stream-analytics-quick-create-vs.md)
* [Skapa ett Stream Analytics-jobb med Visual Studio-kod](quick-create-vs-code.md)

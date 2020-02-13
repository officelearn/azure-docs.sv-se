---
title: Data inmatning för Azure Datautforskaren
description: Lär dig mer om olika sätt att mata in data i Azure Datautforskaren
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 54401d4fd8a911fc9cf5bc67d4324e84005fa752
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157252"
---
# <a name="azure-data-explorer-data-ingestion"></a>Data inmatning för Azure Datautforskaren

Data inmatning är den process som används för att läsa in data poster från en eller flera källor för att skapa eller uppdatera en tabell i Azure Datautforskaren. Data blir tillgängliga för fråga när de har matats in. Diagrammet nedan visar flödet från slut punkt till slut punkt för arbete i Azure Datautforskaren, inklusive data inmatning.

![Dataflöde](media/ingest-data-overview/data-flow.png)

Tjänsten Azure Datautforskaren data management, som är ansvarig för data inmatning, innehåller följande funktioner:

1. **Data hämtning**: Hämta data från externa källor (Event Hubs) eller begäran om läsning av begäran från en Azure-kö.

1. **Batching**: batch-data flödar till samma databas och tabell för att optimera inmatnings data flödet.

1. **Verifiering**: preliminär verifiering och format konvertering vid behov.

1. **Datamanipulering**: matcha schema, organisera, indexera, koda och komprimera data.

1. **Beständig punkt i**inmatnings flödet: hantera inmatnings belastning på motorn och hantera återförsök vid tillfälliga haverier.

1. **Genomför data inmatningen**: gör data tillgängliga för fråga.

## <a name="ingestion-methods"></a>Inmatnings metoder

Azure Datautforskaren stöder flera inmatnings metoder, var och en med egna mål scenarier, fördelar och nack delar. Azure Datautforskaren erbjuder pipelines och kopplingar till vanliga tjänster, programmerings inmatning med SDK: er och direkt åtkomst till motorn för utforsknings syfte.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Inmatning med pipelines, kopplingar och plugin-program

Azure Datautforskaren stöder för närvarande:

* Event Grid pipeline, som kan hanteras med hjälp av hanterings guiden i Azure Portal. Mer information finns i mata [in Azure-blobar i azure datautforskaren](ingest-data-event-grid.md).

* Pipeline för Event Hub, som kan hanteras med hjälp av hanterings guiden i Azure Portal. Mer information finns i mata [in data från händelsehubben till Azure datautforskaren](ingest-data-event-hub.md).

* Logstash-plugin-programmet finns i mata in [data från Logstash till Azure datautforskaren](ingest-data-logstash.md).

* Kafka Connector finns i mata [in data från Kafka till Azure datautforskaren](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Inmatning med integrerings tjänster

* Azure Data Factory (ADF), en fullständigt hanterad data integrerings tjänst för analytiska arbets belastningar i Azure, för att kopiera data till och från Azure Datautforskaren med [stödda data lager och format](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats). Mer information finns i [Kopiera data från Azure Data Factory till Azure datautforskaren](/azure/data-explorer/data-factory-load-data).

### <a name="programmatic-ingestion"></a>Program mässig inmatning

Azure Datautforskaren innehåller SDK: er som kan användas för frågor och data inmatning. Programmerings utmatningen är optimerad för att minska inmatnings kostnader (KSV) genom att minimera lagrings transaktioner under och efter inmatnings processen.

**Tillgängliga SDK: er och projekt med öppen källkod**:

Kusto erbjuder klient-SDK som kan användas för att mata in och fråga data med:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [REST-API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Metoder**för att mata in program vara:

* Inmatning av data via Azure Datautforskaren data Management Service (hög genom strömning och tillförlitlig inmatning):

    [**Batch**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) -inmatning (tillhandahålls av SDK): klienten överför data till Azure Blob Storage (som anges av Azure datautforskaren data Management-tjänsten) och skickar ett meddelande till en Azure-kö. Batch-inmatning är den rekommenderade tekniken för data inmatning med hög volym, tillförlitlig och billig.

* Mata in data direkt i Azure Datautforskaren-motorn (passar bäst för utforskning och prototyper):

  * **Infogad**inmatning: kontroll kommando (. Mata in text) som innehåller in-band-data är avsedda för ad hoc-testning.

  * Mata in **från fråga**: kontroll kommando (. set,. Set-eller-append,. Set-eller-Replace) som pekar på frågeresultat används för att generera rapporter eller små temporära tabeller.

  * Inmatning **från lagring**: kontroll kommando (. Mata in i) med data som lagras externt (till exempel Azure Blob Storage) ger effektiv Mass inmatning av data.

**Svars tid för olika metoder**:

| Metod | Svarstid |
| --- | --- |
| **Infogad inmatning** | Omgående |
| **Mata in från fråga** | Tid för fråga + bearbetnings tid |
| **Mata in från lagring** | Hämtnings tid + bearbetnings tid |
| **Inhämtning i kö** | Tid för batch + bearbetnings tid |
| |

Bearbetnings tiden beror på data storleken, färre än några sekunder. Standardvärdet för satsvis kompilering är 5 minuter.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Välja den lämpligaste inmatnings metoden

Innan du börjar mata in data bör du ställa följande frågor.

* Var finns mina data? 
* Vad är data formatet och kan det ändras? 
* Vilka är de obligatoriska fälten som ska frågas? 
* Vad är den förväntade data volymen och hastigheten? 
* Hur många händelse typer förväntas (visas som antalet tabeller)? 
* Hur ofta ska händelse schemat förväntas ändras? 
* Hur många noder kommer att generera data? 
* Vad är käll-OS? 
* Vilka är latens kraven? 
* Kan en av de befintliga pipelinen för förbrukade inmatnings hantering användas? 

För organisationer med en befintlig infrastruktur som baseras på en meddelande tjänst som Event Hub och IoT Hub, är det sannolikt den lämpligaste lösningen med hjälp av en anslutning. Inmatningen i kö är lämplig för stora data volymer.

## <a name="supported-data-formats"></a>Data format som stöds

För alla inmatnings metoder förutom inmatning från fråga formaterar du data så att Azure Datautforskaren kan parsa det. 
* De data format som stöds är: TXT, CSV, TSV, TSVE, PSV, SCSV, SOH, JSON (Line-separerad, multi-line), Avro, Orc och Parquet. 
* Stöder ZIP-och GZIP-komprimering.

> [!NOTE]
> När data matas in härleds data typer baserat på mål tabellens kolumner. Om en post är ofullständig eller om ett fält inte kan parsas som den data typ som krävs fylls motsvarande tabell kolumner med null-värden.

## <a name="ingestion-recommendations-and-limitations"></a>Rekommendationer och begränsningar för inmatningar

* Den gällande bevarande principen för inmatade data härleds från databasens bevarande princip. Se [bevarande princip](/azure/kusto/concepts/retentionpolicy) för mer information. Inmatning av data kräver en **Table** -eller **databas** inmatnings behörighet.
* Inmatningen stöder en maximal fil storlek på 5 GB. Rekommendationen är att hämta filer mellan 100 MB och 1 GB.

## <a name="schema-mapping"></a>Schema mappning

Schema mappning hjälper till att binda käll data fält till mål tabell kolumner.

* [CSV-mappning](/azure/kusto/management/mappings?branch=master#csv-mapping) (valfritt) fungerar med alla ordinal-baserade format. Det kan utföras med hjälp av kommando parametern för att mata in eller [skapas i tabellen](/azure/kusto/management/tables?branch=master#create-ingestion-mapping) och refereras till i kommando parametern för intag.
* [JSON-mappning](/azure/kusto/management/mappings?branch=master#json-mapping) (obligatorisk) och [Avro-mappning](/azure/kusto/management/mappings?branch=master#avro-mapping) (obligatorisk) kan utföras med hjälp av kommando parametern intag. De kan också [skapas i förväg i tabellen](/azure/kusto/management/tables#create-ingestion-mapping) och refereras till i kommando parametern för intag.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mata in data från Händelsehubben till Azure Datautforskaren](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Mata in data med Event Grid-prenumeration i Azure Datautforskaren](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Mata in data från Kafka till Azure Datautforskaren](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Mata in data med Azure Datautforskaren python-biblioteket](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Mata in data med hjälp av Azure Datautforskaren Node-biblioteket](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Mata in data med Azure Datautforskaren .NET standard SDK (för hands version)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Mata in data från Logstash till Azure Datautforskaren](ingest-data-logstash.md)

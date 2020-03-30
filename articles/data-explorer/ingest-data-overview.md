---
title: Inmatning av Azure Data Explorer-data
description: Lär dig mer om olika sätt att använda (läsa in) data i Azure Data Explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 4846a19c403cce16bed704ed4e7c70499f3b5d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246401"
---
# <a name="azure-data-explorer-data-ingestion"></a>Inmatning av Azure Data Explorer-data

Datainmatning är den process som används för att läsa in dataposter från en eller flera källor för att skapa eller uppdatera en tabell i Azure Data Explorer. När data har förtärs blir de tillgängliga för frågan. Diagrammet nedan visar det end-to-end-flödet för arbete i Azure Data Explorer, inklusive datainmatning.

![Dataflöde](media/ingest-data-overview/data-flow.png)

Datahanteringstjänsten i Azure Data Explorer, som ansvarar för datainmatning, innehåller följande funktioner:

1. **Data hämtar**: Hämta data från externa källor (Event Hubs) eller läs inmatningsbegäranden från en Azure-kö.

1. **Batchbearbetning**: Batchdata som flödar till samma databas och tabell för att optimera inmatningsdataflödet.

1. **Validering**: Preliminär validering och format konvertering om det behövs.

1. **Datamanipulering**: Matcha schema, organisera, indexera, koda och komprimera data.

1. **Persistenspunkt i intagsflödet**: Hantera intagsbelastning på motorn och hantera återförsök vid tillfälliga fel.

1. **Begår dataindet:** Gör data tillgängliga för fråga.

## <a name="ingestion-methods"></a>Metoder för förtligande

Azure Data Explorer stöder flera inmatningsmetoder, var och en med sina egna målscenarier, fördelar och nackdelar. Azure Data Explorer erbjuder pipelines och kopplingar till vanliga tjänster, programmatiskt inmatning med SDK:er och direkt åtkomst till motorn i utforskningssyfte.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Intag med pipelines, kopplingar och plugin-program

Azure Data Explorer stöder för närvarande:

* Event Grid pipeline, som kan hanteras med hjälp av hanteringsguiden i Azure-portalen. Mer information finns i [Ingest Azure Blobbar i Azure Data Explorer](ingest-data-event-grid.md).

* Event Hub pipeline, som kan hanteras med hjälp av hanteringsguiden i Azure-portalen. Mer information finns i [Ingest data från Event Hub i Azure Data Explorer](ingest-data-event-hub.md).

* Logstash plugin, se [Ingest data från Logstash till Azure Data Explorer](ingest-data-logstash.md).

* Kafka-anslutning, se [Ingest data från Kafka i Azure Data Explorer](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Intag med hjälp av integrationstjänster

* Azure Data Factory (ADF), en fullständigt hanterad dataintegrationstjänst för analytiska arbetsbelastningar i Azure, för att kopiera data till och från Azure Data Explorer med hjälp av [datalager och format som stöds](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats). Mer information finns i [Kopiera data från Azure Data Factory till Azure Data Explorer](/azure/data-explorer/data-factory-load-data).

### <a name="programmatic-ingestion"></a>Programmatiskt intag

Azure Data Explorer tillhandahåller SDK:er som kan användas för fråge- och datainmatning. Programmatiskt intag är optimerat för att minska intagskostnaderna (COGs), genom att minimera lagringstransaktioner under och efter intagsprocessen.

**Tillgängliga SDK:er och projekt med öppen källkod:**

Kusto erbjuder klient SDK som kan användas för att inta och fråga data med:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [REST API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Programmatiska intagstekniker:**

* Intag av data via datahanteringstjänsten i Azure Data Explorer (högdataflöde och tillförlitligt inmatning):

    [**Batchinmatning**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (tillhandahålls av SDK): klienten överför data till Azure Blob storage (som anges av Azure Data Explorer datahanteringstjänst) och publicerar ett meddelande till en Azure-kö. Batch intag är den rekommenderade tekniken för hög volym, tillförlitlig och billig datainmatning.

* Intag av data direkt i Azure Data Explorer-motorn (mest lämplig för utforskning och prototyper):

  * **Inline intag:** kontrollkommando (.inmatningsinline) som innehåller in-band-data är avsedd för ad hoc-testning.

  * **Intag av fråga**: kontrollkommando (.set, .set-or-append, .set-or-replace) som pekar på frågeresultat används för att generera rapporter eller små temporära tabeller.

  * **Intas från lagring:** kontrollkommando (.ingest in) med data som lagras externt (till exempel Azure Blob Storage) möjliggör effektiv massinmatning av data.

**Latens av olika metoder:**

| Metod | Svarstid |
| --- | --- |
| **Inline intag** | Omedelbar |
| **Inta från fråga** | Frågetid + bearbetningstid |
| **Inta från lagring** | Nedladdningstid + bearbetningstid |
| **Köat intag** | Batchtid + bearbetningstid |
| |

Bearbetningstiden beror på datastorleken, mindre än några sekunder. Batching-tiden är som standard 5 minuter.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Välja den lämpligaste intagsmetoden

Innan du börjar få in data bör du ställa dig följande frågor.

* Var finns mina data? 
* Vad är dataformatet och kan det ändras? 
* Vilka är de obligatoriska fält som ska efterfrågas? 
* Vad är den förväntade datavolymen och hastigheten? 
* Hur många händelsetyper förväntas (visas som antalet tabeller)? 
* Hur ofta förväntas händelseschemat ändras? 
* Hur många noder genererar data? 
* Vad är käll-OS? 
* Vilka är svarstidskraven? 
* Kan en av de befintliga hanterade inmatningspipelorna användas? 

För organisationer med en befintlig infrastruktur som baseras på en meddelandetjänst som Event Hub och IoT Hub är det troligen den lämpligaste lösningen att använda en anslutningsapp. Köat inmatning är lämpligt för stora datavolymer.

## <a name="supported-data-formats"></a>Dataformat som stöds

För alla inmatningsmetoder förutom att använda från frågan formaterar du data så att Azure Data Explorer kan tolka dem. 
* De dataformat som stöds är: TXT, CSV, TSV, TSVE, PSV, SCSV, SOH, JSON (linjeavskiljande, multi-line), Avro, Orc och Parkett. 
* Stöder ZIP- och GZIP-komprimering.

> [!NOTE]
> När data används härledas datatyper baserat på måltabellkolumnerna. Om en post är ofullständig eller om ett fält inte kan tolkas som den datatyp som krävs fylls motsvarande tabellkolumner med null-värden.

## <a name="ingestion-recommendations-and-limitations"></a>Rekommendationer och begränsningar för förtärdning

* Den effektiva lagringsprincipen för inmatade data härleds från databasens lagringsprincip. Mer information finns i [bevarandeprincipen.](/azure/kusto/concepts/retentionpolicy) För att få data krävs **behörigheter för tabellingestor** **eller Databasingestor.**
* Inmatning stöder en maximal filstorlek på 5 GB. Rekommendationen är att inta filer mellan 100 MB och 1 GB.

## <a name="schema-mapping"></a>Schemamappning

Schemamappning hjälper till att binda källdatafält till måltabellkolumner.

* [CSV Mapping](/azure/kusto/management/mappings?branch=master#csv-mapping) (valfritt) fungerar med alla ordningsbaserade format. Den kan utföras med hjälp av den inta kommandoparametern eller [skapas i föraningen i tabellen](/azure/kusto/management/create-ingestion-mapping-command) och refereras från kommandoparametern ingest.
* [JSON Mapping](/azure/kusto/management/mappings?branch=master#json-mapping) (obligatorisk) och [Avro-mappning](/azure/kusto/management/mappings?branch=master#avro-mapping) (obligatoriskt) kan utföras med hjälp av kommandoparametern för intant. De kan också [skapas i förakan i tabellen](/azure/kusto/management/create-ingestion-mapping-command) och refereras från kommandoparametern för int.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mata in data från Event Hub i Azure Data Explorer](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Övra data med Event Grid-prenumeration i Azure Data Explorer](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Mata in data från Kafka i Azure Data Explorer](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Mata in data med hjälp av Python-biblioteket i Azure Data Explorer](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Mata in data med hjälp av Node-biblioteket i Azure Data Explorer](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Hämtar data med Hjälp av Azure Data Explorer .NET Standard SDK (förhandsversion)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Mata in data från Logstash i Azure Data Explorer](ingest-data-logstash.md)

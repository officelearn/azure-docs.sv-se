---
title: Azure Data Explorer-datainmatning
description: Läs om hur du kan hämta (load) data i Datautforskaren i Azure
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 2/18/2019
ms.openlocfilehash: 4fd0f0990163963fc0cc3c7caf221609da487909
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56340186"
---
# <a name="azure-data-explorer-data-ingestion"></a>Azure Data Explorer-datainmatning

Datainmatning är den process som används för att läsa in dataposter från en eller flera källor för att skapa eller uppdatera en tabell i Datautforskaren i Azure. När matas in, blir data tillgängliga för frågor. Diagrammet nedan visar flödet för att arbeta i Datautforskaren i Azure, inklusive datainmatning slutpunkt till slutpunkt.

![Dataflöde](media/ingest-data-overview/data-flow.png)

Datautforskaren i Azure data management service, som är ansvarig för datainmatning, tillhandahåller följande funktioner:

1. **Data pull**: Hämta data från externa källor (Event Hubs) eller läsa inmatning begäranden från en Azure-kö.

1. **Batchbearbetning**: Batchdata som flödar till samma databas och tabell för att optimera genomflödet inmatning.

1. **Verifiering**: Preliminär verifiering och format konvertering om det behövs.

1. **Datamanipulering**: Matchande schema, ordna, indexering, kodning och genom att komprimera data.

1. **Persistence punkt i flödet inmatning**: Hantera datainmatning belastningen på motorn och hantera återförsök vid tillfälliga fel.

1. **Commit mata in data**: Gör data tillgängliga för frågor.

## <a name="ingestion-methods"></a>Datainmatning metoder

Azure Data Explorer stöder flera inmatning metoder, var och en med sin egen målscenarier, fördelar och nackdelar. Azure Data Explorer erbjuder pipelines och anslutningar till vanliga tjänster, programmässiga datainmatning med hjälp av SDK: er och direktåtkomst till motorn för utforskning.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Inmatning med pipelines, anslutningar och plugin-program

Azure Data Explorer stöder för närvarande:

* Event Grid pipeline, som kan hanteras med hjälp av guiden hantering i Azure-portalen. Mer information finns i [mata in Azure-Blobar i Azure Data Explorer](ingest-data-event-grid.md).

* Event Hub pipeline, som kan hanteras med hjälp av guiden hantering i Azure-portalen. Mer information finns i [mata in data från Event Hub i Azure Data Explorer](ingest-data-event-hub.md).

* Logstash plugin-programmet finns i [mata in data från Logstash till Datautforskaren i Azure](ingest-data-logstash.md).

* Kafka-anslutningstjänsten finns i [mata in data från Kafka i Azure Data Explorer](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Inmatning som använder integrationstjänster

* Azure Data Factory (ADF), en fullständigt hanterad dataintegreringstjänst för analytiska arbetsbelastningar i Azure för att kopiera data till och från Azure Data Explorer. Mer information finns i [kopiera data till och från Azure Data Explorer med Azure Data Factory](/azure/data-factory/connector-azure-data-explorer).

### <a name="programmatic-ingestion"></a>Programmässig inmatning

Azure Data Explorer tillhandahåller SDK: er som kan användas för frågor och inmatning. Programmässig inmatning är optimerad för att minska kostnaderna för inmatning varor, genom att minimera lagringstransaktioner under och efter inmatning processen.

**Tillgängliga SDK: er och projekt för öppen källkod**:

Kusto erbjuder klient-SDK som kan användas för att mata in och fråga efter data med:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [REST-API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Programmässig inmatning tekniker**:

* Mata in data med hjälp av Azure Data Explorer data management-tjänsten (snabb och tillförlitlig inhämtning):

    [**Batch-inmatning** ](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (som tillhandahålls av SDK): klienten överför data till Azure Blob storage (bestäms av tjänsten Azure Data Explorer data management) och publicerar ett meddelande till en Azure-kö. Batch-inmatning är den rekommenderade metod för stora volymer, pålitlig och billiga datainmatning.

* Mata in data direkt i Azure Data Explorer-motor (passar bäst för utforskning och prototyper):

  * **Infogad inmatning**: Trafikstyrning (.ingest infogade) som innehåller in-band-data är avsedd för ad hoc för testning.

  * **Mata in från fråga**: Trafikstyrning (.set .set-eller-tillägg kan .set eller ersätta) som pekar på frågeresultaten används för att skapa rapporter eller små temporära tabeller.

  * **Mata in från storage**: kontroll kommandot (.ingest till) med data som lagras externt (till exempel Azure Blob Storage) kan effektivt bulk inmatning av data.

**Svarstiden för olika metoder**:

| Metod | Svarstid |
| --- | --- |
| **Infogad inmatning** | Omedelbar |
| **Mata in från fråga** | Frågetid + bearbetningstid |
| **Mata in från storage** | Hämtningstid + bearbetningstid |
| **Köade inmatning** | Batchbearbetning tid + bearbetningstid |
| |

Bearbetningstid beror på storleken på data under ett par sekunder. Batchbearbetning tid standardvärdet är 5 minuter.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Välja den lämpligaste metoden för inmatning

Innan du börjar att mata in data, bör du fråga dig själv följande frågor.

* Var finns Mina data? 
* Vad är dataformatet och kan ändra det? 
* Vilka är de obligatoriska fälten ska efterfrågas? 
* Vad är den förväntade datavolym och hastighet? 
* Hur många händelsetyper förväntas (visas som antalet tabeller)? 
* Hur ofta händelseschemat förväntas ändras? 
* Hur många noder genererar data? 
* Vad är källan OS? 
* Vilka är kraven för fördröjning? 
* Kan en befintlig hanterad inmatning pipelines användas? 

För organisationer med en befintlig infrastruktur som baseras på en meddelandetjänst som Event Hub, är med en anslutning antagligen den bästa lösningen. Köade inmatning är lämpligt för stora datavolymer.

## <a name="supported-data-formats"></a>Dataformat som stöds

Formatera data så att Azure Data Explorer kan parsa den för alla inmatningen av metoder än mata in från frågan. Format som stöds är:

* CSV, TSV, PSV, SCSV, SOH
* JSON (rad-fil, flera rader) Avro
* POSTNUMMER och GZIP 

> [!NOTE]
> När data matas är datatyper härledas baserat på mål-tabellkolumner. Om en post är ofullständig eller ett fält kan inte parsas som behövs, fylls motsvarande tabellkolumnerna med null-värden.

## <a name="ingestion-recommendations-and-limitations"></a>Rekommendationer för inmatning och begränsningar

* Effektiva kvarhållningsprincipen för inmatade härleds från databasens bevarandeprincip. Se [bevarandeprincip](/azure/kusto/concepts/retentionpolicy) mer information. Mata in data kräver **tabell lösning** eller **Database lösning** behörigheter.
* Datainmatning stöder en maximal filstorlek på 5 GB. Rekommendationen är att mata in filer mellan 100 MB och 1 GB.

## <a name="schema-mapping"></a>Schemamappning

Schemamappning hjälper till att binda datafält som källa till mål tabellkolumner.

* [Mappning av CSV](/azure/kusto/management/mappings?branch=master#csv-mapping) (valfritt) fungerar med alla ordningstalet-baserade format. Det kan utföras med hjälp av Kommandoparametern som inmatning eller [färdiga för tabellen](/azure/kusto/management/tables?branch=master#create-ingestion-mapping) och refererade från Kommandoparametern som inmatning.
* [JSON-mappning](/azure/kusto/management/mappings?branch=master#json-mapping) (obligatoriskt) och [Avro mappning](/azure/kusto/management/mappings?branch=master#avro-mapping) (obligatorisk) kan utföras med hjälp av Kommandoparametern som inmatning. De kan även vara [färdiga för tabellen](/azure/kusto/management/tables#create-ingestion-mapping) och refererade från Kommandoparametern som inmatning.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mata in data från Event Hub i Azure Data Explorer](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Mata in data med Event Grid-prenumeration i Azure Data Explorer](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Mata in data från Kafka i Azure Data Explorer](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Mata in data med hjälp av Azure Data Explorer Python-bibliotek](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Mata in data med hjälp av Azure Data Explorer nod-bibliotek](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Mata in data med hjälp av Azure Data Explorer .NET Standard SDK (förhandsversion)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Mata in data från Logstash till Datautforskaren i Azure](ingest-data-logstash.md)

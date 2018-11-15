---
title: Azure Data Explorer-datainmatning
description: Läs om hur du kan hämta (load) data i Datautforskaren i Azure
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6c7d4d8d4a16e0679722f9de007870a7ec7554b0
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636007"
---
# <a name="azure-data-explorer-data-ingestion"></a>Azure Data Explorer-datainmatning

Datainmatning är den process som används för att läsa in dataposter från en eller flera källor för att skapa eller uppdatera en tabell i Datautforskaren i Azure. När matas in, blir data tillgängliga för frågor. Diagrammet nedan visar flödet för att arbeta i Datautforskaren i Azure, inklusive datainmatning slutpunkt till slutpunkt.

![Dataflöde](media/ingest-data-overview/data-flow.png)

Datautforskaren i Azure data management service, som är ansvarig för datainmatning, tillhandahåller följande funktioner:

1. **Data pull**: hämta data från externa källor (Event Hubs) eller läsa inmatning begäranden från en Azure-kö.

1. **Batchbearbetning**: Batch-data som flödar till samma databas och tabell för att optimera genomflödet inmatning.

1. **Verifiering**: Preliminär verifiering och format konvertering om det behövs.

1. **Datamanipulering**: matchande schema, ordna, indexering, kodning och genom att komprimera data.

1. **Persistence punkt i flödet inmatning**: hantera datainmatning belastningen på motor och hantera återförsök vid tillfälliga fel.

1. **Commit mata in data**: gör data tillgängliga för frågor.

## <a name="ingestion-methods"></a>Datainmatning metoder

Azure Data Explorer stöder flera inmatning metoder, var och en med sin egen målscenarier, fördelar och nackdelar. Azure Data Explorer erbjuder pipelines och anslutningar till vanliga tjänster, programmässiga datainmatning med hjälp av SDK: er och direktåtkomst till motorn för utforskning.

### <a name="ingestion-using-pipelines"></a>Inmatning med pipelines

Azure Data Explorer stöder för närvarande pipelinen Event Hub, som kan hanteras med hjälp av guiden hantering i Azure-portalen. Mer information finns i [Snabbstart: mata in data från Event Hub i Azure Data Explorer](ingest-data-event-hub.md).

### <a name="ingestion-using-connectors-and-plugins"></a>Datainmatning med hjälp av kopplingar och plugin-program
Azure Data Explorer stöder för närvarande Logstash plugin-programmet. Mer information finns i [Logstash utdata-Plugin för Azure Data Explorer](https://github.com/Azure/logstash-output-kusto/blob/master/README.md).

### <a name="programmatic-ingestion"></a>Programmässig inmatning

Azure Data Explorer tillhandahåller SDK: er som kan användas för frågor och inmatning. Programmässig inmatning är optimerad för att minska kostnaderna för inmatning varor, genom att minimera lagringstransaktioner under och efter inmatning processen.

**Tillgängliga SDK: er och projekt med öppen källkod**:

Kusto erbjuder klient-SDK som kan användas för att mata in och fråga efter data med:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [REST-API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Programmässig inmatning tekniker**:

* Mata in data med hjälp av Azure Data Explorer data management-tjänsten (snabb och tillförlitlig inhämtning):

  * [**Batch-inmatning** ](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (som tillhandahålls av SDK): klienten överför data till Azure Blob storage (bestäms av tjänsten Azure Data Explorer data management) och publicerar ett meddelande till en Azure-kö. Det här är den rekommenderade tekniken för stora volymer, tillförlitliga och billiga datainmatning.

* Mata in data direkt i Azure Data Explorer-motor (passar bäst för utforskning och prototyper):

  * **Infogad inmatning**: Trafikstyrning (.ingest infogade) som innehåller in-band-data är avsedd för ad hoc för testning.

  * **Mata in från fråga**: Trafikstyrning (.set .set-eller-tillägg kan .set eller ersätta) som pekar på frågeresultaten används för att skapa rapporter eller små temporära tabeller.

  * **Mata in från storage**: kontroll kommandot (.ingest till) med data som lagras externt (t.ex. Azure Blob Storage) kan effektivt bulk inmatning av data.

**Svarstiden för olika metoder**:

| Metod | Svarstid |
| --- | --- |
| **Infogad inmatning** | Omedelbar |
| **Mata in från fråga** | Frågetid + bearbetningstid |
| **Mata in från storage** | Hämtningstid + bearbetningstid |
| **Köade inmatning** | Batchbearbetning tid + bearbetningstid |
| |

Bearbetningstid beror på storleken på data, vanligtvis mindre än några sekunder. Batchbearbetning tid standardvärdet är 5 minuter.

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

För organisationer med en befintlig infrastruktur som baseras på en meddelandetjänst som Event Hub är med hjälp av en koppling antagligen den bästa lösningen. Köade inmatning är lämpligt för stora datavolymer.

## <a name="supported-data-formats"></a>Dataformat som stöds

För alla inmatningen av metoder än mata in från fråga måste data vara formaterad på något av format som stöds så att Azure Data Explorer kan parsa den.

* CSV, TVS, PSV, SCSV, HÄLSOBESKED
* JSON (rad-fil, flera rader) Avro
* POSTNUMMER och GZIP 

> [!NOTE]
> När data matas är datatyper härledas baserat på mål-tabellkolumner. Om en post är ofullständig eller ett fält kan inte parsas som behövs, fylls motsvarande tabellkolumnerna med null-värden.

## <a name="ingestion-recommendations-and-limitations"></a>Rekommendationer för inmatning och begränsningar
* Effektiva kvarhållningsprincipen för inmatade härleds från databasens bevarandeprincip. Se [bevarandeprincip](/azure/kusto/concepts/retentionpolicy) mer information. Mata in data kräver **tabell lösning** eller **Database lösning** behörigheter.
* Datainmatning stöder en maximal filstorlek på 5GB. Rekommendationen är att mata in filer mellan 100MB och 1GB.

## <a name="schema-mapping"></a>Schemamappning

Schemamappning hjälper deterministiskt binda datafält som källa till mål tabellkolumner.

* [Mappning av CSV](/azure/kusto/management/mappings?branch=master#csv-mapping) (valfritt) fungerar med alla ordningstalet-baserade format och kan skickas som inmatning kommandoparameter eller [färdiga för tabellen](/azure/kusto/management/tables?branch=master#create-ingestion-mapping) och refererade från Kommandoparametern som inmatning.
* [JSON-mappning](/azure/kusto/management/mappings?branch=master#json-mapping) (obligatoriskt) och [Avro mappning](/azure/kusto/management/mappings?branch=master#avro-mapping) (obligatorisk) kan skickas som inmatning kommandoparameter eller [färdiga för tabellen](/azure/kusto/management/tables#create-ingestion-mapping) och refererade från Kommandoparametern som inmatning.

## <a name="next-steps"></a>Nästa steg

[Snabbstart: Mata in data från Event Hub i Azure Data Explorer](ingest-data-event-hub.md)

[Snabbstart: Mata in data med hjälp av Python-biblioteket i Azure Data Explorer](python-ingest-data.md)

[Snabbstart: Mata in data med hjälp av Azure Data Explorer nod-bibliotek](node-ingest-data.md)


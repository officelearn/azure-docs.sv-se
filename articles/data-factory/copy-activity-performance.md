---
title: Kopiera aktivitet prestanda- och Justeringsguiden i Azure Data Factory | Microsoft Docs
description: Läs mer om viktiga faktorer som påverkar prestandan för dataförflyttning i Azure Data Factory när du använder Kopieringsaktivitet.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: face3719f32ccb44e7479150e94417496141f90b
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509558"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Kopiera aktivitet prestanda- och justeringsguide
> [!div class="op_single_selector" title1="Välj versionen av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-copy-activity-performance.md)
> * [Aktuell version](copy-activity-performance.md)


Azure Data Factory kopieringsaktiviteten levererar en förstklassig säker, tillförlitlig och höga prestanda för inläsning av data lösning. Du kan använda den för att kopiera tiotals terabyte data varje dag på en rad i molnet och lokala datalager. Snabb datainläsning prestanda är nyckeln till att se till att du kan fokusera på problemet core stordata: bygga avancerade Analyslösningar och få djupa insikter från alla data.

Azure tillhandahåller en uppsättning av företagsklass lösningar för lagring och informationslager. Kopieringsaktiviteten erbjuder en optimerad upplevelse som är lätt att göra inställningar för inläsning av data. Du kan använda en enda Kopieringsaktivitet för att läsa in data till:

* Azure SQL Data Warehouse vid 1,2 Gbit/s.
* Azure Blob storage vid 1,0 GB/s.
* Azure Data Lake Store med 1.0 Gbit/s.

Den här artikeln beskrivs:

* [Referensnummer för prestanda](#performance-reference) för källa och mottagare datalager när du planerar ditt projekt som stöds.
* Funktioner som kan öka kopia genomflöde i olika scenarier, vilket innefattar [integrering enheter](#data-integration-units) (DIUs) [parallell kopia](#parallel-copy), och [mellanlagrad kopiering](#staged-copy).
* [Prestandajusteringsvägledning](#performance-tuning-steps) på hur du ställer in prestanda- och de viktigaste faktorerna som kan påverka kopieringen bättre prestanda.

> [!NOTE]
> Om du inte är bekant med Kopieringsaktivitet i allmänhet kan du läsa den [översikt över Kopieringsaktivitet](copy-activity-overview.md) innan du läser den här artikeln.
>

## <a name="performance-reference"></a>Prestandareferens för

Som en referens i följande tabell visas kopia dataflöde i Mbit/s för den angivna källan och mottagaren par i en enda kopieringsaktivitetskörning baserat på interna tester. Jämförelse, visas också hur olika inställningar för [integrering enheter](#data-integration-units) eller [skalbarhet för integration runtime med egen värd](concepts-integration-runtime.md#self-hosted-integration-runtime) (flera noder) kan hjälpa på kopieringen bättre prestanda.

![Matris för prestanda](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> När kopieringen körs på en Azure integration runtime är är den minimala tillåtna Integration enheter (kallades tidigare enheter för Dataflytt) två. Om inte anges, se Data Integration standardenheter som används i [integrering enheter](#data-integration-units).

**Saker att Observera:**

* Dataflödet beräknas med hjälp av följande formel: [storleken på data läses från källan] / [kopiera aktivitet körningens varaktighet].
* Referensnummer för prestanda i tabellen har mäts med hjälp av en [TPC-H](http://www.tpc.org/tpch/) datauppsättning i en enda kopieringsaktivitetskörning. Testfiler för filbaserade butiker finns flera filer med 10 GB i storlek.
* I Azure datalager finns källa och mottagare i samma Azure-region.
* För hybridkopiering mellan lokala och molnbaserade datalager måste varje lokal integration runtime-noden körs på en dator som var avskild från datalagret med följande-specifikationen. När en enda aktivitet kördes förbrukas kopieringen endast en liten del av testdatorn CPU, minne eller bandbredd i nätverket.
    <table>
    <tr>
        <td>Processor</td>
        <td>32 kärnor 2.20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Minne</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Nätverk</td>
        <td>Internet-gränssnittet: 10 Gbit/s; intranät-gränssnitt: 40 Gbit/s</td>
    </tr>
    </table>


> [!TIP]
> Du kan uppnå högre dataflöde med hjälp av flera DIUs. Till exempel med 100 DIUs kan du kopiera data från Azure Blob storage till Azure Data Lake Store med 1.0 Gbit/s. Mer information om den här funktionen och scenario som stöds finns i den [integrering enheter](#data-integration-units) avsnittet. 

## <a name="data-integration-units"></a>Integrering enheter

En enhet för integrering av Data är ett mått som representerar (en kombination av processor, minne och nätverksresursallokering) en enhet i Azure Data Factory. Integrering av dataenheten gäller endast för [med Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime), men inte [integration runtime med egen värd](concepts-integration-runtime.md#self-hosted-integration-runtime).

Minimal DIUs möjligheter för en kopieringsaktivitetskörning är två. Om inte anges visas i följande tabell de standard-DIUs som används i olika kopia scenarier:

| Kopiera scenario | Standard DIUs bestäms av tjänsten |
|:--- |:--- |
| Kopiera data mellan filbaserade lager | Mellan 4 och 32 beroende på antalet och storleken på filerna |
| Kopiera data till Azure SQL Database eller Azure Cosmos DB |Mellan 4 och 16 beroende på mottagare Azure SQL Database eller Cosmos DB-nivån (antalet dtu: er/ru: er) |
| Alla andra kopia-scenarier | 4 |

Om du vill åsidosätta denna standardinställning, ange ett värde för den **dataIntegrationUnits** egenskapen på följande sätt. Den *tillåtna värden* för den **dataIntegrationUnits** egenskapen är upp till 256. Den *faktiska antalet DIUs* att kopieringen använder vid körning är lika med eller mindre än det konfigurerade värdet, beroende på din datamönster. Information om nivå av prestanda som du kan få när du konfigurerar fler enheter för en specifik kopieringskälla och mottagare finns i den [Prestandareferens](#performance-reference).

Du kan se DIUs som används för varje kopia som kör i Kopiera aktivitetsutdata när du övervakar en aktivitet som körs. Mer information finns i [kopiera aktivitetsövervakning](copy-activity-overview.md#monitoring).

> [!NOTE]
> Inställningen för den DIUs större än fyra gäller för närvarande endast när du kopierar flera filer från Azure Storage, Azure Data Lake Storage, Amazon S3, Google Cloud Storage cloud FTP eller SFTP i molnet till andra molndatalager.
>

**Exempel**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="data-integration-units-billing-impact"></a>Data Integration enheter fakturering påverkan

Kom ihåg att du kommer att debiteras baserat på den totala tiden för kopieringsåtgärden. Total varaktighet du faktureras för dataförflyttning är summan av varaktigheten för DIUs. Om ett kopieringsjobb brukade ta en timme med två molnenheter och så tar det nu 15 minuter med åtta molnenheter, förblir övergripande fakturan nästan samma.

## <a name="parallel-copy"></a>Parallell kopia

Du kan använda den **parallelCopies** egenskap som anger parallellitet som du vill Kopieringsaktivitet att använda. Du kan tänka på den här egenskapen som det maximala antalet trådar i kopieringsaktiviteten som kan läsa från källan eller skriva till dina datalager för mottagare parallellt.

För varje körningen av kopieringsaktiviteten, avgör hur många parallella kopior som ska använda för att kopiera data från källan datalagring och att sidan måldatalager lagra i Azure Data Factory. Standardvärdet för antal parallella kopior som används beror på vilken typ av källa och mottagare som du använder.

| Kopiera scenario | Parallell kopia Standardantal bestäms av tjänsten |
| --- | --- |
| Kopiera data mellan filbaserade lager |Beror på storleken på filerna och antalet DIUs som används för att kopiera data mellan två molndatalager eller den fysiska konfigurationen av den lokala installation av integration runtime-datorn. |
| Kopiera data från valfri källa store till Azure Table storage |4 |
| Alla andra kopia-scenarier |1 |

> [!TIP]
> När du kopierar data mellan filbaserade ger standardbeteendet vanligtvis dig bästa dataflödet. Standardinställningen är auto bestäms baserat på din fil från en källa.

Kontrollera belastningen på datorer som är värdar för dina data lagras eller för att justera kopieringen bättre prestanda, kan du åsidosätta standardvärdet och ange ett värde för den **parallelCopies** egenskapen. Värdet måste vara ett heltal större än eller lika med 1. Vid körning använder för bästa prestanda kopieringsaktiviteten ett värde som är mindre än eller lika med värdet som du anger.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

**Saker att Observera:**

* När du kopierar data mellan filbaserade **parallelCopies** avgör parallellitet på filnivå. Dela upp inom en enda fil sker under automatiskt och transparent. Den har utformats för att använda bäst lämpliga segment storlek för en viss källa store datatyp att läsa in data parallellt och rätvinkliga till **parallelCopies**. Det faktiska antalet parallella kopior av data movement service använder för att kopieringen under körning är inte fler än antalet filer som du har. Om kopieringsbeteendet är **mergeFile**, Kopieringsaktivitet inte kan utnyttja parallellitet på filnivå.
* När du kopierar data från butiker som inte är filbaserade (med undantag för Oracle-databas som källa med Datapartitionering aktiverat) till, som är filbaserade, av data movement service ignorerar den **parallelCopies** egenskapen. Även om parallellitet anges, tillämpas den inte i det här fallet.
* Den **parallelCopies** egenskapen är rätvinkliga till **dataIntegrationUnits**. Det tidigare räknas över alla enheter för Data-integrering.
* När du anger ett värde för den **parallelCopies** egenskap, Överväg att belastningen ökar på källan och datalager för mottagare. Överväg också att belastningen ökar till den lokala integreringskörningen om möjligheter kopieringsaktiviteten, till exempel för hybridkopiering. Den här belastningen ökar sker särskilt när du har flera aktiviteter eller samtidiga körningar av samma aktiviteter som körs mot samma datalager. Om du märker att datalagret eller den lokala integreringskörningen blir överbelastad till följd med belastning kan minska den **parallelCopies** värde att avlasta belastningen.

## <a name="staged-copy"></a>Mellanlagrad kopiering

När du kopierar data från källans datalager till mottagarens datalager kan du välja att använda Blob storage som en mellanliggande mellanlagringsarkivet. Mellanlagring är särskilt användbart i följande fall:

- **Du kan mata in data från olika datalager i SQL Data Warehouse via PolyBase.** SQL Data Warehouse använder PolyBase som en mekanism för stora dataflöden för att läsa in en stor mängd data till SQL Data Warehouse. Källdata måste vara i Blob storage eller Azure Data Lake Store och den uppfylla ytterligare kriterier. När du läser in data från ett datalager än Blob storage eller Azure Data Lake Store kan du aktivera data kopiering via tillfälliga mellanlagringsplatsen Blob-lagring. Azure Data Factory utför i så fall vilka Datatransformationer som krävs för att säkerställa att den uppfyller kraven för PolyBase. Sedan används PolyBase för att läsa in data i SQL Data Warehouse effektivt. Mer information finns i [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Ibland kan det ta en stund att utföra en hybriddataförflyttning (det vill säga för att kopiera från ett lokalt datalager till ett datalager i molnet) över en långsam nätverksanslutning.** Du kan använda mellanlagrad kopiering för att komprimera data lagras lokalt så att det tar mindre tid att flytta data till det tillfälliga datalagret i molnet för att förbättra prestanda. Sedan kan du expandera data i mellanlagringsarkivet innan du läser in i måldatalagret.
- **Du vill inte öppna andra portar än port 80 och port 443 i brandväggen på grund av företagets IT-principer.** När du kopierar data från ett lokalt datalager till en Azure SQL Database-mottagare eller en Azure SQL Data Warehouse sink måste aktivera utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företagets brandvägg. I det här scenariot kan mellanlagrad kopiering dra nytta av den lokala integreringskörningen först kopiera data till ett Blob storage för mellanlagring instans via HTTP eller HTTPS på port 443. Det kan sedan läsa in data i SQL Database eller SQL Data Warehouse från mellanlagring för Blob storage. I det här flödet behöver du inte aktivera port 1433.

### <a name="how-staged-copy-works"></a>Hur mellanlagrad kopiering fungerar

När du aktiverar funktionen mellanlagring först data kopieras från källdatalagret till mellanlagring Blob-lagringen (ta med din egen). Därefter kopieras data från datalager för mellanlagring till datalager för mottagare. Azure Data Factory hanterar automatiskt två steg flödet för dig. Azure Data Factory rensar också tillfälliga data från den tillfälliga lagringen efter att dataöverföringen har slutförts.

![Mellanlagrad kopiering](media/copy-activity-performance/staged-copy.png)

När du aktiverar dataförflyttning med hjälp av en mellanlagringsarkivet kan ange du om du vill lagra data som ska komprimeras innan du flyttar data från datakällan pågår eller mellanlagring datalagring och expanderas innan du flyttar data från en mellanliggande eller mellanlagring dat en butik till de mottagande datalagren.

För närvarande kan kopiera du inte data mellan två datalager som är anslutna via olika lokal IRs, varken med eller utan mellanlagrad kopiering. Du kan konfigurera två uttryckligen länkade Kopieringsaktivitet som kopierar från källa till mellanlagring och sedan från mellanlagring för mottagare för sådana scenario.

### <a name="configuration"></a>Konfiguration

Konfigurera den **enableStaging** inställningen i kopieringsaktiviteten till att ange om du vill att data ska mellanlagras i Blob storage innan du läser in dem till ett måldatalager. När du ställer in **enableStaging** till `TRUE`, anger du ytterligare egenskaper som visas i följande tabell. Du måste också skapa en Azure-lagring eller lagring delade åtkomst signatur-länkad tjänst för att mellanlagra om du inte har något.

| Egenskap | Beskrivning | Standardvärde | Obligatoriskt |
| --- | --- | --- | --- |
| enableStaging |Ange om du vill kopiera data via en tiden mellanlagring store. |False |Nej |
| linkedServiceName |Ange namnet på en [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) länkad tjänst som refererar till instansen av lagring som du använder som ett tillfälligt mellanlagringsarkivet. <br/><br/> Du kan inte använda Storage med signatur för delad åtkomst för att läsa in data till SQL Data Warehouse via PolyBase. Du kan använda den i alla andra scenarier. |Gäller inte |Ja, när **enableStaging** har angetts till TRUE |
| sökväg |Ange sökvägen för Blob-lagring som du vill ska innehålla den mellanlagrade data. Om du inte anger en sökväg, skapar en behållare för att lagra tillfälliga data i tjänsten. <br/><br/> Ange en sökväg endast om du använder lagring med signatur för delad åtkomst, eller du kräver tillfälliga data finnas i en viss plats. |Gäller inte |Nej |
| enableCompression |Anger om data ska komprimeras innan den kopieras till målet. Den här inställningen minskar mängden data som överförs. |False |Nej |

>[!NOTE]
> Om du använder mellanlagrad kopiering vid komprimering aktiverat tjänstens huvudnamn eller MSI-autentisering för att mellanlagra länkade blobtjänsten stöds inte.

Här är en exempeldefinition för en Kopieringsaktivitet med egenskaper som beskrivs i tabellen ovan:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Mellanlagrad kopiering fakturering påverkan

Du debiteras baserat på två steg: kopiera varaktighet och typen.

* När du använder mellanlagring under en molnkopieringen som kopierar data från ett molndatalager till ett annat moln, både faser möjligheter med Azure integration runtime, du debiteras [summan av Kopieringstid för steg 1 och 2] x [cloud kopia Enhetspris].
* När du använder mellanlagring under en hybrid-kopia som kopierar data från ett lokalt datalager till ett datalager i molnet, ett steg möjligheter med en lokal integration runtime, du debiteras för [hybrid Kopieringstid] x [Enhetspris hybrid kopia] + [cloud Kopieringstid] x [cloud kopia Enhetspris].

## <a name="performance-tuning-steps"></a>Prestanda justering steg

Gör följande för att finjustera prestanda för din Azure Data Factory-tjänsten med Kopieringsaktivitet.

1. **Upprätta en baslinje.** Testa din pipeline med hjälp av kopieringsaktiviteten mot ett representativt datasampel under utvecklingsfasen för. Samla in information om och prestandaegenskaper följa [kopiera aktivitetsövervakning](copy-activity-overview.md#monitoring).

2. **Diagnostisera och optimera prestanda.** Om du se prestanda inte uppfyller dina förväntningar kan identifiera flaskhalsar i prestanda. Sedan kan optimera prestanda för att ta bort eller minska effekten av flaskhalsar.

    I vissa fall när du kör en Kopieringsaktivitet i Azure Data Factory, visas meddelandet ”prestandajustering tips” ovanpå den [kopiera aktivitetsövervakning sidan](copy-activity-overview.md#monitor-visually), enligt följande exempel. Meddelandet anger att flaskhalsen som identifierades för den angivna kopia-körningen. Du guidas även på vad du kan ändra till boost kopia dataflöde. Tips vid prestandajustering erbjuder för närvarande förslag som:

    - Använd PolyBase när du kopierar data till Azure SQL Data Warehouse.
    - Öka programbegäran för Azure Cosmos DB eller Azure SQL Database dtu: er (Database Throughput Unit) när resursen på data store sida är flaskhalsen.
    - Ta bort onödiga mellanlagrad kopiering.

    Regler för prestandajustering kommer gradvis utökas även.

    **Exempel: Kopiera till Azure SQL Database med tips vid prestandajustering**

    I det här exemplet meddelanden under en kopia som kör, Azure Data Factory mottagare som Azure SQL-databasen uppnår hög DTU-användning, vilket saktar ned skrivåtgärder. Förslag är att öka Azure SQL Database-nivå med mer dtu: er. 

    ![Kopiera övervakning med tips för prestandajustering](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    Dessutom här följer några vanliga överväganden. En fullständig beskrivning av Prestandadiagnostik ligger utanför omfånget för den här artikeln.

   * Prestandafunktioner:
     * [Parallell kopia](#parallel-copy)
     * [Dataintegrationsenheter](#data-integration-units)
     * [Mellanlagrad kopiering](#staged-copy)
     * [Lokal integration runtime skalbarhet](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Lokal Integration Runtime](#considerations-for-self-hosted-integration-runtime)
   * [Källa](#considerations-for-the-source)
   * [mottagare](#considerations-for-the-sink)
   * [Serialisering och deserialisering](#considerations-for-serialization-and-deserialization)
   * [Komprimering](#considerations-for-compression)
   * [Kolumnmappningen](#considerations-for-column-mapping)
   * [Andra överväganden](#other-considerations)

3. **Expandera konfigurationen till hela datauppsättningen.** När du är nöjd med resultat från instruktionskörningar och prestanda kan expandera du definitions- och pipeline för att täcka hela datauppsättningen.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Överväganden för lokal integration runtime

Tänk på följande om Kopieringsaktivitet körs på en lokal integration runtime kan:

**Installationsprogrammet**: Vi rekommenderar att du använder en dedikerad dator till värden integration runtime. Se [att tänka på när integration runtime med egen värd](concepts-integration-runtime.md).

**Skala ut**: En enskild logisk lokal integration runtime med en eller flera noder kan fungera flera kopiera aktivitet körs på samma gång samtidigt. Om du har behov av tunga på hybriddataförflyttning med ett stort antal samtidiga kopia aktivitetskörningar eller med en stor mängd data som ska kopieras du [skala ut den lokala integreringskörningen](create-self-hosted-integration-runtime.md#high-availability-and-scalability) att etablera fler resurser till Stärk kopia.

## <a name="considerations-for-the-source"></a>Överväganden för källan

### <a name="general"></a>Allmänt

Var noga med att det underliggande datalagringen inte är överbelastad till följd av andra arbetsbelastningar som körs på eller mot den.

Microsoft-datalager, se [övervakning och justering ämnen](#performance-reference) som är specifika för datalager. Dessa avsnitt kan hjälpa dig att förstå data store prestandaegenskaper och hur du minimera svarstider och maximera genomströmningen.

* Om du kopierar data från Blob storage till SQL Data Warehouse kan du överväga att använda PolyBase för att öka prestandan. Mer information finns i [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Om du kopierar data från HDFS till Azure Blob storage eller Azure Data Lake Store kan du använda DistCp för att öka prestanda. Mer information finns i [Använd DistCp för att kopiera data från HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Om du kopierar data från Redshift till Azure SQL Data Warehouse, Azure BLob storage eller Azure Data Lake Store kan du använda bort från MINNET för att öka prestanda. Mer information finns i [Använd INAKTIVERAS för att kopiera data från Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Filbaserat datalager

* **Genomsnittlig storlek och antalet filer**: Kopieringsaktiviteten överför en fil i taget. Med samma mängden data som ska flyttas, är det totala arbetsflödet lägre om data består av många små filer i stället för ett par stora filer på grund av fasen bootstrap för varje fil. Om det är möjligt, kombinera små filer i större filer för att få högre dataflöde.
* **Format och komprimering**: Fler sätt att förbättra prestanda finns i den [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och [överväganden för komprimering](#considerations-for-compression) avsnitt.

### <a name="relational-data-stores"></a>Relationsdata

* **Datamönster**: Din tabellschemat påverkar kopia dataflöde. En stor Radstorleken ger bättre prestanda än en liten Radstorleken att kopiera samma mängd data. Anledningen är att databasen mer effektivt kan hämta färre batchar med data som innehåller färre rader.
* **Fråga eller lagrad procedur**: Optimera logiken för frågan eller lagrad procedur som du anger i aktiviteten kopieringskälla för att hämta data mer effektivt.

## <a name="considerations-for-the-sink"></a>Överväganden för mottagaren

### <a name="general"></a>Allmänt

Var noga med att det underliggande datalagringen inte är överbelastad till följd av andra arbetsbelastningar som körs på eller mot den.

Microsoft-datalager, se [övervakning och justering ämnen](#performance-reference) som är specifika för datalager. Dessa avsnitt kan hjälpa dig att förstå data store prestandaegenskaper och hur du minimera svarstider och maximera genomströmningen.

* Om du kopierar data från alla datalager till Azure SQL Data Warehouse kan du överväga att använda PolyBase för att öka prestandan. Mer information finns i [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Om du kopierar data från HDFS till Azure Blob storage eller Azure Data Lake Store kan du använda DistCp för att öka prestanda. Mer information finns i [Använd DistCp för att kopiera data från HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Om du kopierar data från Redshift till Azure SQL Data Warehouse, Azure Blob storage eller Azure Data Lake Store kan du använda bort från MINNET för att öka prestanda. Mer information finns i [Använd INAKTIVERAS för att kopiera data från Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Filbaserat datalager

* **Kopiera beteende**: Om du kopierar data från ett annat filbaserat datalager kopieringsaktiviteten det finns tre alternativ via den **copyBehavior** egenskapen. Den bevarar hierarki, plattar ut hierarki eller sammanfogar filer. Antingen behålla eller förenkla hierarkin har lite eller ingen prestanda försämras, men Sammanfoga filer gör att prestanda försämras att öka.
* **Format och komprimering**: Fler sätt att förbättra prestanda finns i den [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och [överväganden för komprimering](#considerations-for-compression) avsnitt.

### <a name="relational-data-stores"></a>Relationsdata

* **Kopiera beteende och prestanda de**: Det finns olika sätt att skriva data till en SQL-mottagare. Läs mer i [bästa praxis för inläsning av data till Azure SQL Database](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database).

* **Datastorlek för mönstret och batch**:
  * Din tabellschemat påverkar kopia dataflöde. Om du vill kopiera samma mängden data som får en stor Radstorleken du bättre prestanda än en liten Radstorleken eftersom databasen mer effektivt kan genomföra färre batchar av data.
  * Kopieringsaktiviteten infogar data i en serie med batchar. Du kan ange antalet rader i en batch med hjälp av den **writeBatchSize** egenskapen. Om dina data har liten rader, kan du ange den **writeBatchSize** egenskap med ett högre värde kan dra nytta av lägre omkostnader för batch och högre dataflöde. Om Radstorleken på dina data är stor, var försiktig när du ökar **writeBatchSize**. Ett högt värde kan leda till en kopieringen misslyckades på grund av överbelastning i databasen.

### <a name="nosql-stores"></a>NoSQL-Arkiv

* För **tabellagring**:
  * **Partition**: Skriva data till överlagrad partitioner avsevärt försämrar prestanda. Sortera dina källdata efter partitionsnyckel så att data infogas effektivt i en partition efter en annan. Eller så kan du justera logik för att skriva data till en enda partition.

## <a name="considerations-for-serialization-and-deserialization"></a>Överväganden för serialisering och deserialisering

Serialisering och deserialisering kan inträffa när ditt indatauppsättningen eller datauppsättningen för utdata är en fil. Mer information om filformat som stöds av Kopieringsaktivitet finns i [stöds format och komprimering](supported-file-formats-and-compression-codecs.md).

**Kopiera beteende**:

* Kopiera filer mellan filbaserat datalager:
  * När både in- och utdatauppsättningar har samma eller inga filformatinställningar, av data movement service körs en *binär kopia* utan serialisering eller avserialisering. Du kan se ett högre genomflöde jämfört med scenario, där filformatinställningar källa och mottagare skiljer sig från varandra.
  * När indata och utdata datauppsättningar båda är i textformat och endast kodningen typen är olika, av data movement service kräver endast kodning konvertering. Gör den alla serialisering och deserialisering, vilket gör att prestanda försämras jämfört med en binär kopia.
  * När både in- och utdatauppsättningar har olika filformat eller olika konfigurationer som avgränsare, av data movement service deserializes källdata för att strömma, transformera och serialisera det till utdataformat som du angett. Den här åtgärden resulterar i en mycket mer betydande overhead jämfört med andra scenarier.
* När du kopierar filer till eller från ett datalager som inte är filbaserade, till exempel från en butik med filbaserad till en relationslagringsplats krävs serialisering eller avserialisering steg. Det här steget leder till betydande prestanda försämras.

**Filformatet**: Filformat som du väljer kan påverka kopieringen bättre prestanda. Avro är till exempel ett kompakt binärformat som lagrar metadata med data. Det har ett brett stöd i Hadoop-ekosystemet för bearbetning och frågor. Avro är dyrare för serialisering och deserialisering, vilket resulterar i lägre kopia genomflöde jämfört med textformat. 

Se ditt val av filformatet i hela flödet bearbetning holistiskt. Börja med:

- Vad utgör data lagras i datalager som källa eller som ska extraheras från externa system.
- Det bästa formatet för lagring, analytisk behandling och frågor.
- I vilket format ska data exporteras till dataarkiv för verktyg för rapportering och visualisering.

Ibland ett format som är optimal för Läs- och skrivprestanda kan vara ett bra val när du funderar på övergripande analysprocessen.

## <a name="considerations-for-compression"></a>Överväganden för komprimering

När din inkommande eller utgående datauppsättning är en fil kan ange du kopieringsaktiviteten att utföra komprimering eller dekomprimering eftersom den skriver data till målet. När du väljer komprimering kan du göra en kompromiss mellan indata/utdata (I/O) och CPU. Komprimera data kostnaderna extra beräkningsresurser. Men i utbyte kan det minskar nätverkets i/o och lagring. Du kan se en högre övergripande kopia dataflöde beroende på dina data.

**Codec**: Varje komprimerings-codec har fördelar. Till exempel bzip2 har den lägsta kopia dataflöden, men du får bästa Hive frågeprestanda med bzip2 eftersom du kan dela upp den för bearbetning. Den används mest ofta gzip är det mest balanserade alternativet. Välj codec som bäst passar ditt scenario för slutpunkt till slutpunkt.

**Nivå**: Du kan välja mellan två alternativ för varje komprimerings-codec: snabbaste komprimerade och optimalt komprimerad. Det snabbaste komprimerade alternativet komprimerar data så snabbt som möjligt, även om den resulterande filen inte är optimalt komprimeras. Alternativet optimalt komprimerade tillbringar mer tid på komprimering och återger en minimal mängd data. Du kan testa båda alternativen för att se vilket ger bättre prestanda i ditt fall.

**Ersättning**: Överväg att använda för att kopiera en stor mängd data mellan en lokal databas och molnet [mellanlagrad kopiering](#staged-copy) med komprimering aktiverat. Det är praktiskt att använda mellanlagring när bandbredden för företagets nätverk och dina Azure-tjänster är den begränsande faktorn och du vill att datauppsättningen för indata och utdata datauppsättningen både i okomprimerad.

## <a name="considerations-for-column-mapping"></a>Överväganden för kolumnmappningen

Du kan ange den **columnMappings** -egenskapen i en Kopieringsaktivitet som kartan alla eller en delmängd av kolumnerna indata till utdatakolumner. När av data movement service läser data från källan, behöver så utföra kolumnmappning på data innan den skriver data till mottagaren. Den här extra bearbetningen minskar kopia dataflöde.

Om ditt källdatalager är frågningsbar, till exempel om det är en relationslagringsplats som SQL Database eller SQL Server, eller om det är ett NoSQL-Arkiv som Table storage eller Azure Cosmos DB kan du push-överföra den kolumn som filtrering och sortering logik för att den **fråga** egenskapen istället för att använda kolumnmappning. På så sätt kan projektionen inträffar medan av data movement service läser data från källans datalager, där det är mycket mer effektivt.

Läs mer i [kopiera aktivitet schemamappning](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Annat att tänka på

Om storleken på data som du vill kopiera är stor, kan du justera din affärslogik att partitionera data. Du kan schemalägga Kopieringsaktivitet körs oftare för att minska storleken på data för varje kopieringsaktiviteten som körs.

Var försiktig om antalet datauppsättningar och kopiera aktiviteter som kräver Azure Data Factory för att ansluta till samma datalager samtidigt. Många samtidiga kopia jobb kan begränsa ett datalager och leda till försämrade prestanda, kopiera jobbet interna återförsök, och i vissa fall, fel vid körning.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Exempelscenario: Kopiera från en lokal SQLServer till Blob storage

**Scenario**: En pipeline är utformat för att kopiera data från en lokal SQLServer till Blob storage i CSV-format. Om du vill göra kopieringsjobbet snabbare ska CSV-filer komprimeras bzip2-format.

**Testning och analys**: Dataflödet för kopieringsaktiviteten är mindre än 2 Mbit/s, vilket är mycket långsammare än benchmark för prestanda.

**Prestandaanalys och justera**: Om du vill felsöka prestandaproblem, nu ska vi titta på hur data bearbetas och flyttas.

- **Läsa data**: Integreringskörningen öppnar en anslutning till SQL Server och skickar frågan. SQL-servern svarar genom att skicka dataströmmen till integration runtime via intranätet.
- **Serialisera och komprimera data**: Integration runtime Serialiserar dataströmmen till CSV-format och komprimerar data till en bzip2-dataström.
- **Skriva data**: Integration runtime överför bzip2 dataströmmen till Blob storage via internet.

Som du ser data bearbetas och flyttas i strömmande ordning: SQL Server > LAN > Integration runtime > WAN > Blob-lagring. Den övergripande prestandan är begränsad av minsta dataflödet i pipelinen.

![Dataflöde](./media/copy-activity-performance/case-study-pic-1.png)

En eller flera av följande faktorer kan orsaka flaskhals för prestanda:

* **Källa**: SQL-servern har låg genomströmning på grund av tunga belastningar.
* **Integration runtime med egen värd**:
  * **LAN**: Integreringskörningen är placerad är långt från SQL Server-datorn och har en långsam anslutning.
  * **Integreringskörningen**: Integreringskörningen har nått sin belastningen begränsningar om du vill utföra följande åtgärder:
    * **Serialisering**: Serialisering av data i dataströmmen till CSV-format har långsam dataflöde.
    * **Komprimering**: Du har valt en långsam komprimerings-codec, till exempel bzip2, vilket är 2,8 Mbit/s med Core i7.
  * **WAN**: Bandbredden mellan företagsnätverket och dina Azure-tjänster är låg, exempelvis T1 = 1,544 kbit/s; T2 = 6,312 kbit/s.
* **Mottagare**: BLOB-lagring har lågt dataflöde. Det här scenariot är inte troligt eftersom dess servicenivåavtal (SLA) garanterar minst 60 Mbit/s.

I det här fallet kan bzip2 datakomprimering långsammare hela pipelinen. Växla till en gzip komprimerings-codec kan underlätta den här begränsningen.

## <a name="references"></a>Referenser

Här följer prestandaövervakning och justering referenser för några av datalager som stöds:

* Azure Storage, vilket innefattar Blob storage och Table storage: [Azure Storage-skalbarhetsmål](../storage/common/storage-scalability-targets.md) och [checklista för prestanda och skalbarhet i Azure Storage](../storage/common/storage-performance-checklist.md).
* Azure SQL Database: Du kan [övervaka prestanda](../sql-database/sql-database-single-database-monitor.md) och kontrollera procentandelen Database Transaction Unit (DTU).
* Azure SQL Data Warehouse: Dess funktion mäts i Informationslagerenheter (dwu: er). Se [hantera beräkningskraft i Azure SQL Data Warehouse (översikt)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Prestandanivåer i Azure Cosmos DB](../cosmos-db/performance-levels.md).
* En lokal SQLServer: [Övervaka och finjustera prestanda](https://msdn.microsoft.com/library/ms189081.aspx).
* En lokal filserver: [Prestandajustering för filservrar](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Nästa steg
Se andra kopiera aktivitet artiklar:

- [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
- [Kopiera aktivitet schemamappning](copy-activity-schema-and-type-mapping.md)
- [Kopiera aktivitet feltolerans](copy-activity-fault-tolerance.md)

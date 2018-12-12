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
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 7602524675edbf0e3ca96c74a2aba2eac48c417b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084081"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Kopiera aktivitet prestanda- och justeringsguide
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-copy-activity-performance.md)
> * [Aktuell version](copy-activity-performance.md)


Azure Data Factory-Kopieringsaktiviteten levererar en förstklassig säker, tillförlitlig och höga prestanda för inläsning av data lösning. Det gör att du kan kopiera tiotals terabyte data varje dag på en rad olika molnet och lokala datalager. – Blixtsnabb prestanda för datainläsning är nyckeln till att se till att du kan fokusera på problemet core ”stordata”: bygga avancerade Analyslösningar och få djupa insikter från alla data.

Azure innehåller en uppsättning av företagsklass lösningar för lagring och data warehouse och Kopieringsaktivitet erbjuder en optimerad upplevelse som är lätt att göra inställningar för inläsning av data. Med bara en enda Kopieringsaktivitet, kan du åstadkomma:

* Läser in data till **Azure SQL Data Warehouse** på **1,2 Gbit/s**.
* Läser in data till **Azure Blob storage** på **1,0 GB/s**
* Läser in data till **Azure Data Lake Store** på **1,0 GB/s**

Den här artikeln beskrivs:

* [Referensnummer för prestanda](#performance-reference) för källa och mottagare datalager när du planerar ditt projekt; som stöds
* Funktioner som kan öka dataflödet kopia i olika scenarier, inklusive [integrering enheter](#data-integration-units), [parallell kopia](#parallel-copy), och [mellanlagrad kopiering](#staged-copy);
* [Prestandajusteringsvägledning](#performance-tuning-steps) på hur du ställer in prestanda- och de viktigaste faktorerna som kan påverka kopieringen bättre prestanda.

> [!NOTE]
> Om du inte är bekant med en Kopieringsaktivitet i allmänhet, se [översikt över Kopieringsaktivitet](copy-activity-overview.md) innan du läser den här artikeln.
>

## <a name="performance-reference"></a>Prestandareferens för

Som en referens, tabellen nedan visar dataflöde kopia **i Mbit/s** för de angivna käll- och mottagar-par **i en enda kopieringsaktivitetskörning** baserat på interna tester. Jämförelse, visas också hur olika inställningar för [integrering enheter](#data-integration-units) eller [lokal Integration Runtime skalbarhet](concepts-integration-runtime.md#self-hosted-integration-runtime) (flera noder) kan hjälpa på kopieringen bättre prestanda.

![Matris för prestanda](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> När kopieringsaktiviteten körs på en Azure Integration Runtime är två minimal tillåtna Data Integration enheter (kallades tidigare enheter för Dataflytt). Om inte anges, se Data Integration standardenheter som används i [integrering enheter](#data-integration-units).

Saker att Observera:

* Dataflödet beräknas med hjälp av följande formel: [storleken på data läses från källan] / [körningen av Kopieringsaktiviteten varaktighet].
* Referensnummer för prestanda i tabellen har mätas med [TPC-H](http://www.tpc.org/tpch/) datauppsättning i en enda kopieringsaktivitetskörning. Testfiler för filbaserade butiker finns flera filer med 10GB i storlek.
* I Azure datalager finns källa och mottagare i samma Azure-region.
* För hybridkopiering mellan lokala och molnbaserade datalager måste varje lokal Integration Runtime-noden körs på en dator som var avskild från datalagret med nedan specifikationen. När en enda aktivitet kördes förbrukas kopieringen endast en liten del av testdatorn CPU, minne eller bandbredd i nätverket.
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
        <td>Internet-gränssnittet: 10 Gbit/s; intranätgränssnittet: 40 Gbit/s</td>
    </tr>
    </table>


> [!TIP]
> Du kan uppnå högre dataflöde med hjälp av mer Data Integration enheter (DIU). Till exempel med 100 DIUs du kan uppnå kopiering av data från Azure Blob till Azure Data Lake Store på **1.0GBps**. Se den [integrering enheter](#data-integration-units) finns mer information om den här funktionen och scenario som stöds. 

## <a name="data-integration-units"></a>Integrering enheter

En **Data Integration enhet (DIU)** (tidigare kallat Cloud Data Movement enhet eller dmu här) är ett mått som representerar (en kombination av processor, minne och nätverksresursallokering) en enhet i Data Factory. **DIU gäller endast för [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)**, men inte [lokal Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

**Minimal Data Integration Units möjligheter för körningen av Kopieringsaktiviteten är två.** Om inte anges visas i följande tabell de standard-DIUs som används i olika kopia scenarier:

| Kopiera scenario | Standard DIUs bestäms av tjänsten |
|:--- |:--- |
| Kopiera data mellan filbaserade lager | Mellan 4 och 32 beroende på antalet och storleken på filerna. |
| Alla andra kopia-scenarier | 4 |

Om du vill åsidosätta denna standardinställning, ange ett värde för den **dataIntegrationUnits** egenskapen på följande sätt. Den **tillåtna värden** för den **dataIntegrationUnits** egenskapen är **upp till 256**. Den **faktiska antalet DIUs** att kopieringen använder vid körning är lika med eller mindre än det konfigurerade värdet, beroende på din datamönster. Information om nivå av prestanda som du kan få när du konfigurerar fler enheter för en specifik kopieringskälla och mottagare finns i den [Prestandareferens](#performance-reference).

Du kan se den faktiskt används Integration enheter för varje kopia som kör i kopieringsaktiviteten utdata när du övervakar en aktivitet som körs. Få mer detaljerad information från [kopiera aktivitetsövervakning](copy-activity-overview.md#monitoring).

> [!NOTE]
> Inställningen för den DIUs **större än 4** fungerar för närvarande endast när du **kopiera flera filer från Blob-lagring/Data Lake Storage/Amazon S3/i molnet FTP/i molnet SFTP till andra molndata lagrar.**.
>

**Exempel:**

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

Den har **viktiga** att komma ihåg att du debiteras utifrån den totala tiden för kopieringsåtgärden. Total varaktighet debiteras du för dataförflyttning är summan av varaktigheten för DIUs. Om ett kopieringsjobb brukade ta en timme med två molnenheter och så tar det nu 15 minuter med åtta molnenheter, förblir övergripande fakturan nästan samma.

## <a name="parallel-copy"></a>Parallell kopia

Du kan använda den **parallelCopies** egenskap som anger parallellitet som du vill Kopieringsaktivitet att använda. Du kan tänka på den här egenskapen som det maximala antalet trådar i Kopieringsaktiviteten som kan läsa från källan eller skriva till dina datalager för mottagare parallellt.

För varje körningen av Kopieringsaktiviteten, avgör hur många parallella kopior som ska använda för att kopiera data från källan datalagring och att sidan måldatalager lagra i Data Factory. Standardvärdet för antal parallella kopior som används beror på vilken typ av källa och mottagare som du använder:

| Kopiera scenario | Parallell kopia Standardantal bestäms av tjänsten |
| --- | --- |
| Kopiera data mellan filbaserade lager |Beror på storleken på filerna och antalet enheter för Data-Integration (DIUs) används för att kopiera data mellan två molndatalager eller den fysiska konfigurationen av lokal Integration Runtime-datorn. |
| Kopiera data från alla källans datalager till Azure Table storage |4 |
| Alla andra kopia-scenarier |1 |

[!TIP]
> När du kopierar data mellan filbaserade standardbeteendet (auto bestäms) vanligtvis ger dig bästa dataflödet. 

Kontrollera belastningen på datorer som är värdar för dina data lagras eller om du vill justera prestanda för kopia som du kan välja att åsidosätta standardvärdet och ange ett värde för den **parallelCopies** egenskapen. Värdet måste vara ett heltal större än eller lika med 1. Vid körning använder för bästa prestanda Kopieringsaktiviteten ett värde som är mindre än eller lika med värdet som du anger.

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

Saker att Observera:

* När du kopierar data mellan filbaserade den **parallelCopies** fastställa parallellitet på filnivå. Dela upp inom en enda fil skulle inträffa under automatiskt och transparent och den har utformats för att använda den bästa lämpliga segmentstorleken för en viss källa datalagertyp att läsa in data i parallella program och rätvinkliga till parallelCopies. Det faktiska antalet parallella kopior av data movement service använder för att kopieringen under körning är inte fler än antalet filer som du har. Om kopieringsbeteendet är **mergeFile**, Kopieringsaktivitet inte kan utnyttja parallellitet på filnivå.
* När du anger ett värde för den **parallelCopies** egenskapen, Överväg att belastningen ökar på dina käll- och mottagar-datalager och till lokal Integration Runtime om möjligheter Kopieringsaktivitet av den exempelvis för hybridkopiering. Detta sker särskilt när du har flera aktiviteter eller samtidiga körningar av samma aktiviteter som körs mot samma datalager. Om du märker att antingen datalager eller lokal Integration Runtime blir överbelastad till följd med belastning kan minska den **parallelCopies** värde att avlasta belastningen.
* När du kopierar data från butiker som inte är filbaserad till datalager som är filbaserade, av data movement service ignorerar den **parallelCopies** egenskapen. Även om parallellitet anges, tillämpas den inte i det här fallet.
* **parallelCopies** är rätvinkliga till **dataIntegrationUnits**. Det tidigare räknas över alla enheter för Data-integrering.

## <a name="staged-copy"></a>Mellanlagrad kopiering

När du kopierar data från källans datalager till mottagarens datalager kan du välja att använda Blob storage som en mellanliggande mellanlagringsarkivet. Mellanlagring är särskilt användbart i följande fall:

- **Du vill mata in data från olika datalager i SQL Data Warehouse via PolyBase**. SQL Data Warehouse använder PolyBase som en mekanism för stora dataflöden för att läsa in en stor mängd data till SQL Data Warehouse. Dock källdata måste vara i Blob storage eller Azure Data Lake Store och den uppfylla ytterligare kriterier. När du läser in data från ett datalager än Blob storage eller Azure Data Lake Store kan du aktivera data kopiering via tillfälliga mellanlagringsplatsen Blob-lagring. I så fall kan utför Data Factory omvandlingarna data som krävs för att säkerställa att den uppfyller kraven för PolyBase. Sedan används PolyBase för att läsa in data i SQL Data Warehouse effektivt. Mer information finns i [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Ibland kan det ta en stund att utföra en hybriddataförflyttning (det vill säga för att kopiera från ett lokalt datalager till ett datalager i molnet) över en långsam nätverksanslutning**. Du kan använda mellanlagrad kopiering för att förbättra prestanda för att komprimera data lagras lokalt så att det tar mindre tid att flytta data till det tillfälliga datalagret i molnet och sedan expandera data i mellanlagringsarkivet innan du läser in till måldatalagret.
- **Du inte vill öppna portar än port 80 och port 443 i brandväggen, på grund av företagets IT-principer**. När du kopierar data från ett lokalt datalager till en Azure SQL Database-mottagare eller en Azure SQL Data Warehouse sink måste aktivera utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företagets brandvägg. I det här scenariot mellanlagrad kopiering kan dra nytta av den lokala Integration Runtime först kopiera data till ett Blob storage för mellanlagring instans via HTTP eller HTTPS på port 443 och sedan läsa in data i SQL Database eller SQL Data Warehouse från mellanlagring för Blob storage. I det här flödet behöver du inte aktivera port 1433.

### <a name="how-staged-copy-works"></a>Hur mellanlagrad kopiering fungerar

När du aktiverar funktionen mellanlagring först data kopieras från källdatalagret till mellanlagring Blob-lagringen (ta med din egen). Därefter kopieras data från datalager för mellanlagring till datalager för mottagare. Data Factory hanterar automatiskt två steg flödet för dig. Data Factory rensar också tillfälliga data från den tillfälliga lagringen efter att dataöverföringen har slutförts.

![Mellanlagrad kopiering](media/copy-activity-performance/staged-copy.png)

När du aktiverar dataförflyttning med hjälp av en mellanlagringsarkivet kan ange du om du vill att data ska komprimeras innan du flyttar data från källans datalager till ett mellanliggande eller tillfälliga datalager och sedan expandera innan du flyttar data från en tiden eller organiserar data lagra till de mottagande datalagren.

För närvarande kan kopiera du inte data mellan två lokala datalager med hjälp av en mellanlagringsarkivet.

### <a name="configuration"></a>Konfiguration

Konfigurera den **enableStaging** inställningen i Kopieringsaktiviteten till att ange om du vill att data ska mellanlagras i Blob storage innan du läser in dem till ett måldatalager. När du ställer in **enableStaging** till `TRUE`, anger du ytterligare egenskaper som anges i nästa tabell. Om du inte har någon kan du även behöva skapa ett Azure Storage eller Storage shared access signature-länkad tjänst för mellanlagring.

| Egenskap  | Beskrivning | Standardvärde | Krävs |
| --- | --- | --- | --- |
| **enableStaging** |Ange om du vill kopiera data via en tiden mellanlagring store. |False |Nej |
| **linkedServiceName** |Ange namnet på en [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) länkad tjänst som refererar till instansen av lagring som du använder som ett tillfälligt mellanlagringsarkivet. <br/><br/> Du kan inte använda Storage med signatur för delad åtkomst för att läsa in data till SQL Data Warehouse via PolyBase. Du kan använda den i alla andra scenarier. |Gäller inte |Ja, när **enableStaging** har angetts till TRUE |
| **Sökväg** |Ange sökvägen för Blob-lagring som du vill ska innehålla den mellanlagrade data. Om du inte anger en sökväg, skapar en behållare för att lagra tillfälliga data i tjänsten. <br/><br/> Ange en sökväg endast om du använder lagring med signatur för delad åtkomst, eller du kräver tillfälliga data finnas i en viss plats. |Gäller inte |Nej |
| **enableCompression** |Anger om data ska komprimeras innan den kopieras till målet. Den här inställningen minskar mängden data som överförs. |False |Nej |

>[!NOTE]
> Om du använder mellanlagrad kopiering med komprimering aktiverat, tjänstens huvudnamn eller MSI-autentisering för länkade stöds tjänsten inte.

Här är en exempeldefinition av Kopieringsaktiviteten med egenskaper som beskrivs i tabellen ovan:

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

* När du använder debiteras mellanlagring under en molnkopieringen (kopiera data från ett molndatalager till ett annat molndatalager, båda stegen möjligheter med Azure Integration Runtime), du [summan av Kopieringstid för steg 1 och 2] x [cloud kopia Enhetspris].
* När du använder mellanlagring vid en hybridkopiering (kopiera data från ett lokalt datalager till ett molndatalager, ett steg som är drivande av lokal Integration Runtime) debiteras du för [hybrid Kopieringstid] x [Enhetspris hybrid kopia] + [cloud Kopieringstid] x [cloud Kopiera enhetspriset].

## <a name="performance-tuning-steps"></a>Prestanda justering steg

Vi rekommenderar att du gör följande för att finjustera prestanda för Data Factory-tjänsten med en Kopieringsaktivitet:

1. **Upprätta en baslinje för**. Testa din pipeline med hjälp av Kopieringsaktivitet mot ett representativt datasampel under utvecklingsfasen för. Samla in information om och prestandaegenskaper följa [kopiera aktivitetsövervakning](copy-activity-overview.md#monitoring).

2. **Diagnostisera och optimera prestanda**. Om du se prestanda inte uppfyller dina förväntningar kan behöva du identifiera flaskhalsar i prestanda. Sedan kan optimera prestanda för att ta bort eller minska effekten av flaskhalsar. En fullständig beskrivning av Prestandadiagnostik ligger utanför omfånget för den här artikeln, men här följer några vanliga överväganden:

   * Prestandafunktioner:
     * [Parallell kopia](#parallel-copy)
     * [Integrering enheter](#data-integration-units)
     * [Mellanlagrad kopiering](#staged-copy)
     * [Lokal Integration Runtime-skalbarhet](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Lokal Integration Runtime](#considerations-for-self-hosted-integration-runtime)
   * [Källa](#considerations-for-the-source)
   * [mottagare](#considerations-for-the-sink)
   * [Serialisering och deserialisering](#considerations-for-serialization-and-deserialization)
   * [Komprimering](#considerations-for-compression)
   * [Kolumnmappningen](#considerations-for-column-mapping)
   * [Andra överväganden](#other-considerations)

3. **Expandera konfigurationen till hela din datauppsättning**. När du är nöjd med resultat från instruktionskörningar och prestanda kan expandera du definitions- och pipeline för att täcka hela din datauppsättning.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Överväganden för lokal Integration Runtime

Om Kopieringsaktivitet körs på en lokal Integration Runtime, Tänk på följande:

**Installationsprogrammet**: Vi rekommenderar att du använder en dedikerad dator till Integration Runtime-värden. Se [överväganden för att använda lokal Integration Runtime](concepts-integration-runtime.md).

**Skala ut**: en enskild logisk lokal Integration Runtime med en eller flera noder kan hantera flera Kopieringsaktivitet körs på samma gång samtidigt. Om du har behov av tunga på hybriddataförflyttning med stort antal samtidiga kopia aktivitetskörningar eller med stora mängder data som ska kopieras, Överväg att [skala ut lokal Integration Runtime](create-self-hosted-integration-runtime.md#high-availability-and-scalability) för att etablera flera resursen Stärk kopia.

## <a name="considerations-for-the-source"></a>Överväganden för källan

### <a name="general"></a>Allmänt

Var noga med att det underliggande datalagringen inte bli överhopade av andra arbetsbelastningar som körs på eller mot den.

Microsoft-datalager, se [övervakning och justering ämnen](#performance-reference) som är specifika för datalager och hjälper dig att se data lagra prestandaegenskaper, minimera svarstider och maximera genomströmningen.

* Om du kopierar data **från Blob storage till SQL Data Warehouse**, Överväg att använda **PolyBase** att öka prestanda. Se [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) mer information.
* Om du kopierar data **från HDFS till Azure Blob-/ Azure Data Lake Store**, Överväg att använda **DistCp** att öka prestanda. Se [Använd DistCp för att kopiera data från HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) mer information.
* Om du kopierar data **från Redshift till Azure SQL Data Warehouse/Azure BLob-/ Azure Data Lake Store**, Överväg att använda **INAKTIVERAS** att öka prestanda. Se [Använd INAKTIVERAS för att kopiera data från Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) mer information.

### <a name="file-based-data-stores"></a>Filbaserat datalager

* **Genomsnittlig storlek och antalet filer**: Kopieringsaktivitet överför en fil i taget. Med samma mängden data som ska flyttas, är det totala arbetsflödet lägre om data består av många små filer i stället för ett par stora filer på grund av fasen bootstrap för varje fil. Om det är möjligt, kombinera därför små filer i större filer för att få högre dataflöde.
* **Format och komprimering**: fler sätt att förbättra prestanda finns i den [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och [överväganden för komprimering](#considerations-for-compression) avsnitt.

### <a name="relational-data-stores"></a>Relationsdata

* **Datamönster**: din tabellschemat påverkar kopia dataflöde. En stor Radstorleken ger dig en bättre prestanda än små Radstorleken att kopiera samma mängd data. Anledningen är att databasen mer effektivt kan hämta färre batchar med data som innehåller färre rader.
* **Fråga eller lagrad procedur**: Optimera logiken för frågan eller lagrad procedur som du anger i Kopieringsaktiviteten källan för att hämta data mer effektivt.

## <a name="considerations-for-the-sink"></a>Överväganden för mottagaren

### <a name="general"></a>Allmänt

Var noga med att det underliggande datalagringen inte bli överhopade av andra arbetsbelastningar som körs på eller mot den.

Microsoft-datalager, finns i [övervakning och justering ämnen](#performance-reference) som är specifika för datalager. Dessa avsnitt kan hjälpa dig att förstå data store prestandaegenskaper och hur du minimera svarstider och maximera genomströmningen.

* Om du kopierar data **från Blob storage till SQL Data Warehouse**, Överväg att använda **PolyBase** att öka prestanda. Se [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) mer information.
* Om du kopierar data **från HDFS till Azure Blob-/ Azure Data Lake Store**, Överväg att använda **DistCp** att öka prestanda. Se [Använd DistCp för att kopiera data från HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) mer information.
* Om du kopierar data **från Redshift till Azure SQL Data Warehouse/Azure BLob-/ Azure Data Lake Store**, Överväg att använda **INAKTIVERAS** att öka prestanda. Se [Använd INAKTIVERAS för att kopiera data från Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) mer information.

### <a name="file-based-data-stores"></a>Filbaserat datalager

* **Kopiera beteende**: Om du kopierar data från ett annat filbaserat datalager Kopieringsaktiviteten har tre alternativ via den **copyBehavior** egenskapen. Den bevarar hierarki, plattar ut hierarki eller sammanfogar filer. Antingen behålla eller förenkla hierarkin har lite eller ingen prestanda försämras, men Sammanfoga filer gör att prestanda försämras att öka.
* **Format och komprimering**: finns i den [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och [överväganden för komprimering](#considerations-for-compression) avsnitt fler sätt att förbättra prestanda.

### <a name="relational-data-stores"></a>Relationsdata

* **Kopiera beteende**: beroende på vilka egenskaper som du har angett för **sqlSink**, Kopieringsaktivitet skriver data till måldatabasen på olika sätt.
  * Som standard läggs data movement service använder API: et Bulk Copy att infoga data i läge, som ger bästa prestanda.
  * Om du konfigurerar en lagrad procedur i sink gäller databasen en datarad samtidigt i stället för som en massinläsning. Prestanda försämras betydligt. Om datauppsättningen är stor, när så är tillämpligt, Överväg att byta till den **preCopyScript** egenskapen.
  * Om du konfigurerar den **preCopyScript** egenskapen för varje Kopieringsaktiviteten kör tjänsten utlöser skriptet och sedan använder du Bulk Copy API och infoga data. Om du vill skriva över hela tabellen med den senaste informationen, kan du till exempel ange ett skript för att först ta bort alla poster innan massinläsning nya data från källan.
* **Datastorlek för mönstret och batch**:
  * Din tabellschemat påverkar kopia dataflöde. Om du vill kopiera samma mängden data som får en stor Radstorleken du bättre prestanda än en liten Radstorleken eftersom databasen mer effektivt kan genomföra färre batchar av data.
  * Kopieringsaktivitet infogar data i en serie med batchar. Du kan ange antalet rader i en batch med hjälp av den **writeBatchSize** egenskapen. Om dina data har liten rader, kan du ange den **writeBatchSize** egenskap med ett högre värde kan dra nytta av lägre omkostnader för batch och högre dataflöde. Om Radstorleken på dina data är stor, var försiktig när du ökar **writeBatchSize**. Ett högt värde kan leda till en kopieringen misslyckades på grund av överbelastning i databasen.

### <a name="nosql-stores"></a>NoSQL-Arkiv

* För **tabellagring**:
  * **Partition**: skriva data till överlagrad partitioner avsevärt försämrar prestanda. Sortera dina källdata efter partitionsnyckel så att data infogas effektivt i en partition efter en annan eller ändra logik för att skriva data till en enda partition.

## <a name="considerations-for-serialization-and-deserialization"></a>Överväganden för serialisering och deserialisering

Serialisering och deserialisering kan inträffa när ditt inkommande datauppsättning eller en datauppsättning för utdata är en fil. Se [stöds format och komprimering](supported-file-formats-and-compression-codecs.md) med information om filformat som stöds av Kopieringsaktivitet.

**Kopiera beteende**:

* Kopiera filer mellan filbaserat datalager:
  * När in- och utdata datauppsättningar båda har samma eller inga filformatinställningar, av data movement service körs en **binär kopia** utan serialisering eller avserialisering. Du kan se ett högre genomflöde jämfört med scenario, där filformatinställningar källa och mottagare skiljer sig från varandra.
  * När indata och utdata datauppsättningar båda är i textformat och endast kodningen typen är olika, av data movement service kräver endast kodning konvertering. Gör den alla serialisering och deserialisering, vilket gör att prestanda försämras jämfört med en binär kopia.
  * När in- och utdata datauppsättningar båda har olika filformat eller olika konfigurationer som avgränsare, av data movement service deserializes källdata för att strömma, transformera och serialisera det till utdataformat som du angett. Den här åtgärden resulterar i en mycket mer betydande overhead jämfört med andra scenarier.
* När du kopierar filer till/från ett datalager som inte är filbaserade (till exempel från store filbaserade som du kan använda för att en relationslagringsplats) krävs serialisering eller avserialisering steg. Det här steget leder till betydande prestanda försämras.

**Filformatet**: filformatet som du väljer kan påverka kopieringen bättre prestanda. Avro är till exempel ett kompakt binärformat som lagrar metadata med data. Det har ett brett stöd i Hadoop-ekosystemet för bearbetning och frågor. Avro är dock dyrare för serialisering och deserialisering, vilket resulterar i lägre kopia genomflöde jämfört med textformat. Se ditt val av filformatet i hela flödet bearbetning holistiskt. Börja med vilken form data lagras i källdatalagren eller som ska extraheras från externa system. Det bästa formatet för lagring, analytisk behandling och fråga; och i vilket format data ska exporteras till dataarkiv för verktyg för rapportering och visualisering. Ibland ett format som är optimal för Läs- och skrivprestanda kan vara ett bra val när du funderar på övergripande analysprocessen.

## <a name="considerations-for-compression"></a>Överväganden för komprimering

När datauppsättningen inkommande eller utgående är en fil kan ange du Kopieringsaktivitet för att utföra komprimering eller dekomprimering eftersom den skriver data till målet. När du väljer komprimering kan du göra en kompromiss mellan indata/utdata (I/O) och CPU. Komprimera data kostnaderna extra beräkningsresurser. Men i utbyte kan det minskar nätverkets i/o och lagring. Beroende på dina data, kan du se en högre övergripande kopia dataflöde.

**Codec**: varje komprimerings-codec har sina fördelar. Till exempel bzip2 har den lägsta kopia dataflöden, men du får bästa Hive frågeprestanda med bzip2 eftersom du kan dela upp den för bearbetning. Gzip är det mest balanserade alternativet, och det används oftast. Välj codec som bäst passar ditt scenario för slutpunkt till slutpunkt.

**Nivå**: du kan välja mellan två alternativ för varje komprimerings-codec: snabbaste komprimerade och optimalt komprimerad. Det snabbaste komprimerade alternativet komprimerar data så snabbt som möjligt, även om den resulterande filen inte är optimalt komprimerad. Alternativet optimalt komprimerade tillbringar mer tid på komprimering och återger en minimal mängd data. Du kan testa båda alternativen för att se vilket ger bättre prestanda i ditt fall.

**Ersättning**: Överväg att använda för att kopiera en stor mängd data mellan en lokal databas och molnet [mellanlagrad kopiering](#staged-copy) med komprimering aktiverat. Det är praktiskt att använda mellanlagring när bandbredden för företagets nätverk och dina Azure-tjänster är den begränsande faktorn och du vill att den inkommande datauppsättningen och datauppsättningen för utdata både i okomprimerade form.

## <a name="considerations-for-column-mapping"></a>Överväganden för kolumnmappningen

Du kan ange den **columnMappings** egenskapen i Kopieringsaktiviteten till kartan alla eller en delmängd av kolumnerna indata till utdatakolumner. När av data movement service läser data från källan, behöver så utföra kolumnmappning på data innan den skriver data till mottagaren. Den här extra bearbetningen minskar kopia dataflöde.

Om ditt källdatalager är frågningsbar, till exempel om det är en relationslagringsplats som SQL Database eller SQL Server, eller om det är ett NoSQL-Arkiv som Table storage eller Azure Cosmos DB kan du push-överföra den kolumn som filtrering och sortering logik för att den **fråga** egenskapen istället för att använda kolumnmappning. På så sätt kan projektionen inträffar medan av data movement service läser data från källans datalager, där det är mycket mer effektivt.

Läs mer i [Kopieringsaktiviteten schemamappning](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Annat att tänka på

Om storleken på data som du vill kopiera är stor, kan du justera affärslogiken för att ytterligare partitionera data och schemalägga Kopieringsaktivitet körs oftare för att minska storleken på data för varje körningen av Kopieringsaktiviteten.

Var försiktig antalet datauppsättningar och kopieringsaktiviteter som kräver Data Factory för att ansluta till samma datalager samtidigt. Många samtidiga kopia jobb kan begränsa ett datalager och leda till försämrade prestanda, kopiera jobbet interna återförsök, och i vissa fall, fel vid körning.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Exempelscenario: kopiera från en lokal SQL Server till Blob storage

**Scenariot**: en pipeline är utformat för att kopiera data från en lokal SQL Server till Blob storage i CSV-format. Om du vill göra kopieringsjobbet snabbare ska CSV-filer komprimeras bzip2-format.

**Testning och analys**: dataflödet för Kopieringsaktiviteten är mindre än 2 Mbit/s, vilket är mycket långsammare än benchmark för prestanda.

**Prestandaanalys och justera**: Om du vill felsöka prestandaproblem, ska vi titta på hur data bearbetas och flyttas.

1. **Läsa data**: Integration runtime öppnar en anslutning till SQL Server och skickar frågan. SQL-servern svarar genom att skicka dataströmmen till integreringskörning via intranätet.
2. **Serialisera och komprimera data**: Integration runtime Serialiserar dataströmmen till CSV-format och komprimerar data till en bzip2-dataström.
3. **Skriva data**: Integration runtime överför bzip2 dataströmmen till Blob storage via Internet.

Som du ser data håller på att behandlas och flyttas i strömmande ordning: SQL Server > LAN > Integration runtime > WAN > Blob-lagring. **Den övergripande prestandan är begränsad av minsta dataflödet i pipelinen**.

![Dataflöde](./media/copy-activity-performance/case-study-pic-1.png)

En eller flera av följande faktorer kan orsaka flaskhals för prestanda:

* **Källan**: SQL-servern har låg genomströmning på grund av tunga belastningar.
* **Integration Runtime med egen värd**:
  * **LAN**: Integration runtime finns är långt från SQL Server-datorn och har en långsam anslutning.
  * **Integreringskörningen**: Integration runtime har nått sin belastningen begränsningar om du vill utföra följande åtgärder:
    * **Serialisering**: serialisering av data i dataströmmen till CSV-format har långsam dataflöde.
    * **Komprimering**: du har valt en långsam komprimerings-codec (till exempel bzip2, vilket är 2,8 Mbit/s med Core i7).
  * **WAN**: bandbredden mellan företagsnätverket och dina Azure-tjänster är låg (till exempel T1 = 1,544 kbit/s; T2 = 6,312 kbit/s).
* **Mottagare**: Blob storage har lågt dataflöde. (Det här scenariot är inte troligt eftersom dess serviceavtalsgarantier minst 60 Mbit/s.)

I det här fallet kan bzip2 datakomprimering långsammare hela pipelinen. Växla till en gzip komprimerings-codec kan underlätta den här begränsningen.

## <a name="reference"></a>Referens

Här är prestandaövervakning och justering referenser för några av datalager som stöds:

* Azure Storage (inklusive Blob-lagring och tabellagring): [skalbarhetsmål för Azure Storage](../storage/common/storage-scalability-targets.md) och [checklista för prestanda och skalbarhet i Azure Storage](../storage/common/storage-performance-checklist.md)
* Azure SQL Database: Du kan [övervaka prestanda](../sql-database/sql-database-single-database-monitor.md) och kontrollera database transaction unit (DTU) procent
* Azure SQL Data Warehouse: Sin förmåga mäts i informationslagerenheter (dwu: er); Se [hantera beräkningskraft i Azure SQL Data Warehouse (översikt)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [prestandanivåer i Azure Cosmos DB](../cosmos-db/performance-levels.md)
* En lokal SQL Server: [övervaka och finjustera prestanda](https://msdn.microsoft.com/library/ms189081.aspx)
* En lokal filserver: [prestandajustering för filservrar](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>Nästa steg
Se de andra artiklarna i Kopieringsaktiviteten:

- [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
- [Kopiera aktivitet schemamappning](copy-activity-schema-and-type-mapping.md)
- [Kopiera aktivitet feltolerans](copy-activity-fault-tolerance.md)

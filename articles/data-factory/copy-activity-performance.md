---
title: Prestanda-och justerings guiden för kopierings aktiviteter i Azure Data Factory | Microsoft Docs
description: Lär dig mer om viktiga faktorer som påverkar prestanda för data förflyttning i Azure Data Factory när du använder kopierings aktiviteten.
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
ms.openlocfilehash: d8ce0a4f6bacdd1c8c858d474e6f3957a23c6357
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967365"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Prestanda-och justerings guide för kopierings aktivitet
> [!div class="op_single_selector" title1="Välj den version av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-copy-activity-performance.md)
> * [Aktuell version](copy-activity-performance.md)


Kopierings aktiviteten Azure Data Factory ger en första klass säker, tillförlitlig och högpresterande data inläsnings lösning. Du kan använda den för att kopiera flera terabyte data varje dag i flera olika moln-och lokala data lager. Snabba data inläsnings prestanda är nyckeln för att säkerställa att du kan fokusera på viktiga problem med stor data: skapa avancerade analys lösningar och få djupgående insikter från alla dessa data.

Azure tillhandahåller en uppsättning data lagrings-och informations lager lösningar i företags klass. Kopierings aktiviteten ger en mycket optimerad data inläsnings upplevelse som är enkel att konfigurera och konfigurera. Med en enda kopierings aktivitet kan du läsa in data i:

* Azure SQL Data Warehouse 1,2 Gbit/s.
* Azure Blob Storage med 1,0 Gbit/s.
* Azure Data Lake Store 1,0 Gbit/s.

Den här artikeln beskrivs:

* [Prestanda referens nummer](#performance-reference) för käll-och mottagar data lager som stöds och som hjälper dig att planera ditt projekt.
* Funktioner som kan förbättra kopierings flödet i olika scenarier, bland annat [data integrerings enheter](#data-integration-units) (DIUs), [parallell kopiering](#parallel-copy)och [mellanlagrad kopiering](#staged-copy).
* [Vägledning för prestanda justering](#performance-tuning-steps) om hur du finjusterar prestanda och viktiga faktorer som kan påverka kopierings prestanda.

> [!NOTE]
> Om du inte är bekant med kopierings aktiviteten i allmänhet kan du läsa [översikten kopiera aktivitet](copy-activity-overview.md) innan du läser den här artikeln.
>

## <a name="performance-reference"></a>Prestandareferens för

Som referens visar följande tabell det kopierade data flödes numret i Mbit/s för den aktuella käll-och mottagar paret i en enda kopierings aktivitet, baserat på intern testning. För jämförelse visar det också hur olika inställningar för [data integrerings enheter](#data-integration-units) eller [skalbarhet för egen värd för integration runtime](concepts-integration-runtime.md#self-hosted-integration-runtime) (flera noder) kan hjälpa till med kopierings prestanda.

![Matris för prestanda](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> När kopierings aktiviteten körs på en Azure integration runtime är den minsta tillåtna data integrerings enheten (tidigare kallad data förflyttnings enhet) två. Om inget värde anges, se standard data integrerings enheterna som används i [data integrerings enheter](#data-integration-units).

**Poäng till Anmärkning:**

* Data flödet beräknas med hjälp av följande formel: [storlek på data som läses från källa]/[varaktighet för kopierings aktivitets körning].
* Prestanda referens numren i tabellen mättes med hjälp av en [TPC-H-](http://www.tpc.org/tpch/) datauppsättning i en enda kopierings aktivitets körning. Testfiler för filbaserade arkiv är flera filer med en storlek på 10 GB.
* I Azure datalager finns källa och mottagare i samma Azure-region.
* För Hybrid kopiering mellan lokala och molnbaserade data lager kördes varje lokal integration runtime-nod på en dator som var skild från data lagringen med följande specifikation. När en enda aktivitet kördes förbrukas kopieringen endast en liten del av testdatorn CPU, minne eller bandbredd i nätverket.
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
        <td>Internet gränssnitt: 10 Gbit/s; intranäts gränssnitt: 40 Gbit/s</td>
    </tr>
    </table>


> [!TIP]
> Du kan få högre genomflöde genom att använda fler DIUs. Med 100 DIUs kan du till exempel kopiera data från Azure Blob Storage till Azure Data Lake Store 1,0 Gbit/s. Mer information om den här funktionen och det scenario som stöds finns i avsnittet [data integrerings enheter](#data-integration-units) . 

## <a name="data-integration-units"></a>Data integrerings enheter

En data integrerings enhet är ett mått som representerar styrkan (en kombination av processor, minne och tilldelning av nätverks resurser) för en enskild enhet i Azure Data Factory. Data integrerings enheten gäller endast för [Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime), men inte för [integration runtime med egen värd](concepts-integration-runtime.md#self-hosted-integration-runtime).

Den minsta DIUs som gör det möjligt att köra kopierings aktiviteten är två. Om inte anges visas i följande tabell de standard-DIUs som används i olika kopia scenarier:

| Kopiera scenario | Standard DIUs bestäms av tjänsten |
|:--- |:--- |
| Kopiera data mellan filbaserade lager | Mellan 4 och 32 beroende på antalet och storleken på filerna |
| Kopiera data till Azure SQL Database eller Azure Cosmos DB |Mellan 4 och 16 beroende på Azure SQL Database mottagarens eller Cosmos DBs nivå (antal DTU: er/ru: er) |
| Alla andra kopierings scenarier | 4 |

Om du vill åsidosätta denna standardinställning, ange ett värde för den **dataIntegrationUnits** egenskapen på följande sätt. De *tillåtna värdena* för egenskapen **dataIntegrationUnits** är upp till 256. Den *faktiska antalet DIUs* att kopieringen använder vid körning är lika med eller mindre än det konfigurerade värdet, beroende på din datamönster. Information om nivå av prestanda som du kan få när du konfigurerar fler enheter för en specifik kopieringskälla och mottagare finns i den [Prestandareferens](#performance-reference).

Du kan se DIUs som används för varje kopierings körning i kopierings aktivitetens utdata när du övervakar en aktivitets körning. Mer information finns i avsnittet [Kopiera aktivitets övervakning](copy-activity-overview.md#monitoring).

> [!NOTE]
> Inställningen av DIUs som är större än fyra gäller för närvarande endast när du kopierar flera filer från Azure Storage, Azure Data Lake Storage, Amazon S3, Google Cloud Storage, Cloud FTP eller Cloud SFTP till andra moln data lager.
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

Kom ihåg att du debiteras utifrån den totala tiden för kopierings åtgärden. Den totala varaktigheten som du debiteras för data förflyttning är summan av varaktigheten för DIUs. Om ett kopieringsjobb brukade ta en timme med två molnenheter och så tar det nu 15 minuter med åtta molnenheter, förblir övergripande fakturan nästan samma.

## <a name="parallel-copy"></a>Parallell kopia

Du kan använda egenskapen **parallelCopies** för att ange den parallellitet som du vill att kopierings aktiviteten ska använda. Du kan tänka på den här egenskapen som det maximala antalet trådar i kopierings aktiviteten som kan läsa från din källa eller skriva till dina mottagar data lager parallellt.

För varje kopierings aktivitets körning fastställer Azure Data Factory antalet parallella kopior som ska användas för att kopiera data från käll data lagret och till mål data lagret. Standard antalet parallella kopior som används beror på vilken typ av källa och mottagare du använder.

| Kopiera scenario | Parallell kopia Standardantal bestäms av tjänsten |
| --- | --- |
| Kopiera data mellan filbaserade lager |Beror på storleken på filerna och antalet DIUs som används för att kopiera data mellan två moln data lager eller den fysiska konfigurationen för den lokala integration runtime-datorn. |
| Kopiera data från alla käll arkiv till Azure Table Storage |4 |
| Alla andra kopia-scenarier |1 |

> [!TIP]
> När du kopierar data mellan filbaserade butiker ger standard beteendet oftast det bästa data flödet. Standard beteendet bestäms automatiskt baserat på ditt käll fils mönster.

Om du vill styra belastningen på datorer som är värdar för dina data lager, eller om du vill justera kopierings prestanda, kan du åsidosätta standardvärdet och ange ett värde för egenskapen **parallelCopies** . Värdet måste vara ett heltal större än eller lika med 1. Vid körning, för bästa prestanda, använder kopierings aktiviteten ett värde som är mindre än eller lika med det värde som du anger.

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

**Poäng till Anmärkning:**

* När du kopierar data mellan filbaserade lager, bestämmer **parallelCopies** parallellt på filnivå. Delningen i en enskild fil sker under automatiskt och transparent. Den är utformad för att använda den bästa lämpliga segment storleken för en specifik käll data lager typ för att läsa in data parallellt och rätvinkligt till **parallelCopies**. Det faktiska antalet parallella kopior av data movement service använder för att kopieringen under körning är inte fler än antalet filer som du har. Om kopierings beteendet är **mergeFile**kan kopierings aktiviteten inte dra nytta av Parallel på filnivå.
* När du kopierar data från butiker som inte är filbaserade (förutom [Oracle](connector-oracle.md#oracle-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)och [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) Connector som källa med data partitionering aktiverat) till butiker som är filbaserade, tjänsten för data förflyttning ignorerar egenskapen **parallelCopies** . Även om parallellitet anges, tillämpas den inte i det här fallet.
* Egenskapen **parallelCopies** är rätvinklig till **dataIntegrationUnits**. Det tidigare räknas över alla enheter för Data-integrering.
* När du anger ett värde för egenskapen **parallelCopies** bör du ta hänsyn till belastnings ökningen på källan och mottagar data lager. Tänk också på belastnings ökningen till den egna värdbaserade integrerings körningen om kopierings aktiviteten har befogenheter av den, till exempel för Hybrid kopiering. Den här belastnings ökningen sker särskilt när du har flera aktiviteter eller samtidiga körningar av samma aktiviteter som körs mot samma data lager. Om du ser att antingen data lagret eller den egen värdbaserade integrerings körningen är överbelastad, minskar du **parallelCopies** -värdet för att avlasta belastningen.

## <a name="staged-copy"></a>Mellanlagrad kopiering

När du kopierar data från källans datalager till mottagarens datalager kan du välja att använda Blob storage som en mellanliggande mellanlagringsarkivet. Mellanlagring är särskilt användbart i följande fall:

- **Du vill mata in data från olika data lager i SQL Data Warehouse via PolyBase.** SQL Data Warehouse använder PolyBase som en mekanism för stora dataflöden för att läsa in en stor mängd data till SQL Data Warehouse. Källdata måste finnas i Blob Storage eller Azure Data Lake Store, och det måste uppfylla ytterligare kriterier. När du läser in data från ett datalager än Blob storage eller Azure Data Lake Store kan du aktivera data kopiering via tillfälliga mellanlagringsplatsen Blob-lagring. I så fall utför Azure Data Factory nödvändiga data transformationer för att säkerställa att de uppfyller kraven för PolyBase. Sedan används PolyBase för att läsa in data i SQL Data Warehouse effektivt. Mer information finns i [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Ibland tar det en stund att utföra en hybrid data förflyttning (det vill säga kopiera från ett lokalt data lager till ett moln data lager) över en långsam nätverks anslutning.** Du kan förbättra prestanda genom att använda mellanlagrad kopia för att komprimera data lokalt så att det tar mindre tid att flytta data till lagrings data lagret i molnet. Sedan kan du expandera data i mellanlagrings platsen innan du läser in i mål data lagret.
- **Du vill inte öppna andra portar än port 80 och port 443 i brand väggen på grund av företagets IT-principer.** När du kopierar data från ett lokalt datalager till en Azure SQL Database-mottagare eller en Azure SQL Data Warehouse sink måste aktivera utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företagets brandvägg. I det här scenariot kan mellanlagrad kopiering dra nytta av den lokala integrerings körningen för att först kopiera data till en mellanlagringsplats för Blob Storage via HTTP eller HTTPS på port 443. Sedan kan den läsa in data i SQL Database eller SQL Data Warehouse från mellanlagring av blob-lagring. I det här flödet behöver du inte aktivera port 1433.

### <a name="how-staged-copy-works"></a>Hur mellanlagrad kopiering fungerar

När du aktiverar funktionen mellanlagring först data kopieras från källdatalagret till mellanlagring Blob-lagringen (ta med din egen). Därefter kopieras data från datalager för mellanlagring till datalager för mottagare. Azure Data Factory hanterar automatiskt flödet i två steg åt dig. Azure Data Factory rensar också tillfälliga data från mellanlagringen när data förflyttningen är klar.

![Mellanlagrad kopiering](media/copy-activity-performance/staged-copy.png)

När du aktiverar data förflyttning med hjälp av ett mellanlagrings lager, kan du ange om du vill att data ska komprimeras innan du flyttar data från käll data lagret till ett interimistiskt eller mellanlagrat data lager och sedan expanderar innan du flyttar data från ett interimistiskt eller mellanlagrat dat en butik till data lagret för mottagare.

För närvarande kan du inte kopiera data mellan två data lager som är anslutna via en annan egen värd, varken med eller utan mellanlagrad kopia. I sådana fall kan du konfigurera två explicit länknings aktivitet som ska kopieras från källa till mellanlagring och sedan från mellanlagring till mottagare.

### <a name="configuration"></a>Konfiguration

Konfigurera inställningen **enableStaging** i kopierings aktiviteten och ange om du vill att data ska mellanlagras i Blob Storage innan du läser in dem i ett mål data lager. När du ställer in enableStaging `TRUE`på anger du ytterligare egenskaper som anges i följande tabell. Du måste också skapa en signatur för delad åtkomst för Azure Storage eller lagrings plats för delad åtkomst för mellanlagring om du inte har någon.

| Egenskap | Beskrivning | Standardvärde | Obligatorisk |
| --- | --- | --- | --- |
| enableStaging |Ange om du vill kopiera data via en tiden mellanlagring store. |False |Nej |
| linkedServiceName |Ange namnet på en [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) länkad tjänst som refererar till instansen av lagring som du använder som ett tillfälligt mellanlagringsarkivet. <br/><br/> Du kan inte använda Storage med en signatur för delad åtkomst för att läsa in data i SQL Data Warehouse via PolyBase. Du kan använda den i alla andra scenarier. |Gäller inte |Ja, när **enableStaging** har angetts till TRUE |
| path |Ange sökvägen för Blob-lagring som du vill ska innehålla den mellanlagrade data. Om du inte anger en sökväg skapar tjänsten en behållare för att lagra temporära data. <br/><br/> Ange en sökväg endast om du använder lagring med signatur för delad åtkomst, eller du kräver tillfälliga data finnas i en viss plats. |Gäller inte |Nej |
| enableCompression |Anger om data ska komprimeras innan de kopieras till målet. Den här inställningen minskar mängden data som överförs. |False |Nej |

>[!NOTE]
> Om du använder mellanlagrad kopiering med komprimering aktive rad stöds inte tjänstens huvud namn eller MSI-autentisering för den länkade Blob-tjänsten.

Här är en exempel definition av en kopierings aktivitet med de egenskaper som beskrivs i föregående tabell:

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

Du debiteras baserat på två steg: kopiera varaktighet och kopierings typ.

* När du använder mellanlagring under en moln kopia, som kopierar data från ett moln data lager till ett annat moln data lager, båda stegen som du har fått av Azure integration runtime debiteras du [summan av kopierings tiden för steg 1 och steg 2] x [Cloud Copy Unit Price].
* När du använder mellanlagring under en hybrid kopiering, som kopierar data från ett lokalt data lager till ett moln data lager, en fas som har en egen värd för integration runtime debiteras du för [hybrid kopieringens varaktighet] x [hybrid kopiera enhets pris] + [moln kopieringens varaktighet] x [Cloud Copy Unit-pris].

## <a name="performance-tuning-steps"></a>Prestanda justering steg

Följ dessa steg för att justera prestandan för din Azure Data Factory-tjänst med kopierings aktiviteten.

1. **Upprätta en bas linje.** Under utvecklings fasen testar du din pipeline med hjälp av kopierings aktiviteten mot ett representativt data exempel. Samla in körnings information och prestanda egenskaper efter [Kopiera aktivitets övervakning](copy-activity-overview.md#monitoring).

2. **Diagnostisera och optimera prestanda.** Om den prestanda du anser inte uppfyller dina förväntningar kan du identifiera Flask halsar i prestanda. Sedan kan optimera prestanda för att ta bort eller minska effekten av flaskhalsar.

    I vissa fall, när du kör en kopierings aktivitet i Azure Data Factory, visas ett meddelande om "prestanda justerings tips" ovanpå [sidan Kopiera aktivitets övervakning](copy-activity-overview.md#monitor-visually), som du ser i följande exempel. Meddelandet anger att du har identifierat Flask halsen som identifierades för den angivna kopierings körningen. Du får också information om vad du kan ändra för att förstärka kopieringen av kopierings data. Tips för prestanda justering ger för närvarande förslag som:

    - Använd PolyBase när du kopierar data till Azure SQL Data Warehouse.
    - Öka Azure Cosmos DB enheter för programbegäran eller Azure SQL Database DTU: er (databas data flödes enheter) när resursen på data lagrings sidan är Flask halsen.
    - Ta bort den onödiga mellanlagrade kopian.

    Reglerna för prestanda justering kommer även att berikas.

    **Exempel: Kopiera till Azure SQL Database med prestanda justerings tips**

    I det här exemplet under en kopierings körning Azure Data Factory meddelanden om att mottagaren Azure SQL Database når hög DTU-användning, vilket gör att Skriv åtgärderna blir långsammare. Förslaget är att öka Azure SQL Database nivån med fler DTU: er. 

    ![Kopiera övervakning med prestanda justerings tips](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    Dessutom är följande några vanliga överväganden. En fullständig beskrivning av prestanda diagnosen ligger utanför omfånget för den här artikeln.

   * Prestandafunktioner:
     * [Parallell kopia](#parallel-copy)
     * [Dataintegrationsenheter](#data-integration-units)
     * [Mellanlagrad kopiering](#staged-copy)
     * [Skalbarhet för integration runtime med egen värd](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Lokal Integration Runtime](#considerations-for-self-hosted-integration-runtime)
   * [Källa](#considerations-for-the-source)
   * [mottagare](#considerations-for-the-sink)
   * [Serialisering och deserialisering](#considerations-for-serialization-and-deserialization)
   * [Komprimering](#considerations-for-compression)
   * [Kolumnmappningen](#considerations-for-column-mapping)
   * [Andra överväganden](#other-considerations)

3. **Expandera konfigurationen till hela data uppsättningen.** När du är nöjd med körnings resultatet och prestandan kan du expandera definitionen och pipelinen för att hantera hela data uppsättningen.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Överväganden för integration runtime med egen värd

Tänk på följande om kopierings aktiviteten körs på en egen värd för integration Runtime:

**Installations program**: Vi rekommenderar att du använder en dedikerad dator som värd för integration Runtime. Se [överväganden vid användning av integration runtime med egen värd](concepts-integration-runtime.md).

**Skala ut**: En enda logisk integrerings körning med egen värd med en eller flera noder kan betjäna flera kopierings aktiviteter samtidigt samtidigt. Om du har hög behov av hybrid data förflyttning antingen med ett stort antal samtidiga kopierings aktiviteter körs eller med en stor mängd data som ska kopieras bör du överväga att [skala ut den](create-self-hosted-integration-runtime.md#high-availability-and-scalability) lokala integrerings körningen för att etablera fler resurser för att kunna kopiera.

## <a name="considerations-for-the-source"></a>Överväganden för källan

### <a name="general"></a>Allmänt

Se till att det underliggande data lagret inte är överbelastat för andra arbets belastningar som körs på eller mot det.

Information om Microsoft-datalager finns i avsnittet om [övervakning och justering](#performance-reference) som är specifika för data lager. Dessa avsnitt kan hjälpa dig att förstå data store prestandaegenskaper och hur du minimera svarstider och maximera genomströmningen.

* Om du kopierar data från Blob Storage till SQL Data Warehouse bör du överväga att använda PolyBase för att öka prestandan. Mer information finns i [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Om du kopierar data från HDFS till Azure Blob Storage eller Azure Data Lake Store kan du använda DistCp för att öka prestandan. Mer information finns i [använda DistCp för att kopiera data från HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Om du kopierar data från RedShift till Azure SQL Data Warehouse, Azure BLob Storage eller Azure Data Lake Store kan du använda Unload för att öka prestandan. Mer information finns i [använda Unload för att kopiera data från Amazon RedShift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Filbaserat datalager

* **Genomsnittlig fil storlek och antal filer**: Kopierings aktiviteten överför data en fil i taget. Med samma mängden data som ska flyttas, är det totala arbetsflödet lägre om data består av många små filer i stället för ett par stora filer på grund av fasen bootstrap för varje fil. Om möjligt kan du kombinera små filer till större filer för att få större data flöde.
* **Fil format och komprimering**: Fler sätt att förbättra prestandan finns i [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och överväganden [för komprimerings](#considerations-for-compression) avsnitt.

### <a name="relational-data-stores"></a>Relationsdata

* **Data mönster**: Din tabellschemat påverkar kopia dataflöde. En stor rad storlek ger bättre prestanda än en liten rad storlek för att kopiera samma mängd data. Anledningen är att databasen mer effektivt kan hämta färre batchar med data som innehåller färre rader.
* **Fråga eller lagrad procedur**: Optimera logiken för frågan eller den lagrade proceduren som du anger i kopierings aktivitets källan för att hämta data mer effektivt.

## <a name="considerations-for-the-sink"></a>Överväganden för mottagaren

### <a name="general"></a>Allmänt

Se till att det underliggande data lagret inte är överbelastat för andra arbets belastningar som körs på eller mot det.

Information om Microsoft-datalager finns i avsnittet om [övervakning och justering](#performance-reference) som är specifika för data lager. Dessa avsnitt kan hjälpa dig att förstå data store prestandaegenskaper och hur du minimera svarstider och maximera genomströmningen.

* Om du kopierar data från ett data lager till Azure SQL Data Warehouse bör du överväga att använda PolyBase för att öka prestandan. Mer information finns i [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Om du kopierar data från HDFS till Azure Blob Storage eller Azure Data Lake Store kan du använda DistCp för att öka prestandan. Mer information finns i [använda DistCp för att kopiera data från HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Om du kopierar data från RedShift till Azure SQL Data Warehouse, Azure Blob Storage eller Azure Data Lake Store kan du använda Unload för att öka prestandan. Mer information finns i [använda Unload för att kopiera data från Amazon RedShift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Filbaserat datalager

* **Kopiera beteende**: Om du kopierar data från ett annat filbaserat data lager, har kopierings aktiviteten tre alternativ via egenskapen **copyBehavior** . Den bevarar hierarki, plattar ut hierarki eller sammanfogar filer. Antingen behålla eller förenkla hierarkin har lite eller ingen prestanda försämras, men Sammanfoga filer gör att prestanda försämras att öka.
* **Fil format och komprimering**: Fler sätt att förbättra prestandan finns i [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och överväganden [för komprimerings](#considerations-for-compression) avsnitt.

### <a name="relational-data-stores"></a>Relationsdata

* **Indirekt för kopierings beteende och prestanda**: Det finns olika sätt att skriva data till en SQL-mottagare. Lär dig mer från [bästa praxis för att läsa in data i Azure SQL Database](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database).

* **Datastorlek för mönstret och batch**:
  * Din tabellschemat påverkar kopia dataflöde. Om du vill kopiera samma mängden data som får en stor Radstorleken du bättre prestanda än en liten Radstorleken eftersom databasen mer effektivt kan genomföra färre batchar av data.
  * Kopierings aktiviteten infogar data i en serie batchar. Du kan ange antalet rader i en batch med hjälp av den **writeBatchSize** egenskapen. Om dina data har liten rader, kan du ange den **writeBatchSize** egenskap med ett högre värde kan dra nytta av lägre omkostnader för batch och högre dataflöde. Om Radstorleken på dina data är stor, var försiktig när du ökar **writeBatchSize**. Ett högt värde kan leda till en kopieringen misslyckades på grund av överbelastning i databasen.

### <a name="nosql-stores"></a>NoSQL-Arkiv

* För **tabellagring**:
  * **Partition**: Om du skriver data till överlagrade partitioner försämras prestanda avsevärt. Sortera dina källdata efter partitionsnyckel så att data infogas effektivt i en partition efter en annan. Du kan också justera logiken för att skriva data till en enda partition.

## <a name="considerations-for-serialization-and-deserialization"></a>Överväganden för serialisering och deserialisering

Serialisering och deserialisering kan ske när data uppsättningen eller data uppsättningen för utdata är en fil. Mer information om fil format som stöds av kopierings aktivitet finns i [fil-och komprimerings format som stöds](supported-file-formats-and-compression-codecs.md).

**Kopiera beteende**:

* Kopiera filer mellan filbaserat datalager:
  * När både indata och utdata-datauppsättningar har samma eller inga fil format inställningar, körs en *binär kopia* utan någon serialisering eller deserialisering i tjänsten för data förflyttning. Du kan se ett högre genomflöde jämfört med scenario, där filformatinställningar källa och mottagare skiljer sig från varandra.
  * När både indata och utdata är i text format och endast kodnings typen är annorlunda, stöder endast data flyttnings tjänsten konvertering. Gör den alla serialisering och deserialisering, vilket gör att prestanda försämras jämfört med en binär kopia.
  * När både indata och utdata har olika fil format eller olika konfigurationer, t. ex. avgränsare, deserialiserar data flyttnings tjänsten källdata till Stream, transformerar och serialiserar sedan den till det utdataformat du angav. Den här åtgärden resulterar i en mycket mer betydande overhead jämfört med andra scenarier.
* När du kopierar filer till eller från ett data lager som inte är fil baserat, till exempel från en filbaserad lagring till ett Relations lager, krävs steget serialisering eller avserialisering. Det här steget leder till betydande prestanda försämras.

**Fil format**: Det fil format som du väljer kan påverka kopierings prestandan. Avro är till exempel ett kompakt binärformat som lagrar metadata med data. Det har ett brett stöd i Hadoop-ekosystemet för bearbetning och frågor. Avro är dyrare för serialisering och deserialisering, vilket resulterar i lägre kopierings data flöde jämfört med text format. 

Se ditt val av filformatet i hela flödet bearbetning holistiskt. Börja med:

- Det formulär som data lagras i, käll data lagras eller extraheras från externa system.
- Det bästa formatet för lagring, analys bearbetning och frågor.
- I vilket format ska data exporteras till datamarts för rapporterings-och visualiserings verktyg.

Ibland ett format som är optimal för Läs- och skrivprestanda kan vara ett bra val när du funderar på övergripande analysprocessen.

## <a name="considerations-for-compression"></a>Överväganden för komprimering

När din indata-eller utdata-datauppsättning är en fil, kan du ställa in kopierings aktiviteten så att den utför komprimering eller expandering när data skrivs till målet. När du väljer komprimering kan du göra en kompromiss mellan indata/utdata (I/O) och CPU. Komprimera data kostnaderna extra beräkningsresurser. Men i utbyte kan det minskar nätverkets i/o och lagring. Beroende på dina data kan du se en ökning i det totala kopierings flödet.

**Codec**: Varje komprimerings-codec har fördelar. Till exempel bzip2 har den lägsta kopia dataflöden, men du får bästa Hive frågeprestanda med bzip2 eftersom du kan dela upp den för bearbetning. GZIP är det mest balanserade alternativet och används oftast. Välj codec som bäst passar ditt scenario för slutpunkt till slutpunkt.

**Nivå**: Du kan välja mellan två alternativ för varje komprimerings-Codec: snabbast komprimerad och optimalt komprimerad. Det snabbast komprimerade alternativet komprimerar data så snabbt som möjligt, även om den resulterande filen inte komprimeras optimalt. Alternativet optimalt komprimerade tillbringar mer tid på komprimering och återger en minimal mängd data. Du kan testa båda alternativen för att se vilket ger bättre prestanda i ditt fall.

**Ett övervägande**: Om du vill kopiera en stor mängd data mellan en lokal lagrings plats och molnet kan du överväga att använda mellanlagrad [kopia](#staged-copy) med komprimering aktiverat. Att använda mellanlagring är användbart när bandbredden i företagets nätverk och dina Azure-tjänster är den begränsande faktorn, och du vill att data uppsättningen och utdata-datauppsättningen ska båda vara i okomprimerat format.

## <a name="considerations-for-column-mapping"></a>Överväganden för kolumnmappningen

Du kan ställa in egenskapen **columnMappings** i en kopierings aktivitet för att mappa alla eller en delmängd av inmatnings kolumnerna till utdatakolumner. När av data movement service läser data från källan, behöver så utföra kolumnmappning på data innan den skriver data till mottagaren. Den här extra bearbetningen minskar kopia dataflöde.

Om ditt källdatalager är frågningsbar, till exempel om det är en relationslagringsplats som SQL Database eller SQL Server, eller om det är ett NoSQL-Arkiv som Table storage eller Azure Cosmos DB kan du push-överföra den kolumn som filtrering och sortering logik för att den **fråga** egenskapen istället för att använda kolumnmappning. På så sätt inträffar projektionen medan data flyttnings tjänsten läser data från käll data lagret, där det är mycket mer effektivt.

Läs mer från [schema mappning för kopierings aktivitet](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Annat att tänka på

Om storleken på de data som du vill kopiera är stor kan du justera affärs logiken för att ytterligare partitionera data. Du kan schemalägga kopierings aktiviteten så att den körs oftare för att minska data storleken för varje kopierings aktivitet som körs.

Var försiktig med antalet data uppsättningar och kopierings aktiviteter som kräver Azure Data Factory att ansluta till samma data lager samtidigt. Många samtidiga kopia jobb kan begränsa ett datalager och leda till försämrade prestanda, kopiera jobbet interna återförsök, och i vissa fall, fel vid körning.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Exempel scenario: Kopiera från en lokal SQL Server till Blob Storage

**Scenario**: En pipeline är byggd för att kopiera data från en lokal SQL Server till Blob Storage i CSV-format. Om du vill göra kopieringsjobbet snabbare ska CSV-filer komprimeras bzip2-format.

**Test och analys**: Data flödet för kopierings aktiviteten är mindre än 2 Mbit/s, vilket är mycket långsammare än prestanda måttet.

**Prestanda analys och-justering**: För att felsöka prestanda problemet ska vi titta på hur data bearbetas och flyttas.

- **Läs data**: Integrerings körningen öppnar en anslutning till SQL Server och skickar frågan. SQL Server svarar genom att skicka data strömmen till integration runtime via intranätet.
- **Serialisera och komprimera data**: Integrerings körningen serialiserar data strömmen till CSV-format och komprimerar data till en bzip2-dataström.
- **Skriv data**: Integrerings körningen överför bzip2-dataströmmen till Blob Storage via Internet.

Som du kan se bearbetas och flyttas data i ett sekventiellt strömmande sätt: SQL Server > LAN > integration runtime > WAN-> Blob Storage. Den övergripande prestandan är gated med minsta data flöde i pipelinen.

![Dataflöde](./media/copy-activity-performance/case-study-pic-1.png)

En eller flera av följande faktorer kan orsaka flaskhals för prestanda:

* **Källa**: SQL Server har låg genom strömning på grund av tung belastning.
* **Integration runtime med egen värd**:
  * **LAN**: Integration runtime finns långt från SQL Server datorn och har en anslutning med låg bandbredd.
  * **Integration runtime**: Integrerings körningen har nått sina inläsnings begränsningar för att utföra följande åtgärder:
    * **Serialisering**: Serialisering av data strömmen till CSV-format har långsam data flöde.
    * **Komprimering**: Du har valt en låg komprimerings-codec, till exempel bzip2, som är 2,8 Mbit/s med Core i7.
  * **WAN**: Bandbredden mellan företags nätverket och Azure-tjänsterna är låg, till exempel T1 = 1 544 kbps; T2 = 6 312 kbps.
* **Mottagare**: Blob Storage har ett lågt data flöde. Det här scenariot är osannolikt eftersom dess service nivå avtal (SLA) garanterar minst 60 Mbit/s.

I det här fallet kan bzip2 datakomprimering långsammare hela pipelinen. Växla till en gzip komprimerings-codec kan underlätta den här begränsningen.

## <a name="references"></a>Referenser

Här följer prestanda övervakning och justering av referenser för några av de data lager som stöds:

* Azure Storage, inklusive Blob Storage och table Storage: [Azure Storage skalbarhets mål](../storage/common/storage-scalability-targets.md) och [Azure Storage check lista för prestanda och skalbarhet](../storage/common/storage-performance-checklist.md).
* Azure SQL Database: Du kan [övervaka prestanda](../sql-database/sql-database-single-database-monitor.md) och kontrol lera DTU-procenten (Database Transaction Unit).
* Azure SQL Data Warehouse: Dess funktion mäts i informations lager enheter (DWU: er). Se [hantera beräknings kraft i Azure SQL Data Warehouse (översikt)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Prestanda nivåer i Azure Cosmos DB](../cosmos-db/performance-levels.md).
* Lokala SQL Server: [Övervaka och justera för prestanda](https://msdn.microsoft.com/library/ms189081.aspx).
* Lokal fil Server: [Prestanda justering för fil servrar](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Nästa steg
Se andra artiklar om kopierings aktiviteter:

- [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
- [Kopiera aktivitets schema mappning](copy-activity-schema-and-type-mapping.md)
- [Kopiera aktivitet feltolerans](copy-activity-fault-tolerance.md)

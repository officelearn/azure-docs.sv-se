---
title: Kopiera aktivitet prestanda- och justeringsguide
description: Läs mer om viktiga faktorer som påverkar prestandan för dataförflyttning i Azure Data Factory när du använder Kopieringsaktivitet.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9ca44b1917cfaed5d01c31f8f06d98e5e4b611a8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357272"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Kopiera aktivitet prestanda- och justeringsguide

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-copy-activity-performance.md)
> * [Version 2 (aktuell version)](../copy-activity-performance.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se prestanda- [och justerings guiden för kopierings aktiviteter för Data Factory](../copy-activity-performance.md).

Azure Data Factory-Kopieringsaktiviteten levererar en förstklassig säker, tillförlitlig och höga prestanda för inläsning av data lösning. Det gör att du kan kopiera tiotals terabyte data varje dag på en rad olika molnet och lokala datalager. – Blixtsnabb prestanda för datainläsning är nyckeln till att se till att du kan fokusera på problemet core ”stordata”: bygga avancerade Analyslösningar och få djupa insikter från alla data.

Azure innehåller en uppsättning av företagsklass lösningar för lagring och data warehouse och Kopieringsaktivitet erbjuder en optimerad upplevelse som är lätt att göra inställningar för inläsning av data. Med bara en enda Kopieringsaktivitet, kan du åstadkomma:

* Läser in data i Azure SQL Data Warehouse **1,2 Gbit/s**. För en genom gång med ett användnings fall, se [load 1 TB till Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Läsa in data i **Azure Blob Storage** med **1,0 Gbit/s**
* Läser in data i Azure Data Lake Store **1,0 Gbit/s**

Den här artikeln beskrivs:

* [Prestanda referens nummer](#performance-reference) för käll-och mottagar data lager som stöds för att hjälpa dig att planera ditt projekt.
* Funktioner som kan förbättra kopierings flödet i olika scenarier, inklusive [enheter för moln data flytt](#cloud-data-movement-units), [parallell kopiering](#parallel-copy)och [mellanlagrad kopiering](#staged-copy).
* [Vägledning för prestanda justering](#performance-tuning-steps) om hur du finjusterar prestanda och viktiga faktorer som kan påverka kopierings prestanda.

> [!NOTE]
> Om du inte är bekant med kopierings aktiviteten i allmänhet, se [Flytta data med hjälp av kopierings aktivitet](data-factory-data-movement-activities.md) innan du läser den här artikeln.
>

## <a name="performance-reference"></a>Prestandareferens för

Som referens visas det kopierade data flödes numret i MBps i Mbit/s för den aktuella käll-och mottagar paret, baserat på intern testning. För jämförelse visar det också hur olika inställningar för [moln data förflyttnings enheter](#cloud-data-movement-units) eller [Data Management Gateway skalbarhet](data-factory-data-management-gateway-high-availability-scalability.md) (flera gateway-noder) kan hjälpa till med kopierings prestanda.

![Matris för prestanda](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>I Azure Data Factory version 1 är de minsta moln data flytt enheterna för moln-till-moln-kopia två. Om inget värde anges, se standard enheter för data förflyttning som används i [enheter för moln data flytt](#cloud-data-movement-units).

**Poäng till Anmärkning:**
* Dataflödet beräknas med hjälp av följande formel: [storleken på data läses från källan] / [körningen av Kopieringsaktiviteten varaktighet].
* Prestanda referens numren i tabellen mättes med hjälp av [TPC-H-](http://www.tpc.org/tpch/) data uppsättning i en enda kopierings aktivitet.
* I Azure datalager finns källa och mottagare i samma Azure-region.
* För Hybrid kopiering mellan lokala och molnbaserade data lager, kördes varje gateway-nod på en dator som var skild från det lokala data lagret med specifikationen nedan. När en enskild aktivitet kördes på Gateway, förbrukade kopierings åtgärden bara en liten del av test datorns processor, minne eller nätverks bandbredd. Lär dig mer [om att tänka på Data Management Gateway](#considerations-for-data-management-gateway).
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
> Du kan få högre genomflöde genom att använda fler data rörelse enheter (DMUs) än den högsta standard DMUs, som är 32 för en kopierings aktivitet från moln till molnet. Med 100 DMUs kan du till exempel få kopiera data från Azure blob till Azure Data Lake Store vid **1,0 Gbit/s**. Mer information om den här funktionen och det scenario som stöds finns i avsnittet [Cloud data transport enheter](#cloud-data-movement-units) . Kontakta [Azure-supporten](https://azure.microsoft.com/support/) om du vill begära fler DMUs.

## <a name="parallel-copy"></a>Parallell kopia
Du kan läsa data från källan eller skriva data till målet **parallellt i en kopierings aktivitets körning**. Den här funktionen förbättrar data flödet för en kopierings åtgärd och minskar den tid det tar att flytta data.

Den här inställningen skiljer sig från egenskapen **concurrency** i aktivitets definitionen. Egenskapen **concurrency** bestämmer antalet **samtidiga kopierings aktiviteter som körs** för att bearbeta data från olika aktivitets fönster (1 till 2 fm, 2 am till 3 am, 3, och så vidare). Den här funktionen är användbar när du utför en historisk belastning. Funktionen för parallell kopiering gäller för en **enda aktivitets körning**.

Nu ska vi titta på ett exempel scenario. I följande exempel måste flera sektorer från det förflutna bearbetas. Data Factory kör en instans av kopierings aktiviteten (en aktivitets körning) för varje sektor:

* Data sektorn från det första aktivitets fönstret (1 till 2 AM) = = > aktivitet kör 1
* Data sektorn från det andra aktivitets fönstret (mellan 2 och 3 AM) = = > aktivitet kör 2
* Data sektorn från det andra aktivitets fönstret (3 till 4 AM) = = > aktivitet kör 3

Och så vidare.

I det här exemplet, när **samtidighets** värdet är inställt på 2, körs **aktivitet 1** och **aktivitet kör 2** kopiera data från två aktivitets fönster **samtidigt** för att förbättra data flyttens prestanda. Men om flera filer är associerade med aktivitet kör 1, kopierar tjänsten för data flyttning filer från källan till en fil i taget.

### <a name="cloud-data-movement-units"></a>Enheter för flytt av moln data
En **moln data förflyttnings enhet (DMU)** är ett mått som representerar styrkan (en kombination av processor, minne och tilldelning av nätverks resurser) för en enskild enhet i Data Factory. DMU kan användas för kopiering av moln till molnet, men inte i en hybrid kopia.

**De minimala moln data flytt enheterna för att kunna köra kopierings aktiviteter är två.** Om detta inte anges visas standard-DMUs som används i olika kopierings scenarier i följande tabell:

| Kopiera scenario | Standard DMUs som fastställs av tjänsten |
|:--- |:--- |
| Kopiera data mellan filbaserade lager | Mellan 4 och 16 beroende på filernas antal och storlek. |
| Alla andra kopia-scenarier | 4 |

Om du vill åsidosätta standardvärdet anger du ett värde för egenskapen **cloudDataMovementUnits** enligt följande. De **tillåtna värdena** för egenskapen **cloudDataMovementUnits** är 2, 4, 8, 16, 32. Det **faktiska antalet moln DMUs** som kopierings åtgärden använder vid körningen är lika med eller mindre än det konfigurerade värdet, beroende på ditt data mönster. Information om den prestanda nivå som du kan få när du konfigurerar fler enheter för en bestämd kopierings källa och mottagare finns i [prestanda referensen](#performance-reference).

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

> [!NOTE]
> Kontakta [Azure-supporten](https://azure.microsoft.com/support/)om du behöver fler moln DMUs för ett högre data flöde. Inställningen av 8 och senare fungerar för närvarande bara när du **kopierar flera filer från Blob Storage/data Lake Store/Amazon S3/Cloud FTP/Cloud SFTP till Blob Storage/data Lake Store/Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Du kan använda egenskapen **parallelCopies** för att ange vilken parallellitet som ska användas för kopierings aktiviteten. Du kan tänka på den här egenskapen som det maximala antalet trådar i Kopieringsaktiviteten som kan läsa från källan eller skriva till dina datalager för mottagare parallellt.

För varje körningen av Kopieringsaktiviteten, avgör hur många parallella kopior som ska använda för att kopiera data från källan datalagring och att sidan måldatalager lagra i Data Factory. Standard antalet parallella kopior som används beror på vilken typ av källa och mottagare du använder.

| Källa och mottagare | Parallell kopia Standardantal bestäms av tjänsten |
| --- | --- |
| Kopiera data mellan filbaserade arkiv (Blob Storage; Data Lake Store; Amazon S3; ett lokalt fil system; en lokal HDFS) |Mellan 1 och 32. Beror på storleken på filerna och antalet moln data förflyttnings enheter (DMUs) som används för att kopiera data mellan två moln data lager eller den fysiska konfigurationen för den Gateway-dator som används för en hybrid kopia (för att kopiera data till eller från ett lokalt data lager). |
| Kopiera data från **ett käll data lager till Azure Table Storage** |4 |
| Alla andra käll-och mottagar par |1 |

Normalt bör standard beteendet ge dig det bästa data flödet. Men för att kontrol lera belastningen på datorer som är värdar för dina data lager, eller för att justera kopierings prestanda, kan du välja att åsidosätta standardvärdet och ange ett värde för egenskapen **parallelCopies** . Värdet måste vara mellan 1 och 32 (båda). Vid körning använder för bästa prestanda Kopieringsaktiviteten ett värde som är mindre än eller lika med värdet som du anger.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
Saker att Observera:

* När du kopierar data mellan filbaserade butiker bestämmer **parallelCopies** parallellt på fil nivå. Dela upp inom en enda fil skulle inträffa under automatiskt och transparent och den har utformats för att använda den bästa lämpliga segmentstorleken för en viss källa datalagertyp att läsa in data i parallella program och rätvinkliga till parallelCopies. Det faktiska antalet parallella kopior av data movement service använder för att kopieringen under körning är inte fler än antalet filer som du har. Om kopierings beteendet är **mergeFile**kan inte kopierings aktiviteten dra nytta av Parallel på filnivå.
* När du anger ett värde för egenskapen **parallelCopies** bör du ta hänsyn till belastnings ökningen på käll-och mottagar data lager och till gateway om det är en hybrid kopia. Detta sker särskilt när du har flera aktiviteter eller samtidiga körningar av samma aktiviteter som körs mot samma datalager. Om du ser att data lagret eller gatewayen är överbelasta med belastningen minskar du **parallelCopies** -värdet för att minska belastningen.
* När du kopierar data från butiker som inte är filbaserade till arkiv som är filbaserade ignorerar tjänsten för data förflyttning **parallelCopies** -egenskapen. Även om parallellitet anges, tillämpas den inte i det här fallet.

> [!NOTE]
> Du måste använda Data Management Gateway version 1,11 eller senare för att använda funktionen **parallelCopies** när du gör en hybrid kopia.
>
>

För att bättre använda de här två egenskaperna och för att förbättra data flödet, se exempel användnings fall. Du behöver inte konfigurera **parallelCopies** för att dra nytta av standard beteendet. Om du konfigurerar och **parallelCopies** är för liten används kanske inte flera moln DMUs fullt ut.

### <a name="billing-impact"></a>Fakturerings påverkan
Det är **viktigt** att komma ihåg att du debiteras utifrån den totala tiden för kopierings åtgärden. Om ett kopierings jobb som används för att ta en timme med en molnen het och nu tar 15 minuter med fyra moln enheter, förblir den övergripande fakturan nästan samma. Du kan till exempel använda fyra moln enheter. Den första moln enheten tillbringar 10 minuter, den andra, 10 minuter, den tredje, 5 minuter och den fjärde, 5 minuter, allt i en kopierings aktivitet. Du debiteras för den totala kopierings tiden (data förflyttnings tiden), som är 10 + 10 + 5 + 5 = 30 minuter. Att använda **parallelCopies** påverkar inte faktureringen.

## <a name="staged-copy"></a>Mellanlagrad kopiering
När du kopierar data från källans datalager till mottagarens datalager kan du välja att använda Blob storage som en mellanliggande mellanlagringsarkivet. Mellanlagring är särskilt användbart i följande fall:

1. **Du vill mata in data från olika data lager i SQL Data Warehouse via PolyBase**. SQL Data Warehouse använder PolyBase som en mekanism för stora dataflöden för att läsa in en stor mängd data till SQL Data Warehouse. Källdata måste dock vara i Blob Storage och måste uppfylla ytterligare kriterier. När du läser in data från ett annat data lager än Blob Storage, kan du aktivera data kopiering via tillfällig mellanlagring av blob-lagring. I så fall kan utför Data Factory omvandlingarna data som krävs för att säkerställa att den uppfyller kraven för PolyBase. Sedan används PolyBase för att läsa in data i SQL Data Warehouse. Mer information finns i [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). För en genom gång med ett användnings fall, se [load 1 TB till Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Ibland tar det en stund att utföra en hybrid data förflyttning (det vill säga kopiera mellan ett lokalt data lager och ett moln data lager) över en långsam nätverks anslutning**. Du kan förbättra prestanda genom att komprimera data lokalt så att det tar mindre tid att flytta data till lagrings data lagret i molnet. Sedan kan du expandera data i mellanlagrings platsen innan du läser in den i mål data lagret.
3. **Du vill inte öppna andra portar än port 80 och port 443 i brand väggen, på grund av företagets IT-principer**. När du kopierar data från ett lokalt datalager till en Azure SQL Database-mottagare eller en Azure SQL Data Warehouse sink måste aktivera utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företagets brandvägg. I det här scenariot kan du dra nytta av gatewayen för att först kopiera data till en mellanlagringsplats för Blob Storage via HTTP eller HTTPS på port 443. Läs sedan in data i SQL Database eller SQL Data Warehouse från mellanlagring av blob-lagring. I det här flödet behöver du inte aktivera port 1433.

### <a name="how-staged-copy-works"></a>Hur mellanlagrad kopiering fungerar
När du aktiverar mellanlagrings funktionen kopieras först data från käll data lagret till lagrings data lagret (ta med din egen). Därefter kopieras data från datalager för mellanlagring till datalager för mottagare. Data Factory hanterar automatiskt två steg flödet för dig. Data Factory rensar också tillfälliga data från den tillfälliga lagringen efter att dataöverföringen har slutförts.

I moln kopierings scenariot (både käll-och mottagar data lager finns i molnet) används inte gatewayen. Tjänsten Data Factory utför kopierings åtgärderna.

![Mellanlagrad kopiering: moln scenario](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

I hybrid kopierings scenariot (källan är lokalt och mottagaren är i molnet) flyttar gatewayen data från käll data lagringen till ett mellanlagrat data lager. Data Factory tjänst flyttar data från lagrings data lagret till data lagret för mottagare. Att kopiera data från ett moln data lager till ett lokalt data lager via mellanlagring, stöds också med det omvända flödet.

![Mellanlagrad kopiering: hybrid scenario](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

När du aktiverar dataförflyttning med hjälp av en mellanlagringsarkivet kan ange du om du vill att data ska komprimeras innan du flyttar data från källans datalager till ett mellanliggande eller tillfälliga datalager och sedan expandera innan du flyttar data från en tiden eller organiserar data lagra till de mottagande datalagren.

För närvarande kan kopiera du inte data mellan två lokala datalager med hjälp av en mellanlagringsarkivet. Vi förväntar dig att det här alternativet ska vara tillgängligt snart.

### <a name="configuration"></a>Konfiguration
Konfigurera **enableStaging** -inställningen i kopierings aktivitet för att ange om du vill att data ska mellanlagras i Blob Storage innan du läser in dem i ett mål data lager. När du anger **enableStaging** till true anger du ytterligare egenskaper som anges i nästa tabell. Om du inte har någon kan du även behöva skapa ett Azure Storage eller Storage shared access signature-länkad tjänst för mellanlagring.

| Egenskap | Beskrivning | Standardvärde | Krävs |
| --- | --- | --- | --- |
| **enableStaging** |Ange om du vill kopiera data via en tiden mellanlagring store. |False |Nej |
| **linkedServiceName** |Ange namnet på en länkad [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) -eller [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) -tjänst som refererar till den lagrings instans som du använder som ett interimistiskt mellanlagrings lager. <br/><br/> Du kan inte använda Storage med signatur för delad åtkomst för att läsa in data till SQL Data Warehouse via PolyBase. Du kan använda den i alla andra scenarier. |Ej tillämpligt |Ja, när **enableStaging** är inställt på True |
| **path** |Ange sökvägen för Blob-lagring som du vill ska innehålla den mellanlagrade data. Om du inte anger en sökväg, skapar en behållare för att lagra tillfälliga data i tjänsten. <br/><br/> Ange en sökväg endast om du använder lagring med signatur för delad åtkomst, eller du kräver tillfälliga data finnas i en viss plats. |Ej tillämpligt |Nej |
| **enableCompression** |Anger om data ska komprimeras innan den kopieras till målet. Den här inställningen minskar mängden data som överförs. |False |Nej |

Här är en exempeldefinition av Kopieringsaktiviteten med egenskaper som beskrivs i tabellen ovan:

```json
"activities":[
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>Fakturerings påverkan
Du debiteras baserat på två steg: kopiera varaktighet och typen.

* När du använder mellanlagring under en moln kopia (kopiera data från ett moln data lager till ett annat moln data lager) debiteras du [summan av kopierings tiden för steg 1 och steg 2] x [Cloud Copy Unit Price].
* När du använder mellanlagring under en hybrid kopiering (kopiering av data från ett lokalt data lager till ett moln data lager) debiteras du för [hybrid kopieringens varaktighet] x [hybrid kopiera enhets pris] + [moln kopieringens varaktighet] x [Cloud Copy Unit Price].

## <a name="performance-tuning-steps"></a>Prestanda justering steg
Vi rekommenderar att du gör följande för att finjustera prestanda för Data Factory-tjänsten med en Kopieringsaktivitet:

1. **Upprätta en bas linje**. Testa din pipeline med hjälp av Kopieringsaktivitet mot ett representativt datasampel under utvecklingsfasen för. Du kan använda Data Factory [segmenterings modell](data-factory-scheduling-and-execution.md) för att begränsa den mängd data som du arbetar med.

   Samla in körnings tid och prestanda egenskaper med hjälp av **appen övervakning och hantering**. Välj **övervaka & hantera** på din Data Factory start sida. I trädvyn väljer du **data uppsättningen för utdata**. I listan **aktivitets fönster** väljer du kopierings aktiviteten Kör. **Aktivitets fönster** visar varaktigheten för kopierings aktiviteten och storleken på de data som kopieras. Data flödet visas i **Utforskaren i aktivitets fönstret**. Mer information om appen finns i [övervaka och hantera Azure Data Factory pipelines med hjälp av appen övervakning och hantering](data-factory-monitor-manage-app.md).

   ![Aktivitetskörningsinformation](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Senare i artikeln kan du jämföra prestanda och konfiguration för ditt scenario för att kopiera aktivitetens [prestanda referens](#performance-reference) från våra tester.
2. **Diagnostisera och optimera prestanda**. Om du se prestanda inte uppfyller dina förväntningar kan behöva du identifiera flaskhalsar i prestanda. Sedan kan optimera prestanda för att ta bort eller minska effekten av flaskhalsar. En fullständig beskrivning av Prestandadiagnostik ligger utanför omfånget för den här artikeln, men här följer några vanliga överväganden:

   * Prestandafunktioner:
     * [Parallell kopia](#parallel-copy)
     * [Enheter för flytt av moln data](#cloud-data-movement-units)
     * [Mellanlagrad kopia](#staged-copy)
     * [Data Management Gateway skalbarhet](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Gateway för datahantering](#considerations-for-data-management-gateway)
   * [Källa](#considerations-for-the-source)
   * [Sjönk](#considerations-for-the-sink)
   * [Serialisering och deserialisering](#considerations-for-serialization-and-deserialization)
   * [Komprimering](#considerations-for-compression)
   * [Kolumnmappning](#considerations-for-column-mapping)
   * [Andra överväganden](#other-considerations)
3. **Expandera konfigurationen till hela data uppsättningen**. När du är nöjd med körnings resultatet och prestandan kan du expandera definitionen och den aktiva pipeline-perioden så att hela data uppsättningen täcker hela din data uppsättning.

## <a name="considerations-for-data-management-gateway"></a>Överväganden för Data Management Gateway
**Gateway-konfiguration**: Vi rekommenderar att du använder en dedikerad dator som värd för data Management Gateway. Se [överväganden vid användning av data Management Gateway](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Gateway-övervakning och skalbarhet**: en enda logisk Gateway med en eller flera gateway-noder kan betjäna flera kopierings aktiviteter samtidigt samtidigt. Du kan visa en ögonblicks bild av resursutnyttjande i real tid (CPU, minne, nätverk (in/ut) osv.) på en Gateway-dator samt antalet samtidiga jobb som körs mot gränsen i Azure Portal. mer information finns i [övervaka gateway i portalen](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Om du har hög behov av hybrid data förflyttning antingen med ett stort antal samtidiga kopierings aktiviteter körs eller med stor mängd data som ska kopieras, bör du överväga att [skala upp eller skala ut gatewayen](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) så att du bättre kan använda din resurs eller för att etablera mer resurs för att ge en kopia.

## <a name="considerations-for-the-source"></a>Överväganden för källan
### <a name="general"></a>Allmänt
Var noga med att det underliggande datalagringen inte bli överhopade av andra arbetsbelastningar som körs på eller mot den.

Information om Microsoft-datalager finns i avsnittet om [övervakning och justering](#performance-reference) som är specifika för data lager och hjälper dig att förstå prestanda egenskaper för data lager, minimera svars tider och maximera data flödet.

Om du kopierar data från Blob Storage till SQL Data Warehouse bör du överväga att använda **PolyBase** för att öka prestandan. Mer information finns i [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) . För en genom gång med ett användnings fall, se [load 1 TB till Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Filbaserat datalager
*(Inkluderar Blob Storage, Data Lake Store, Amazon S3, lokala fil system och lokala HDFS)*

* **Genomsnittlig fil storlek och antal filer**: kopierings aktivitet överför data en fil i taget. Med samma mängden data som ska flyttas, är det totala arbetsflödet lägre om data består av många små filer i stället för ett par stora filer på grund av fasen bootstrap för varje fil. Om det är möjligt, kombinera därför små filer i större filer för att få högre dataflöde.
* **Fil format och komprimering**: Mer information om hur du kan förbättra prestanda finns i [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och [överväganden för komprimerings](#considerations-for-compression) avsnitt.
* I det **lokala fil system** scenariot, där **Data Management Gateway** krävs, se avsnittet [överväganden för data Management Gateway](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Relationsdata
*(Innehåller SQL Database; SQL Data Warehouse; Amazon-RedShift; SQL Server-databaser; och Oracle-, MySQL-, DB2-, Teradata-, Sybase-och PostgreSQL-databaser osv.)*

* **Data mönster**: tabellens schema påverkar kopiering av data flöde. En stor Radstorleken ger dig en bättre prestanda än små Radstorleken att kopiera samma mängd data. Anledningen är att databasen mer effektivt kan hämta färre batchar med data som innehåller färre rader.
* **Fråga eller lagrad procedur**: optimera logiken för frågan eller den lagrade proceduren som du anger i kopierings aktivitets källan för att hämta data mer effektivt.
* Information om **lokala Relations databaser**, till exempel SQL Server och Oracle, som kräver användning av **Data Management Gateway**, finns i avsnittet överväganden för data Management Gateway.

## <a name="considerations-for-the-sink"></a>Överväganden för mottagaren
### <a name="general"></a>Allmänt
Var noga med att det underliggande datalagringen inte bli överhopade av andra arbetsbelastningar som körs på eller mot den.

Information om Microsoft-datalager finns i [avsnittet om övervakning och justering](#performance-reference) som är specifika för data lager. Dessa avsnitt kan hjälpa dig att förstå data store prestandaegenskaper och hur du minimera svarstider och maximera genomströmningen.

Om du kopierar data från **Blob Storage** till **SQL Data Warehouse**bör du överväga att använda **PolyBase** för att öka prestandan. Mer information finns i [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) . För en genom gång med ett användnings fall, se [load 1 TB till Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Filbaserat datalager
*(Inkluderar Blob Storage, Data Lake Store, Amazon S3, lokala fil system och lokala HDFS)*

* **Kopierings beteende**: om du kopierar data från ett annat filbaserat data lager, har kopierings aktiviteten tre alternativ via egenskapen **copyBehavior** . Den bevarar hierarki, plattar ut hierarki eller sammanfogar filer. Antingen behålla eller förenkla hierarkin har lite eller ingen prestanda försämras, men Sammanfoga filer gör att prestanda försämras att öka.
* **Fil format och komprimering**: Mer information om hur du kan förbättra prestanda finns i [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och [överväganden för komprimerings](#considerations-for-compression) avsnitt.
* **Blob Storage**: för närvarande stöder Blob Storage bara block-blobbar för optimerad data överföring och data flöde.
* För scenarier med **lokala fil system** som kräver användning av **Data Management Gateway**, se avsnittet [överväganden för data Management Gateway](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Relationsdata
*(Innehåller SQL Database, SQL Data Warehouse, SQL Server databaser och Oracle-databaser)*

* **Kopierings beteende**: beroende på de egenskaper som du har angett för **sqlSink**skriver kopierings aktiviteten data till mål databasen på olika sätt.
  * Som standard läggs data movement service använder API: et Bulk Copy att infoga data i läge, som ger bästa prestanda.
  * Om du konfigurerar en lagrad procedur i sink gäller databasen en datarad samtidigt i stället för som en massinläsning. Prestanda försämras betydligt. Om din data uppsättning är stor kan du, när det är tillämpligt, byta till att använda egenskapen **sqlWriterCleanupScript** .
  * Om du konfigurerar egenskapen **sqlWriterCleanupScript** för varje kopierings aktivitet, utlöser tjänsten skriptet och använder sedan Mass kopierings-API: et för att infoga data. Om du vill skriva över hela tabellen med den senaste informationen, kan du till exempel ange ett skript för att först ta bort alla poster innan massinläsning nya data från källan.
* **Data mönster och batchstorlek**:
  * Din tabellschemat påverkar kopia dataflöde. Om du vill kopiera samma mängden data som får en stor Radstorleken du bättre prestanda än en liten Radstorleken eftersom databasen mer effektivt kan genomföra färre batchar av data.
  * Kopieringsaktivitet infogar data i en serie med batchar. Du kan ange antalet rader i en batch med hjälp av egenskapen **writeBatchSize** . Om dina data har små rader kan du ange egenskapen **writeBatchSize** med ett högre värde för att dra nytta av den lägre batch-overheadkostnaden och högre data flöde. Var försiktig när du ökar **writeBatchSize**om rad storleken för dina data är stor. Ett högt värde kan leda till en kopieringen misslyckades på grund av överbelastning i databasen.
* Information om **lokala Relations databaser** som SQL Server och Oracle, som kräver användning av **Data Management Gateway**, finns i avsnittet [överväganden för data Management Gateway](#considerations-for-data-management-gateway) .

### <a name="nosql-stores"></a>NoSQL-Arkiv
*(Innehåller tabell lagring och Azure Cosmos DB)*

* För **Table Storage**:
  * **Partition**: om du skriver data till överlagrade partitioner försämras prestanda avsevärt. Sortera dina källdata efter partitionsnyckel så att data infogas effektivt i en partition efter en annan eller ändra logik för att skriva data till en enda partition.
* För **Azure Cosmos DB**:
  * **Batchstorlek**: egenskapen **writeBatchSize** anger antalet parallella förfrågningar till Azure Cosmos DB tjänsten för att skapa dokument. Du kan vänta bättre prestanda när du ökar **writeBatchSize** eftersom fler parallella förfrågningar skickas till Azure Cosmos dB. Titta dock efter begränsning när du skriver till Azure Cosmos DB (fel meddelandet är "begär ande frekvens är stort"). Olika faktorer kan orsaka begränsning, inklusive dokument storlek, antalet termer i dokumenten och mål samlingens indexerings princip. Överväg att använda en bättre samling, till exempel S3, för att uppnå högre kopierings data flöde.

## <a name="considerations-for-serialization-and-deserialization"></a>Överväganden för serialisering och deserialisering
Serialisering och deserialisering kan inträffa när ditt inkommande datauppsättning eller en datauppsättning för utdata är en fil. Se [fil-och komprimerings format som stöds](data-factory-supported-file-and-compression-formats.md) med information om vilka fil format som stöds genom kopierings aktivitet.

**Kopierings beteende**:

* Kopiera filer mellan filbaserat datalager:
  * När indata-och utdata-data anges både har samma eller inga fil format inställningar, kör data flyttnings tjänsten en binär kopia utan serialisering eller deserialisering. Du kan se ett högre genomflöde jämfört med scenario, där filformatinställningar källa och mottagare skiljer sig från varandra.
  * När indata och utdata datauppsättningar båda är i textformat och endast kodningen typen är olika, av data movement service kräver endast kodning konvertering. Gör den alla serialisering och deserialisering, vilket gör att prestanda försämras jämfört med en binär kopia.
  * När in- och utdata datauppsättningar båda har olika filformat eller olika konfigurationer som avgränsare, av data movement service deserializes källdata för att strömma, transformera och serialisera det till utdataformat som du angett. Den här åtgärden resulterar i en mycket mer betydande overhead jämfört med andra scenarier.
* När du kopierar filer till/från ett datalager som inte är filbaserade (till exempel från store filbaserade som du kan använda för att en relationslagringsplats) krävs serialisering eller avserialisering steg. Det här steget leder till betydande prestanda försämras.

**Fil format**: det fil format som du väljer kan påverka kopierings prestandan. Avro är till exempel ett kompakt binärformat som lagrar metadata med data. Det har ett brett stöd i Hadoop-ekosystemet för bearbetning och frågor. Avro är dock dyrare för serialisering och deserialisering, vilket resulterar i lägre kopia genomflöde jämfört med textformat. Se ditt val av filformatet i hela flödet bearbetning holistiskt. Börja med vilken form data lagras i källdatalagren eller som ska extraheras från externa system. Det bästa formatet för lagring, analytisk behandling och fråga; och i vilket format data ska exporteras till dataarkiv för verktyg för rapportering och visualisering. Ibland ett format som är optimal för Läs- och skrivprestanda kan vara ett bra val när du funderar på övergripande analysprocessen.

## <a name="considerations-for-compression"></a>Överväganden för komprimering
När datauppsättningen inkommande eller utgående är en fil kan ange du Kopieringsaktivitet för att utföra komprimering eller dekomprimering eftersom den skriver data till målet. När du väljer komprimering kan du göra en kompromiss mellan indata/utdata (I/O) och CPU. Komprimera data kostnaderna extra beräkningsresurser. Men i utbyte kan det minskar nätverkets i/o och lagring. Beroende på dina data, kan du se en högre övergripande kopia dataflöde.

**Codec**: kopierings aktiviteten stöder gzip-, bzip2-och DEFLATE-komprimerings typer. Azure HDInsight kan använda alla tre typerna för bearbetning. Varje komprimerings-codec har fördelar. Till exempel bzip2 har den lägsta kopia dataflöden, men du får bästa Hive frågeprestanda med bzip2 eftersom du kan dela upp den för bearbetning. Gzip är det mest balanserade alternativet, och det används oftast. Välj codec som bäst passar ditt scenario för slutpunkt till slutpunkt.

**Nivå**: du kan välja mellan två alternativ för varje komprimerings-Codec: snabbast komprimerad och optimalt komprimerad. Det snabbaste komprimerade alternativet komprimerar data så snabbt som möjligt, även om den resulterande filen inte är optimalt komprimerad. Alternativet optimalt komprimerade tillbringar mer tid på komprimering och återger en minimal mängd data. Du kan testa båda alternativen för att se vilket ger bättre prestanda i ditt fall.

**Tänk på följande:** om du vill kopiera en stor mängd data mellan en lokal lagrings plats och molnet kan du överväga att använda interimistisk Blob Storage med komprimering. Det är praktiskt att använda mellanlagring när bandbredden för företagets nätverk och dina Azure-tjänster är den begränsande faktorn och du vill att den inkommande datauppsättningen och datauppsättningen för utdata både i okomprimerade form. Mer specifikt kan du dela upp en enskild kopierings aktivitet i två kopierings aktiviteter. Den första kopierings aktiviteten kopierar från källan till en tillfällig eller mellanlagringsplats av BLOB i komprimerad form. Den andra kopierings aktiviteten kopierar komprimerade data från mellanlagring och dekomprimerar när den skrivs till mottagaren.

## <a name="considerations-for-column-mapping"></a>Överväganden för kolumnmappningen
Du kan ställa in egenskapen **columnMappings** i kopierings aktivitet för att mappa alla eller en delmängd av inmatnings kolumnerna till utdatakolumner. När av data movement service läser data från källan, behöver så utföra kolumnmappning på data innan den skriver data till mottagaren. Den här extra bearbetningen minskar kopia dataflöde.

Om ditt källdata till exempel kan frågas, till exempel om det är en Relations butik som SQL Database eller SQL Server, eller om det är en NoSQL-butik som Table Storage eller Azure Cosmos DB, bör du överväga att flytta kolumn filtreringen och ändra ordningen på logiken till egenskapen **fråga** i stället för att använda kolumn mappning. På så sätt kan projektionen inträffar medan av data movement service läser data från källans datalager, där det är mycket mer effektivt.

## <a name="other-considerations"></a>Andra överväganden
Om storleken på de data som du vill kopiera är stor kan du justera affärs logiken för att ytterligare partitionera data med hjälp av segmenterings metoden i Data Factory. Schemalägg sedan kopierings aktiviteten så att den körs oftare för att minska data storleken för varje kopierings aktivitet.

Var försiktig med antalet data uppsättningar och kopierings aktiviteter som kräver Data Factory att ansluta till samma data lager samtidigt. Många samtidiga kopia jobb kan begränsa ett datalager och leda till försämrade prestanda, kopiera jobbet interna återförsök, och i vissa fall, fel vid körning.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Exempelscenario: kopiera från en lokal SQL Server till Blob storage
**Scenario**: en pipeline är byggd för att kopiera data från en lokal SQL Server till Blob Storage i CSV-format. Om du vill göra kopieringsjobbet snabbare ska CSV-filer komprimeras bzip2-format.

**Test och analys**: data flödet för kopierings aktiviteten är mindre än 2 Mbit/s, vilket är mycket långsammare än prestanda måttet.

**Prestanda analys och-justering**: för att felsöka prestanda problemet ska vi titta på hur data bearbetas och flyttas.

1. **Läsa data**: Gateway öppnar en anslutning till SQL Server och skickar frågan. SQL Server svarar genom att skicka data strömmen till gateway via intranätet.
2. **Serialisera och komprimera data**: gatewayen serialiserar data strömmen till CSV-format och komprimerar data till en bzip2-dataström.
3. **Skriv data**: Gateway laddar upp bzip2-dataströmmen till Blob Storage via Internet.

Som du kan se bearbetas och flyttas data i ett sekventiellt strömnings sätt: SQL Server > LAN > Gateway > WAN-> Blob Storage. **Den övergripande prestandan är gated med minsta data flöde i pipelinen**.

![Dataflöde](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

En eller flera av följande faktorer kan orsaka flaskhals för prestanda:

* **Källa**: SQL Server har låg genom strömning på grund av tung belastning.
* **Data Management Gateway**:
  * **LAN**: gatewayen ligger långt från SQL Server datorn och har en anslutning med låg bandbredd.
  * **Gateway**: gatewayen har nått sina inläsnings begränsningar för att utföra följande åtgärder:
    * **Serialisering**: serialisering av data strömmen till CSV-format har långsam data flöde.
    * **Komprimering**: du väljer en låg komprimerings-codec (till exempel bzip2, som är 2,8 Mbit/s med Core i7).
  * **WAN**: bandbredden mellan företags nätverket och Azure-tjänsterna är låg (till exempel T1 = 1 544 kbps; T2 = 6 312 kbit/s).
* **Sink**: Blob Storage har ett lågt data flöde. (Det här scenariot är inte troligt eftersom dess serviceavtalsgarantier minst 60 Mbit/s.)

I det här fallet kan bzip2 datakomprimering långsammare hela pipelinen. Växla till en gzip komprimerings-codec kan underlätta den här begränsningen.

## <a name="sample-scenarios-use-parallel-copy"></a>Exempel scenarier: Använd parallell kopia
**Scenario I:** Kopiera 1 000 1 MB-filer från det lokala fil systemet till Blob Storage.

**Analys och prestanda justering**: om du till exempel har installerat gateway på en quad core-dator använder Data Factory 16 parallella kopior för att flytta filer från fil systemet till Blob Storage samtidigt. Den här parallella körningen bör resultera i högt data flöde. Du kan också uttryckligen ange antalet parallella kopior. När du kopierar många små filer, bidrar parallella kopior dramatiskt genom att använda resurser på ett mer effektivt sätt.

![Scenario 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenario II**: kopiera 20 blobbar på 500 MB från Blob storage till data Lake Store Analytics och justera sedan prestanda.

**Analys och prestanda justering**: i det här scenariot kopierar data Factory data från Blob storage till data Lake Store genom att använda en enda kopia (**parallelCopies** inställt på 1) och data flytt enheter med en enda moln mängd. Data flödet du ser ligger nära det som beskrivs i avsnittet om [prestanda referens](#performance-reference).

![Scenario 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenario III**: individuell fil storlek är större än dussin tals MB och den totala volymen är stor.

**Analys och prestanda**: om du ökar **parallelCopies** resulterar det inte i bättre kopierings prestanda på grund av resurs begränsningarna i en DMU med ett enda moln. I stället bör du ange fler moln DMUs för att få fler resurser för att utföra data förflyttningen. Ange inget värde för egenskapen **parallelCopies** . Data Factory hanterar parallellitet åt dig. I det här fallet, om du anger **cloudDataMovementUnits** till 4, sker en genom strömning av fyra gånger.

![Scenario 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referens
Här följer prestanda övervakning och justering av referenser för några av de data lager som stöds:

* Azure Blob Storage: [skalbarhets-och prestanda mål för Blob Storage](../../storage/blobs/scalability-targets.md) och [Check lista för prestanda och skalbarhet för Blob Storage](../../storage/blobs/storage-performance-checklist.md).
* Azure Table Storage: [skalbarhets-och prestanda mål för](../../storage/tables/scalability-targets.md) [Check lista för tabell lagring och prestanda och skalbarhet för Table Storage](../../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: du kan [övervaka prestanda](../../sql-database/sql-database-single-database-monitor.md) och kontrol lera DTU-procenten (Database Transaction Unit)
* Azure SQL Data Warehouse: dess funktion mäts i informations lager enheter (DWU: er). Se [hantera beräknings kraft i Azure SQL Data Warehouse (översikt)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [prestanda nivåer i Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* Lokala SQL Server: [övervaka och justera för prestanda](https://msdn.microsoft.com/library/ms189081.aspx)
* Lokal fil Server: [prestanda justering för fil servrar](https://msdn.microsoft.com/library/dn567661.aspx)

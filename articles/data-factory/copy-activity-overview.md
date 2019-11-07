---
title: Kopierings aktivitet i Azure Data Factory
description: Läs mer om kopierings aktiviteten i Azure Data Factory. Du kan använda den för att kopiera data från ett käll data lager som stöds till ett mottagar data lager som stöds.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: b88983b4941143e5323ee795908cb332bdd79817
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678422"
---
# <a name="copy-activity-in-azure-data-factory"></a>Kopierings aktivitet i Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory som du använder:"]
> * [Version 1](v1/data-factory-data-movement-activities.md)
> * [Aktuell version](copy-activity-overview.md)

I Azure Data Factory kan du använda kopierings aktiviteten för att kopiera data mellan data lager som finns lokalt och i molnet. När du har kopierat data kan du använda andra aktiviteter för att ytterligare transformera och analysera den. Du kan också använda kopierings aktiviteten för att publicera omvandlings-och analys resultat för Business Intelligence (BI) och program förbrukning.

![Kopierings aktivitetens roll](media/copy-activity-overview/copy-activity.png)

Kopierings aktiviteten körs i en [integration runtime](concepts-integration-runtime.md). Du kan använda olika typer av integrerings körningar för olika data kopierings scenarier:

* När du kopierar data mellan två data lager som är offentligt tillgängliga via Internet från alla IP-adresser kan du använda Azure integration runtime för kopierings aktiviteten. Integrerings körningen är säker, tillförlitlig, skalbar och [globalt tillgänglig](concepts-integration-runtime.md#integration-runtime-location).
* När du kopierar data till och från data lager som finns lokalt eller i ett nätverk med åtkomst kontroll (till exempel ett virtuellt Azure-nätverk) måste du konfigurera en integration runtime med egen värd.

En integration runtime måste vara kopplad till varje käll-och mottagar data lager. Information om hur kopierings aktiviteten bestämmer vilken integrerings körning som ska användas finns i [bestämma vilken IR som ska användas](concepts-integration-runtime.md#determining-which-ir-to-use).

För att kopiera data från en källa till en mottagare utför tjänsten som kör kopierings aktiviteten följande steg:

1. Läser data från ett käll data lager.
2. Utför serialisering/deserialisering, komprimering/expandering, kolumn mappning och så vidare. Den utför dessa åtgärder baserat på konfigurationen av indata-DataSet, utdata-datauppsättning och kopierings aktivitet.
3. Skriver data till data lagret för mottagare/målet.

![Översikt över kopieringsaktivitet](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Data lager och format som stöds

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Filformat som stöds

Du kan använda kopierings aktiviteten för att kopiera filer som är mellan två filbaserade data lager. I det här fallet kopieras data effektivt utan att behöva serialiseras eller deserialiseras.

Kopierings aktiviteten kan också läsa från och skriva till filer i följande format:
- Text
- JSON
- Avro
- ORC
- Parquet

Kopierings aktiviteten kan komprimera och expandera filer med dessa codecenheter: 
- Gzip
- DEFLATE
- Bzip2
- ZipDeflate

Mer information finns i [fil-och komprimerings format som stöds](supported-file-formats-and-compression-codecs.md).

Du kan till exempel utföra följande kopierings aktiviteter:

* Kopiera data från en lokal SQL Server databas och Skriv data till Azure Data Lake Storage Gen2 i Parquet-format.
* Kopiera filer i text format (CSV) från ett lokalt fil system och skriva till Azure Blob Storage i Avro-format.
* Kopiera zippade filer från ett lokalt fil system, expandera dem och skriv dem till Azure Data Lake Storage Gen2.
* Kopiera data i formatet GZIP-komprimerad text (CSV) från Azure Blob Storage och skriv den till Azure SQL Database.
* Många fler aktiviteter som kräver serialisering/deserialisering eller komprimering/expandering.

## <a name="supported-regions"></a>Regioner som stöds

Tjänsten som gör det möjligt att kopiera aktiviteten är tillgänglig globalt i de regioner och geografiska områden som visas i [Azure integration runtime-platser](concepts-integration-runtime.md#integration-runtime-location). Den globalt tillgängliga topologin garanterar effektiv data förflyttning som vanligt vis förhindrar hopp över flera regioner. Se [produkter efter region](https://azure.microsoft.com/regions/#services) för att kontrol lera tillgängligheten för Data Factory och data förflyttning i en angiven region.

## <a name="configuration"></a>Konfiguration

Om du vill använda kopierings aktiviteten i Azure Data Factory måste du:

1. **Skapa länkade tjänster för käll data lagret och data lagret för mottagare.** Information om konfigurations information och vilka egenskaper som stöds finns i avsnittet "länkade tjänst egenskaper" i kopplings artikeln. Du hittar listan över anslutningar som stöds i avsnittet [data lager och format som stöds](#supported-data-stores-and-formats) i den här artikeln.
2. **Skapa data uppsättningar för källan och mottagaren.** Information om konfigurations information och vilka egenskaper som stöds hittar du i avsnitten "data uppsättnings egenskaper" i artiklarna källa och mottagar koppling.
3. **Skapa en pipeline med kopierings aktiviteten.** Nästa avsnitt innehåller ett exempel.

### <a name="syntax"></a>Syntax

Följande mall för en kopierings aktivitet innehåller en fullständig lista över vilka egenskaper som stöds. Ange de som passar ditt scenario.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>Information om syntax

| Egenskap | Beskrivning | Krävs? |
|:--- |:--- |:--- |
| typ | För en kopierings aktivitet anger du till `Copy` | Ja |
| tillför | Ange den data uppsättning som du har skapat som pekar på käll data. Kopierings aktiviteten har endast stöd för en enda Indatatyp. | Ja |
| utdata | Ange den data uppsättning som du har skapat som pekar på mottagar data. Kopierings aktiviteten har endast stöd för en enda utdata. | Ja |
| typeProperties | Ange egenskaper för att konfigurera kopierings aktiviteten. | Ja |
| källa | Ange typ av kopierings källa och motsvarande egenskaper för att hämta data.<br/><br/>Mer information finns i avsnittet "Kopiera aktivitets egenskaper" i den kopplings artikel som visas i [data lager och format som stöds](#supported-data-stores-and-formats). | Ja |
| Sjönk | Ange typ av kopierings mottagare och motsvarande egenskaper för att skriva data.<br/><br/>Mer information finns i avsnittet "Kopiera aktivitets egenskaper" i den kopplings artikel som visas i [data lager och format som stöds](#supported-data-stores-and-formats). | Ja |
| konvertera | Ange explicita kolumn mappningar från källan till Sink. Den här egenskapen gäller när standard kopierings beteendet inte uppfyller dina behov.<br/><br/>Mer information finns i [schema mappning i kopierings aktivitet](copy-activity-schema-and-type-mapping.md). | Nej |
| dataIntegrationUnits | Ange ett mått som representerar den mängd potens som [Azure integration runtime](concepts-integration-runtime.md) använder för data kopiering. Dessa enheter kallades tidigare för moln data förflyttnings enheter (DMU). <br/><br/>Mer information finns i [data integrerings enheter](copy-activity-performance.md#data-integration-units). | Nej |
| parallelCopies | Ange den parallellitet som du vill att kopierings aktiviteten ska använda vid inläsning av data från källan och skrivning av data till mottagaren.<br/><br/>Mer information finns i [parallell kopiering](copy-activity-performance.md#parallel-copy). | Nej |
| enableStaging<br/>stagingSettings | Ange om du vill mellanlagra interims data i Blob Storage i stället för att kopiera data direkt från källa till mottagare.<br/><br/>Information om användbara scenarier och konfigurations information finns i [mellanlagrad kopia](copy-activity-performance.md#staged-copy). | Nej |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Välj hur du vill hantera inkompatibla rader när du kopierar data från källa till mottagare.<br/><br/>Mer information finns i [fel tolerans](copy-activity-fault-tolerance.md). | Nej |

## <a name="monitoring"></a>Övervakning

Du kan övervaka körningen av kopierings aktiviteten i Azure Data Factory **författar & övervaka** användar gränssnitt eller program mässigt.

### <a name="monitor-visually"></a>Övervaka visuellt

För att visuellt övervaka kopierings aktiviteten, går du till din data fabrik och går sedan till **redigera & övervakare**. På fliken **övervaka** kan du se en lista över pipelines som körs med knappen **Visa aktivitets körning** i kolumnen **åtgärder** :

![Övervaka pipelinekörningar](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Välj **Visa aktivitets körningar** för att se listan över aktiviteter i pipeline-körningen. I kolumnen **åtgärder** visas länkar till indata för kopierings aktiviteten, utdata, fel (om körningen av kopierings aktiviteten Miss lyckas) och information:

![Övervaka aktivitetskörningar](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Välj knappen **information** i kolumnen **åtgärder** för att se kopierings aktivitetens körnings information och prestanda egenskaper. Du ser information som volym/antal rader/antal filer med data som kopieras från källa till mottagare, data flöde, steg som kopierings aktiviteten går igenom med motsvarande varaktighet och konfigurationer som används för ditt kopierings scenario.

>[!TIP]
>I vissa fall visas också **prestanda justerings tips** överst på sidan Kopiera övervakning. De här tipsen visar dig om identifierade Flask halsar och ger information om vad som ska ändras för att förstärka kopieringen av data. Ett exempel finns i avsnittet [prestanda och justering](#performance-and-tuning) i den här artikeln.

**Exempel: kopiera från Amazon S3 till Azure Data Lake Store**
![övervaka aktivitets körnings information](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Exempel: kopiera från Azure SQL Database till Azure SQL Data Warehouse med mellanlagrad kopiering**
![övervaka aktivitets körnings information](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Övervaka program mässigt

Information om körningen av kopierings aktiviteten och prestanda egenskaperna returneras också i avsnittet **kopierings aktivitets körnings** > **utdata** . Nedan visas en fullständig lista över egenskaper som kan returneras. Du ser bara de egenskaper som gäller för ditt kopierings scenario. Information om hur du övervakar aktivitets körningar finns i [övervaka en pipeline-körning](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Egenskapsnamn  | Beskrivning | Enhet |
|:--- |:--- |:--- |
| DataRead | Mängden data som läses från källan. | Int64-värde, i byte |
| DataWritten | Mängden data som skrivs till Sink. | Int64-värde, i byte |
| filesRead | Antal filer som kopierats från fil lagring. | Int64-värde (ingen enhet) |
| filesWritten | Antal filer som kopierats under kopiering till fil lagring. | Int64-värde (ingen enhet) |
| sourcePeakConnections | Det högsta antalet samtidiga anslutningar som upprättats till käll data lagret under kopierings aktivitets körningen. | Int64-värde (ingen enhet) |
| sinkPeakConnections | Det högsta antalet samtidiga anslutningar som upprättats till mottagar data lagret under kopierings aktiviteten. | Int64-värde (ingen enhet) |
| rowsRead | Antal rader som lästs från källan (gäller inte för binär kopia). | Int64-värde (ingen enhet) |
| rowsCopied | Antal rader som kopierats till Sink (gäller inte för binär kopia). | Int64-värde (ingen enhet) |
| rowsSkipped | Antal inkompatibla rader som hoppades över. Du kan aktivera inkompatibla rader som ska hoppas över genom att ange `enableSkipIncompatibleRow` till true. | Int64-värde (ingen enhet) |
| copyDuration | Kopierings körningens längd. | Int32-värde, i sekunder |
| Dataflöde | Hastighet för data överföring. | Flytt ALS nummer, i kbit/s |
| sourcePeakConnections | Det högsta antalet samtidiga anslutningar som upprättats till käll data lagret under kopierings aktivitets körningen. | Int32-värde (ingen enhet) |
| sinkPeakConnections| Det högsta antalet samtidiga anslutningar som upprättats till mottagar data lagret under kopierings aktiviteten.| Int32-värde (ingen enhet) |
| sqlDwPolyBase | Om PolyBase används när data kopieras till SQL Data Warehouse. | Boolesk |
| redshiftUnload | Anger om borttagning används när data kopieras från RedShift. | Boolesk |
| hdfsDistcp | Om DistCp används när data kopieras från HDFS. | Boolesk |
| effectiveIntegrationRuntime | Integrerings körningen (IR) eller körningar som används för att köra aktivitets körningen i formatet `<IR name> (<region if it's Azure IR>)`. | Text (sträng) |
| usedDataIntegrationUnits | De effektiva enheterna för data integrering under kopiering. | Int32-värde |
| usedParallelCopies | Det effektiva parallelCopies under kopieringen. | Int32-värde |
| redirectRowPath | Sökväg till loggen över inkompatibla rader i blob-lagringen som du konfigurerar i egenskapen `redirectIncompatibleRowSettings`. Se [fel tolerans](#fault-tolerance) senare i den här artikeln. | Text (sträng) |
| executionDetails | Mer information om de steg som kopierings aktiviteten går igenom och motsvarande steg, varaktigheter, konfigurationer och så vidare. Vi rekommenderar inte att du analyserar det här avsnittet eftersom det kan ändras.<br/><br/>Data Factory rapporterar även detaljerade varaktigheter (i sekunder) som ägnats åt olika stadier under `detailedDurations`. Varaktigheten för de här stegen är exklusiva. Endast varaktigheter som gäller för den givna kopierings aktivitets körningen visas:<br/>**Köernas varaktighet** (`queuingDuration`): hur lång tid det tar innan kopierings aktiviteten startar i integration Runtime. Om du använder en IR med egen värd och detta värde är stort, kontrollerar du IR-kapaciteten och användningen och skalar upp eller ut enligt din arbets belastning. <br/>**Skript varaktighet för förkopiering** (`preCopyScriptDuration`): den tid som förflutit mellan när kopierings aktiviteten startar på IR-filen och när kopierings aktiviteten har slutfört körningen av skriptet för för kopiering i data lagret för mottagare. Gäller när du konfigurerar skriptet för att kopiera. <br/>**Tid till första byte** (`timeToFirstByte`): tiden som förflutit mellan föregående stegs slut och den tid då IR tar emot den första byten från käll data lagret. Gäller icke-filbaserade källor. Om det här värdet är stort kontrollerar du och optimerar frågan eller servern.<br/>**Överförings varaktighet** (`transferDuration`): tiden som förflutit mellan föregående steg och den tid då IR överför alla data från källa till mottagare. | Matris |
| perfRecommendation | Kopiera optimerings tips för prestanda. Se [prestanda och justering](#performance-and-tuning) för mer information. | Matris |

```json
"output": {
    "dataRead": 6198358,
    "dataWritten": 19169324,
    "filesRead": 1,
    "sourcePeakConnections": 1,
    "sinkPeakConnections": 2,
    "rowsRead": 39614,
    "rowsCopied": 39614,
    "copyDuration": 1325,
    "throughput": 4.568,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 4,
    "usedParallelCopies": 1,
    "executionDetails": [
        {
            "source": {
                "type": "AzureBlobStorage"
            },
            "sink": {
                "type": "AzureSqlDatabase"
            },
            "status": "Succeeded",
            "start": "2019-08-06T01:01:36.7778286Z",
            "duration": 1325,
            "usedDataIntegrationUnits": 4,
            "usedParallelCopies": 1,
            "detailedDurations": {
                "queuingDuration": 2,
                "preCopyScriptDuration": 12,
                "transferDuration": 1311
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "Sink Azure SQL Database: The DTU utilization was high during the copy activity run. To achieve better performance, you are suggested to scale the database to a higher tier than the current 1600 DTUs.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2043368",
            "RuleName": "AzureDBTierUpgradePerfRecommendRule"
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Schema-och data typs mappning

Se [schema-och data typs mappning](copy-activity-schema-and-type-mapping.md) för information om hur kopierings aktiviteten mappar dina källdata till din mottagare.

## <a name="fault-tolerance"></a>Feltolerans

Som standard slutar kopierings aktiviteten att kopiera data och returnerar ett problem när käll data rader är inkompatibla med mottagar data rader. För att kopieringen ska lyckas kan du konfigurera kopierings aktiviteten så att den hoppar över och loggar de inkompatibla raderna och bara kopierar de kompatibla data. Mer information finns i [fel tolerans för kopierings aktivitet](copy-activity-fault-tolerance.md) .

## <a name="performance-and-tuning"></a>Prestanda- och justering

I [guiden Kopiera aktivitet prestanda och skalbarhet](copy-activity-performance.md) beskrivs viktiga faktorer som påverkar prestanda för data förflyttning via kopierings aktiviteten i Azure Data Factory. Den visar även de prestanda värden som observerats under testningen och beskriver hur kopierings aktiviteten optimeras.

I vissa fall kan du när du kör en kopierings aktivitet i Data Factory se **tips för prestanda justering** överst på [sidan för övervakning av kopierings aktiviteter](#monitor-visually), som du ser i följande exempel. Tipsen visar vilken Flask hals som identifierats för den angivna kopierings körningen. De ger också information om vad som ska ändras för att förstärka kopieringen av data. Tipsen för prestanda justering ger för närvarande förslag som att använda PolyBase när du kopierar data till Azure SQL Data Warehouse, ökar Azure Cosmos DB ru: er eller Azure SQL Database DTU: er när resursen på data lagrings sidan är Flask halsen och tar bort onödiga mellanlagrade kopior.

**Exempel: kopiera till Azure SQL Database med ett prestanda justerings tips**

I det här exemplet under en kopierings körning spårar Data Factory en hög DTU-användning i mottagarens Azure SQL Database. Det här tillståndet saktar ned Skriv åtgärder. Förslaget är att öka DTU: er på Azure SQL Databases nivå:

![Kopiera övervakning med prestanda justerings tips](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Stegvis kopia
Med Data Factory kan du stegvis kopiera delta data från ett käll data lager till ett data lager för mottagare. Mer information finns i [Självstudier: kopiera data stegvis](tutorial-incremental-copy-overview.md).

## <a name="next-steps"></a>Nästa steg
Se följande snabb starter, självstudier och exempel:

- [Kopiera data från en plats till en annan plats i samma Azure Blob Storage-konto](quickstart-create-data-factory-dot-net.md)
- [Kopiera data från Azure Blob Storage till Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Kopiera data från en lokal SQL Server-databas till Azure](tutorial-hybrid-copy-powershell.md)

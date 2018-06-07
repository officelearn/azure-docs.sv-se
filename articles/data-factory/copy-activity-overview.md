---
title: Kopieringsaktiviteten i Azure Data Factory | Microsoft Docs
description: Läs mer om kopieringsaktiviteten i Azure Data Factory som du kan använda för att kopiera data från ett dataarkiv som stöds källan till datakällan stöds mottagare.
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
ms.date: 05/01/2018
ms.author: jingwang
ms.openlocfilehash: c5ec07603088edd3f95f08f12b6982022b396d05
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618495"
---
# <a name="copy-activity-in-azure-data-factory"></a>Kopieringsaktiviteten i Azure Data Factory

## <a name="overview"></a>Översikt

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-data-movement-activities.md)
> * [Version 2 – förhandsversion](copy-activity-overview.md)

Du kan använda Kopieringsaktiviteten i Azure Data Factory för att kopiera data mellan data lagras lokalt och i molnet. När data kopieras, kan ytterligare omvandlas och analyseras. Du kan också använda Kopieringsaktiviteten för att publicera omvandling och analysresultat för business intelligence (BI) och förbrukning av programmet.

![Rollen för Kopieringsaktivitet](media/copy-activity-overview/copy-activity.png)

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i V1](v1/data-factory-data-movement-activities.md).

Kopieringsaktiviteten körs på en [integrering Runtime](concepts-integration-runtime.md). Olika smak Integration Runtime kan utnyttjas för olika kopiera scenariot:

* När kopiera data mellan data lagrar båda är offentligt tillgänglig, kan ha rätt kopieringsaktiviteten av **Azure Integration Runtime**, vilket är säker, tillförlitlig och skalbar och [globalt tillgänglig](concepts-integration-runtime.md#integration-runtime-location).
* När data kopieras från/till datalager finns lokalt eller i ett nätverk med åtkomstkontroll (till exempel Azure virtuellt nätverk), måste du ställa in en **egenvärdbaserat integrerad Runtime** att möta kopiering av data.

Integration Runtime måste vara kopplad till varje källa och mottagare datalagret. Mer information om hur kopieringsaktiviteten [avgör vilka IR att använda](concepts-integration-runtime.md#determining-which-ir-to-use).

Kopieringsaktiviteten går igenom följande steg för att kopiera data från en källa till en mottagare. Den tjänst som används för Kopieringsaktiviteten:

1. Läser data från ett dataarkiv som källa.
2. Utför serialisering/deserialisering, komprimering/dekomprimering, kolumnmappningen osv. Den gör dessa åtgärder baserat på konfigurationer av inkommande dataset, utdatauppsättningen och Kopieringsaktivitet.
3. Skriver data till datalagret sink/mål.

![Översikt över kopieringsaktivitet](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Lagrar data som stöds och format

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Filformat som stöds

Du kan använda Kopieringsaktiviteten till **kopiera filer som-är** mellan två filbaserade datalager, i vilket fall data kopieras effektivt utan någon serialisering/deserialisering.

Kopieringsaktiviteten stöder också läsa från och skriva till filer i angivna format: **Text, JSON, Avro, ORC och parkettgolv**, och komprimerings-codec **GZip, Deflate, BZip2 och ZipDeflate** stöds. Se [stöds format och komprimering](supported-file-formats-and-compression-codecs.md) med information.

Du kan till exempel göra kopiera följande aktiviteter:

* Kopierar data i lokal SQL Server och skriva till Azure Data Lake Store i ORC-format.
* Kopiera filerna i textformat (CSV) från lokala filsystemet och skriva till Azure Blob i Avro-formatet.
* Kopiera komprimerade filer från lokala filsystemet och expandera sedan mark till Azure Data Lake Store.
* Kopiera data i GZip komprimerade (CSV)-format från Azure Blob och skriva till Azure SQL Database.

## <a name="supported-regions"></a>Regioner som stöds

Den tjänst som används för Kopieringsaktiviteten är tillgängligt globalt i regioner och geografiska områden som anges i [Azure Integration Runtime platser](concepts-integration-runtime.md#integration-runtime-location). Globalt tillgänglig topologin garanterar effektiv dataflyttning som vanligtvis undviker mellan region hopp. Se [tjänster efter region](https://azure.microsoft.com/regions/#services) tillgänglighet för Data Factory och flytt av Data i en region.

## <a name="configuration"></a>Konfiguration

Om du vill använda kopieringsaktiviteten i Azure Data Factory, måste du:

1. **Skapa länkade tjänster för datalager för källa och mottagare datalagret.** Referera till artikeln connector ”länkade tjänstegenskaper” avsnitt om hur du konfigurerar och egenskaper som stöds. Du kan hitta listan stöds koppling i [datalager och format stöds](#supported-data-stores-and-formats) avsnitt.
2. **Skapa datauppsättningar för källa och mottagare.** Referera till källa och mottagare connector artiklar ”egenskaper för datamängd” avsnittet om hur du konfigurerar och egenskaper som stöds.
3. **Skapa en pipeline med kopieringsaktiviteten.** Nästa avsnitt innehåller ett exempel.  

### <a name="syntax"></a>Syntax

Följande mall för en kopia aktivitet innehåller en fullständig lista över egenskaper som stöds. Ange de som passar din situation.

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
            "cloudDataMovementUnits": <number>,
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

### <a name="syntax-details"></a>Information om syntax

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för en kopieringsaktiviteten måste anges till: **kopia** | Ja |
| inmatningar | Ange den dataset som du skapade som pekar till källdata. Kopieringsaktiviteten stöder bara en enda inmatning. | Ja |
| utdata | Ange datamängden som du skapade som pekar på sink-data. Kopieringsaktiviteten stöder bara ett enda utflöde. | Ja |
| typeProperties | En grupp egenskaper att konfigurera kopieringsaktiviteten. | Ja |
| källa | Ange källtypen kopia och motsvarande egenskaper på hur du hämtar data.<br/><br/>Mer information i avsnittet ”Kopiera Aktivitetsegenskaper” i kopplingen artikeln i [datalager och format stöds](#supported-data-stores-and-formats). | Ja |
| sink | Ange Mottagartypen kopia och motsvarande egenskaper på hur du skriver data.<br/><br/>Mer information i avsnittet ”Kopiera Aktivitetsegenskaper” i kopplingen artikeln i [datalager och format stöds](#supported-data-stores-and-formats). | Ja |
| Översättare | Ange explicita kolumnmappningar från källan till mottagare. Används när kopiera standardbeteendet inte kan uppfylla dina behov.<br/><br/>Mer information från [Schema och data mappning](copy-activity-schema-and-type-mapping.md). | Nej |
| cloudDataMovementUnits | Ange powerfulness av [Azure Integration Runtime](concepts-integration-runtime.md) att möta kopiering av data.<br/><br/>Mer information från [molnet data movement enheter](copy-activity-performance.md). | Nej |
| parallelCopies | Ange parallellitet som du vill kopiera aktiviteter ska användas när data lästes från käll- och skriver data till mottagare.<br/><br/>Mer information från [parallell kopiera](copy-activity-performance.md#parallel-copy). | Nej |
| enableStaging<br/>stagingSettings | Välj att mellanlagra mellanliggande data i aa blob storage i stället för att kopieringsdata direkt från källan till mottagare.<br/><br/>Lär dig användbar scenarier och konfigurationsinformation från [mellanlagrad kopiera](copy-activity-performance.md#staged-copy). | Nej |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Välj hur du hanterar inkompatibla rader när du kopierar data från källan till mottagare.<br/><br/>Mer information från [feltolerans](copy-activity-fault-tolerance.md). | Nej |

## <a name="monitoring"></a>Övervakning

Du kan övervaka kopieringsaktiviteten körs på Azure Data Factory ”författare och övervakaren” Användargränssnittet eller programmässigt. Du kan sedan jämföra prestanda och konfigurationen av din scenariot för att kopiera aktivitetens [Prestandareferens](copy-activity-performance.md#performance-reference) från interna tester.

### <a name="monitor-visually"></a>Övervaka visuellt

Om du vill övervaka kopieringsaktiviteten kör visuellt, gå till din data factory -> **författare & övervakaren** -> **övervakningsfliken**, visas en lista över pipelinen körs med en ”visa aktiviteten körs” länk i  **Åtgärder** kolumn. 

![Övervaka pipelinekörningar](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Klicka om du vill se en lista över aktiviteter i denna pipeline-körning. I den **åtgärder** kolumn och, du har länkar till kopiera aktivitet indata, utdata, fel (om det inte går att köra kopieringsaktiviteten) och information.

![Övervaka aktivitetskörningar](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Klicka på den ”**information**” länken under **åtgärder** Kopiera aktivitetens körning information och prestandaegenskaper. Den visar information, inklusive volym/rader/filer för data som kopieras från källan till mottagare, dataflöde, steg den passerar med motsvarande varaktighet och konfigurationer för ditt scenario kopia.

**Exempel: kopiera från Amazon S3 till Azure Data Lake Store**
![övervakaraktiviteten kör information](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Exempel: kopiera från Azure SQL-databas till Azure SQL Data Warehouse med mellanlagrad kopiera**
![övervakaraktiviteten kör information](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Övervaka programmässigt

Kopiera aktivitetsinformation körning och prestandaegenskaper returneras också i en Kopieringsaktivitet kör resultatet -> utdata. Nedan visas en fullständig förteckning; de tillämpliga som att kopiera scenariot kommer att visas. Lär dig att övervaka aktivitet som körs från [quickstart avsnittet övervakning](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Egenskapsnamn  | Beskrivning | Enhet |
|:--- |:--- |:--- |
| DataRead | Storleken på data som läses från källan | Int64-intervall i **byte** |
| DataWritten | Storleken på data som skrivs till mottagare | Int64-intervall i **byte** |
| filerskrivskyddad | Antal filer som kopieras när du kopierar data från fillagring. | Int64-intervall (ingen enhet) |
| filesWritten | Antal filer som kopieras vid kopiering av data till fillagring. | Int64-intervall (ingen enhet) |
| rowsCopied | Antal rader som kopieras (gäller inte för binär kopian). | Int64-intervall (ingen enhet) |
| rowsSkipped | Antal inkompatibla rader hoppas över. Du kan aktivera funktionen genom att ange ”enableSkipIncompatibleRow” till true. | Int64-intervall (ingen enhet) |
| Dataflöde | Förhållandet mellan där data överförs | Flyttal i **KB/s** |
| copyDuration | Varaktighet för kopian | Int32-värde i sekunder |
| sqlDwPolyBase | Om PolyBase används vid kopiering av data till SQL Data Warehouse. | Boolesk |
| redshiftUnload | Om UNLOAD används när du kopierar data från Redshift. | Boolesk |
| hdfsDistcp | Om DistCp används när du kopierar data från HDFS. | Boolesk |
| effectiveIntegrationRuntime | Visa som Integration Runtime(s) används för att ge aktiviteten kör i formatet `<IR name> (<region if it's Azure IR>)`. | Text (sträng) |
| usedCloudDataMovementUnits | Effektiv moln data movement enheterna vid kopiering. | Int32-värde |
| usedParallelCopies | Effektiva parallelCopies vid kopiering. | Int32-värde|
| redirectRowPath | Sökvägen till en logg för överhoppade inkompatibla rader i blob storage som du konfigurerar under ”redirectIncompatibleRowSettings”. Se exemplet nedan. | Text (sträng) |
| executionDetails | Mer information om stegen kopieringsaktiviteten passerar och motsvarande steg, varaktighet, används konfigurationer, etc. Vi rekommenderar inte för att parsa det här avsnittet som den kan ändras. | Matris |

```json
"output": {
    "dataRead": 107280845500,
    "dataWritten": 107280845500,
    "filesRead": 10,
    "filesWritten": 10,
    "copyDuration": 224,
    "throughput": 467707.344,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US 2)",
    "usedCloudDataMovementUnits": 32,
    "usedParallelCopies": 8,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureDataLakeStore"
            },
            "status": "Succeeded",
            "start": "2018-01-17T15:13:00.3515165Z",
            "duration": 221,
            "usedCloudDataMovementUnits": 32,
            "usedParallelCopies": 8,
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 219
            }
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Schemat och datatypsmappningen

Finns det [Schema och data mappning](copy-activity-schema-and-type-mapping.md), som beskriver hur kopieringsaktiviteten mappas datakällan till mottagare.

## <a name="fault-tolerance"></a>Feltolerans

Som standard kopieringsaktiviteten slutar att kopiera data och returnerar fel vid inkompatibla data mellan käll- och mottagarnoderna. Du kan uttryckligen konfigurera om du vill hoppa över och logga inkompatibla rader och bara kopiera dessa kompatibel datakälla om du vill att kopiera lyckades. Finns det [Kopieringsaktiviteten feltolerans](copy-activity-fault-tolerance.md) för mer information.

## <a name="performance-and-tuning"></a>Prestanda- och justering

Finns det [prestandajustering guide och Kopieringsaktivitet prestanda](copy-activity-performance.md), som beskriver viktiga faktorer som påverkar prestandan för flytt av data (Kopieringsaktiviteten) i Azure Data Factory. Den visar observerade prestanda under interna tester och beskrivs olika sätt för att optimera prestanda för Kopieringsaktivitet.

## <a name="incremental-copy"></a>Inkrementell kopia 
Data Factory version 2 har stöd för scenarier för att kopiera inkrementellt deltadata från ett dataarkiv som källa till ett dataarkiv som mål. Se [Självstudier: kopiera data inkrementellt](tutorial-incremental-copy-overview.md). 

## <a name="read-and-write-partitioned-data"></a>Läsa och skriva partitionerade data
Azure Data Factory stöds läsning eller skrivning partitionerade data med hjälp av SliceStart/SliceEnd/WindowStart/WindowEnd systemvariabler i version 1. Du kan åstadkomma detta genom att använda en pipeline-parameter och utlösarens schemalagd tid/starttid som ett värde för parametern i version 2. Mer information finns i [så att läsa eller skriva partitionerad data](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Nästa steg
Se följande Snabbstart, självstudier och exempel:

- [Kopiera data från en plats till en annan plats i samma Azure Blob Storage](quickstart-create-data-factory-dot-net.md)
- [Kopiera data från Azure Blobblagring till Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Kopiera data från lokala SQL Server till Azure](tutorial-hybrid-copy-powershell.md)

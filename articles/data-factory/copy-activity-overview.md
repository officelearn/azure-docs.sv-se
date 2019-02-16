---
title: Kopiera aktivitet i Azure Data Factory | Microsoft Docs
description: Läs mer om kopieringsaktiviteten i Azure Data Factory som du kan använda för att kopiera data från ett dataarkiv till ett datalager för mottagare som stöds.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: jingwang
ms.openlocfilehash: 154e0dcefab6d5bcdfc9532ba4258d09593f0970
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311147"
---
# <a name="copy-activity-in-azure-data-factory"></a>Kopiera aktivitet i Azure Data Factory

## <a name="overview"></a>Översikt

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-data-movement-activities.md)
> * [Aktuell version](copy-activity-overview.md)

Du kan använda Kopieringsaktivitet i Azure Data Factory för att kopiera data mellan data butiker finns lokalt och i molnet. När data kopieras kan den ytterligare omvandlas och analyseras. Du kan också använda Kopieringsaktivitet för att publicera omvandling och analysresultat för business intelligence (BI) och förbrukning av programmet.

![Rollen för Kopieringsaktivitet](media/copy-activity-overview/copy-activity.png)

Kopieringsaktivitet körs på en [Integreringskörningen](concepts-integration-runtime.md). Olika smak för Integration Runtime kan utnyttjas för olika kopia scenariot:

* När kopiering av data mellan data lagrar att båda är offentligt tillgänglig, Kopieringsaktivitet kan få behörighet av **Azure Integration Runtime**, vilket är säker, tillförlitlig och skalbar och [globalt tillgänglig](concepts-integration-runtime.md#integration-runtime-location).
* När kopierar data från/till datalager som finns lokalt eller i ett nätverk med åtkomstkontroll (till exempel Azure Virtual Network) kan du behöva ställa in en **integrerad Runtime med egen värd** möjligheter för kopiering av data.

Integreringskörningen måste vara kopplad till varje datalager för källa och mottagare. Mer information om hur Kopieringsaktivitet [avgör vilken IR som ska använda](concepts-integration-runtime.md#determining-which-ir-to-use).

Kopieringsaktivitet går igenom följande steg för att kopiera data från en källa till en mottagare. Den tjänst som används av Kopieringsaktiviteten:

1. Läser data från ett källdatalager.
2. Utför serialisering/deserialisering, komprimering/dekomprimering, kolumnmappning osv. Dessa åtgärder baserat på konfigurationerna för datauppsättningen för indata, utdata datauppsättningen och Kopieringsaktivitet sker.
3. Skriver data till mottagare/måldatalagret.

![Översikt över kopieringsaktivitet](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Datalager som stöds och format

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Filformat som stöds

Du kan använda Kopieringsaktivitet som **kopiera filer som – är** mellan två filbaserat datalager, där fallet data kopieras effektivt utan att någon serialisering/deserialisering.

Kopieringsaktivitet har också stöd för att läsa från och skriva till filer i angivna format: **Text, JSON, Avro, ORC och Parquet**, och komprimerings-codec **GZip, Deflate, BZip2 och ZipDeflate** stöds. Se [stöds format och komprimering](supported-file-formats-and-compression-codecs.md) med information.

Du kan exempelvis göra följande kopieringsaktiviteter:

* Kopiera data i en lokal SQL Server och skriva till Azure Data Lake Store i ORC-format.
* Kopiera filer i textformat (CSV) från den lokala filsystem och skriva till Azure Blob i Avro-format.
* Kopiera komprimerade filer från den lokala filsystem och expandera sedan mark till Azure Data Lake Store.
* Kopiera data i GZip-komprimerade textfiler (CSV)-format från Azure Blob och skriva till Azure SQL Database.

## <a name="supported-regions"></a>Regioner som stöds

Tjänsten som driver Kopieringsaktivitet är tillgängligt globalt i regioner och geografiska områden som anges i [Azure Integration Runtime platser](concepts-integration-runtime.md#integration-runtime-location). Globalt tillgänglig topologin säkerställer effektiv dataförflyttning som vanligtvis undviker interregionala hopp. Se [tjänster efter region](https://azure.microsoft.com/regions/#services) för tillgänglighet för Data Factory och dataförflyttning i en region.

## <a name="configuration"></a>Konfiguration

Om du vill använda Kopieringsaktivitet i Azure Data Factory, måste du:

1. **Skapa länkade tjänster för källans datalager och datalager för mottagare.** Se artikeln connector ”länkade tjänstegenskaper” avsnittet om hur du konfigurerar och egenskaper som stöds. Du hittar listan stöds connector i [datalager och format som stöds](#supported-data-stores-and-formats) avsnittet.
2. **Skapa datauppsättningar för källa och mottagare.** Referera till källan och mottagare connector artiklarnas ”egenskaper för datamängd” avsnittet om hur du konfigurerar och egenskaper som stöds.
3. **Skapa en pipeline med en Kopieringsaktivitet.** Nästa avsnitt innehåller ett exempel.  

### <a name="syntax"></a>Syntax

Följande mall med en Kopieringsaktivitet innehåller en fullständig förteckning över egenskaper som stöds. Ange de som passar din situation.

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

### <a name="syntax-details"></a>Information om syntax

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för en Kopieringsaktivitet måste anges till: **Kopiera** | Ja |
| indata | Ange den datauppsättning som du skapade som pekar till källdata. Kopieringsaktivitet stöder bara en enda indata. | Ja |
| utdata | Ange den datauppsättning som du skapade som pekar till mottagardata. Kopieringsaktivitet stöder bara ett enda utflöde. | Ja |
| typeProperties | En grupp egenskaper för att konfigurera kopieringsaktiviteten. | Ja |
| källa | Ange den kopiering av källtypen och motsvarande egenskaper på hur du hämtar data.<br/><br/>Få mer detaljerad information från avsnittet ”Kopiera Aktivitetsegenskaper” i connector artikeln listas i [datalager och format som stöds](#supported-data-stores-and-formats). | Ja |
| mottagare | Ange kopieringsmottagartyper och motsvarande egenskaper om hur du skriver data.<br/><br/>Få mer detaljerad information från avsnittet ”Kopiera Aktivitetsegenskaper” i connector artikeln listas i [datalager och format som stöds](#supported-data-stores-and-formats). | Ja |
| Translator | Ange explicita kolumnmappningarna från källa till mottagare. Gäller när kopia standardbeteendet inte uppfyller dina behov.<br/><br/>Få mer detaljerad information från [Schema och data typmappningen](copy-activity-schema-and-type-mapping.md). | Nej |
| dataIntegrationUnits | Ange powerfulness av [Azure Integration Runtime](concepts-integration-runtime.md) möjligheter för kopiering av data. Kallades tidigare för molnet dmu-enheter (Data Movement här). <br/><br/>Få mer detaljerad information från [integrering enheter](copy-activity-performance.md#data-integration-units). | Nej |
| parallelCopies | Ange parallellitet som du vill Kopieringsaktivitet ska användas vid läsning av data från käll- och skriva data till mottagare.<br/><br/>Få mer detaljerad information från [parallell kopiera](copy-activity-performance.md#parallel-copy). | Nej |
| enableStaging<br/>stagingSettings | Välja att mellanlagra tillfälliga data i ditt blob storage i stället för direkt kopieringsdata från källa till mottagare.<br/><br/>Lär dig användbara scenarier och konfigurationsinformation från [mellanlagrad kopiering](copy-activity-performance.md#staged-copy). | Nej |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Välj hur du kan hantera inkompatibla rader vid kopiering av data från källa till mottagare.<br/><br/>Få mer detaljerad information från [feltolerans](copy-activity-fault-tolerance.md). | Nej |

## <a name="monitoring"></a>Övervakning

Du kan övervaka kopieringsaktivitetskörningen på Azure Data Factory ”författare och Övervakare” Användargränssnittet eller programmässigt. Du kan sedan jämföra prestanda och konfiguration av ditt scenario för att Kopieringsaktivitet [Prestandareferens](copy-activity-performance.md#performance-reference) från interna testning.

### <a name="monitor-visually"></a>Övervaka visuellt

För att visuellt övervaka körningen av kopieringsaktiviteten, gå till din data factory -> **författare och Övervakare** -> **övervakningsfliken**, visas en lista över pipeline körs med en ”visa Aktivitetskörningar”-länk i  **Åtgärder** kolumn. 

![Övervaka pipelinekörningar](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Klicka om du vill se en lista över aktiviteter i denna pipeline-körning. I den **åtgärder** kolumnen har länkar till kopiera aktivitetsindata, utdata, fel (om det inte går att körningen av kopieringsaktiviteten) och information.

![Övervaka aktivitetskörningar](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Klicka på den ”**information**” länka **åtgärder** visa körningsinformation om Kopieringsaktivitet och prestandaegenskaper. Den visar information inklusive volym/rader/filer av data som kopieras från källan till mottagare, dataflöde, steg den går igenom med motsvarande tid och används av konfigurationer för ditt scenario kopia. 

>[!TIP]
>För vissa scenarier, du kan även se ”**prestandajustering tips**” ovanpå kopian övervakning som talar om flaskhalsen identifieras och hjälper dig på vad du kan ändra för att öka dataflödet kopia, se exempel med information [här](#performance-and-tuning).

**Exempel: kopiera från Amazon S3 till Azure Data Lake Store**
![övervakaren aktivitetskörningsinformation](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Exempel: kopiera från Azure SQL Database till Azure SQL Data Warehouse med hjälp av mellanlagrad kopiering**
![övervakaren aktivitetskörningsinformation](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Övervaka programmässigt

Körningen kopiering och prestandaegenskaper returneras också i resultatet för Kopieringsaktivitetskörningen -> Outputs-avsnittet. Nedan visas en fullständig förteckning; bara de tillämpliga filer för ditt scenario kopia visas. Lär dig att övervaka aktivitet som körs från [Snabbstart avsnittet övervakning](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Egenskapsnamn  | Beskrivning | Enhet |
|:--- |:--- |:--- |
| DataRead | Storleken på data som läses från källa | Int64 värdet i **byte** |
| DataWritten | Storleken på data som skrivs till mottagare | Int64 värdet i **byte** |
| filerskrivskyddad | Antal filer som kopieras när du kopierar data från fillagring. | Int64-värde (ingen enhet) |
| fileScanned | Antal filer som genomsöks från källan för file storage. | Int64-värde (ingen enhet) |
| filesWritten | Antal filer som kopieras när du kopierar data till file storage. | Int64-värde (ingen enhet) |
| rowsCopied | Antal rader som kopieras (gäller inte för binär kopia). | Int64-värde (ingen enhet) |
| rowsSkipped | Antal inkompatibla rader hoppas över. Du kan aktivera funktionen genom att ange ”enableSkipIncompatibleRow” till true. | Int64-värde (ingen enhet) |
| Dataflöde | Förhållandet mellan där data överförs | Flyttal i **KB/s** |
| copyDuration | Varaktigheten för kopian | Ett Int32-värde i sekunder |
| sqlDwPolyBase | Om PolyBase används när du kopierar data till SQL Data Warehouse. | Boolesk |
| redshiftUnload | Om du ta bort när du kopierar data från Redshift. | Boolesk |
| hdfsDistcp | Om DistCp används när du kopierar data från HDFS. | Boolesk |
| effectiveIntegrationRuntime | Visar vilka Integration Runtime(s) används för att ge den aktivitet som körs i formatet `<IR name> (<region if it's Azure IR>)`. | Text (sträng) |
| usedDataIntegrationUnits | Integrering för effektiv dataenheter vid kopiering. | Ett Int32-värde |
| usedParallelCopies | Den effektiva parallelCopies vid kopiering. | Ett Int32-värde|
| redirectRowPath | Sökvägen till loggfilen för hoppades över inkompatibla rader i blob storage som du konfigurerar under ”redirectIncompatibleRowSettings”. Se exemplet nedan. | Text (sträng) |
| executionDetails | Mer information om vilka steg som Kopieringsaktivitet genomgår och motsvarande steg, varaktighet, används konfigurationer, osv. Vi rekommenderar inte för att parsa det här avsnittet som den kan ändras. | Matris |

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
    "usedDataIntegrationUnits": 32,
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
            "usedDataIntegrationUnits": 32,
            "usedParallelCopies": 8,
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 219
            }
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Schema- och datatypsmappningen

Se den [Schema och data typmappningen](copy-activity-schema-and-type-mapping.md), som beskriver hur kopieringsaktiviteten mappar dina källdata till mottagare.

## <a name="fault-tolerance"></a>Feltolerans

Som standard kopieringsaktiviteten stoppar kopiering av data och returnerar fel när påträffas av inkompatibla data mellan källa och mottagare. Du kan uttryckligen konfigurera för att hoppa över och logga inkompatibla rader och bara kopiera dessa kompatibel data så att kopieringen har slutförts. Se den [Kopieringsaktiviteten feltolerans](copy-activity-fault-tolerance.md) på mer information.

## <a name="performance-and-tuning"></a>Prestanda- och justering

Se den [Kopieringsaktiviteten prestanda- och Justeringsguiden](copy-activity-performance.md), som beskriver viktiga faktorer som påverkar prestandan för dataflytt (Kopieringsaktivitet) i Azure Data Factory. Den visar en lista över de observerade prestandan under interna tester och beskriver olika sätt att optimera prestandan för Kopieringsaktiviteten.

I vissa fall när du kör en Kopieringsaktivitet i ADF, direkt ser du ”**prestandajustering tips**” ovanpå den [kopiera aktivitetsövervakning sidan](#monitor-visually) som visas i följande exempel. Det inte bara berättar flaskhalsen identifieras för den angivna kopia körningen, men även hjälper dig på vad du kan ändra för att öka dataflödet kopia. Tips vid prestandajustering för närvarande erbjuder förslag vilja använda PolyBase när du kopierar data till Azure SQL Data Warehouse för att öka Azure Cosmos DB RU eller Azure SQL DB DTU när resursen på data lagrar sida prestanda är flaskhalsen, ta bort onödiga mellanlagrad Kopiera, osv. Regler för prestandajustering kommer gradvis utökas även.

**Exempel: kopiera i Azure SQL DB med tips för prestandajustering**

I det här exemplet är vid kopiering kör ADF-meddelande som mottagaren Azure SQL DB når hög DTU-användningen som saktar ned skrivåtgärder, vilket förslaget att öka Azure SQL DB-nivån med högre DTU. 

![Kopiera övervakning med tips för prestandajustering](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Inkrementell kopia 
Data Factory stöder scenarier för att kopiera deltadata stegvis från ett källdatalager till ett måldatalager. Se [självstudie: kopiera data stegvis](tutorial-incremental-copy-overview.md). 

## <a name="read-and-write-partitioned-data"></a>Läsa och skriva partitionerade data
I version 1 stöd för Azure Data Factory läsa eller skriva partitionerade data med hjälp av SliceStart/SliceEnd/WindowStart/WindowEnd systemvariabler. I den aktuella versionen kan du göra detta med hjälp av en pipeline-parameter och utlösarens schemalagd tid/starttid som ett värde för parametern. Mer information finns i [hur att läsa eller skriva partitionerade data](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Nästa steg
Se följande snabbstarter, självstudier och exempel:

- [Kopiera data från en plats till en annan plats i samma Azure Blob Storage](quickstart-create-data-factory-dot-net.md)
- [Kopieringsdata från Azure Blob Storage till Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Kopiera data från en lokal SQL Server till Azure](tutorial-hybrid-copy-powershell.md)

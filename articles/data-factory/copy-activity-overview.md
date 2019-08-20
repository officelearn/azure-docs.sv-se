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
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 23ae7b5cfec26fb2483a3e4ac13a1220888d76ee
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614261"
---
# <a name="copy-activity-in-azure-data-factory"></a>Kopiera aktivitet i Azure Data Factory

## <a name="overview"></a>Översikt

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-data-movement-activities.md)
> * [Aktuell version](copy-activity-overview.md)

Du kan använda Kopieringsaktivitet i Azure Data Factory för att kopiera data mellan data butiker finns lokalt och i molnet. När data har kopierats kan de transformeras och analyseras ytterligare med andra aktiviteter. Du kan också använda Kopieringsaktivitet för att publicera omvandling och analysresultat för business intelligence (BI) och förbrukning av programmet.

![Rollen för Kopieringsaktivitet](media/copy-activity-overview/copy-activity.png)

Kopieringsaktivitet körs på en [Integreringskörningen](concepts-integration-runtime.md). För olika data kopierings scenarier kan olika varianter av Integration Runtime utnyttjas:

* När du kopierar data mellan data lager som både är offentligt tillgängliga via Internet från alla IP-adresser, kan kopierings aktiviteter skyddas av **Azure integration runtime**, vilket är säkert, tillförlitligt, skalbart och [globalt tillgängligt](concepts-integration-runtime.md#integration-runtime-location).
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

Kopierings aktiviteten stöder även läsning från och skrivning till filer i angivna format: **Text, JSON, Avro, Orc och Parquet**, och komprimering och expandering av filer med följande codecenheter: **Gzip, deflate, BZip2 och ZipDeflate**. Se [stöds format och komprimering](supported-file-formats-and-compression-codecs.md) med information.

Du kan exempelvis göra följande kopieringsaktiviteter:

* Kopiera data i lokala SQL Server och skriva till Azure Data Lake Storage Gen2 i Parquet-format.
* Kopiera filer i textformat (CSV) från den lokala filsystem och skriva till Azure Blob i Avro-format.
* Kopiera zippade filer från det lokala fil systemet och expandera sedan land till Azure Data Lake Storage Gen2.
* Kopiera data i GZip-komprimerade textfiler (CSV)-format från Azure Blob och skriva till Azure SQL Database.
* Och många fler fall med serialisering/deserialisering eller komprimering/expandering behöver.

## <a name="supported-regions"></a>Regioner som stöds

Tjänsten som driver Kopieringsaktivitet är tillgängligt globalt i regioner och geografiska områden som anges i [Azure Integration Runtime platser](concepts-integration-runtime.md#integration-runtime-location). Globalt tillgänglig topologin säkerställer effektiv dataförflyttning som vanligtvis undviker interregionala hopp. Se [tjänster efter region](https://azure.microsoft.com/regions/#services) för tillgänglighet för Data Factory och dataförflyttning i en region.

## <a name="configuration"></a>Konfiguration

Om du vill använda Kopieringsaktivitet i Azure Data Factory, måste du:

1. **Skapa länkade tjänster för källans datalager och datalager för mottagare.** Se artikeln connector ”länkade tjänstegenskaper” avsnittet om hur du konfigurerar och egenskaper som stöds. Du hittar listan stöds connector i [datalager och format som stöds](#supported-data-stores-and-formats) avsnittet.
2. **Skapa datauppsättningar för källa och mottagare.** Mer information om hur du konfigurerar och dess egenskaper som stöds hittar du i artikeln om käll-och mottagar anslutningar.
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

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Typ egenskapen för en kopierings aktivitet måste anges till: **Exemplar** | Ja |
| inputs | Ange den datauppsättning som du skapade som pekar till källdata. Kopieringsaktivitet stöder bara en enda indata. | Ja |
| outputs | Ange den datauppsättning som du skapade som pekar till mottagardata. Kopieringsaktivitet stöder bara ett enda utflöde. | Ja |
| typeProperties | En grupp egenskaper för att konfigurera kopieringsaktiviteten. | Ja |
| source | Ange den kopiering av källtypen och motsvarande egenskaper på hur du hämtar data.<br/><br/>Få mer detaljerad information från avsnittet ”Kopiera Aktivitetsegenskaper” i connector artikeln listas i [datalager och format som stöds](#supported-data-stores-and-formats). | Ja |
| sink | Ange kopieringsmottagartyper och motsvarande egenskaper om hur du skriver data.<br/><br/>Få mer detaljerad information från avsnittet ”Kopiera Aktivitetsegenskaper” i connector artikeln listas i [datalager och format som stöds](#supported-data-stores-and-formats). | Ja |
| translator | Ange explicita kolumnmappningarna från källa till mottagare. Gäller när kopia standardbeteendet inte uppfyller dina behov.<br/><br/>Få mer detaljerad information från [Schema och data typmappningen](copy-activity-schema-and-type-mapping.md). | Nej |
| dataIntegrationUnits | Ange powerfulness av [Azure Integration Runtime](concepts-integration-runtime.md) möjligheter för kopiering av data. Kallades tidigare för molnet dmu-enheter (Data Movement här). <br/><br/>Få mer detaljerad information från [integrering enheter](copy-activity-performance.md#data-integration-units). | Nej |
| parallelCopies | Ange parallellitet som du vill Kopieringsaktivitet ska användas vid läsning av data från käll- och skriva data till mottagare.<br/><br/>Få mer detaljerad information från [parallell kopiera](copy-activity-performance.md#parallel-copy). | Nej |
| enableStaging<br/>stagingSettings | Välj att mellanlagra tillfälliga data i en blob-lagring i stället för att kopiera data direkt från källa till mottagare.<br/><br/>Lär dig användbara scenarier och konfigurationsinformation från [mellanlagrad kopiering](copy-activity-performance.md#staged-copy). | Nej |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Välj hur du kan hantera inkompatibla rader vid kopiering av data från källa till mottagare.<br/><br/>Få mer detaljerad information från [feltolerans](copy-activity-fault-tolerance.md). | Nej |

## <a name="monitoring"></a>Övervakning

Du kan övervaka kopieringsaktivitetskörningen på Azure Data Factory ”författare och Övervakare” Användargränssnittet eller programmässigt.

### <a name="monitor-visually"></a>Övervaka visuellt

För att visuellt övervaka körningen av kopieringsaktiviteten, gå till din data factory -> **författare och Övervakare** -> **övervakningsfliken**, visas en lista över pipeline körs med en ”visa Aktivitetskörningar”-länk i  **Åtgärder** kolumn.

![Övervaka pipelinekörningar](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Klicka om du vill se en lista över aktiviteter i denna pipeline-körning. I den **åtgärder** kolumnen har länkar till kopiera aktivitetsindata, utdata, fel (om det inte går att körningen av kopieringsaktiviteten) och information.

![Övervaka aktivitetskörningar](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Klicka på den ”**information**” länka **åtgärder** visa körningsinformation om Kopieringsaktivitet och prestandaegenskaper. Den visar information inklusive volym/rader/filer av data som kopieras från källan till mottagare, dataflöde, steg den går igenom med motsvarande tid och används av konfigurationer för ditt scenario kopia.

>[!TIP]
>I vissa fall kan du också se "**prestanda justerings tips**" ovanpå sidan Kopiera övervakning, som visar att du har identifierat Flask halsen och hjälper dig att ändra det så att kopierings data flödet ökar, se ett exempel med information [här](#performance-and-tuning).

**Exempel: kopiera från Amazon S3 till Azure Data Lake Store**
![övervakaren aktivitetskörningsinformation](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Exempel: kopiera från Azure SQL Database till Azure SQL Data Warehouse med hjälp av mellanlagrad kopiering**
![övervakaren aktivitetskörningsinformation](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Övervaka programmässigt

Information om körningen av kopierings aktiviteten och prestanda egenskaperna returneras också i avsnittet kopierings aktivitet körnings resultat-> utdata. Nedan visas en fullständig förteckning; bara de tillämpliga filer för ditt scenario kopia visas. Lär dig att övervaka aktivitet som körs från [Snabbstart avsnittet övervakning](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Egenskapsnamn  | Beskrivning | Enhet |
|:--- |:--- |:--- |
| dataRead | Storleken på data som läses från källa | Int64 värdet i **byte** |
| dataWritten | Storleken på data som skrivs till mottagare | Int64 värdet i **byte** |
| filesRead | Antal filer som kopieras när du kopierar data från fillagring. | Int64-värde (ingen enhet) |
| filesWritten | Antal filer som kopieras när du kopierar data till file storage. | Int64-värde (ingen enhet) |
| sourcePeakConnections | Antal maximalt antal samtidiga anslutningar som upprättats till käll data lagret under kopierings aktivitets körningen. | Int64-värde (ingen enhet) |
| sinkPeakConnections | Antal maximalt antal samtidiga anslutningar som upprättats till mottagar data lagret under kopierings aktivitets körningen. | Int64-värde (ingen enhet) |
| rowsRead | Antal rader som läses från källan (gäller inte för binär kopia). | Int64-värde (ingen enhet) |
| rowsCopied | Antal rader som kopieras till Sink (gäller inte för binär kopia). | Int64-värde (ingen enhet) |
| rowsSkipped | Antal inkompatibla rader hoppas över. Du kan aktivera funktionen genom att ange ”enableSkipIncompatibleRow” till true. | Int64-värde (ingen enhet) |
| copyDuration | Kopians varaktighet. | Ett Int32-värde i sekunder |
| throughput | Förhållandet som data överförs till. | Flyttal i **KB/s** |
| sourcePeakConnections | Det högsta antalet samtidiga anslutningar som upprättats till käll data lagret under kopieringen. | Ett Int32-värde |
| sinkPeakConnections| Det högsta antalet samtidiga anslutningar som upprättats till mottagar data lagret under kopieringen.| Ett Int32-värde |
| sqlDwPolyBase | Om PolyBase används när du kopierar data till SQL Data Warehouse. | Boolesk |
| redshiftUnload | Om du ta bort när du kopierar data från Redshift. | Boolesk |
| hdfsDistcp | Om DistCp används när du kopierar data från HDFS. | Boolesk |
| effectiveIntegrationRuntime | Visar vilka Integration Runtime(s) används för att ge den aktivitet som körs i formatet `<IR name> (<region if it's Azure IR>)`. | Text (sträng) |
| usedDataIntegrationUnits | Integrering för effektiv dataenheter vid kopiering. | Ett Int32-värde |
| usedParallelCopies | Den effektiva parallelCopies vid kopiering. | Ett Int32-värde |
| redirectRowPath | Sökvägen till loggfilen för hoppades över inkompatibla rader i blob storage som du konfigurerar under ”redirectIncompatibleRowSettings”. Se exemplet nedan. | Text (sträng) |
| executionDetails | Mer information om vilka steg som Kopieringsaktivitet genomgår och motsvarande steg, varaktighet, används konfigurationer, osv. Vi rekommenderar inte för att parsa det här avsnittet som den kan ändras.<br/><br/>ADF rapporterar även detaljerade varaktigheter (i sekunder) som ägnas åt respektive steg `detailedDurations`under. Varaktigheten för de här stegen är exklusiva och bara de som gäller för den aktuella kopierings aktivitets körningen visas:<br/>- **Köns varaktighet** (`queuingDuration`): Den tid som förflutit tills kopierings aktiviteten faktiskt börjar på integration Runtime. Om du använder IR med egen värd och detta värde är stort, föreslår du att kontrol lera IR-kapaciteten och användningen och skala upp och ut enligt din arbets belastning. <br/>- **Varaktighet för att kopiera skript** (`preCopyScriptDuration`): Tiden mellan kopierings aktiviteten som startar på IR-och kopierings aktiviteten som kör skriptet för att kopiera i mottagar data lagret. Använd när du konfigurerar skriptet för att kopiera. <br/>- **Tid till första byte** (`timeToFirstByte`): Tiden som förflutit från slutet av föregående steg och den IR som tar emot den första byten från käll data lagret. Använd för icke-filbaserad källa. Om det här värdet är stort kan du föreslå att kontrol lera och optimera frågan eller servern.<br/>- **Överförings tid** (`transferDuration`): Tiden i slutet av föregående steg och IR-överföring av alla data från källan till Sink. | Array |
| perfRecommendation | Kopiera optimerings tips för prestanda. Mer information finns i avsnittet om [prestanda och justeringar](#performance-and-tuning) . | Array |

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

## <a name="schema-and-data-type-mapping"></a>Schema- och datatypsmappningen

Se den [Schema och data typmappningen](copy-activity-schema-and-type-mapping.md), som beskriver hur kopieringsaktiviteten mappar dina källdata till mottagare.

## <a name="fault-tolerance"></a>Feltolerans

Som standard slutar kopierings aktiviteten att kopiera data och returnerar ett problem när den påträffar inkompatibla data mellan källa och mottagare. Du kan uttryckligen konfigurera för att hoppa över och logga inkompatibla rader och bara kopiera dessa kompatibel data så att kopieringen har slutförts. Se den [Kopieringsaktiviteten feltolerans](copy-activity-fault-tolerance.md) på mer information.

## <a name="performance-and-tuning"></a>Prestanda- och justering

Se den [Kopieringsaktiviteten prestanda- och Justeringsguiden](copy-activity-performance.md), som beskriver viktiga faktorer som påverkar prestandan för dataflytt (Kopieringsaktivitet) i Azure Data Factory. Den visar en lista över de observerade prestandan under interna tester och beskriver olika sätt att optimera prestandan för Kopieringsaktiviteten.

I vissa fall kan du, när du kör en kopierings aktivitet i ADF, direkt se "**prestanda justerings tips**" ovanpå [sidan Kopiera aktivitets övervakning](#monitor-visually) , som du ser i följande exempel. Det visar inte bara den Flask hals som identifierats för den angivna kopierings körningen, men du får också hjälp med vad du kan ändra så att kopierings data flödet ökar. Tipsen för prestanda justering ger för närvarande förslag som att använda PolyBase när du kopierar data till Azure SQL Data Warehouse, för att öka Azure Cosmos DB RU eller Azure SQL DB DTU när resursen på data lager sidan är Flask halsen, för att ta bort onödiga mellanlagrade Kopiera osv. Reglerna för prestanda justering kommer även att berikas.

**Exempel: kopiera till Azure SQL DB med prestanda justerings tips**

I det här exemplet, under kopierings körningen, visas ett meddelande om att mottagaren i Azure SQL DB når en hög DTU-användning som saktar ned Skriv åtgärderna, så förslaget är att öka Azure SQL DB-nivån med mer DTU.

![Kopiera övervakning med prestanda justerings tips](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Inkrementell kopia
Data Factory stöder scenarier för att stegvis kopiera delta data från ett käll data lager till ett data lager för mottagare. Se [självstudie: kopiera data stegvis](tutorial-incremental-copy-overview.md).

## <a name="read-and-write-partitioned-data"></a>Läsa och skriva partitionerade data
I version 1 stöd för Azure Data Factory läsa eller skriva partitionerade data med hjälp av SliceStart/SliceEnd/WindowStart/WindowEnd systemvariabler. I den aktuella versionen kan du göra detta med hjälp av en pipeline-parameter och utlösarens schemalagd tid/starttid som ett värde för parametern. Mer information finns i [hur att läsa eller skriva partitionerade data](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Nästa steg
Se följande snabbstarter, självstudier och exempel:

- [Kopiera data från en plats till en annan plats i samma Azure Blob Storage](quickstart-create-data-factory-dot-net.md)
- [Kopieringsdata från Azure Blob Storage till Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Kopiera data från en lokal SQL Server till Azure](tutorial-hybrid-copy-powershell.md)

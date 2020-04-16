---
title: Kopiera aktivitet i Azure Data Factory
description: Läs mer om kopieringsaktiviteten i Azure Data Factory. Du kan använda den för att kopiera data från ett källdatalager som stöds till ett diskinserveringsdatalager som stöds.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: 2557ce7be44f0505b96df06cd2b44a2fa3ce3fdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414226"
---
# <a name="copy-activity-in-azure-data-factory"></a>Kopiera aktivitet i Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory som du använder:"]
> * [Version 1](v1/data-factory-data-movement-activities.md)
> * [Aktuell version](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I Azure Data Factory kan du använda aktiviteten Kopiera för att kopiera data mellan datalager som finns lokalt och i molnet. När du har kopierat data kan du använda andra aktiviteter för att ytterligare transformera och analysera dem. Du kan också använda aktiviteten Kopiera för att publicera omvandlings- och analysresultat för Business Intelligence (BI) och programförbrukning.

![Kopieringsaktivitetens roll](media/copy-activity-overview/copy-activity.png)

Kopieringsaktiviteten körs på en [integrationskörning](concepts-integration-runtime.md). Du kan använda olika typer av integrationskörningar för olika datakopieringsscenarier:

* När du kopierar data mellan två datalager som är allmänt tillgängliga via internet från valfri IP kan du använda Azure-integreringskörningen för kopieringsaktiviteten. Den här integrationskörningen är säker, tillförlitlig, skalbar och [globalt tillgänglig.](concepts-integration-runtime.md#integration-runtime-location)
* När du kopierar data till och från datalager som finns lokalt eller i ett nätverk med åtkomstkontroll (till exempel ett virtuellt Azure-nätverk) måste du konfigurera en självvärd integreringskörning.

En integrationskörning måste associeras med varje käll- och sink-datalager. Information om hur kopieringsaktiviteten avgör vilken integrationskörning som ska användas finns i [Bestämma vilken IR som ska användas](concepts-integration-runtime.md#determining-which-ir-to-use).

Om du vill kopiera data från en källa till en mottagare utför tjänsten som kör aktiviteten Kopiera följande steg:

1. Läser data från ett källdatalager.
2. Utför serialisering/deserialisering, komprimering/dekompression, kolumnmappning och så vidare. Den utför dessa åtgärder baserat på konfigurationen av indatauppsättningen, utdatauppsättningen och kopieringsaktiviteten.
3. Skriver data till datalagret sink/destination.

![Översikt över kopieringsaktivitet](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Datalager och format som stöds

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Filformat som stöds

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Du kan använda kopieringsaktiviteten för att kopiera filer som de är mellan två filbaserade datalager, i vilket fall data kopieras effektivt utan serialisering eller deserialisering. Dessutom kan du också tolka eller generera filer med ett visst format, till exempel kan du utföra följande:

* Kopiera data från en lokal SQL Server-databas och skriv till Azure Data Lake Storage Gen2 i parkettformat.
* Kopiera filer i CSV-format (Text) från ett lokalt filsystem och skriv till Azure Blob-lagring i Avro-format.
* Kopiera zippade filer från ett lokalt filsystem, expandera dem i farten och skriv extraherade filer till Azure Data Lake Storage Gen2.
* Kopiera data i Gzip-formatet med komprimerad text (CSV) från Azure Blob-lagring och skriv det till Azure SQL Database.
* Många fler aktiviteter som kräver serialisering/deserialisering eller komprimering/dekompression.

## <a name="supported-regions"></a>Regioner som stöds

Tjänsten som aktiverar kopieringsaktiviteten är tillgänglig globalt i de regioner och geografiska områden som anges i [Azure-integrationskörningsplatser](concepts-integration-runtime.md#integration-runtime-location). Den globalt tillgängliga topologin säkerställer effektiv dataförflyttning som vanligtvis undviker hopp mellan regioner. Se [Produkter efter region](https://azure.microsoft.com/regions/#services) för att kontrollera tillgängligheten för Data Factory och dataflyttning i en viss region.

## <a name="configuration"></a>Konfiguration

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Om du vill använda kopieringsaktiviteten i Azure Data Factory måste du i allmänhet:

1. **Skapa länkade tjänster för källdatalagret och sink-datalagret.** Du hittar listan över kopplingar som stöds i avsnittet [Datalager och format som stöds](#supported-data-stores-and-formats) i den här artikeln. I anslutningsartikelns avsnitt "Länkade tjänstegenskaper" finns konfigurationsinformation och egenskaper som stöds. 
2. **Skapa datauppsättningar för källan och diskhon.** Se avsnitten "Datauppsättningsegenskaper" i käll- och sink-anslutningsartiklarna för konfigurationsinformation och egenskaper som stöds.
3. **Skapa en pipeline med aktiviteten Kopiera.** Nästa avsnitt innehåller ett exempel.

### <a name="syntax"></a>Syntax

Följande mall för en kopieringsaktivitet innehåller en fullständig lista över egenskaper som stöds. Ange de som passar ditt scenario.

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

#### <a name="syntax-details"></a>Syntaxinformation

| Egenskap | Beskrivning | Krävs? |
|:--- |:--- |:--- |
| typ | För en kopieringsaktivitet ställer du in på`Copy` | Ja |
| Ingångar | Ange den datauppsättning som du skapade som pekar på källdata. Kopieringsaktiviteten stöder endast en enda indata. | Ja |
| Utgångar | Ange den datauppsättning som du skapade som pekar på diskhondata. Kopieringsaktiviteten stöder bara en enda utdata. | Ja |
| typeProperties | Ange egenskaper för att konfigurera kopieringsaktiviteten. | Ja |
| källa | Ange kopierakälltypen och motsvarande egenskaper för att hämta data.<br/>Mer information finns i avsnittet "Kopiera aktivitetsegenskaper" i kopplingsartikeln som visas i [Datalager och format som stöds](#supported-data-stores-and-formats). | Ja |
| Diskbänken | Ange kopierarkänningstypen och motsvarande egenskaper för att skriva data.<br/>Mer information finns i avsnittet "Kopiera aktivitetsegenskaper" i kopplingsartikeln som visas i [Datalager och format som stöds](#supported-data-stores-and-formats). | Ja |
| translator | Ange explicita kolumnmappningar från källa till mottagare. Den här egenskapen gäller när standardkopieringsbeteendet inte uppfyller dina behov.<br/>Mer information finns [i Schemamappning i kopieringsaktivitet](copy-activity-schema-and-type-mapping.md). | Inga |
| dataIntegrationUnits | Ange ett mått som representerar hur mycket ström som [Azure-integreringen](concepts-integration-runtime.md) körs för datakopiering. Dessa enheter var tidigare kända som moln datarörelseenheter (DMU). <br/>Mer information finns i [Dataintegrationsenheter](copy-activity-performance-features.md#data-integration-units). | Inga |
| parallelCopies (parallelCopies) | Ange den parallellitet som du vill att kopieringsaktiviteten ska använda när data läses från källan och skriver data till diskhon.<br/>Mer information finns i [Parallellkopia](copy-activity-performance-features.md#parallel-copy). | Inga |
| Bevara | Ange om metadata/ACL:er ska bevaras under datakopiering. <br/>Mer information finns i [Bevara metadata](copy-activity-preserve-metadata.md). |Inga |
| enableStaging<br/>mellanlagringsinställningar | Ange om interimsdata ska fasas i Blob-lagring i stället för att direkt kopiera data från källa till mottagare.<br/>Information om användbara scenarier och konfigurationsinformation finns i [Stegvis kopia](copy-activity-performance-features.md#staged-copy). | Inga |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Välj hur du ska hantera inkompatibla rader när du kopierar data från källa till diskho.<br/>Mer information finns i [Feltolerans](copy-activity-fault-tolerance.md). | Inga |

## <a name="monitoring"></a>Övervakning

Du kan övervaka kopieringsaktiviteten som körs i Azure Data Factory både visuellt och programmässigt. Mer information finns i [Övervaka kopieringsaktivitet](copy-activity-monitoring.md).

## <a name="incremental-copy"></a>Inkrementell kopia

Med Data Factory kan du stegvis kopiera deltadata från ett källdatalager till ett sink-datalager. Mer information finns i [Självstudiekurs: Kopiera stegvis data](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering

[Kopieringsaktivitetsövervakningsupplevelsen](copy-activity-monitoring.md) visar kopieringsprestandastatistiken för var och en av dina aktiviteter. Guiden [Kopiera aktivitetsprestanda och skalbarhet](copy-activity-performance.md) beskriver viktiga faktorer som påverkar prestanda för dataflyttning via kopieringsaktiviteten i Azure Data Factory. Den visar också de prestandavärden som observerats under testningen och diskuterar hur du optimerar prestanda för kopieringsaktiviteten.

## <a name="resume-from-last-failed-run"></a>Återuppta från senaste misslyckade körningen

Kopiera aktivitet stöder återuppta från den senaste misslyckade körningen när du kopierar en stor storlek på filer som den är med binärt format mellan filbaserade butiker och väljer att bevara mapp-/filhierarkin från källa till diskho, t.ex. Det gäller följande filbaserade kopplingar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), Azure Data Lake Storage [Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)och [SFTP](connector-sftp.md).

Du kan utnyttja kopieringsaktivitetens meritförteckning på följande två sätt:

- **Försök på aktivitetsnivå:** Du kan ange antalet försök att försöka på kopieringsaktivitet. Om den här kopieringsaktiviteten misslyckas startar nästa automatiska återförsök från den senaste utvärderingsversionens felpunkt under pipelinekörningen.
- **Kör från misslyckad aktivitet:** När pipelinekörning har slutförts kan du också utlösa en repris från den misslyckade aktiviteten i ADF-övervakningsvyn eller programmässigt. Om den misslyckade aktiviteten är en kopieringsaktivitet körs pipelinen inte bara från den här aktiviteten, utan även återupptas från föregående körnings felpunkt.

    ![Kopiera återuppta](media/copy-activity-overview/resume-copy.png)

Några punkter att notera:

- Återuppta händer på filnivå. Om kopieringsaktiviteten misslyckas när en fil kopieras kopieras den här specifika filen i nästa körning.
- För att återuppta arbetet korrekt ska du inte ändra inställningarna för kopieringsaktivitet mellan omkörningarna.
- När du kopierar data från Amazon S3, Azure Blob, Azure Data Lake Storage Gen2 och Google Cloud Storage kan kopieringsaktiviteten återupptas från godtyckligt antal kopierade filer. Medan för resten av filbaserade kopplingar som källa, för närvarande kopiera aktivitet stöder återupptas från ett begränsat antal filer, vanligtvis på intervallet tiotusentals och varierar beroende på längden på filsökvägarna; filer utöver detta nummer kommer att kopieras igen under repriser.

För andra scenarier än binär filkopia startar kopieringsaktivitetens omkörning från början.

## <a name="preserve-metadata-along-with-data"></a>Bevara metadata tillsammans med data

När du kopierar data från källa till diskho, i scenarier som datasjömigrering, kan du också välja att bevara metadata och ACL tillsammans med data med hjälp av kopieringsaktivitet. Mer information [finns i Bevara metadata.](copy-activity-preserve-metadata.md)

## <a name="schema-and-data-type-mapping"></a>Schema- och datatypsmappning

Se [Schema- och datatypsmappning](copy-activity-schema-and-type-mapping.md) för information om hur kopieringsaktiviteten mappar källdata till diskhon.

## <a name="add-additional-columns-during-copy"></a>Lägga till ytterligare kolumner under kopiering

Förutom att kopiera data från källdatalagret till sink, kan du också konfigurera för att lägga till ytterligare datakolumner att kopiera tillsammans med sink. Ett exempel:

- När du kopierar från den filbaserade källan lagrar du den relativa filsökvägen som ytterligare en kolumn för att spåra från vilken fil data kommer ifrån.
- Lägg till en kolumn med ADF-uttryck om du vill koppla ADF-systemvariabler som pipelinenamn/pipeline-ID eller lagra annat dynamiskt värde från aktivitetens utdata uppströms.
- Lägg till en kolumn med statiskt värde för att uppfylla ditt nedströmsförbrukningsbehov.

Du hittar följande konfiguration på fliken kopiera aktivitetskälla: 

![Lägga till ytterligare kolumner i kopieringsaktivitet](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Den här funktionen fungerar med den senaste datauppsättningsmodellen. Om du inte ser det här alternativet från användargränssnittet kan du prova att skapa en ny datauppsättning.

Om du vill konfigurera den `additionalColumns` programmässigt lägger du till egenskapen i kopieringsaktivitetskällan:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| ytterligareKolunnor | Lägg till ytterligare datakolumner som ska kopieras till handfat.<br><br>Varje objekt `additionalColumns` under matrisen representerar en extra kolumn. Kolumnnamnet `name` definieras och `value` anger datavärdet för den kolumnen.<br><br>Tillåtna datavärden är:<br>- **`$$FILEPATH`**- en reserverad variabel anger att källfilernas relativa sökväg lagras till den mappsökväg som anges i datauppsättningen. Använd på filbaserad källa.<br>- **Uttryck**<br>- **Statiskt värde** | Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="fault-tolerance"></a>Feltolerans

Som standard slutar kopieringsaktiviteten att kopiera data och returnerar ett fel när källdatarader är inkompatibla med sink-datarader. Om du vill att kopian ska lyckas kan du konfigurera kopieringsaktiviteten så att den hoppar över och loggar de inkompatibla raderna och kopierar endast kompatibla data. Mer information finns i [Kopiera aktivitetsfeltolerans.](copy-activity-fault-tolerance.md)

## <a name="next-steps"></a>Nästa steg
Se följande snabbstarter, självstudier och exempel:

- [Kopiera data från en plats till en annan plats i samma Azure Blob-lagringskonto](quickstart-create-data-factory-dot-net.md)
- [Kopiera data från Azure Blob-lagring till Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Kopiera data från en lokal SQL Server-databas till Azure](tutorial-hybrid-copy-powershell.md)

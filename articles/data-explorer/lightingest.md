---
title: LightIngest är ett kommandoradsverktyg för inmatning i Azure Data Explorer.
description: Lär dig mer om LightIngest, ett kommandoradsverktyg för ad hoc-datainmatning i Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 99517e45892cd7a6167ae83ff3058edae1377b10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109568"
---
# <a name="install-and-use-lightingest"></a>Installera och använd LightIngest

LightIngest är ett kommandoradsverktyg för ad hoc-datainmatning i Azure Data Explorer.
Verktyget kan hämta källdata från en lokal mapp eller från en Azure blob-lagringsbehållare.

## <a name="prerequisites"></a>Krav

* LightIngest - ladda ner det som en del av [Paketet Microsoft.Azure.Kusto.Tools NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)
* WinRAR - ladda ner den från [www.win-rar.com/download.html](http://www.win-rar.com/download.html)

## <a name="install-lightingest"></a>Installera LightIngest

1. Navigera till den plats på datorn där du hämtade LightIngest. 
1. Med WinRAR extraherar du *verktygskatalogen* till datorn.

## <a name="run-lightingest"></a>Kör LightIngest

1. Navigera till katalogen extraherade *verktyg* på datorn.
1. Ta bort befintlig platsinformation från platsfältet.
    
      ![Ta bort platsinformation](media/lightingest/lightingest-location-bar.png)

1. Skriv `cmd` in och tryck på **Retur**.
1. I kommandotolken `LightIngest.exe` anger du följt av det relevanta kommandoradsargumentet.

    > [!Tip]
    > En lista över kommandoradsargument som stöds anger du `LightIngest.exe /help`.
    >
    >![Hjälp om kommandoraden](media/lightingest/lightingest-cmd-line-help.png)

1. Ange `LightIngest` följt av anslutningssträngen till Azure Data Explorer-klustret som hanterar inmatningen.
    Bifoga anslutningssträngen med dubbla citattecken och följ [specifikationen för Kusto-anslutningssträngar](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto).

    Ett exempel:
    ```
    LightIngest "Data Source=https://{Cluster name and region}.kusto.windows.net;AAD Federated Security=True"  -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* Den rekommenderade metoden `LightIngest` är att arbeta med slutpunkten för intag vid `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net`. På så sätt kan Azure Data Explorer-tjänsten hantera inmatningsbelastningen och du kan enkelt återställa från tillfälliga fel. Du kan dock `LightIngest` också konfigurera för att arbeta`https://{yourClusterNameAndRegion}.kusto.windows.net`direkt med motorns slutpunkt ( ).
* För optimal intag prestanda, är det viktigt för LightIngest `LightIngest` att känna till rådata storlek och så kommer att uppskatta den okomprimerade storleken på lokala filer. Det `LightIngest` kanske dock inte kan uppskattas av den råa storleken på komprimerade blobbar på rätt sätt utan att först hämta dem. När du intar komprimerade blobbar anger `rawSizeBytes` du därför egenskapen på blobmetadata till okomprimerad datastorlek i byte.

## <a name="general-command-line-arguments"></a>Allmänna kommandoradsargument

|Argumentnamn         |Kort namn   |Typ    |Obligatorisk |Beskrivning                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |sträng  |Obligatorisk |[Azure Data Explorer Connection String](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto) anger Kusto-slutpunkten som ska hantera inmatningen. Bör omges av dubbla citattecken |
|-databas             |-db          |sträng  |Valfri  |Databasnamn för Mål Azure Data Explorer |
|-bord                |             |sträng  |Obligatorisk |Tabellnamn för Mål Azure Data Explorer |
|-sourcePath           |-källa      |sträng  |Obligatorisk |Sökväg till källfiler eller rot-URI för blob-behållaren. Om data finns i blobbar måste den innehålla lagringskontonyckel eller SAS. Rekommenderas att bifoga i dubbla citattecken |
|-prefix               |             |sträng  |Valfri  |När källdata som ska förtäras finns på blob-lagring delas det här URL-prefixet av alla blobbar, exklusive behållarnamnet. <br>Om data till exempel `MyContainer/Dir1/Dir2`finns i ska prefixet vara `Dir1/Dir2`. Omslutande i dubbla citattecken rekommenderas |
|-mönster              |             |sträng  |Valfri  |Mönster som källfiler/blobbar plockas med. Stöder jokertecken. Till exempel `"*.csv"`. Rekommenderas att bifoga i dubbla citattecken |
|-zipMönster           |             |sträng  |Valfri  |Reguljärt uttryck som ska användas när du väljer vilka filer i ett ZIP-arkiv som ska användas.<br>Alla andra filer i arkivet ignoreras. Till exempel `"*.csv"`. Det rekommenderas att omge den i dubbla citattecken |
|-format               |-f           |sträng  |Valfri  |Källdataformat. Måste vara ett av de [format som stöds](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#supported-data-formats) |
|-intagMappingPath |-mappingPath |sträng  |Valfri  |Sökväg till kolumnmappningsfil för inmatning (obligatoriskt för Json- och Avro-format). Se [datamappningar](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-intagMappingRef  |-mappingRef  |sträng  |Valfri  |Namn på en förskapad inmatningskolumnmappning (obligatoriskt för Json- och Avro-format). Se [datamappningar](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-creationTimePattern  |             |sträng  |Valfri  |Används för att extrahera egenskapen CreationTime från filen eller blob-sökvägen när den är klar. Se [Använda argumentet CreationTimePattern](#using-creationtimepattern-argument) |
|-ignoreraFirstRow       |-ignoreraFörsta |bool    |Valfri  |Om den är inställd ignoreras den första posten för varje fil/blob (till exempel om källdata har rubriker) |
|-tagg                  |             |sträng  |Valfri  |[Taggar](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) som ska associeras med de intjesterade data. Flera förekomster är tillåtna |
|-dontWait             |             |bool    |Valfri  |Om den är inställd på "true", väntar inte på att intag ska slutföras. Användbart vid intag av stora mängder filer/blobbar |

### <a name="using-creationtimepattern-argument"></a>Använda argumentet CreationTimePattern

Argumentet `-creationTimePattern` extraherar egenskapen CreationTime från filen eller blob-sökvägen. Mönstret behöver inte återspegla hela objektsökvägen, bara det avsnitt som omsluter den tidsstämpel som du vill använda.
Argumentets värde skall innehålla tre avsnitt:
* Konstant test omedelbart före tidsstämpeln, innesluten i enstaka citattecken
* Tidsstämpelformatet i [standard-.NET DateTime-notation](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)
* Konstant text omedelbart efter tidsstämpeln Om blobnamn till exempel slutar med "historicalvalues19840101.parkquet" (tidsstämpeln är fyra siffror för året, två siffror för `-creationTimePattern` månaden och två siffror för dagen i månaden), är motsvarande värde för argumentet "historicalvalues'yyyYMMdd'.parquet".

### <a name="command-line-arguments-for-advanced-scenarios"></a>Kommandoradsargument för avancerade scenarier

|Argumentnamn         |Kort namn   |Typ    |Obligatorisk |Beskrivning                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-komprimering          |-cr (cr)          |double  |Valfri  |Kompressionsförhållande tips. Användbart vid intag av komprimerade filer/blobbar för att hjälpa Azure Data Explorer att bedöma rådatastorleken. Beräknat som ursprunglig storlek dividerat med komprimerad storlek |
|-gräns                |- Jag är inte här.           |heltal |Valfri  |Om den är inställd begränsar inmatningen till de första N-filerna |
|-listOnly             |-lista        |bool    |Valfri  |Om den är inställd visar endast de objekt som skulle ha valts för inmatning| 
|-ingestTimeout        |             |heltal |Valfri  |Timeout i minuter för alla inmattningsåtgärder slutföras. Standardvärden till`60`|
|-forceSync            |             |bool    |Valfri  |Om den är inställd tvingar synkron intag. Standardvärden till`false` |
|-dataBatchSize        |             |heltal |Valfri  |Anger den totala storleksgränsen (MB, okomprimerad) för varje intagsåtgärd |
|-filerInBatch         |             |heltal |Valfri  |Anger gränsen för fil-/blobantal för varje int-åtgärd |
|-devTracing           |-spår       |sträng  |Valfri  |Om den är inställd skrivs diagnostikloggar till `RollingLogs` en lokal katalog (som standard i den aktuella katalogen eller kan ändras genom att ange växelvärdet) |

## <a name="blob-metadata-properties"></a>Egenskaper för Blobmetadata
När den används med `LightIngest` Azure-blobbar använder du vissa blobmetadataegenskaper för att öka inmatningsprocessen.

|Egenskapen Metadata                            | Användning                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | Om den är inställd tolkas den okomprimerade datastorleken                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | Tolkas som UTC-tidsstämpel. Om den är inställd används för att åsidosätta skapandetiden i Kusto. Användbart för återfyllning av scenarier |

## <a name="usage-examples"></a>Exempel på användning

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>Intag av blobbar med hjälp av en lagringskontonyckel eller en SAS-token

* Ingest 10 blobbar under `ACCOUNT`angivet `DIR`lagringskonto, i mapp , under behållare `CONT`och matchande mönstret`*.csv.gz`
* Mål är `DB`databas, tabell `TABLE`och inmatningsmappningen `MAPPING` är förskapad på målet
* Verktyget väntar tills intagsoperationerna är slutförd
* Notera de olika alternativen för att ange måldatabas och lagringskontonyckel jämfört med SAS-token

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>Intag av alla blobbar i en behållare, exklusive rubrikrader

* Matar alla blobbar under angivet lagringskonto, `ACCOUNT`i mapp `DIR1/DIR2`, under behållare `CONT`och matchande mönstret`*.csv.gz`
* Mål är `DB`databas, tabell `TABLE`och inmatningsmappningen `MAPPING` är förskapad på målet
* Källblobbar innehåller rubrikrad, så verktyget instrueras att släppa den första posten för varje blob
* Verktyget bokför data för inmatning och väntar inte på att de inmatningsåtgärderna ska slutföras

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>Intag av alla JSON-filer från en sökväg

* Matar alla filer `PATH`under sökvägen , som matchar mönstret`*.json`
* Mål är `DB`databas, tabell `TABLE`och inmatningsmappningen definieras i lokal fil`MAPPING_FILE_PATH`
* Verktyget bokför data för inmatning och väntar inte på att de inmatningsåtgärderna ska slutföras

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>Du får in filer och skriver diagnostiska spårningsfiler

* Matar alla filer `PATH`under sökvägen , som matchar mönstret`*.json`
* Mål är `DB`databas, tabell `TABLE`och inmatningsmappningen definieras i lokal fil`MAPPING_FILE_PATH`
* Verktyget bokför data för inmatning och väntar inte på att de inmatningsåtgärderna ska slutföras
* Diagnostikspårningsfiler skrivs lokalt under mapp`LOGS_PATH`

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
## <a name="changelog"></a>Ändringsloggen
|Version        |Ändringar                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>Lagt `-zipPattern` till argument</li><li>Lagt `-listOnly` till argument</li><li>Sammanfattning av argument visas innan körningen påbörjas</li><li>CreationTime läss från blobmetadataegenskaper eller från blob- eller filnamn, enligt `-creationTimePattern` argumentet</li></ul>|

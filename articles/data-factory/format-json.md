---
title: JSON-format i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar JSON-format i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: 7b554ea5c2868559574979c58697fd31f8d2a2c4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686270"
---
# <a name="json-format-in-azure-data-factory"></a>JSON-format i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Följ den här artikeln när du vill **tolka JSON-filerna eller skriva data i JSON-format**. 

JSON-format stöds för följande kopplingar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)och [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av JSON-datauppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Datauppsättningens typegenskap måste anges till **Json**. | Ja      |
| location         | Platsinställningar för filen/filerna. Varje filbaserad koppling har sin egen platstyp och egenskaper som stöds under `location`. **Se information i kopplingens artikel -> egenskaper för datauppsättning**. | Ja      |
| encodingName     | Kodningstypen som används för att läsa/skriva testfiler. <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Inga       |
| komprimering | Grupp med egenskaper för att konfigurera filkomprimering. Konfigurera det här avsnittet när du vill göra komprimering/dekompression under aktivitetskörningen. | Inga |
| typ | Komprimeringscodec används för att läsa / skriva JSON filer. <br>Tillåtna värden är **bzip2,** **gzip**, **deflate**, **ZipDeflate**, **snappy**eller **lz4**. att använda när du sparar filen. Standard är inte komprimerat.<br>**Kopiera** aktivitet stöder för närvarande inte "snappy" & "lz4", och mappningsdataflödet stöder inte "ZipDeflate".<br>**Observera** att när du använder kopieringsaktivitet för att expandera ZipDeflate-filer och skriva till filbaserat `<path specified in dataset>/<folder named as source zip file>/`sink-datalager extraheras filerna till mappen: . | Nej.  |
| nivå | Kompressionsförhållandet. <br>Tillåtna värden är **Optimal** eller **Snabbaste**.<br>- **Snabbast:** Komprimeringsåtgärden bör slutföras så snabbt som möjligt, även om den resulterande filen inte är optimalt komprimerad.<br>- **Optimal**: Komprimeringsoperationen ska komprimeras optimalt, även om operationen tar längre tid att slutföra. Mer information finns i avsnittet [Komprimeringsnivå.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) | Inga       |

Nedan följer ett exempel på JSON-datauppsättning på Azure Blob Storage:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av JSON-källan och diskhon.

### <a name="json-as-source"></a>JSON som källa

Följande egenskaper stöds i källavsnittet för *** \*kopieringsaktivitet.\* ***

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Egenskapen Type property för kopians aktivitetskälla måste anges till **JSONSource**. | Ja      |
| storeInställningar | En grupp egenskaper för hur du läser data från ett datalager. Varje filbaserad anslutning har sina egna `storeSettings`läsinställningar som stöds under . **Se information i kopplingens artikel -> Kopiera aktivitetsegenskaper avsnitt**. | Inga       |

### <a name="json-as-sink"></a>JSON som sjunker

Följande egenskaper stöds i avsnittet kopiera *** \*aktivitetsmottagare.\* ***

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Egenskapen Type property för kopians aktivitetskälla måste anges till **JSONSink**. | Ja      |
| formateraInställningar | En grupp egenskaper. Se **JSON skrivinställningar** tabellen nedan. | Inga       |
| storeInställningar | En grupp egenskaper för hur du skriver data till ett datalager. Varje filbaserad anslutning har sina egna `storeSettings`skrivinställningar som stöds under . **Se information i kopplingens artikel -> Kopiera aktivitetsegenskaper avsnitt**. | Inga       |

**JSON-skrivinställningar** `formatSettings`som stöds under:

| Egenskap      | Beskrivning                                                  | Krävs                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typen av formatInställningar måste ställas in på **JsonWriteSettings**. | Ja                                                   |
| filePattern |Ange mönstret för de data som lagras i varje JSON-fil. Tillåtna värden är: **setOfObjects** och **arrayOfObjects**. **Standardvärdet** är **setOfObjects**. Detaljerad information om dessa mönster finns i avsnittet om [JSON-filmönster](#json-file-patterns). |Inga |

### <a name="json-file-patterns"></a>JSON-filmönster

Kopieringsaktivitet kan automatiskt identifiera och tolka följande mönster för JSON-filer. 

- **Typ I: setOfObjects**

    Varje fil som innehåller ett enskilt objekt eller flera radavgränsade/sammanfogade objekt. 
    När det här alternativet väljs i kopiera aktivitetsmottagare, skapar kopieringsaktivitet en enda JSON-fil med varje objekt per rad (radavgränsad).

    * **Exempel på JSON med enskilda objekt**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Exempel med radavgränsad JSON**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Exempel med sammanfogad JSON**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Typ II: arrayOfObjects**

    Varje fil innehåller en matris med objekt.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>Mappa dataflödesegenskaper

JSON-filtyper kan användas både som en diskho och en källa vid mappning av dataflöde.

### <a name="creating-json-structures-in-a-derived-column"></a>Skapa JSON-strukturer i en härledd kolumn

Du kan lägga till en komplex kolumn i dataflödet via den härledda kolumnuttrycksverktyget. Lägg till en ny kolumn i den härledda kolumnomformningen och öppna uttrycksverktyget genom att klicka på den blå rutan. Om du vill göra en kolumn komplex kan du ange JSON-strukturen manuellt eller använda användarupplevelsen för att lägga till underkolumner interaktivt.

#### <a name="using-the-expression-builder-ux"></a>Använda uttrycksverktyget UX

Hovra över en kolumn i fönstret utdataschema och klicka på plusikonen. Välj **Lägg till underkolumn** om du vill göra kolumnen till en komplex typ.

![Lägg till underkolumn](media/data-flow/addsubcolumn.png "Lägg till underkolumn")

Du kan lägga till ytterligare kolumner och underkolumner på samma sätt. För varje icke-komplext fält kan ett uttryck läggas till i uttrycksredigeraren till höger.

![Komplex kolumn](media/data-flow/complexcolumn.png "Komplex kolumn")

#### <a name="entering-the-json-structure-manually"></a>Ange JSON-strukturen manuellt

Om du vill lägga till en JSON-struktur manuellt lägger du till en ny kolumn och anger uttrycket i redigeraren. Uttrycket följer följande allmänna format:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Om detta uttryck angavs för en kolumn med namnet "complexColumn", skulle det skrivas till diskhon som följande JSON:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Exempel på manuellt skript för fullständig hierarkisk definition
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

### <a name="source-format-options"></a>Alternativ för källformat

Med hjälp av en JSON-datauppsättning som källa i dataflödet kan du ange ytterligare fem inställningar. Dessa inställningar finns under **JSON-inställningarnas** dragspel på fliken **Källalternativ.**  

![JSON-inställningar](media/data-flow/json-settings.png "JSON-inställningar")

#### <a name="default"></a>Default

Som standard läses JSON-data i följande format.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Ett enda dokument

Om **Ett enda dokument** är markerat läser mappningsdataflöden ett JSON-dokument från varje fil. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```
> [!NOTE]
> Om dataflöden genererar ett fel som anger "corrupt_record" när du förhandsgranskar dina JSON-data, är det troligt att dina data innehåller innehåller ett enda dokument i din JSON-fil. Om du ställer in "ett enda dokument" bör felet visas.

#### <a name="unquoted-column-names"></a>Namn på ej citerad kolumn

Om **Namn på ej citerade kolumnnamn** är markerat läser mappningsdataflöden JSON-kolumner som inte omges av citattecken. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Har kommentarer

Välj **Har kommentarer** om JSON-data har C- eller C++-stilkommentarer.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Singel citerad

Välj **Enkelciterad** om JSON-fälten och värdena använder enstaka citattecken i stället för dubbla citattecken.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Omvänt snedstreck flydde

Välj **Enstaka citerade** om omvänt snedstreck används för att escape tecken i JSON-data.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>Nästa steg

- [Kopiera aktivitetsöversikt](copy-activity-overview.md)
- [Mappa dataflöde](concepts-data-flow-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

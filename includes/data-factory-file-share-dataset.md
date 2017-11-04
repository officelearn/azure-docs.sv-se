## <a name="fileshare-dataset-type-properties"></a>Egenskaperna för filresursen dataset-typ.
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](../articles/data-factory/v1/data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av datauppsättningen.

Den **typeProperties** avsnittet är olika för varje typ av datauppsättningen. Den innehåller information som är specifik för dataset-typen. TypeProperties avsnittet för en dataset av typen **filresursen** datamängden har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sub sökvägen till mappen. Använda escape-tecknet ' \ ' för specialtecken i strängen. Se [exempel länkad tjänst-och dataset](#sample-linked-service-and-dataset-definitions) exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappen sökvägar baserat på sektorn börja/sluta datum gånger. |Ja |
| fileName |Ange namnet på filen i den **folderPath** om du vill att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen tabellen pekar på alla filer i mappen.<br/><br/>Om filnamnet inte anges för en datamängd för utdata är namnet på den genererade filen i följande det här formatet: <br/><br/>Data. <Guid>.txt (exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| fileFilter |Ange ett filter som används för att välja en delmängd av filer i mappsökvägen i stället för alla filer.<br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (valfritt tecken).<br/><br/>Exempel 1:`"fileFilter": "*.log"`<br/>Exempel 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter gäller för en inkommande filresursen datauppsättning. Den här egenskapen stöds inte med HDFS. |Nej |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath filnamn för tid series-data. Till exempel folderPath som innehåller parametrar för varje timme av data. |Nej |
| Format | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](#specifying-textformat), [Json-Format](#specifying-jsonformat), [Avro-formatet](#specifying-avroformat), [Orc Format](#specifying-orcformat), och [parkettgolv Format](#specifying-parquetformat) avsnitt. <br><br> Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner. |Nej |
| Komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**; och nivåer som stöds är: **Optimal** och **snabbast**. Mer information finns i [anger komprimering](#specifying-compression) avsnitt. |Nej |
| useBinaryTransfer |Ange om använder binära överföringsläge. True för en binär och FALSKT ASCII. Standardvärde: True. Den här egenskapen kan endast användas när associerade linked service-typen är av typen: FtpServer. |Nej |

> [!NOTE]
> filnamnet och fileFilter kan inte användas samtidigt.
>
>

### <a name="using-partionedby-property"></a>Med hjälp av egenskapen partionedBy
Du kan ange en dynamisk folderPath filnamn för tidsdata serien med partitionedBy som nämns i föregående avsnitt. Du kan göra det med Data Factory-makron och systemvariabeln SliceStart, SliceEnd som indikerar den logiska tidsperioden för en viss datasektorn.

Läs om tid serien datauppsättningar schemaläggning och segment i [skapa datauppsättningar](../articles/data-factory/v1/data-factory-create-datasets.md), [schemaläggning och utförande](../articles/data-factory/v1/data-factory-scheduling-and-execution.md), och [skapar Pipelines](../articles/data-factory/v1/data-factory-create-pipelines.md) artiklar.

#### <a name="sample-1"></a>Exempel 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
I det här exemplet {segment} ersättas med det angivna värdet för Data Factory systemvariabel SliceStart i formatet (YYYYMMDDHH). SliceStart refererar till starttid av sektorn. FolderPath är olika för varje segment. Exempel: 2014100103-wikidatagateway/wikisampledataout eller 2014100104-wikidatagateway/wikisampledataout.

#### <a name="sample-2"></a>Exempel 2:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
I det här exemplet extraheras år, månad, dag och tidpunkt för SliceStart till olika variabler som används av egenskaperna folderPath och filnamn.

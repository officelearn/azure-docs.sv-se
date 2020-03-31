---
title: Kopiera data till/från Azure Blob Storage
description: 'Lär dig hur du kopierar blob-data i Azure Data Factory. Använd vårt exempel: Så här kopierar du data till och från Azure Blob Storage och Azure SQL Database.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eab332f102b9e39981e2d8ed6e84f73fada87a1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282138"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Kopiera data till eller från Azure Blob Storage med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-azure-blob-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-blob-storage.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Azure Blob Storage connector i V2](../connector-azure-blob-storage.md).


I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data till och från Azure Blob Storage. Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

## <a name="overview"></a>Översikt
Du kan kopiera data från alla källdatalagringar som stöds till Azure Blob Storage eller från Azure Blob Storage till alla sink-datalager som stöds. I följande tabell finns en lista över datalager som stöds som källor eller sänkor av kopieringsaktiviteten. Du kan till exempel flytta data **från** en SQL Server-databas eller en Azure SQL-databas **till** en Azure-bloblagring. Och du kan kopiera data **från** Azure blob storage **till** en Azure SQL Data Warehouse eller en Azure Cosmos DB-samling.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure Blob Storage** till följande datalager:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till Azure Blob Storage:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> Kopieringsaktivitet stöder kopiering av data från/till både Azure Storage-konton för allmänt syfte och Lagring av frekvent/cool blob. Aktiviteten stöder **läsning från block-, tilläggs- eller sidblobbar,** men stöder **att skriva för att bara blockera blobbar**. Azure Premium Storage stöds inte som en mottagare eftersom den backas upp av sidblobar.
>
> Kopieringsaktivitet tar inte bort data från källan när data har kopierats till målet. Om du behöver ta bort källdata efter en lyckad kopia skapar du en [anpassad aktivitet](data-factory-use-custom-activities.md) för att ta bort data och använda aktiviteten i pipelinen. Ett exempel finns i [exempelmen Ta bort blob eller mapp på GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data till/från en Azure Blob Storage med hjälp av olika verktyg/API:er.

Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Den här artikeln har en [genomgång](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) för att skapa en pipeline för att kopiera data från en Azure Blob Storage-plats till en annan Azure Blob Storage-plats. En självstudiekurs om hur du skapar en pipeline för att kopiera data från en Azure Blob Storage till Azure SQL Database finns i [Självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md).

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera pipelines.
2. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken. Om du till exempel kopierar data från en Azure-bloblagring till en Azure SQL-databas skapar du två länkade tjänster för att länka ditt Azure-lagringskonto och Azure SQL-databas till din datafabrik. För länkade tjänstegenskaper som är specifika för Azure Blob Storage finns i avsnittet [länkade tjänstegenskaper.](#linked-service-properties)
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen. I exemplet som nämns i det sista steget skapar du en datauppsättning för att ange blob-behållaren och mappen som innehåller indata. Och du skapar en annan datauppsättning för att ange SQL-tabellen i Azure SQL-databasen som innehåller data som kopieras från blob-lagringen. För datauppsättningsegenskaper som är specifika för Azure Blob Storage finns i avsnittet [egenskaper för datauppsättning.](#dataset-properties)
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. I det tidigare exemplet använder du BlobSource som källa och SqlSink som en mottagare för kopieringsaktiviteten. Om du kopierar från Azure SQL Database till Azure Blob Storage använder du sqlsource och blobSink i kopieringsaktiviteten. För kopieringsaktivitetsegenskaper som är specifika för Azure Blob Storage finns i avsnittet [kopiera aktivitetsegenskaper.](#copy-activity-properties) Om du vill ha information om hur du använder ett datalager som källa eller en diskbänk klickar du på länken i föregående avsnitt för datalagret.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet.  För exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en Azure Blob Storage finns i avsnittet [JSON-exempel](#json-examples-for-copying-data-to-and-from-blob-storage  ) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure Blob Storage.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Det finns två typer av länkade tjänster som du kan använda för att länka en Azure Storage till en Azure-datafabrik. De är: **AzureStorage** länkad tjänst och **AzureStorageSas** länkade tjänst. Den Azure Storage-länkade tjänsten ger datafabriken global åtkomst till Azure Storage. Azure Storage SAS-tjänsten (Shared Access Signature) ger datafabriken begränsad/tidsbunden åtkomst till Azure Storage. Det finns inga andra skillnader mellan dessa två länkade tjänster. Välj den länkade tjänsten som passar dina behov. Följande avsnitt innehåller mer information om dessa två länkade tjänster.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Egenskaper för datamängd
Om du vill ange en datauppsättning som ska representera indata eller utdata i en Azure Blob Storage anger du typegenskapen för datauppsättningen till: **AzureBlob**. Ange egenskapen **linkedServiceName** för datauppsättningen till namnet på azure storage- eller Azure Storage SAS-länkade tjänsten.  Datauppsättningens typegenskaper anger **blob-behållaren** och **mappen** i blob-lagringen.

En fullständig lista över JSON-avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell osv.).

Data factory stöder följande CLS-kompatibla .NET-baserade typvärden för att tillhandahålla typinformation i "struktur" för schema-på-läsning datakällor som Azure blob: Int16, Int32, Int64, Enkel, Dubbel, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. Data Factory utför automatiskt typkonverteringar när data flyttas från ett källdatalager till ett sink-datalager.

Avsnittet **typeProperties** är olika för varje typ av datauppsättning och ger information om platsen, formatet etc., för data i datalagret. Avsnittet typeProperties för datauppsättning av typen **AzureBlob-datauppsättning** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökväg till behållaren och mappen i blob-lagringen. Exempel: myblobcontainer\myblobfolder\ |Ja |
| fileName |Namnet på blobben. fileName är valfritt och skiftlägeskänsligt.<br/><br/>Om du anger ett filnamn fungerar aktiviteten (inklusive Kopiera) på den specifika Bloben.<br/><br/>När filnamn inte har angetts innehåller Copy alla Blobbar i folderPath för indatauppsättning.<br/><br/>När **fileName** inte anges för en utdatauppsättning och **bevaraHierarchy** inte anges i aktivitetsmottagaren, skulle namnet `Data.<Guid>.txt` på den genererade filen vara i följande format: (till exempel: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Inga |
| partitioneradAv |partitionedBy är en valfri egenskap. Du kan använda den för att ange en dynamisk folderPath och filnamn för tidsseriedata. FolderPath kan till exempel parameteriseras för varje timme data. Mer information och exempel finns i [egenskapsavsnittet Använda partitionedBy.](#using-partitionedby-property) |Inga |
| format | Följande formattyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet Format.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata. |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns [i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

### <a name="using-partitionedby-property"></a>Använda partitionedBy-egenskapen
Som nämnts i föregående avsnitt kan du ange en dynamisk folderPath och filnamn för tidsseriedata med egenskapen **partitionedBy,** [Data Factory-funktioner och systemvariablerna](data-factory-functions-variables.md).

Mer information om tidsseriedatauppsättningar, schemaläggning och segment finns i [Skapa datauppsättningar](data-factory-create-datasets.md) och [schemaläggning & artiklar för körning.](data-factory-scheduling-and-execution.md)

#### <a name="sample-1"></a>Exempel 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

I det här exemplet ersätts {Slice} med värdet för Data Factory systemvariabeln SliceStart i det format (YYYYMMDDHH) som anges. Segmentstart refererar till starttiden för segmentet. folderPath är olika för varje segment. Till exempel: wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Exempel 2

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

I det här exemplet extraheras år, månad, dag och tid för SegmentStart i separata variabler som används av egenskaperna folderPath och fileName.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatauppsättningar och principer är tillgängliga för alla typer av aktiviteter. Medan egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten varierar med varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns. Om du flyttar data från en Azure Blob Storage anger du källtypen i kopieringsaktiviteten till **BlobSource**. Om du flyttar data till en Azure Blob Storage anger du även sink-typen i kopieringsaktiviteten till **BlobSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av BlobSource och BlobSink.

**BlobSource** stöder följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiv |Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. |Sant (standardvärde), Falskt |Inga |

**BlobSink** stöder följande **egenskapstypEgenskaper:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| kopieraBehavior |Definierar kopieringsbeteendet när källan är BlobSource eller FileSystem. |<b>PreserveHierarchy</b>: bevarar filhierarkin i målmappen. Den relativa sökvägen för källfilen till källmappen är identisk med målfilens relativa sökväg till målmappen.<br/><br/><b>FlattenHierarchy</b>: alla filer från källmappen är i den första nivån av målmappen. Målfilerna har automatiskt genererat namn. <br/><br/><b>MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om fil-/blobnamnet anges är det kopplade filnamnet det angivna namnet. annars skulle vara automatiskt genererat filnamn. |Inga |

**BlobSource** stöder även dessa två egenskaper för bakåtkompatibilitet.

* **treatEmptyAsNull**: Anger om null- eller tom sträng ska behandlas som null-värde.
* **skipHeaderLineCount** - Anger hur många rader som behöver hoppas över. Den är endast tillämplig när indatauppsättningen använder TextFormat.

På samma sätt stöder **BlobSink** följande egenskap för bakåtkompatibilitet.

* **blobWriterAddHeader**: Anger om du vill lägga till en rubrik med kolumndefinitioner medan du skriver till en utdatauppsättning.

Datauppsättningar stöder nu följande egenskaper som implementerar samma funktioner: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

I följande tabell finns vägledning om hur du använder de nya datauppsättningsegenskaperna i stället för dessa blob-käll-/diskbänksegenskaper.

| Egenskapen Kopiera aktivitet | Egenskapen Dataset |
|:--- |:--- |
| skipHeaderLineCount på BlobSource |skipLineCount och firstRowAsHeader. Linjer hoppas över först och sedan den första raden läss som en rubrik. |
| treatEmptyAsNull på BlobSource |treatEmptyAsNull på indatauppsättning |
| blobWriterAddHeader på BlobSink |firstRowAsHeader på utdatauppsättning |

Se [Ange textformatavsnitt](data-factory-supported-file-and-compression-formats.md#text-format) för detaljerad information om dessa egenskaper.

### <a name="recursive-and-copybehavior-examples"></a>rekursiva och copyBehavior exempel
I det här avsnittet beskrivs det resulterande beteendet för kopieringsåtgärden för olika kombinationer av rekursiva värden och copyBehavior-värden.

| Rekursiv | kopieraBehavior | Resulterande beteende |
| --- | --- | --- |
| true |bevaraHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmappen Folder1 skapas med samma struktur som källan<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5. |
| true |plattaHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File5 |
| true |mergeFiles |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 innehåll slås samman till en fil med automatiskt genererat filnamn |
| false |bevaraHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmappen Folder1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/><br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |
| false |plattaHierarchy |För en källmapp Mapp1 med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmappen Folder1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File2<br/><br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |
| false |mergeFiles |För en källmapp Mapp1 med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmappen Folder1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + File2-innehåll sammanfogas till en fil med automatiskt genererat filnamn. automatiskt genererat namn för Arkiv1<br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Genomgång: Använd kopieringsguiden för att kopiera data till/från Blob Storage
Låt oss titta på hur du snabbt kopierar data till/från en Azure-bloblagring. I den här genomgången lagras både käll- och måldatalager av typen Azure Blob Storage. Pipelinen i den här genomgången kopierar data från en mapp till en annan mapp i samma blob-behållare. Den här genomgången är avsiktligt enkel att visa inställningar eller egenskaper när du använder Blob Storage som källa eller diskho.

### <a name="prerequisites"></a>Krav
1. Skapa ett **Azure Storage-konto** för allmänt ändamål om du inte redan har ett. Du använder blob-lagringen som både **käll-** och **måldatalagring** i den här genomgången. om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-account-create.md).
2. Skapa en blob-behållare med namnet **adfblobconnector** i lagringskontot.
4. Skapa en mapp med namnet **indata** i **adfblobconnector-behållaren.**
5. Skapa en fil med namnet **emp.txt** med följande innehåll och ladda upp den till **indatamappen** med hjälp av verktyg som [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Skapa datafabriken
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Skapa en resurs** från det övre vänstra hörnet, klicka på Intelligens + **analys**och klicka på **Data Factory**.
3. I fönstret **Nytt datafabrik:**  
    1. Ange **ADFBlobConnectorDF** för **namnet**. Namnet på Azure Data Factory måste vara globalt unikt. Om felet visas: `*Data factory name “ADFBlobConnectorDF” is not available`ändrar du namnet på datafabriken (till exempel ditt namnADFBlobConnectorDF) och försök skapa igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
    2. Välj din **Azure-prenumeration**.
    3. För Resursgrupp väljer du **Använd befintlig** för att välja en befintlig resursgrupp (eller) väljer **Skapa ny** om du vill ange ett namn för en resursgrupp.
    4. Välj **plats** för datafabriken.
    5. Välj kryssrutan **PIN-kod för instrumentpanelen** längst ned på bladet.
    6. Klicka på **Skapa**.
3. När skapandet är klart visas **bladet Data Factory** som ![visas i följande bild: Startsida för datafabriken](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Guiden Kopiera
1. Klicka på panelen **Kopiera data** på startsidan för Data factory för att starta guiden **Kopiera data** på en separat flik.  

    > [!NOTE]
    > Om du ser att webbläsaren har fastnat på "Auktorisera..." inaktiverar/avmarkerar **du Blockera/avmarkera Blockera cookies från tredje part och platsdatainställning** (eller) hålla den aktiverad och skapa ett undantag för **login.microsoftonline.com** och försök sedan starta guiden igen.
2. På sidan **Egenskaper**:
    1. Ange **CopyPipeline** för **Aktivitetsnamn**. Aktivitetsnamnet är namnet på pipelinen i datafabriken.
    2. Ange en **beskrivning** av uppgiften (valfritt).
    3. För **aktivitetskadens eller aktivitetsschema**håller du alternativet **Kör regelbundet enligt schemat.** Om du bara vill köra den här aktiviteten en gång i stället för att köras upprepade gånger enligt ett schema väljer du **Kör en gång nu**. Om du väljer alternativet **Kör en gång nu** skapas en [engångspipeline.](data-factory-create-pipelines.md#onetime-pipeline)
    4. Behåll inställningarna för **Återkommande mönster**. Den här aktiviteten körs dagligen mellan start- och sluttider som du anger i nästa steg.
    5. Ändra **startdatumtiden** till **2017-04-21**.
    6. Ändra **slutdatumtiden** till **2017-04-25**. Du kanske vill skriva datumet i stället för att bläddra igenom kalendern.
    8. Klicka på **Nästa**.
        ![Verktyget Kopiera – sidan Egenskaper](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. På sidan **Källans datalager** klickar du på ikonen **Azure blobblagring**. Du kan använda den här sidan till att ange källans datalager för kopieringsaktiviteten. Du kan använda en länkad tjänst för ett befintligt datalager (eller) ange ett nytt datalager. Om du vill använda en befintlig länkad tjänst väljer du **FRÅN BEFINTLIGA LÄNKADE TJÄNSTER** och väljer rätt länkad tjänst.
    ![Verktyget Kopiera – sidan Källans datalager](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. På sidan **Ange konto för Azure blobblagring**:
    1. Behåll det automatiskt genererade namnet för **anslutningsnamn**. Anslutningsnamnet är namnet på den länkade tjänsten av typen: Azure Storage.
    2. Kontrollera att alternativet **Från Azure-prenumerationer** har valts för **Metod för kontoval**.
    3. Välj din Azure-prenumeration eller **behåll Välj alla** för **Azure-prenumeration**.
    4. Välj ett **Azure-lagringskonto** i listan med Azure-lagringskonton som är tillgängliga för den prenumeration som du har valt. Du kan också välja att ange inställningar för lagringskonto manuellt genom att välja alternativet **Ange manuellt** för **metoden Kontoval**.
    5. Klicka på **Nästa**.  
        ![Verktyget Kopiera – Ange konto för Azure blobblagring](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. På sidan **Välj indatafil eller mapp**:
    1. Dubbelklicka **på adfblobcontainer**.
    2. Välj **indata**och klicka på **Välj**. I den här genomgången väljer du indatamappen. Du kan också välja filen emp.txt i mappen i stället.
        ![Verktyget Kopiera – Välj indatafil eller mapp](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. På sidan **Välj indatafil eller mapp:**
    1. Bekräfta att **filen eller mappen** är inställd på **adfblobconnector/indata**. Om filerna finns i undermappar, till exempel 2017/04/01, 2017/04/02, och så vidare, anger du adfblobconnector/input/{year}/{month}/{day} för filen eller mappen. När du trycker på TABB ur textrutan visas tre listrutor för att välja format för år (yyyy), månad (MM) och dag (dd).
    2. Ange inte **kopiera filen rekursivt**. Välj det här alternativet om du vill gå igenom mappar för att filer ska kopieras till målet.
    3. Inte alternativet **binär kopia.** Välj det här alternativet om du vill utföra en binär kopia av källfilen till målet. Välj inte för den här genomgången så att du kan se fler alternativ på nästa sida.
    4. Bekräfta att **komprimeringstypen** är inställd på **Ingen**. Välj ett värde för det här alternativet om källfilerna komprimeras i något av formaten som stöds.
    5. Klicka på **Nästa**.
    ![Verktyget Kopiera – Välj indatafil eller mapp](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. På den **formatinställningar** kan du se avgränsare och det schema som upptäcks automatiskt i guiden genom att parsa filen.
    1. Bekräfta följande alternativ:  
        a. **Filformatet** är inställt på **Textformat**. Du kan se alla format som stöds i listrutan. Till exempel: JSON, Avro, ORC, Parkett.
       b. **Kolumn avgränsare** är inställd `Comma (,)`på . Du kan se de andra kolumnavgränsarna som stöds av Data Factory i listrutan. Du kan också ange en anpassad avgränsare.
       c. **Radavgränsaren** är inställd `Carriage Return + Line feed (\r\n)`på . Du kan se de andra radavgränsarna som stöds av Data Factory i listrutan. Du kan också ange en anpassad avgränsare.
       d. Antalet **överhopprader** är inställt på **0**. Om du vill att några rader ska hoppas över högst upp i filen anger du numret här.
       e. Den **första dataraden innehåller kolumnnamn** är inte inställd. Om källfilerna innehåller kolumnnamn på den första raden markerar du det här alternativet.
       f. Värdet **för behandla tom kolumn som null** anges.
    2. Expandera **avancerade inställningar** för att se avancerade alternativ tillgängligt.
    3. Längst ned på sidan, se **förhandsgranskningen** av data från filen emp.txt.
    4. Klicka på fliken **SCHEMA** längst ned om du vill se det schema som kopieringsguiden härledde genom att titta på data i källfilen.
    5. Klicka på **Nästa** när du har granskat avgränsarna och förhandsgranskat data.
    ![Verktyget Kopiera – Filformatinställningar](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. På **sidan Måldatalager**väljer du **Azure Blob Storage**och klickar på **Nästa**. Du använder Azure Blob Storage som både käll- och måldatalager i den här genomgången.  
    ![Kopieringsverktyg - välj måldatalag](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. På Ange sidan **för Azure Blob-lagringskonto:**  
    1. Ange **AzureStorageLinkedService** för fältet **Anslutningsnamn.**
    2. Kontrollera att alternativet **Från Azure-prenumerationer** har valts för **Metod för kontoval**.
    3. Välj din **Azure-prenumeration**.
    4. Välj ditt Azure-lagringskonto.
    5. Klicka på **Nästa**.
10. På sidan **Välj utdatafil eller mapp:**  
    1. ange **mappsökväg** som **adfblobconnector/output/{year}/{month}/{day}**. Ange **TABB**.
    1. För **året**väljer du **yyyy**.
    1. För **månaden**bekräftar du att den är inställd på **MM**.
    1. För **dagen**bekräftar du att den är inställd på **dd**.
    1. Bekräfta att **komprimeringstypen** är inställd på **Ingen**.
    1. Bekräfta att **kopieringsbeteendet** är inställt **på Sammanfoga filer**. Om utdatafilen med samma namn redan finns läggs det nya innehållet till i samma fil i slutet.
    1. Klicka på **Nästa**.
       ![Kopieringsverktyg - Välj utdatafil eller -mapp](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. På sidan **Inställningar för filformat** granskar du inställningarna och klickar på **Nästa**. Ett av de ytterligare alternativen här är att lägga till ett huvud i utdatafilen. Om du väljer det alternativet läggs en rubrikrad till med namnen på kolumnerna från schemat för källan. Du kan byta namn på standardkolumnnamnen när du visar schemat för källan. Du kan till exempel ändra den första kolumnen till Förnamn och andra kolumnen till Efternamn. Sedan genereras utdatafilen med ett sidhuvud med dessa namn som kolumnnamn.
    ![Kopieringsverktyg - Inställningar för filformat för mål](media/data-factory-azure-blob-connector/file-format-destination.png)
12. På sidan **Prestandainställningar** bekräftar du att **molnenheter** och **parallella kopior** är **inställda**på Auto och klickar på Nästa. Mer information om de här inställningarna finns i [Kopiera aktivitetsprestanda och justeringsguide](data-factory-copy-activity-performance.md#parallel-copy).
    ![Kopieringsverktyg - Prestandainställningar](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. På sidan **Sammanfattning** granskar du alla inställningar (aktivitetsegenskaper, inställningar för källa och mål och kopiera inställningar) och klickar på **Nästa**.
    ![Kopieringsverktyg - sammanfattningssida](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Granska informationen på sidan **Sammanfattning** och klicka på **Slutför**. Guiden skapar två länkade tjänster, två datauppsättningar (indata och utdata) och en pipeline i datafabriken (från den plats där du startade guiden Kopiera).
    ![Kopieringsverktyg - distributionssida](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Övervaka pipelinen (kopiera uppgift)

1. Klicka på `Click here to monitor copy pipeline` länken på **sidan Distribution.**
2. Du bör se **programmet Övervaka och hantera** på en separat flik.  ![Övervaka och hantera app](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Ändra **starttiden** högst upp `04/19/2017` till och `04/27/2017` **sluttid** till och klicka sedan på **Använd**.
4. Du bör se fem aktivitetsfönster i **AKTIVITETS WINDOWS-listan.** **WindowStart-tiderna** bör omfatta alla dagar från pipelinestart till pipelinesluttider.
5. Klicka på **Uppdatera** för **ACTIVITY WINDOWS-listan** några gånger tills du ser status för alla aktivitetsfönster är inställt på Klar.
6. Kontrollera nu att utdatafilerna genereras i utdatamappen i adfblobconnector-behållaren. Du bör se följande mappstruktur i utdatamappen:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Detaljerad information om övervakning och hantering av datafabriker finns i [Övervaka och hantera datafabrikens pipelineartikel.](data-factory-monitor-manage-app.md)

### <a name="data-factory-entities"></a>Data Factory enheter
Växla nu tillbaka till fliken med startsidan för Data Factory. Observera att det finns två länkade tjänster, två datauppsättningar och en pipeline i datafabriken nu.

![Data Factory hemsida med entiteter](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Klicka på **Författare och distribuera** för att starta Data Factory Editor.

![Data Factory-redigeraren](media/data-factory-azure-blob-connector/data-factory-editor.png)

Du bör se följande Data Factory-entiteter i datafabriken:

- Två länkade tjänster. En för källan och den andra för destinationen. Båda de länkade tjänsterna refererar till samma Azure Storage-konto i den här genomgången.
- Två datamängder. En indatauppsättning och en utdatauppsättning. I den här genomgången använder båda samma blob-behållare men refererar till olika mappar (indata och utdata).
- En pipeline. Pipelinen innehåller en kopieringsaktivitet som använder en blob-källa och en blob-mottagare för att kopiera data från en Azure-blob-plats till en annan Azure-blob-plats.

Följande avsnitt innehåller mer information om dessa entiteter.

#### <a name="linked-services"></a>Länkade tjänster
Du bör se två länkade tjänster. En för källan och den andra för destinationen. I den här genomgången ser båda definitionerna likadana ut förutom namnen. **Typen** av den länkade tjänsten är inställd på **AzureStorage**. Den viktigaste egenskapen för den länkade tjänstdefinitionen är **connectionString**, som används av Data Factory för att ansluta till ditt Azure Storage-konto vid körning. Ignorera egenskapen hubName i definitionen.

##### <a name="source-blob-storage-linked-service"></a>Länkad tjänst för källbloloblagring
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Länkad tjänst för målblobblagring

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Mer information om Azure Storage-länkad tjänst finns i avsnittet [Länkade tjänstegenskaper.](#linked-service-properties)

#### <a name="datasets"></a>Datauppsättningar
Det finns två datauppsättningar: en indatauppsättning och en utdatauppsättning. Typen av datauppsättningen är inställd på **AzureBlob** för båda.

Indatauppsättningen pekar på **indatamappen** i **adfblobconnector-blob-behållaren.** Den **externa** egenskapen är inställd på **true** för den här datauppsättningen eftersom data inte produceras av pipelinen med kopieringsaktiviteten som tar den här datauppsättningen som indata.

Utdatauppsättningen pekar på **utdatamappen** i samma blob-behållare. Utdatauppsättningen använder också år, månad och dag för systemvariabeln **SegmentStart** för att dynamiskt utvärdera sökvägen för utdatafilen. En lista över funktioner och systemvariabler som stöds av Data Factory finns i [Data Factorys funktioner och systemvariabler](data-factory-functions-variables.md). Den **externa** egenskapen är inställd på **false** (standardvärde) eftersom den här datauppsättningen produceras av pipelinen.

Mer information om egenskaper som stöds av Azure Blob-datauppsättning finns i avsnittet [Egenskaper för datauppsättning.](#dataset-properties)

##### <a name="input-dataset"></a>Indatauppsättning

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Utdatauppsättning

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Pipeline
Pipelinen har bara en aktivitet. **Typen** av aktivitet är inställd på **Kopiera**. I typegenskaperna för aktiviteten finns det två avsnitt, en för källa och den andra för sink. Källtypen är inställd på **BlobSource** eftersom aktiviteten kopierar data från en blob-lagring. Sink-typen är inställd på **BlobSink** som aktivitetskopieringsdata till en blob-lagring. Kopieringsaktiviteten tar InputDataset-z4y som indata och OutputDataset-z4y som utdata.

Mer information om egenskaper som stöds av BlobSource och BlobSink finns i Avsnittet [Kopiera aktivitetsegenskaper.](#copy-activity-properties)

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>JSON-exempel för kopiering av data till och från Blob Storage
Följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure Blob Storage och Azure SQL Database. Data kan dock kopieras **direkt** från någon av källorna till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON Exempel: Kopiera data från Blob Storage till SQL Database
Följande exempel visar:

1. En länkad tjänst av typen [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en kopieringsaktivitet som använder [BlobSource](#copy-activity-properties) och [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

Exemplet kopierar tidsseriedata från en Azure-blob till en Azure SQL-tabell varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

**Azure SQL-länkad tjänst:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Storage-länkad tjänst:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory stöder två typer av Azure Storage-länkade tjänster: **AzureStorage** och **AzureStorageSas**. För den första anger du anslutningssträngen som innehåller kontonyckeln och för den senare anger du SAS-uri (Shared Access Signature). Mer information finns i avsnittet [Länkade tjänster.](#linked-service-properties)

**Azure Blob-indatauppsättning:**

Data hämtas från en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder år, månad och dag del av starttiden och filnamnet använder timdelen av starttiden. "extern": "true"-inställningen informerar Data Factory om att tabellen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure SQL-datadatauppsättning:**

Exemplet kopierar data till en tabell med namnet "MyTable" i en Azure SQL-databas. Skapa tabellen i din Azure SQL-databas med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader läggs till i tabellen varje timme.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**En kopieringsaktivitet i en pipeline med Blob-källa och SQL-mottagare:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I pipeline-JSON-definitionen anges **källtypen** till **BlobSource** och **sink-typen** är inställd på **SqlSink**.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON Exempel: Kopiera data från Azure SQL till Azure Blob
Följande exempel visar:

1. En länkad tjänst av typen [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) och [BlobSink](#copy-activity-properties).

Exemplet kopierar tidsseriedata från en Azure SQL-tabell till en Azure-blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

**Azure SQL-länkad tjänst:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Storage-länkad tjänst:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory stöder två typer av Azure Storage-länkade tjänster: **AzureStorage** och **AzureStorageSas**. För den första anger du anslutningssträngen som innehåller kontonyckeln och för den senare anger du SAS-uri (Shared Access Signature). Mer information finns i avsnittet [Länkade tjänster.](#linked-service-properties)

**Azure SQL-indatauppsättning:**

Exemplet förutsätter att du har skapat en tabell "MyTable" i Azure SQL och den innehåller en kolumn som kallas "tidsstämpelpcolumn" för tidsseriedata.

Inställningen "extern": "true" informerar Data Factory-tjänsten om att tabellen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Blob-utdatauppsättning:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**En kopieringsaktivitet i en pipeline med SQL-källa och Blob-mottagare:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I pipeline-JSON-definitionen anges **källtypen** till **SqlSource** och **sink-typen** är inställd på **BlobSink**. Sql-frågan som angetts för egenskapen **SqlReaderQuery** väljer de data som ska kopieras under den senaste timmen.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

> [!NOTE]
> Information om hur du mappar kolumner från källdatauppsättning till kolumner från sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.

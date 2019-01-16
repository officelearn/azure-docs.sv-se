---
title: Kopiera data till och från Azure Blob Storage | Microsoft Docs
description: 'Lär dig hur du kopierar en blob-data i Azure Data Factory. Använd vårt exempel: Hur du kopierar data till och från Azure Blob Storage och Azure SQL Database.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 733ae4451988651df2a62a22aa6eb1b6fae44309
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331732"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Kopiera data till och från Azure Blob Storage med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-azure-blob-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-blob-storage.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Azure Blob Storage connector i V2](../connector-azure-blob-storage.md).


Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data till och från Azure Blob Storage. Den bygger på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten.

## <a name="overview"></a>Översikt
Du kan kopiera data från alla dataarkiv till Azure Blob Storage eller Azure Blob Storage till alla mottagarens datalager. I följande tabell innehåller en lista över datalager som stöds som källor eller egenskaperna av kopieringsaktiviteten. Du kan exempelvis flytta data **från** en SQL Server-databas eller en Azure SQL database **till** Azure blob storage. Och du kan kopiera data **från** Azure blobblagring **till** en Azure SQL Data Warehouse eller en Azure Cosmos DB-samling.

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure Blob Storage** till följande data lagras:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till Azure Blob Storage**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> Kopieringsaktivitet har stöd för kopiering av data från/till både Allmänt Azure Storage-konton och frekvent/lågfrekvent – Blob storage. Aktiviteten stöder **läsningen från block, lägga till eller sidblobbar**, men stöder **skrivning till endast blockblobar**. Azure Premium Storage stöds inte som en mottagare eftersom den backas upp av sidblobar.
>
> Kopieringsaktivitet tar inte bort data från källan när data har kopierats till målet. Om du vill ta bort källdata efter en lyckad kopiering kan du skapa en [anpassad aktivitet](data-factory-use-custom-activities.md) att ta bort data och använda aktiviteten i pipelinen. Ett exempel finns i den [ta bort blob- eller mappnamn exemplet på GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Kom igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data till och från Azure Blob Storage med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Den här artikeln innehåller en [genomgången](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) för att skapa en pipeline för att kopiera data från en Azure Blob Storage-plats till en annan plats i Azure Blob Storage. En självstudiekurs om hur du skapar en pipeline för att kopiera data från Azure Blob Storage till Azure SQL Database finns i [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md).

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera pipelines.
2. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik. Till exempel om du kopierar data från Azure blob storage till en Azure SQL-databas skapa du två länkade tjänster för att länka ditt Azure storage-konto och Azure SQL-databas till datafabriken. Länkade tjänstegenskaper som är specifika för Azure Blob Storage, se [länkade tjänstegenskaper](#linked-service-properties) avsnittet.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen. I det tidigare exemplet i det sista steget, skapar du en datauppsättning för att ange blobbehållare och mapp som innehåller indata. Och skapar du en annan datauppsättning för att ange den SQL-tabellen i Azure SQL-databasen som innehåller de data som kopieras från blob storage. Egenskaper för datamängd som är specifika för Azure Blob Storage, se [egenskaper för datamängd](#dataset-properties) avsnittet.
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. I exemplet som nämnts tidigare, använder du BlobSource som en källa och SqlSink som mottagare för kopieringsaktiviteten. På samma sätt, om du kopierar från Azure SQL Database till Azure Blob Storage, använder du SqlSource och BlobSink i kopieringsaktiviteten. Kopiera Aktivitetsegenskaper som är specifika för Azure Blob Storage, se [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnittet. Mer information om hur du använder ett datalager som en källa eller mottagare klickar du på länken i föregående avsnitt för ditt datalager.

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format.  Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till och från Azure Blob Storage finns [JSON-exempel](#json-examples-for-copying-data-to-and-from-blob-storage  ) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure Blob Storage.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Det finns två typer av länkade tjänster som du kan använda för att länka ett Azure Storage till en Azure-datafabrik. De är: **AzureStorage** länkad tjänst och **AzureStorageSas** länkad tjänst. Länkad Azure Storage-tjänsten tillhandahåller data factory med global åtkomst till Azure Storage. Medan det Azure Storage SAS (Shared Access Signature) länkad tillhandahåller service data factory med begränsade/Tidsbundna åtkomst till Azure Storage. Det finns inga andra skillnader mellan dessa två länkade tjänster. Välj den länkade tjänst som passar dina behov. Följande avsnitt innehåller mer information om dessa två länkade tjänster.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Egenskaper för datamängd
Om du vill ange en datauppsättning för att representera inkommande eller utgående data i Azure Blob Storage kan du ange egenskapen type på datauppsättningen till: **AzureBlob**. Ange den **linkedServiceName** egenskap med namnet på Azure Storage eller Azure Storage SAS länkad tjänst.  Typegenskaperna för datauppsättningen anger den **blobbehållare** och **mappen** i blob storage.

En fullständig lista över JSON-avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av (Azure SQL, Azure-blob, Azure-tabell osv.).

Data factory stöder följande CLS-kompatibel .NET baserat typvärden för att tillhandahålla anger du följande information i ”strukturen” för schema vid läsning-datakällor som Azure blob: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. Data Factory utför typkonverteringar automatiskt när du flyttar data från källans datalager till mottagarens datalager.

Den **typeProperties** avsnittet är olika för varje typ av datauppsättning och tillhandahåller information om plats, formatera osv, på data i datalagret. TypeProperties avsnittet för datauppsättningen av typen **AzureBlob** datauppsättning har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökvägen till behållaren och mappen i blob storage. Exempel: myblobcontainer\myblobfolder\ |Ja |
| fileName |Namnet på bloben. Filnamnet är valfria och skiftlägeskänsliga.<br/><br/>Om du anger ett filename fungerar aktiviteten (inklusive kopia) på den specifika blobben.<br/><br/>Om filnamnet inte anges innehåller kopiera alla Blobbar i folderPath för datauppsättningen för indata.<br/><br/>När **fileName** har inte angetts för en utdatauppsättning och **preserveHierarchy** har inte angetts i aktiviteten mottagare, namnet på den genererade filen skulle vara i följande det här formatet: Data. <Guid>.txt (exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| partitionedBy |partitionedBy är en valfri egenskap. Du kan använda den för att ange en dynamisk folderPath och ett filnamn för time series-data. Till exempel kan folderPath parameteriseras för varje timme som data. Se den [med partitionedBy egenskapen](#using-partitionedBy-property) information och exempel. |Nej |
| Format | Följande formattyper av stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia). |Nej |
| Komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

### <a name="using-partitionedby-property"></a>Med hjälp av egenskapen partitionedBy
Som tidigare nämnts ovan kan du ange en dynamisk folderPath och ett filnamn för time series-data med den **partitionedBy** egenskapen [Data Factory-funktioner och systemvariablerna](data-factory-functions-variables.md).

Läs mer på time series datauppsättningar, schemaläggning och segment, [skapar datauppsättningar](data-factory-create-datasets.md) och [schemaläggning och utförande](data-factory-scheduling-and-execution.md) artiklar.

#### <a name="sample-1"></a>Exempel 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

I det här exemplet {sektorn} ersätts med det angivna värdet av Data Factory systemvariabeln SliceStart i formatet (YYYYMMDDHH). SliceStart refererar starttid för sektorn. FolderPath är olika för varje segment. Till exempel: wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104

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

I det här exemplet extraheras år, månad, dag och tid för SliceStart till olika variabler som används av egenskaper för folderPath och filnamn.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata datauppsättningar och principer är tillgängliga för alla typer av aktiviteter. Å andra sidan Egenskaper som är tillgängliga i den **typeProperties** avsnittet aktivitetens varierar med varje aktivitetstyp av. För kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare. Om du flyttar data från Azure Blob Storage måste du ange källtypen i kopieringsaktiviteten till **BlobSource**. På samma sätt om du flyttar data till Azure Blob Storage måste du ange Mottagartyp i kopieringsaktiviteten till **BlobSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av BlobSource och BlobSink.

**BlobSource** har stöd för följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiv |Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. |SANT (standard), FALSKT |Nej |

**BlobSink** har stöd för följande egenskaper **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| copyBehavior |Definierar kopieringsbeteendet när källan är BlobSource eller filsystem. |<b>PreserveHierarchy</b>: bevarar filen hierarkin i målmappen. Den relativa sökvägen för källfilen för källmappen är identisk med den relativa sökvägen för målfilen till målmappen.<br/><br/><b>FlattenHierarchy</b>: alla filer från källmappen finns i den första nivån i målmappen. Målfiler har automatiskt genererad namn. <br/><br/><b>MergeFiles</b>: slår samman alla filer från källmappen till en fil. Om namnet på filen/blobben har angetts, blir namnet på sammanfogade filen det angivna namnet; annars skulle vara automatiskt genererade filnamn. |Nej |

**BlobSource** stöder också dessa två egenskaper för bakåtkompatibilitet.

* **treatEmptyAsNull**: Anger om du vill hantera null eller tomma strängar som null-värde.
* **skipHeaderLineCount** – anger hur många rader måste hoppas över. Det gäller endast när datauppsättningen för indata använder TextFormat.

På samma sätt **BlobSink** stöder följande egenskap för bakåtkompatibilitet.

* **blobWriterAddHeader**: Anger om du vill lägga till en rubrik för kolumndefinitionerna vid skrivning till en datauppsättning för utdata.

Datauppsättningar har nu stöd för följande egenskaper som implementerar samma funktion: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

I följande tabell innehåller råd om hur du använder de nya egenskaperna för datamängd i stället för dessa egenskaper för blob-källa/mottagare.

| Kopiera aktivitetsegenskap | DataSet egenskapen |
|:--- |:--- |
| skipHeaderLineCount på BlobSource |skipLineCount och firstRowAsHeader. Hoppas raderna över först och sedan läses den första raden som rubrik. |
| treatEmptyAsNull på BlobSource |treatEmptyAsNull på datauppsättningen för indata |
| blobWriterAddHeader på BlobSink |firstRowAsHeader på datauppsättningen för utdata |

Se [att ange TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) för detaljerad information om dessa egenskaper.

### <a name="recursive-and-copybehavior-examples"></a>rekursiva och copyBehavior exempel
Det här avsnittet beskrivs kopieringsåtgärden för olika kombinationer av värden för rekursiv och copyBehavior resulterande beteende.

| rekursiv | copyBehavior | Resulterande beteende |
| --- | --- | --- |
| true |preserveHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med samma struktur som källa<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatiskt genererade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File5 |
| true |mergeFiles |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 + fil3 + File4 + filen 5 innehållet slås samman i en fil med autogenererade filnamn |
| false |preserveHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/><br/>Subfolder1 med fil3, File4 och File5 plockas inte upp. |
| false |flattenHierarchy |För en källmapp Mapp1 med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatiskt genererade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/><br/><br/>Subfolder1 med fil3, File4 och File5 plockas inte upp. |
| false |mergeFiles |För en källmapp Mapp1 med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 innehållet slås samman i en fil med automatiskt genererade namnet. Automatiskt genererade namn på File1<br/><br/>Subfolder1 med fil3, File4 och File5 plockas inte upp. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Steg-för-steg-beskrivning: Använd guiden Kopiera för att kopiera data till och från Blob Storage
Nu ska vi titta på hur du snabbt kopiera data till och från Azure blob storage. I den här genomgången lagrar både källa och mål data av typen: Azure Blob Storage. Pipelinen i den här genomgången kopierar data från en mapp till en annan mapp i samma blob-behållaren. Den här genomgången är avsiktligt enkelt att visa egenskaper eller inställningar när du använder Blob Storage som källa eller mottagare.

### <a name="prerequisites"></a>Förutsättningar
1. Skapa ett allmänt **Azure Storage-konto** om du inte redan har en. Du kan använda bloblagringen som båda **källa** och **mål** datalager i den här genomgången. om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md).
2. Skapa en blobbehållare med namnet **adfblobconnector** i lagringskontot.
4. Skapa en mapp med namnet **inkommande** i den **adfblobconnector** behållare.
5. Skapa en fil med namnet **emp.txt** med följande innehåll och överför den till den **inkommande** mapp genom att använda verktyg som [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```
### <a name="create-the-data-factory"></a>Skapa data factory
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **skapa en resurs** i det övre vänstra hörnet, klickar du på **information + analys**, och klicka på **Data Factory**.
3. I den **ny datafabrik** fönstret:  
    1. Ange **ADFBlobConnectorDF** för den **namn**. Namnet på Azure Data Factory måste vara globalt unikt. Om du får felet: `*Data factory name “ADFBlobConnectorDF” is not available`, ändra namnet på datafabriken (till exempel yournameADFBlobConnectorDF) och försöker skapa igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
    2. Välj din Azure-**prenumeration**.
    3. Resursgrupp, Välj **Använd befintlig** att välja en befintlig resursgrupp (eller) Välj **Skapa nytt** och ange ett namn för en resursgrupp.
    4. Välj **plats** för datafabriken.
    5. Välj kryssrutan **PIN-kod för instrumentpanelen** längst ned på bladet.
    6. Klicka på **Skapa**.
3. När datafabriken har skapats visas bladet **Datafabrik** enligt nedanstående bild:  ![Datafabrikens startsida](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Guiden Kopiera
1. På startsidan Datafabrik klickar du på den **kopiera data** att starta **Data Kopieringsguiden** i en separat flik.  
    
    > [!NOTE]
    > Om du ser att webbläsaren har fastnat på ”auktoriserar...”, inaktiverar/avmarkerar **blockera cookies från tredje part och platsdata** inställning (eller) behåller den aktiverad och skapar ett undantag för **login.microsoftonline.com**och försök sedan starta guiden igen.
2. På sidan **Egenskaper**:
    1. Ange **CopyPipeline** för **aktivitetsnamn**. Uppgiftens namn är namnet på pipelinen i datafabriken.
    2. Ange en **beskrivning** för aktiviteten (valfritt).
    3. För **uppgift takt eller schemalägga**, hålla den **körs regelbundet enligt schema** alternativet. Om du vill köra den här uppgiften bara en gång i stället för att köras upprepade gånger enligt ett schema, väljer **kör en gång nu**. Om du väljer **kör en gång nu** alternativet en [enstaka pipeline](data-factory-create-pipelines.md#onetime-pipeline) har skapats.
    4. Synkronisera inställningarna för **återkommande mönster**. Den här aktiviteten körs dagligen mellan start- och sluttider som du anger i nästa steg.
    5. Ändra den **startdatum / tid** till **2017-04-21**.
    6. Ändra den **Slutdatum / tid** till **2017-04-25**. Du kanske vill ange det datum då i stället för att gå igenom kalendern.
    8. Klicka på **Nästa**.
        ![Verktyget kopiera – sidan Egenskaper](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. På sidan **Källans datalager** klickar du på ikonen **Azure blobblagring**. Du kan använda den här sidan till att ange källans datalager för kopieringsaktiviteten. Du kan använda en länkad tjänst för ett befintligt datalager (eller) ange ett nytt datalager. Om du vill använda en befintlig länkad tjänst, väljer du **från befintliga LÄNKADE tjänster** och väljer lämplig länkad tjänst.
    ![Verktyget kopiera – sidan källans datalager](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. På sidan **Ange konto för Azure blobblagring**:
    1. Behåll det automatisk genererade namnet för **anslutningsnamn**. Anslutningens namn är namnet på den länkade tjänsten av typen: Azure Storage.
    2. Kontrollera att alternativet **Från Azure-prenumerationer** har valts för **Metod för kontoval**.
    3. Välj din Azure-prenumeration eller Behåll **Markera alla** för **Azure-prenumeration**.
    4. Välj ett **Azure-lagringskonto** i listan med Azure-lagringskonton som är tillgängliga för den prenumeration som du har valt. Du kan också välja att ange inställningar för lagringskontot manuellt genom att välja **ange manuellt** för den **Kontometod**.
    5. Klicka på **Nästa**.  
        ![Verktyget kopiera - ange Azure Blob storage-konto](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. På sidan **Välj indatafil eller mapp**:
    1. Dubbelklicka på **adfblobcontainer**.
    2. Välj **inkommande**, och klicka på **Välj**. I den här genomgången väljer du Indatamappen. Du kan även välja emp.txt-filen i mappen i stället.
        ![Verktyget kopiera – Välj indatafil eller mapp](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. På den **Välj indatafil eller mapp** sidan:
    1. Bekräfta att den **fil eller mapp** är inställd på **adfblobconnector/input**. Om filerna finns i undermappar, till exempel 2017-04-01, 2017/04/02 och så vidare, ange adfblobconnector/input / {year} / {month} / {day} för filen eller mappen. När du trycker på FLIKEN utanför textrutan visas tre listrutorna Välj format för år (åååå), månad (MM) och dag (dd).
    2. Ange inte **kopiera filen rekursivt**. Välj det här alternativet för att rekursivt Bläddra igenom mapparna för filer som ska kopieras till målet.
    3. Inte den **binär kopia** alternativet. Välj det här alternativet för att utföra en binär kopia av källfilen till målet. Markera inte den här genomgången så att du kan se fler alternativ i de följande sidorna.
    4. Bekräfta att den **Komprimeringstypen** är inställd på **ingen**. Välj ett värde för det här alternativet om källfilerna är komprimerade i en av format som stöds.
    5. Klicka på **Nästa**.
    ![Verktyget kopiera – Välj indatafil eller mapp](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. På den **formatinställningar** kan du se avgränsare och det schema som upptäcks automatiskt i guiden genom att parsa filen.
    1. Kontrollera följande alternativ:  
        a. Den **filformat** är inställd på **textformat**. Du kan se alla format som stöds i den nedrullningsbara listan. Exempel: JSON, Avro, ORC, Parquet.
       b. Den **kolumnavgränsare** är inställd på `Comma (,)`. Du kan se de andra kolumnen avgränsare stöds av Data Factory i den nedrullningsbara listan. Du kan också ange en anpassad avgränsare.
       c. Den **radavgränsaren** är inställd på `Carriage Return + Line feed (\r\n)`. Du kan se de andra raden avgränsare stöds av Data Factory i den nedrullningsbara listan. Du kan också ange en anpassad avgränsare.
       d. Den **hoppa över radnummer** är inställd på **0**. Om du vill att några rader som ska hoppas över överst i filen, anger du numret här.
       e. Den **första dataraden innehåller kolumnnamn** har inte angetts. Om källfilerna som innehåller kolumnnamn i den första raden, väljer du det här alternativet.
       f. Den **behandla tomma kolumnvärde som null** alternativet är inställt.
    2. Expandera **avancerade inställningar** att se avancerade alternativ.
    3. Längst ned på sidan finns i den **förhandsversion** av data från filen emp.txt.
    4. Klicka på **schemat** flik längst ned att visa schemat som Kopieringsguiden härleds genom att titta på data i källfilen.
    5. Klicka på **Nästa** när du har granskat avgränsarna och förhandsgranskat data.
    ![Verktyget kopiera – filformatinställningar](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. På den **måldatalager lagra sidan**väljer **Azure Blob Storage**, och klicka på **nästa**. Du använder Azure Blob Storage som både käll- och datalager i den här genomgången.  
    ![Verktyget kopiera – Välj måldatalager](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. På **ange Azure Blob storage-konto** sidan:  
    1. Ange **AzureStorageLinkedService** för den **anslutningsnamn** fält.
    2. Kontrollera att alternativet **Från Azure-prenumerationer** har valts för **Metod för kontoval**.
    3. Välj din Azure-**prenumeration**.
    4. Välj ditt Azure storage-konto.
    5. Klicka på **Nästa**.
10. På den **Välj utdatafil eller mapp** sidan:  
    6. Ange **mappsökväg** som **adfblobconnector/output / {year} / {month} / {day}**. Ange **FLIKEN**.
    7. För den **år**väljer **åååå**.
    8. För den **månad**, bekräfta att den är inställd på **MM**.
    9. För den **dag**, bekräfta att den är inställd på **dd**.
    10. Bekräfta att den **Komprimeringstypen** är inställd på **ingen**.
    11. Bekräfta att den **kopiera beteende** är inställd på **Sammanfoga filer**. Om utdatafilen med samma namn redan finns läggs det nya innehållet till samma fil i slutet.
    12. Klicka på **Nästa**.
    ![Verktyget kopiera – Välj utdatafil eller mapp](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. På den **filformatinställningar** sidan, granskar du inställningarna och klicka på **nästa**. En av de ytterligare alternativen är att lägga till en rubrik till utdatafilen. Om du har valt alternativet läggs en rubrikrad med namnen på kolumnerna från schemat för källan. Du kan byta namn på standardkolumnvärdena när du visar schemat för källan. Du kan till exempel ändra den första kolumnen till förnamn och efternamn andra kolumn. Sedan skapas utdatafilen med ett huvud med namnen som kolumnnamn.
    ![Verktyget kopiera – filformatinställningar för mål](media/data-factory-azure-blob-connector/file-format-destination.png)
12. På den **prestandainställningar** bekräftar som **molnbaserade enheter** och **parallell kopior** är inställda på **automatisk**, och klicka på Nästa. Mer information om dessa inställningar finns i [kopiera aktivitet prestanda- och Justeringsguiden](data-factory-copy-activity-performance.md#parallel-copy).
    ![Verktyget kopiera - prestandainställningar](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. På den **sammanfattning** , granska alla inställningar (Aktivitetsegenskaper, inställningar för källa och mål och inställningar) och klicka på **nästa**.
    ![Verktyget kopiera – sammanfattningssida](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Granska informationen på sidan **Sammanfattning** och klicka på **Slutför**. Guiden skapar två länkade tjänster, två datauppsättningar (indata och utdata) och en pipeline i datafabriken (från den plats där du startade guiden Kopiera).
    ![Verktyget kopiera – distributionssida](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Övervaka pipelinen (kopieringsaktiviteten)

1. Klicka på länken `Click here to monitor copy pipeline` på den **distribution** sidan.
2. Du bör se den **övervaka och hantera program** i en separat flik.  ![Övervaka och hantera App](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Ändra den **starta** tid längst upp till `04/19/2017` och **slutet** till `04/27/2017`, och klicka sedan på **tillämpa**.
4. Du bör se fem aktivitetsfönster i den **AKTIVITETSFÖNSTER** lista. Den **WindowStart** gånger ska täcka alla dagar från pipelinens start till pipeline-sluttid.
5. Klicka på **uppdatera** för den **AKTIVITETSFÖNSTER** några gånger tills du ser status för alla aktivitetsfönster är inställd på klart.
6. Nu, kontrollera att utdatafilerna som genereras i Utdatamappen för adfblobconnector behållare. Du bör se följande mappstrukturen i den utgående mappen:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
Detaljerad information om övervaka och hantera datafabriker finns i [övervaka och hantera Data Factory-pipeline](data-factory-monitor-manage-app.md) artikeln.

### <a name="data-factory-entities"></a>Data Factory-entiteter
Gå tillbaka till fliken med Data Factory-startsidan. Observera att det finns två länkade tjänster, två datauppsättningar och en pipeline i data factory nu.

![Datafabrikens startsida med entiteter](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Klicka på **författare och distribuera** att starta Data Factory-redigeraren.

![Data Factory-redigeraren](media/data-factory-azure-blob-connector/data-factory-editor.png)

Du bör se följande Data Factory-entiteter i din datafabrik:

- Två länkade tjänster. En för käll- och det andra för målet. Länkade tjänster finns på samma Azure Storage-konto i den här genomgången.
- Två datauppsättningar. En indatauppsättning och en utdatauppsättning. I den här genomgången ska båda använder samma blobbehållare men referera till olika mappar (indata och utdata).
- En pipeline. Pipelinen innehåller en Kopieringsaktivitet som använder en blobkälla och mottagare blob för att kopiera data från en Azure blobbplats till en annan Azure-blob.

Följande avsnitt innehåller mer information om dessa entiteter.

#### <a name="linked-services"></a>Länkade tjänster
Du bör se två länkade tjänster. En för käll- och det andra för målet. I den här genomgången båda definitioner ser likadana ut förutom namnen. Den **typ** på den länkade tjänsten är inställd på **AzureStorage**. Den viktigaste egenskapen för den länkade tjänstdefinitionen är den **connectionString**, som används av Data Factory för att ansluta till Azure Storage-kontot vid körning. Ignorera egenskapen hubName i definitionen.

##### <a name="source-blob-storage-linked-service"></a>Källa blob storage-länkad tjänst
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

##### <a name="destination-blob-storage-linked-service"></a>Mål blob storage-länkad tjänst

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

Läs mer om Azure Storage-länkade tjänsten [länkade tjänstegenskaper](#linked-service-properties) avsnittet.

#### <a name="datasets"></a>Datauppsättningar
Det finns två datauppsättningar: en indatauppsättning och en utdatauppsättning. Vilken typ av datauppsättningen är inställd på **AzureBlob** för båda.

Den inkommande datauppsättningen pekar på den **inkommande** mappen för den **adfblobconnector** blob-behållare. Den **externa** är inställd på **SANT** för den här datauppsättningen eftersom data inte produceras av pipelinen med en Kopieringsaktivitet som använder den här datauppsättningen som indata.

Datauppsättningen för utdata som pekar på den **utdata** mapp med samma blob-behållaren. Datauppsättningen för utdata använder också den år, månad och dagen i den **SliceStart** systemvariabeln att dynamiskt utvärdera sökvägen till utdatafilen. En lista över funktioner och systemvariabler som stöds av Data Factory finns i [Data Factory-funktioner och systemvariabler](data-factory-functions-variables.md). Den **externa** är inställd på **FALSKT** (standardvärde) eftersom den här datauppsättningen produceras av pipelinen.

Läs mer om egenskaper som stöds av Azure Blob-datauppsättning, [egenskaper för datamängd](#dataset-properties) avsnittet.

##### <a name="input-dataset"></a>Datauppsättningen för indata

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

##### <a name="output-dataset"></a>Datauppsättningen för utdata

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
Pipelinen har bara en aktivitet. Den **typ** för aktiviteten är inställd på **kopiera**. I typegenskaperna för aktiviteten finns två delar, en för källan och en för mottagaren. Källtypen är inställd på **BlobSource** som aktiviteten kopierar data från ett blob storage. Mottagartyp är inställd på **BlobSink** som aktiviteten kopiera data till ett blob storage. Kopieringsaktivitet tar InputDataset-z4y som indata och OutputDataset – z4y som utdata.

Läs mer om egenskaper som stöds av BlobSource och BlobSink [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnittet.

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

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>JSON-exempel för att kopiera data till och från Blob Storage
I följande exempel får exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure Blob Storage och Azure SQL Database. Men du kan kopiera data **direkt** från källor till någon av de mottagare som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON-exempel: Kopiera data från Blob Storage till SQL-databas
I följande exempel visas:

1. En länkad tjänst av typen [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [BlobSource](#copy-activity-properties) och [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

Exempel-kopior time series-data från en Azure-blobb till en Azure SQL tabellen varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Länkad Azure SQL-tjänst:**

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
**Länkad Azure Storage-tjänst:**

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
Azure Data Factory stöder två typer av Azure Storage-länkade tjänster: **AzureStorage** och **AzureStorageSas**. För den första som du anger anslutningssträngen som innehåller kontonyckeln och för det senare anger du Uri för signatur för delad åtkomst (SAS). Se [länkade tjänster](#linked-service-properties) information.

**Indatauppsättning för Azure-Blobb:**

Data hämtas från en ny blob varje timme (frequency: timme, intervall: 1). Mappnamn för sökvägen och filnamnet för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad och dag för starttiden och filnamnet använder timme en del av starttiden. ”external”: ”true” inställningen informerar Data Factory att tabellen är extern till datafabriken och inte kommer från en aktivitet i data factory.

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
**Utdatauppsättning för Azure SQL:**

Exemplet kopierar data till en tabell med namnet ”MyTable” i en Azure SQL database. Skapa tabell i Azure SQL-databasen med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader har lagts till i tabell varje timme.

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
**En Kopieringsaktivitet i en pipeline med Blob-källan och SQL-mottagare:**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **BlobSource** och **mottagare** är **SqlSink**.

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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON-exempel: Kopiera data från Azure SQL till Azure Blob
I följande exempel visas:

1. En länkad tjänst av typen [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) och [BlobSink](#copy-activity-properties).

Exemplet kopierar time series-data från en Azure SQL-tabell till en Azure-blob per timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Länkad Azure SQL-tjänst:**

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
**Länkad Azure Storage-tjänst:**

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
Azure Data Factory stöder två typer av Azure Storage-länkade tjänster: **AzureStorage** och **AzureStorageSas**. För den första som du anger anslutningssträngen som innehåller kontonyckeln och för det senare anger du Uri för signatur för delad åtkomst (SAS). Se [länkade tjänster](#linked-service-properties) information.

**Indatauppsättning för Azure SQL:**

Exemplet förutsätter att du har skapat en tabell ”MyTable” i Azure SQL och den innehåller en kolumn med namnet ”timestampcolumn” för time series-data.

Ange ”external”: ”true” informerar Data Factory-tjänsten att tabellen är extern till datafabriken och inte kommer från en aktivitet i data factory.

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

**Utdatauppsättning för Azure Blob:**

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1). Sökvägen till mappen för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

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

**En Kopieringsaktivitet i en pipeline med SQL-käll- och Blob-mottagare:**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **SqlSource** och **mottagare** är **BlobSink**. SQL-frågan som angetts för den **SqlReaderQuery** egenskapen väljer vilka data under den senaste timmen att kopiera.

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
> Om du vill mappa kolumner från datauppsättningen för källan till kolumner från en datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.

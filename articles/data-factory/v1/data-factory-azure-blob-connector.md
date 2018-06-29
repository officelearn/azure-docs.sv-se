---
title: Kopiera data till/från Azure Blob Storage | Microsoft Docs
description: 'Lär dig att kopiera blob-data i Azure Data Factory. Använda våra exempel: hur du kopierar data till och från Azure Blob Storage och Azure SQL Database.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ec3c87c39cd8b8d1dafa8ad062776171c602135b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047068"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Kopiera data till och från Azure Blob Storage med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-azure-blob-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-blob-storage.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns [Azure Blob Storage-anslutningen i V2](../connector-azure-blob-storage.md).


Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data till och från Azure Blob Storage. Den bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet.

## <a name="overview"></a>Översikt
Du kan kopiera data från alla datalager för stöds källan till Azure Blob Storage eller Azure Blob Storage till alla stöds sink-datalagret. I följande tabell innehåller en lista över datakällor som stöds som källor eller egenskaperna av aktiviteten kopia. Du kan till exempel flytta data **från** en SQL Server-databas eller en Azure SQL database **till** en Azure-blobblagring. Och du kan kopiera data **från** Azure-blobblagring **till** en Azure SQL Data Warehouse eller en Azure DB som Cosmos-samling. 

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure Blob Storage** till följande data lagras:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till Azure Blob Storage**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]
 
> [!IMPORTANT]
> Kopieringsaktiviteten stöder kopiering av data från/till både allmänna Azure Storage-konton och frekvent/lågfrekvent Blob storage. Aktiviteten stöder **läsning från block, Lägg till eller sidblobbar**, men stöder **skrivning till endast blockblobbar**. Azure Premium-lagring stöds inte som en mottagare eftersom den backas upp av sidblobar.
> 
> Kopieringsaktiviteten tar inte bort data från källan när data har kopierats till målet. Om du behöver ta bort källdata efter en lyckad kopia, skapar du en [anpassad aktivitet](data-factory-use-custom-activities.md) att ta bort data och använda aktiviteten i pipelinen. Ett exempel finns i [ta bort blob- eller mappnamn prov på GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity). 

## <a name="get-started"></a>Kom igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data till och från ett Azure Blob Storage med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Den här artikeln innehåller en [genomgången](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) för att skapa en pipeline för att kopiera data från en Azure Blob Storage-plats till en annan plats i Azure Blob Storage. En självstudiekurs om hur du skapar en pipeline för att kopiera data från ett Azure Blob Storage till Azure SQL Database finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall** , **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare:

1. Skapa en **datafabriken**. En datafabrik kan innehålla en eller flera pipelines. 
2. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory. Om du kopierar data från ett Azure blob storage till en Azure SQL database, skapa till exempel två länkade tjänster för att länka ditt Azure storage-konto och Azure SQL database till din data factory. Länkad tjänstegenskaper som är specifika för Azure Blob Storage, se [länkade tjänstegenskaper](#linked-service-properties) avsnitt. 
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. I det tidigare exemplet i det sista steget, skapar du en datauppsättning för att ange blob-behållaren och mappen som innehåller indata. Och du skapar en annan dataset för att ange SQL-tabellen i Azure SQL-databas som innehåller de data som kopieras från blob storage. Egenskaper för datamängd som är specifika för Azure Blob Storage, se [egenskaper för datamängd](#dataset-properties) avsnitt.
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. I exemplet ovan kan använda du BlobSource som en källa och SqlSink som en mottagare för kopieringsaktiviteten. På samma sätt om du kopierar från Azure SQL-databas till Azure Blob Storage, använder du SqlSource och BlobSink i kopieringsaktiviteten. Kopiera Aktivitetsegenskaper som är specifika för Azure Blob Storage, se [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnitt. Mer information om hur du använder ett dataarkiv som en källa eller en mottagare klickar du på länken i föregående avsnitt för datalager.  

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en Azure Blob Storage finns [JSON-exempel](#json-examples-for-copying-data-to-and-from-blob-storage  ) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter till Azure Blob Storage.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Det finns två typer av länkade tjänster som du kan använda för att länka ett Azure Storage till ett Azure data factory. De är: **AzureStorage** länkade tjänsten och **AzureStorageSas** länkade tjänsten. Länkad Azure Storage-tjänsten tillhandahåller data factory med global åtkomst till Azure Storage. Medan det Azure Storage SAS (signatur för delad åtkomst) länkade ger tjänsten data factory med begränsad/Tidsbundna åtkomst till Azure Storage. Det finns några skillnader mellan dessa två länkade tjänster. Välj den länkade tjänst som passar dina behov. Följande avsnitt innehåller mer information om dessa två länkade tjänster.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Egenskaper för datamängd
Om du vill ange en datamängd som representerar inkommande eller utgående data i ett Azure Blob Storage, anger du egenskapen typen på datamängden som ska: **AzureBlob**. Ange den **linkedServiceName** -egenskapen för datauppsättningen till namnet på Azure Storage eller Azure Storage SAS länkade tjänsten.  Typegenskaperna för datauppsättningen ange den **blobbehållaren** och **mappen** i blob storage.

En fullständig lista över JSON avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns på [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av dataset (Azure SQL Azure blob, Azure-tabellen, osv.).

Datafabriken har stöd för följande CLS-kompatibelt .NET baserat typen värden för att ange information i ”struktur” för schemat på Läs datakällor som Azure blob: Int16, Int32, Int64, enskild, Double, Decimal, Byte [], Bool, String, Guid, Datetime, DateTimeOffset Timespan. Data Factory utför konverteringar automatiskt när data flyttas från ett dataarkiv som källa till datakällan sink.

Den **typeProperties** avsnitt är olika för varje typ av dataset och innehåller information om plats, format, etc. data i datalagret. TypeProperties avsnittet för dataset av typen **AzureBlob** datamängden har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökvägen till behållaren och mappen i blob storage. Exempel: myblobcontainer\myblobfolder\ |Ja |
| fileName |Namnet på blob. Filnamnet är valfria och skiftlägeskänsligt.<br/><br/>Om du anger ett filnamn fungerar aktiviteten (inklusive kopia) i specifika Blob.<br/><br/>Om filnamnet har angetts, innehåller kopia alla BLOB i mappsökvägen för inkommande dataset.<br/><br/>När **fileName** har inte angetts för en datamängd för utdata och **preserveHierarchy** har inte angetts i aktiviteten sink är namnet på filen skapas i följande följande format: Data.<Guid>. txt (till exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| partitionedBy |partitionedBy är en valfri egenskap. Du kan använda den för att ange en dynamisk folderPath och ett filnamn för tid series-data. Exempelvis kan folderPath parameteriseras för varje timme av data. Finns det [med partitionedBy egenskap](#using-partitionedBy-property) information och exempel. |Nej |
| Format | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner. |Nej |
| komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbast**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

### <a name="using-partitionedby-property"></a>Med hjälp av partitionedBy egenskap
Som nämnts ovan, du kan ange en dynamisk folderPath och ett filnamn för tid series-data med den **partitionedBy** egenskapen [Data Factory-funktioner och systemvariablerna](data-factory-functions-variables.md).

Mer information om tid serien datauppsättningar, schemaläggning och segment finns [skapa datauppsättningar](data-factory-create-datasets.md) och [schemaläggning och utförande](data-factory-scheduling-and-execution.md) artiklar.

#### <a name="sample-1"></a>Exempel 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

I det här exemplet {segment} ersättas med det angivna värdet för Data Factory systemvariabel SliceStart i formatet (YYYYMMDDHH). SliceStart refererar till starttid av sektorn. FolderPath är olika för varje segment. Till exempel: 2014100103-wikidatagateway/wikisampledataout eller 2014100104-wikidatagateway/wikisampledataout

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

I det här exemplet extraheras år, månad, dag och tidpunkt för SliceStart till olika variabler som används av egenskaperna folderPath och filnamn.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, indata och utdata-datauppsättningar och -principer är tillgängliga för alla typer av aktiviteter. Medan egenskaper som är tillgängliga i den **typeProperties** avsnitt i aktiviteten varierar med varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor. Om du flyttar data från ett Azure Blob Storage måste du ange källtypen i kopieringsaktiviteten till **BlobSource**. På samma sätt om du flyttar data till ett Azure Blob Storage du anger sink i kopieringsaktiviteten till **BlobSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av BlobSource och BlobSink.

**BlobSource** stöder följande egenskaper på den **typeProperties** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiva |Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. |SANT (standardvärdet), FALSKT |Nej |

**BlobSink** stöder följande egenskaper **typeProperties** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| copyBehavior |Definierar beteendet kopia när källan är BlobSource eller filsystem. |<b>PreserveHierarchy</b>: bevarar fil hierarkin i målmappen. Den relativa sökvägen för källfilen till källmappen är identisk med den relativa sökvägen för filen till målmappen.<br/><br/><b>FlattenHierarchy</b>: alla filer från källmappen finns i den första nivån i målmappen. Mål-filer har genereras automatiskt namn. <br/><br/><b>MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om namnet på filen/Blob har angetts, är kopplade filnamnet det angivna namnet; annars skulle vara automatiskt genererade filnamn. |Nej |

**BlobSource** stöder också de här två egenskaperna för bakåtkompatibilitet.

* **treatEmptyAsNull**: Anger om du vill hantera null eller en tom sträng som null-värde.
* **skipHeaderLineCount** – anger hur många rader måste hoppas över. Det gäller endast när inkommande dataset använder TextFormat.

På liknande sätt **BlobSink** stöder följande-egenskapen för bakåtkompatibilitet.

* **blobWriterAddHeader**: Anger om du vill lägga till ett sidhuvud för kolumndefinitionerna vid skrivning till en datamängd för utdata.

DataSet stöder nu följande egenskaper som implementerar samma funktion: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

Följande tabell ger vägledning om hur du använder de nya egenskaperna för datamängd i stället för dessa källor/mottagare blob-egenskaper.

| Kopiera aktivitetsegenskap | Egenskapen DataSet |
|:--- |:--- |
| skipHeaderLineCount på BlobSource |skipLineCount och firstRowAsHeader. Rader hoppas över först och sedan den första raden är skrivskyddat som en rubrik. |
| treatEmptyAsNull på BlobSource |treatEmptyAsNull för inkommande datauppsättningen |
| blobWriterAddHeader på BlobSink |firstRowAsHeader på datamängd för utdata |

Se [anger TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) för detaljerad information om dessa egenskaper.    

### <a name="recursive-and-copybehavior-examples"></a>rekursiva och copyBehavior exempel
Det här avsnittet beskriver resultatet av kopieringen för olika kombinationer av värden rekursiv och copyBehavior.

| rekursiva | copyBehavior | Resultatet |
| --- | --- | --- |
| true |preserveHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med samma struktur som källa<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File5 |
| true |mergeFiles |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 + fil3 + File4 + filen 5 innehållet slås samman till en fil med automatiskt genererade namnet |
| false |preserveHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målmappen Mapp1 har skapats med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/><br/>Subfolder1 med fil3, File4 och File5 har inte plockats. |
| false |flattenHierarchy |För en källmapp Mapp1 med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målmappen Mapp1 har skapats med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil2<br/><br/><br/>Subfolder1 med fil3, File4 och File5 har inte plockats. |
| false |mergeFiles |För en källmapp Mapp1 med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målmappen Mapp1 har skapats med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 innehållet slås samman till en fil med automatiskt genererade namnet. automatiskt genererade namnet på File1<br/><br/>Subfolder1 med fil3, File4 och File5 har inte plockats. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Genomgång: Använd kopiera för att kopiera data till/från Blob Storage
Nu ska vi titta på hur du snabbt kopiera data till och från ett Azure-blobblagring. I den här genomgången lagrar data både källa och mål av typen: Azure Blob Storage. Pipeline i den här genomgången kopierar data från en mapp till en annan mapp i samma blob-behållaren. Den här genomgången är avsiktligt enkelt att visa inställningar eller egenskaper när du använder Blob Storage som källa eller mottagare. 

### <a name="prerequisites"></a>Förutsättningar
1. Skapa en generell **Azure Storage-konto** om du inte redan har en. Du använder blob-lagring som båda **källa** och **mål** datalager i den här genomgången. om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
2. Skapa en blobbbehållare med namnet **adfblobconnector** i lagringskontot. 
4. Skapa en mapp med namnet **inkommande** i den **adfblobconnector** behållare.
5. Skapa en fil med namnet **emp.txt** med följande innehåll och överföra den till den **inkommande** mappen med hjälp av verktyg som [Azure Lagringsutforskaren](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```
### <a name="create-the-data-factory"></a>Skapa datafabriken
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **skapar du en resurs** från det övre vänstra hörnet, klickar du på **Intelligence + analys**, och klicka på **Data Factory**.
3. I den **nya data factory** fönstret:   
    1. Ange **ADFBlobConnectorDF** för den **namn**. Namnet på Azure Data Factory måste vara globalt unikt. Om du får felmeddelandet: `*Data factory name “ADFBlobConnectorDF” is not available`, ändra namnet på data factory (till exempel yournameADFBlobConnectorDF) och försök att skapa igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
    2. Välj din Azure-**prenumeration**.
    3. Resursgrupp, Välj **Använd befintliga** att välja en befintlig resursgrupp (eller) Välj **Skapa nytt** att ange ett namn för en resursgrupp.
    4. Välj **plats** för datafabriken.
    5. Välj kryssrutan **PIN-kod för instrumentpanelen** längst ned på bladet.
    6. Klicka på **Skapa**.
3. När den har skapats visas den **Datafabriken** bladet som visas i följande bild: ![Data factory-startsida](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Guiden Kopiera
1. På startsidan Data Factory klickar du på den **kopiera data** rutan för att starta **guiden Kopiera** i en separat flik.    
    
    > [!NOTE]
    >    Om du ser att webbläsaren har ”auktorisera...”, inaktivera/avmarkera **blockerar cookies från tredje part och platsdata** inställning (eller) se till att den är aktiverad och skapa ett undantag för **login.microsoftonline.com**och försök sedan starta guiden igen.
2. På sidan **Egenskaper**:
    1. Ange **CopyPipeline** för **aktivitet**. Namnet på aktiviteten är namnet på pipeline i din data factory.
    2. Ange en **beskrivning** för aktiviteten (valfritt).
    3. För **aktivitet takt eller schemalägga**, hålla den **körs regelbundet enligt schema** alternativet. Om du vill köra aktiviteten en gång i stället för att köras upprepade gånger enligt ett schema, väljer **kör nu en gång**. Om du väljer **kör nu en gång** alternativet en [enstaka pipeline](data-factory-create-pipelines.md#onetime-pipeline) skapas. 
    4. Behåll inställningarna för **mönster för återkommande**. Den här aktiviteten körs dagligen mellan start- och sluttider som du anger i nästa steg.
    5. Ändra den **startdatum tid** till **2017-04/21**. 
    6. Ändra den **sluttiden datum** till **04/25/2017**. Du kanske vill ange det datum då i stället för att gå igenom kalendern.     
    8. Klicka på **Nästa**.
      ![Kopiera verktyget - egenskapssidan](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png) 
3. På sidan **Källans datalager** klickar du på ikonen **Azure blobblagring**. Du kan använda den här sidan till att ange källans datalager för kopieringsaktiviteten. Du kan använda en länkad tjänst för ett befintligt datalager (eller) ange ett nytt datalager. Om du vill använda en befintlig länkad tjänst, väljer du **från befintliga LÄNKADE tjänster** och välj en länkad tjänst. 
    ![Kopiera verktyget - källdata lagra sidan](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. På sidan **Ange konto för Azure blobblagring**:
   1. Behåll det automatisk genererade namnet för **anslutningsnamn**. Anslutningens namn är namnet på den länkade tjänsten av typen: Azure Storage. 
   2. Kontrollera att alternativet **Från Azure-prenumerationer** har valts för **Metod för kontoval**.
   3. Välj din Azure-prenumeration eller behålla **Markera alla** för **Azure-prenumeration**.   
   4. Välj ett **Azure-lagringskonto** i listan med Azure-lagringskonton som är tillgängliga för den prenumeration som du har valt. Du kan också välja att ange lagringsutrymmet kontoinställningar manuellt genom att välja **ange manuellt** för den **konto urvalsmetod**.
   5. Klicka på **Nästa**. 
      ![Kopiera verktyget - Ange Azure Blob storage-konto](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. På sidan **Välj indatafil eller mapp**:
   1. Dubbelklicka på **adfblobcontainer**.
   2. Välj **inkommande**, och klicka på **Välj**. I den här genomgången väljer du mappen Inkommande. Du kan också markera emp.txt-filen i mappen i stället. 
      ![Kopiera verktyget – Välj den inkommande filen eller mappen](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. På den **Välj inkommande filen eller mappen** sidan:
    1. Bekräfta att den **filen eller mappen** är inställd på **adfblobconnector/indata**. Om filerna finns i undermappar, till exempel 2017-04-01, 02-04-2017 och så vidare, ange adfblobconnector/indata / {year} / {month} / {day} för filen eller mappen. När du trycker på FLIKEN utanför textrutan finns tre listrutorna Välj format för år (åååå), månad (MM) och dag (dd). 
    2. Ange inte **kopiera filen rekursivt**. Välj det här alternativet rekursivt Bläddra igenom mappar för filer som ska kopieras till målet. 
    3. Inte den **binära kopiera** alternativet. Välj det här alternativet för att utföra en binär kopia av källfilen till målet. Markera inte den här genomgången så att du kan se fler alternativ i de följande sidorna. 
    4. Bekräfta att den **Komprimeringstypen** är inställd på **ingen**. Välj ett värde för det här alternativet om källfilerna komprimeras i ett format som stöds. 
    5. Klicka på **Nästa**.
    ![Kopiera verktyget – Välj den inkommande filen eller mappen](./media/data-factory-azure-blob-connector/chose-input-file-folder.png) 
7. På den **formatinställningar** kan du se avgränsare och det schema som upptäcks automatiskt i guiden genom att parsa filen. 
    1. Bekräfta följande alternativ: en. Den **filformatet** är inställd på **textformat**. Du kan se alla format som stöds i den nedrullningsbara listan. Till exempel: JSON, Avro, ORC, parkettgolv.
        b. Den **kolumnen avgränsare** är inställd på `Comma (,)`. Du kan se de andra kolumnen avgränsare stöds av Data Factory i den nedrullningsbara listan. Du kan även ange anpassade avgränsare.
        c. Den **raden avgränsare** är inställd på `Carriage Return + Line feed (\r\n)`. Du kan se de andra raden avgränsare stöds av Data Factory i den nedrullningsbara listan. Du kan även ange anpassade avgränsare.
        d. Den **hoppa över radnummer** är inställd på **0**. Om du vill några rader hoppas över överst i filen, anger du numret här.
        e.  Den **första dataraden innehåller kolumnnamn** har inte angetts. Om källfilerna innehåller kolumnnamnen i den första raden, väljer du det här alternativet.
        f. Den **behandlar tomma kolumnvärde som null** alternativet anges.
    2. Expandera **avancerade inställningar** att se avancerade alternativ som är tillgänglig.
    3. Längst ned på sidan finns på **preview** av data från filen emp.txt.
    4. Klicka på **schemat** fliken längst ned för att se det schema som guiden Kopiera härledas genom att titta på data i källfilen.
    5. Klicka på **Nästa** när du har granskat avgränsarna och förhandsgranskat data.
    ![Kopiera verktyget - inställningar för format](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)  
8. På den **mål datalager sidan**väljer **Azure Blob Storage**, och klicka på **nästa**. Du använder Azure Blob Storage som både käll- och datalager i den här genomgången.    
    ![Kopiera verktyget - Välj målserver datalager](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. På **ange Azure Blob storage-konto** sidan:
   1. Ange **AzureStorageLinkedService** för den **anslutningsnamn** fältet.
   2. Kontrollera att alternativet **Från Azure-prenumerationer** har valts för **Metod för kontoval**.
   3. Välj din Azure-**prenumeration**.  
   4. Välj Azure storage-konto. 
   5. Klicka på **Nästa**.     
10. På den **välja filen eller mappen** sidan: 
    6. Ange **mappsökväg** som **adfblobconnector-/ utdata / {year} / {month} / {day}**. Ange **FLIKEN**.
    7. För den **år**väljer **åååå**.
    8. För den **månad**, bekräfta att den är inställd på **MM**.
    9. För den **dag**, bekräfta att den är inställd på **dd**.
    10. Bekräfta att den **Komprimeringstypen** är inställd på **ingen**.
    11. Bekräfta att den **kopiera beteende** är inställd på **Sammanfoga filer**. Om filen med samma namn redan finns läggs det nya innehållet i samma fil i slutet.
    12. Klicka på **Nästa**.
    ![Kopiera verktyget – Välj filen eller mappen](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. På den **filen formatinställningar** , granskar du inställningarna och klicka på **nästa**. En av de extra alternativen är att lägga till ett sidhuvud till utdatafilen. Om du väljer det alternativet läggs en rubrikrad med namnen på kolumnerna från schemat för källa. Du kan byta namn på standardkolumnvärdena när du visar schemat för datakällan. Du kan till exempel ändra den första kolumnen till förnamn och efternamn andra kolumn. Utdatafilen genereras sedan med ett huvud med dessa namn som kolumnnamn. 
    ![Kopiera verktyget - format-inställningarna för mål](media/data-factory-azure-blob-connector/file-format-destination.png)
12. På den **prestandainställningar** bekräftar som **molnet enheter** och **parallell kopior** är inställda på att **automatisk**, och klicka på Nästa. Mer information om dessa inställningar finns [kopiera aktivitet prestanda och prestandajustering guiden](data-factory-copy-activity-performance.md#parallel-copy).
    ![Kopiera verktyget - prestandainställningar](media/data-factory-azure-blob-connector/copy-performance-settings.png) 
14. På den **sammanfattning** , granska alla inställningar (Aktivitetsegenskaper, inställningar för källa och mål och inställningar) och klicka på **nästa**.
    ![Kopiera verktyget - sammanfattningssida](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Granska informationen på sidan **Sammanfattning** och klicka på **Slutför**. Guiden skapar två länkade tjänster, två datauppsättningar (indata och utdata) och en pipeline i datafabriken (från den plats där du startade guiden Kopiera).
    ![Kopiera verktyget - distribution sida](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Övervaka pipeline (kopiera aktivitet)

1. Klicka på länken `Click here to monitor copy pipeline` på den **distribution** sidan. 
2. Du bör se den **övervaka och hantera program** i en separat flik.  ![Övervaka och hantera appen](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Ändra den **starta** tid längst upp till `04/19/2017` och **end** dags att `04/27/2017`, och klicka sedan på **tillämpa**. 
4. Du bör se fem aktivitet windows i den **aktivitet WINDOWS** lista. Den **WindowStart** gånger bör omfatta alla dagar från pipeline början till slut gånger i pipeline. 
5. Klicka på **uppdatera** knappen för den **aktivitet WINDOWS** några gånger tills du ser statusen för alla aktivitetsfönster är inställd på klart. 
6. Nu, kontrollera att utdatafilerna genereras i Utdatamappen för adfblobconnector behållare. Du bör se följande mappstruktur i Utdatamappen: 
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25    
    ```
Detaljerad information om övervakning och hantering av datafabriker finns [övervaka och hantera Data Factory-pipelinen](data-factory-monitor-manage-app.md) artikel. 
 
### <a name="data-factory-entities"></a>Data Factory entiteter
Växla tillbaka till fliken med Data Factory-startsidan. Observera att det finns två länkade tjänster, två datamängder och en pipeline i din data factory nu. 

![Startsida för data Factory med entiteter](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Klicka på **författare och distribuera** att starta Data Factory-redigeraren. 

![Data Factory-redigeraren](media/data-factory-azure-blob-connector/data-factory-editor.png)

Du bör se följande Data Factory-enheter i din data factory: 

 - Två länkade tjänster. En för käll- och det andra för målet. Länkade tjänster finns i samma Azure Storage-konto i den här genomgången. 
 - Två datauppsättningar. En inkommande datauppsättning och en datamängd för utdata. I den här genomgången båda använder samma blob-behållaren men finns i olika mappar (indata och utdata).
 - En pipeline. Pipelinen innehåller en kopia-aktivitet som använder en blob-källa och en blob-mottagare för att kopiera data från en Azure blob-plats till en annan plats i Azure blob. 

Följande avsnitt innehåller mer information om dessa enheter. 

#### <a name="linked-services"></a>Länkade tjänster
Du bör se två länkade tjänster. En för käll- och det andra för målet. I den här genomgången båda definitionerna ser likadana ut förutom namnen. Den **typen** på den länkade tjänsten är inställd på **AzureStorage**. De viktigaste egenskapen länkade service definition är den **connectionString**, som används av Data Factory för att ansluta till ditt Azure Storage-konto vid körning. Ignorera egenskapen hubName i definitionen. 

##### <a name="source-blob-storage-linked-service"></a>Källan blob länkade lagringstjänsten
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

##### <a name="destination-blob-storage-linked-service"></a>Mål-blob länkade lagringstjänsten

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

Mer information om länkad Azure Storage-tjänst finns [länkade tjänstegenskaper](#linked-service-properties) avsnitt. 

#### <a name="datasets"></a>Datauppsättningar
Det finns två datamängder: en inkommande datauppsättning och en datamängd för utdata. Typ av datamängden har angetts till **AzureBlob** för båda. 

Inkommande dataset pekar på den **inkommande** mappen för den **adfblobconnector** blob-behållare. Den **externa** egenskap är inställd på **SANT** för den här datauppsättningen eftersom data inte tillverkas av rörledningen med de kopieringsaktiviteten som äger denna dataset som indata. 

Datamängd för utdata som pekar på den **utdata** mapp med samma blob-behållaren. Datamängd för utdata använder också året, månaden och dagen i den **SliceStart** systemvariabeln dynamiskt utvärdera sökvägen för utdatafilen. En lista över funktioner och systemvariabler som stöds av Data Factory finns [Data Factory-funktioner och systemvariabler](data-factory-functions-variables.md). Den **externa** egenskap är inställd på **FALSKT** (standardvärdet) eftersom den här datauppsättningen produceras av pipeline. 

Mer information om egenskaper som stöds av Azure-blobbdatauppsättning finns [egenskaper för datamängd](#dataset-properties) avsnitt.

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

##### <a name="output-dataset"></a>Datamängd för utdata

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
Pipelinen har bara en aktivitet. Den **typen** för aktiviteten är inställd på **kopiera**.  I typegenskaperna för aktiviteten finns två delar, ett för källa och en för sink. Källtypen har angetts till **BlobSource** som aktiviteten kopierar data från en blob storage. Sink-typ har angetts till **BlobSink** som aktiviteten kopiera data till en blob-lagring. Kopieringsaktiviteten tar InputDataset z4y som indata och OutputDataset z4y som utdata. 

Mer information om egenskaper som stöds av BlobSource och BlobSink finns [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnitt. 

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
Följande exempel ger exempel JSON definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure Blob Storage och Azure SQL Database. Dock datan kan kopieras **direkt** från alla källor till någon av sänkor anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON-exempel: Kopiera data från Blob Storage till SQL-databas
I följande exempel visas:

1. En länkad tjänst av typen [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en kopia-aktivitet som använder [BlobSource](#copy-activity-properties) och [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

Exempel kopiorna tidsserier data från ett Azure blob till SQL Azure tabellen varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Azure SQL länkade tjänsten:**

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
**Azure Storage länkade tjänsten:**

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
Azure Data Factory stöder två typer av länkad Azure Storage services: **AzureStorage** och **AzureStorageSas**. För den första, anger du den anslutningssträng som innehåller nyckeln konto och du ska ange Uri för delad åtkomst signatur (SAS) för det senare. Se [länkade tjänster](#linked-service-properties) information.  

**Azure Blob inkommande datamängd:**

Data hämtas från en ny blob varje timme (frekvens: timme, intervall: 1). Mappen sökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad och som en dagdel av starttiden filnamn används i timmen som en del av starttiden. ”externa”: ”true” inställningen informerar Data Factory att tabellen är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

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
**Azure SQL utdatauppsättningen:**

Kopior exempeldata i en tabell med namnet ”mytable” som prefix i en Azure SQL database. Skapa tabellen i din Azure SQL-databas med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader läggs till i tabell varje timme.

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
**Kopieringsaktiviteten i en pipeline med Blob källa och mottagare för SQL:**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **BlobSource** och **sink** är inställd på **SqlSink**.

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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON-exempel: Kopieringsdata från Azure SQL Azure-Blob
I följande exempel visas:

1. En länkad tjänst av typen [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktiviteten som använder [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) och [BlobSink](#copy-activity-properties).

Exemplet kopierar time series-data från en Azure SQL-tabell till en Azure blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Azure SQL länkade tjänsten:**

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
**Azure Storage länkade tjänsten:**

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
Azure Data Factory stöder två typer av länkad Azure Storage services: **AzureStorage** och **AzureStorageSas**. För den första, anger du den anslutningssträng som innehåller nyckeln konto och du ska ange Uri för delad åtkomst signatur (SAS) för det senare. Se [länkade tjänster](#linked-service-properties) information.  

**Azure SQL inkommande datamängd:**

Exemplet förutsätter att du har skapat en tabell ”mytable” som prefix i Azure SQL och innehåller en kolumn med namnet ”timestampcolumn” för tid series-data.

Inställningen ”externa”: ”true” informerar Data Factory-tjänsten att tabellen är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

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

**Azure Blob utdatauppsättningen:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Sökvägen till mappen för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delar av starttiden.

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
          "value": { "type": "DateTime",  "date": "SliceStart", "format": "yyyy" } },
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

**Kopieringsaktiviteten i en pipeline med SQL-källa och mottagare för Blob:**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **SqlSource** och **sink** är inställd på **BlobSink**. SQL-frågan som angetts för den **SqlReaderQuery** egenskapen väljer vilka data under den senaste timmen att kopiera.

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
> Om du vill mappa kolumner från källan dataset till kolumner från sink dataset finns [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.

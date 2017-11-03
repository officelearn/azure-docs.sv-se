---
title: "Kopiera data till/från ett filsystem med Azure Data Factory | Microsoft Docs"
description: "Lär dig mer om att kopiera data till och från ett lokalt filsystem med hjälp av Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6e5fa3391d7acf93a3362533d0a65d600913eee3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Kopiera data till och från ett lokalt filsystem med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-onprem-file-system-connector.md)
> * [Version 2 – förhandsversion](../connector-file-system.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [filsystemet connector i V2](../connector-file-system.md).


Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data till och från ett lokalt filsystem. Den bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet.

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från ett lokalt filsystem** till följande data lagras:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till ett lokalt filsystem**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Kopieringsaktiviteten tar inte bort källfilen när den har kopierats till målet. Om du vill ta bort källfilen efter en lyckad kopiering kan du skapa en anpassad aktivitet för att ta bort filen och använda aktiviteten i pipelinen. 

## <a name="enabling-connectivity"></a>Aktivera anslutning
Data Factory stöder anslutning till och från ett lokalt filsystem via **Data Management Gateway**. Data Management Gateway måste du installera i din lokala miljö för tjänsten Data Factory för att ansluta till alla stöds lokala datalagret inklusive filsystemet. Mer information om Data Management Gateway och stegvisa instruktioner om hur du konfigurerar gatewayen finns [flytta data mellan lokala källor och molnet med Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md). Inga andra binära filer måste installeras för att kommunicera till och från ett lokalt filsystem förutom Data Management Gateway. Du måste installera och använda Data Management Gateway, även om filsystemet är i Azure IaaS-VM. Detaljerad information om gateway finns [Data Management Gateway](data-factory-data-management-gateway.md).

Om du vill använda en Linux-filresurs, installera [Samba](https://www.samba.org/) på Linux-servern och installera Data Management Gateway på en Windows server. Det går inte att installera Data Management Gateway på en Linux-server.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data till/från ett filsystem med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare:

1. Skapa en **datafabriken**. En datafabrik kan innehålla en eller flera pipelines. 
2. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory. Om du kopierar data från ett Azure blob storage till ett lokalt filsystem, skapa till exempel två länkade tjänster om du vill länka dina lokala filsystemet och Azure storage-konto till din data factory. Länkad tjänstegenskaper som är specifika för ett lokalt filsystem, se [länkade tjänstegenskaper](#linked-service-properties) avsnitt.
3. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. I det tidigare exemplet i det sista steget, skapar du en datauppsättning för att ange blob-behållaren och mappen som innehåller indata. Och du skapar en annan dataset för att ange mappen och filnamnet (valfritt) i filsystemet. Egenskaper för datamängd som är specifika för lokalt filsystem, se [egenskaper för datamängd](#dataset-properties) avsnitt.
4. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. I exemplet ovan kan använda du BlobSource som en källa och FileSystemSink som en mottagare för kopieringsaktiviteten. På samma sätt om du kopierar från lokala filsystemet till Azure Blob Storage, använder du FileSystemSource och BlobSink i kopieringsaktiviteten. Kopiera Aktivitetsegenskaper som är specifika för lokalt filsystem, se [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnitt. Mer information om hur du använder ett dataarkiv som en källa eller en mottagare klickar du på länken i föregående avsnitt för datalager.

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från ett filsystem finns [JSON-exempel](#json-examples-for-copying-data-to-and-from-file-system) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter för filsystemet:

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Du kan länka ett lokalt filsystem till ett Azure data factory med den **lokala filserver** länkade tjänsten. Följande tabell innehåller beskrivningar för JSON-element som är specifika för lokala filserver länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Se till att egenskapen type har angetts **OnPremisesFileServer**. |Ja |
| värden |Anger rotsökvägen för den mapp som du vill kopiera. Använda escape-tecknet ' \ ' för specialtecken i strängen. Se [exempel länkad tjänst-och dataset](#sample-linked-service-and-dataset-definitions) exempel. |Ja |
| användar-ID |Ange ID för den användare som har åtkomst till servern. |Nej (om du väljer encryptedCredential) |
| lösenord |Ange lösenord för användaren (användar-ID). |Nej (om du väljer encryptedCredential |
| encryptedCredential |Ange de kryptera autentiseringsuppgifter som du kan få genom att köra cmdlet New-AzureRmDataFactoryEncryptValue. |Nej (om du väljer att ange användarnamn och lösenord i klartext) |
| gatewayName |Anger namnet på den gateway som Data Factory ska använda för att ansluta till den lokala filservern. |Ja |


### <a name="sample-linked-service-and-dataset-definitions"></a>Exempel på länkad tjänst och dataset definitioner
| Scenario | Värden i länkade tjänstdefinitionen | folderPath i datauppsättningsdefinitionen |
| --- | --- | --- |
| Lokal mapp på Data Management Gateway-datorn: <br/><br/>Exempel: D:\\ \* eller D:\folder\subfolder\\* |D:\\ \\ (för Data Management Gateway 2.0 och senare) <br/><br/> localhost (för tidigare versioner än Data Management Gateway 2.0) |. \\ \\ eller mappen\\\\undermapp (för Data Management Gateway 2.0 och senare) <br/><br/>D:\\ \\ eller D:\\\\mappen\\\\undermapp (för gateway-versionen nedan 2.0) |
| Delad fjärrmapp: <br/><br/>Exempel: \\ \\minserver\\dela\\ \* eller \\ \\minserver\\dela\\mappen\\undermapp\\* |\\\\\\\\minserver\\\\dela |. \\ \\ eller mappen\\\\undermapp |


### <a name="example-using-username-and-password-in-plain-text"></a>Exempel: Med användarnamn och lösenord i klartext

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Exempel: Använda encryptedcredential

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns [skapa datauppsättningar](data-factory-create-datasets.md). Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av datauppsättningen.

Avsnittet typeProperties är olika för varje typ av datauppsättningen. Den ger information, till exempel plats och formatet på data i datalagret. TypeProperties avsnittet för datauppsättningen av typen **filresursen** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Anger undersökvägen till mappen. Använda escape-tecknet ' \' för specialtecken i strängen. Se [exempel länkad tjänst-och dataset](#sample-linked-service-and-dataset-definitions) exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappen sökvägar baserat på sektorn börja/sluta datum gånger. |Ja |
| fileName |Ange namnet på filen i den **folderPath** om du vill att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen tabellen pekar på alla filer i mappen.<br/><br/>När **fileName** har inte angetts för en datamängd för utdata och **preserveHierarchy** har inte angetts i aktiviteten sink namnet på den genererade filen är i följande format: <br/><br/>`Data.<Guid>.txt`(Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nej |
| fileFilter |Ange ett filter som används för att välja en delmängd av filer i mappsökvägen i stället för alla filer. <br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (valfritt tecken).<br/><br/>Exempel 1: ”fileFilter” ”: * .log”<br/>Exempel 2: ”fileFilter”: 2014 - 1-?. txt ”<br/><br/>Observera att fileFilter gäller för en inkommande filresursen datauppsättning. |Nej |
| partitionedBy |Du kan använda partitionedBy för att ange en dynamisk folderPath/filnamn för time series-data. Ett exempel är folderPath parametriserade varje timme av data. |Nej |
| Format | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner. |Nej |
| Komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbast**. Se [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

> [!NOTE]
> Du kan inte använda filnamn och fileFilter samtidigt.

### <a name="using-partitionedby-property"></a>Med hjälp av partitionedBy egenskap
Som nämnts ovan, du kan ange en dynamisk folderPath och ett filnamn för tid series-data med den **partitionedBy** egenskapen [Data Factory-funktioner och systemvariablerna](data-factory-functions-variables.md).

Mer information om tidsserier datauppsättningar, schemaläggning och segment finns [skapa datauppsättningar](data-factory-create-datasets.md), [schemaläggning och körning](data-factory-scheduling-and-execution.md), och [skapar pipelines](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Exempel 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

I det här exemplet {segment} ersätts med värdet från Data Factory systemvariabeln SliceStart i formatet (YYYYMMDDHH). SliceStart refererar till starttid av sektorn. FolderPath är olika för varje segment. Till exempel: 2014100103-wikidatagateway/wikisampledataout eller 2014100104-wikidatagateway/wikisampledataout.

#### <a name="sample-2"></a>Exempel 2:

```JSON
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

I det här exemplet extraheras år, månad, dag och tid för SliceStart i separata variabler med egenskaperna folderPath och filnamn.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, indata och utdata-datauppsättningar och -principer är tillgängliga för alla typer av aktiviteter. Medan egenskaper som är tillgängliga i den **typeProperties** avsnitt i aktiviteten varierar med varje aktivitetstyp.

För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor. Om du flyttar data från ett lokalt filsystem du ange typen av datakälla i kopieringsaktiviteten till **FileSystemSource**. På samma sätt om du flyttar data till ett lokalt filsystem du anger sink i kopieringsaktiviteten till **FileSystemSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av FileSystemSource och FileSystemSink.

**FileSystemSource** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiva |Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. |SANT, FALSKT (standard) |Nej |

**FileSystemSink** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| copyBehavior |Definierar beteendet kopia när källan är BlobSource eller filsystem. |**PreserveHierarchy:** bevarar fil hierarkin i målmappen. Den relativa sökvägen för källfilen för målmappen är samma som den relativa sökvägen för filen till målmappen.<br/><br/>**FlattenHierarchy:** alla filer från källmappen skapas i den första nivån i målmappen. Mål-filer som skapas med ett namn som genererats automatiskt.<br/><br/>**MergeFiles:** sammanfogar alla filer från källmappen till en fil. Om name-blob filnamn har angetts är kopplade filnamnet det angivna namnet. Annars är ett automatiskt genererat namn. |Nej |

### <a name="recursive-and-copybehavior-examples"></a>rekursiva och copyBehavior exempel
Det här avsnittet beskriver resultatet av kopieringen för olika kombinationer av värden för egenskaperna rekursiv och copyBehavior.

| rekursiva värde | copyBehavior värde | Resultatet |
| --- | --- | --- |
| SANT |preserveHierarchy |För en källmapp Mapp1 med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med samma struktur som källa:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| SANT |flattenHierarchy |För en källmapp Mapp1 med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File5 |
| SANT |mergeFiles |För en källmapp Mapp1 med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 + fil3 + File4 + filen 5 innehållet slås samman till en fil med ett automatiskt genererat namn. |
| FALSKT |preserveHierarchy |För en källmapp Mapp1 med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |
| FALSKT |flattenHierarchy |För en källmapp Mapp1 med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil2<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |
| FALSKT |mergeFiles |För en källmapp Mapp1 med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 innehållet slås samman till en fil med ett automatiskt genererat namn.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatiskt genererade namnet på File1<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimering format som stöds
Se [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikeln för information.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>JSON-exempel för att kopiera data till och från filsystemet
Följande exempel ger exempel JSON definitioner som du kan använda för att skapa en pipeline med hjälp av den [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från ett lokalt filsystem och Azure Blob storage. Du kan dock kopiera data *direkt* från någon av källorna till någon av sänkor som anges i [källor och sänkor stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Kopieringsaktiviteten i Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Exempel: Kopiera data från ett lokalt filsystem till Azure Blob storage
Det här exemplet visas hur du kopierar data från ett lokalt filsystem till Azure Blob storage. Exemplet har följande Data Factory-enheter:

* En länkad tjänst av typen [OnPremisesFileServer](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Indata [dataset](data-factory-create-datasets.md) av typen [filresursen](#dataset-properties).
* Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med Kopieringsaktiviteten som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

I följande exempel kopierar time series-data från ett lokalt filsystem till Azure Blob storage varje timme. JSON-egenskaper som används i exemplen beskrivs i avsnitten efter exemplen.

Som ett första steg bör ställa in Data Management Gateway enligt anvisningarna i [flytta data mellan lokala källor och molnet med Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

**Lokala filserver länkade tjänsten:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Vi rekommenderar att du använder den **encryptedCredential** egenskapen i stället den **userid** och **lösenord** egenskaper. Se [filserver länkade tjänsten](#linked-service-properties) mer information om den här länkade tjänsten.

**Azure Storage länkade tjänsten:**

```JSON
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

**Lokal fil system inkommande datauppsättningen:**

Data hämtas från en ny fil varje timme. Egenskaperna folderPath och fileName bestäms baserat på starttid av sektorn.  

Ange `"external": "true"` informerar Data Factory att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
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

**Azure Blob storage utdatauppsättningen:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Sökvägen till mappen för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timme delar av starttiden.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
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

**Kopieringsaktiviteten i en pipeline med filsystemet källa och mottagare för Blob:**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **FileSystemSource**, och **sink** är inställd på **BlobSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[  
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Exempel: Kopiera data från Azure SQL Database till ett lokalt filsystem
I följande exempel visas:

* En länkad tjänst av typen [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* En länkad tjänst av typen [OnPremisesFileServer](#linked-service-properties).
* En datauppsättning som indata av typen [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* En datamängd för utdata av typen [filresursen](#dataset-properties).
* En pipeline med en kopia-aktivitet som använder [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) och [FileSystemSink](#copy-activity-properties).

Exemplet kopierar time series-data från en Azure SQL-tabell till ett lokalt filsystem varje timme. JSON-egenskaper som används i exemplen beskrivs i avsnitt efter exemplen.

**Azure SQL Database länkade tjänsten:**

```JSON
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

**Lokala filserver länkade tjänsten:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Vi rekommenderar att du använder den **encryptedCredential** egenskapen istället för att använda den **userid** och **lösenord** egenskaper. Se [filsystemet länkade tjänsten](#linked-service-properties) mer information om den här länkade tjänsten.

**Azure SQL inkommande datamängd:**

Exemplet förutsätter att du har skapat en tabell ”mytable” som prefix i Azure SQL och den innehåller en kolumn med namnet ”timestampcolumn” för time series-data.

Ange ``“external”: ”true”`` informerar Data Factory att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

```JSON
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

**Lokal fil system utdatauppsättningen:**

Data kopieras till en ny fil varje timme. FolderPath och filnamn för blob bestäms baserat på starttid av sektorn.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
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

**Kopieringsaktiviteten i en pipeline med SQL-källa och mottagare för filsystemet:**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **SqlSource**, och **sink** är inställd på **FileSystemSink**. SQL-frågan som har angetts för den **SqlReaderQuery** egenskapen väljer vilka data under den senaste timmen att kopiera.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```


Du kan också mappa kolumner från källan dataset till kolumner från sink datamängd i aktivitetsdefinitionen kopia. Mer information finns i [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering
 Mer information om viktiga faktorer som påverkar prestandan hos dataflytten (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera det, finns det [prestandajustering guide och Kopieringsaktivitet prestanda](data-factory-copy-activity-performance.md).

---
title: Kopiera data till/från ett filsystem med Azure Data Factory
description: Lär dig hur du kopierar data till och från ett lokalt filsystem med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d298c83c0c1a0f33f28644e2e467ad5035300221
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265940"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Kopiera data till och från ett lokalt filsystem med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-onprem-file-system-connector.md)
> * [Version 2 (aktuell version)](../connector-file-system.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Filsystemanslutningen i V2](../connector-file-system.md).


I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data till/från ett lokalt filsystem. Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från ett lokalt filsystem** till följande datalager:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till ett lokalt filsystem:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Kopieringsaktivitet tar inte bort källfilen när den har kopierats till målet. Om du behöver ta bort källfilen efter en lyckad kopia skapar du en anpassad aktivitet för att ta bort filen och använda aktiviteten i pipelinen.

## <a name="enabling-connectivity"></a>Aktivera anslutning
Data Factory stöder anslutning till och från ett lokalt filsystem via **Data Management Gateway**. Du måste installera datahanteringsgatewayen i din lokala miljö för datafabrikstjänsten för att kunna ansluta till alla lokala datalager som stöds, inklusive filsystem. Mer information om Data Management Gateway och steg-för-steg-instruktioner om hur du konfigurerar gatewayen finns i [Flytta data mellan lokala källor och molnet med Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md). Förutom Data Management Gateway behöver inga andra binära filer installeras för att kommunicera med och från ett lokalt filsystem. Du måste installera och använda Data Management Gateway även om filsystemet finns i Azure IaaS VM. Detaljerad information om gatewayen finns i [Data Management Gateway](data-factory-data-management-gateway.md).

Om du vill använda en Linux-filresurs installerar du [Samba](https://www.samba.org/) på din Linux-server och installerar Data Management Gateway på en Windows-server. Det går inte att installera Data Management Gateway på en Linux-server.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data till/från ett filsystem med hjälp av olika verktyg/API:er.

Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera pipelines.
2. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken. Om du till exempel kopierar data från en Azure-bloblagring till ett lokalt filsystem skapar du två länkade tjänster för att länka ditt lokala filsystem och Azure-lagringskonto till din datafabrik. För länkade tjänstegenskaper som är specifika för ett lokalt filsystem finns i avsnittet [länkade tjänstegenskaper.](#linked-service-properties)
3. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen. I exemplet som nämns i det sista steget skapar du en datauppsättning för att ange blob-behållaren och mappen som innehåller indata. Du kan också skapa en annan datauppsättning för att ange mapp- och filnamnet (valfritt) i filsystemet. För datauppsättningsegenskaper som är specifika för lokalt filsystem finns i avsnittet [egenskaper för datauppsättningen.](#dataset-properties)
4. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. I det tidigare exemplet använder du BlobSource som källa och FileSystemSink som en mottagare för kopieringsaktiviteten. Om du kopierar från lokalt filsystem till Azure Blob Storage använder du Även FileSystemSource och BlobSink i kopieringsaktiviteten. För kopieringsaktivitetsegenskaper som är specifika för lokalt filsystem finns i [avsnittet kopiera aktivitetsegenskaper.](#copy-activity-properties) Om du vill ha information om hur du använder ett datalager som källa eller en diskbänk klickar du på länken i föregående avsnitt för datalagret.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet.  För exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från ett filsystem finns i avsnittet [JSON-exempel](#json-examples-for-copying-data-to-and-from-file-system) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för filsystemet:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Du kan länka ett lokalt filsystem till en Azure-datafabrik med den **lokala filserverlänkade** tjänsten. I följande tabell finns beskrivningar av JSON-element som är specifika för den lokala filserverlänkade tjänsten.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Kontrollera att typegenskapen är inställd **på OnPremisesFileServer**. |Ja |
| värd |Anger rotsökvägen för den mapp som du vill kopiera. Använd escape-tecknet ' \ ' för specialtecken i strängen. Se [Exempel på länkade tjänst- och datauppsättningsdefinitioner](#sample-linked-service-and-dataset-definitions) för exempel. |Ja |
| userid |Ange ID för den användare som har åtkomst till servern. |Nej (om du väljer encryptedCredential) |
| password |Ange lösenordet för användaren (userid). |Nej (om du väljer encryptedCredential |
| krypteradKnuren |Ange de krypterade autentiseringsuppgifter som du kan få genom att köra cmdleten New-AzDataFactoryEncryptValue. |Nej (om du väljer att ange userid och lösenord i oformaterad text) |
| gatewayName (gatewayName) |Anger namnet på den gateway som Data Factory ska använda för att ansluta till den lokala filservern. |Ja |


### <a name="sample-linked-service-and-dataset-definitions"></a>Exempel på länkade tjänst- och datauppsättningsdefinitioner
| Scenario | Värd i länkad tjänstdefinition | folderPath i definition av datauppsättning |
| --- | --- | --- |
| Lokal mapp på datahanteringsgateway-dator: <br/><br/>Exempel: D:\\ \* eller D:\folder\subfolder\\\* |D:\\ \\ (för Data Management Gateway 2.0 och senare versioner) <br/><br/> localhost (för tidigare versioner än Data Management Gateway 2.0) |. eller\\\\mappundermapp (för Data Management Gateway 2.0 och senare versioner) \\ \\ <br/><br/>D:\\ \\ eller\\\\D: mappundermapp\\\\(för gateway version nedan 2.0) |
| Delad mapp för fjärr: <br/><br/>Exempel: \\ \\undermappen \\ \\dela\\\\\\\\ \* minserver\\eller minservermapp\\\* |\\\\\\\\myserver\\\\dela |. eller mappundermapp\\\\ \\ \\ |

>[!NOTE]
>När du redigerar via användargränssnittet behöver du inte`\\`mata in dubbla omvänt snedstreck ( ) för att fly som du gör via JSON, ange ett enda omvänt snedstreck.

### <a name="example-using-username-and-password-in-plain-text"></a>Exempel: Använda användarnamn och lösenord i oformaterad text

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

### <a name="example-using-encryptedcredential"></a>Exempel: Använda krypteradkredential

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
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Skapa datauppsättningar](data-factory-create-datasets.md). Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper.

Avsnittet typeProperties är olika för varje typ av datauppsättning. Den innehåller information som platsen och formatet för data i datalagret. Avsnittet typeProperties för datauppsättningen av typen **FileShare** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Anger undersökvägen till mappen. Använd escape-tecknet '\' för specialtecken i strängen. Jokerteckenfiltret stöds inte. Se [Exempel på länkade tjänst- och datauppsättningsdefinitioner](#sample-linked-service-and-dataset-definitions) för exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** om du vill ha mappsökvägar baserat på start-/slutdatumtider för segment. |Ja |
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När **filnamn** inte har angetts för en utdatauppsättning och **bevaraHierarchy** inte anges i aktivitetsmottagaren, är namnet på den genererade filen i följande format: <br/><br/>`Data.<Guid>.txt`(Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Inga |
| filFilter |Ange ett filter som ska användas för att välja en delmängd av filer i folderPath i stället för alla filer. <br/><br/>Tillåtna värden `*` är: (flera tecken) och `?` (enstaka tecken).<br/><br/>Exempel 1: "fileFilter": "*.log"<br/>Exempel 2: "fileFilter": 2014-1-?. txt"<br/><br/>Observera att fileFilter är tillämpligt för en indatauppsättning fileshare.Note that fileFilter is applicable for an input FileShare dataset. |Inga |
| partitioneradAv |Du kan använda partitionedBy för att ange en dynamisk folderPath/fileName för tidsseriedata. Ett exempel är folderPath-parameteriserad för varje timme data. |Inga |
| format | Följande formattyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet Format.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata. |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. se [Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

> [!NOTE]
> Du kan inte använda fileName och fileFilter samtidigt.

### <a name="using-partitionedby-property"></a>Använda partitionedBy-egenskapen
Som nämnts i föregående avsnitt kan du ange en dynamisk folderPath och filnamn för tidsseriedata med egenskapen **partitionedBy,** [Data Factory-funktioner och systemvariablerna](data-factory-functions-variables.md).

Mer information om tidsseriedatauppsättningar, schemaläggning och segment finns i [Skapa datauppsättningar,](data-factory-create-datasets.md) [Schemaläggning och körning](data-factory-scheduling-and-execution.md)och Skapa [pipelines](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Prov 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

I det här exemplet ersätts {Slice} med värdet för datafabrikssystemets variabel SliceStart i formatet (YYYYMMDDHH). SegmentStart refererar till starttiden för segmentet. folderPath är olika för varje segment. Till exempel: wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Prov 2:

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

I det här exemplet extraheras år, månad, dag och tid för SegmentStart i separata variabler som egenskaperna mappPath och fileName använder.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatauppsättningar och principer är tillgängliga för alla typer av aktiviteter. Medan egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten varierar med varje aktivitetstyp.

För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns. Om du flyttar data från ett lokalt filsystem anger du källtypen i kopieringsaktiviteten till **FileSystemSource**. Om du flyttar data till ett lokalt filsystem anger du även sink-typen i kopieringsaktiviteten till **FileSystemSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av FileSystemSource och FileSystemSink.

**FileSystemSource** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiv |Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. |Sant, Falskt (standard) |Inga |

**FileSystemSink** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| kopieraBehavior |Definierar kopieringsbeteendet när källan är BlobSource eller FileSystem. |**BevaraHierarchy:** Bevarar filhierarkin i målmappen. Det vill än, källfilens relativa sökväg till källmappen är samma som målfilens relativa sökväg till målmappen.<br/><br/>**FlattenHierarchy:** Alla filer från källmappen skapas i den första nivån i målmappen. Målfilerna skapas med ett autogenererat namn.<br/><br/>**MergeFiles:** Sammanfogar alla filer från källmappen till en fil. Om filnamnet/blobnamnet anges är det kopplade filnamnet det angivna namnet. Annars är det ett automatiskt genererat filnamn. |Inga |

### <a name="recursive-and-copybehavior-examples"></a>rekursiva och copyBehavior exempel
I det här avsnittet beskrivs det resulterande beteendet för kopieringsåtgärden för olika kombinationer av värden för egenskaperna rekursiva och copyBehavior.

| rekursivt värde | kopieraBeteendevärde | Resulterande beteende |
| --- | --- | --- |
| true |bevaraHierarchy |För en källmapp Mapp1 med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmappen Folder1 skapas med samma struktur som källan:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 |
| true |plattaHierarchy |För en källmapp Mapp1 med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File5 |
| true |mergeFiles |För en källmapp Mapp1 med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 innehåll slås samman till en fil med ett automatiskt genererat filnamn. |
| false |bevaraHierarchy |För en källmapp Mapp1 med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmappen Folder1 skapas med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |
| false |plattaHierarchy |För en källmapp Mapp1 med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmappen Folder1 skapas med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File2<br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |
| false |mergeFiles |För en källmapp Mapp1 med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmappen Folder1 skapas med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + File2-innehåll sammanfogas till en fil med ett automatiskt genererat filnamn.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatiskt genererat namn för Arkiv1<br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimeringsformat som stöds
Mer information finns [i fil- och komprimeringsformat i](data-factory-supported-file-and-compression-formats.md) azure data factory-artikeln.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>JSON-exempel för kopiering av data till och från filsystemet
Följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från ett lokalt filsystem och Azure Blob-lagring. Du kan dock kopiera data *direkt* från någon av källorna till någon av de sänkor som anges i [källor och sänkor som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Kopiera aktivitet i Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Exempel: Kopiera data från ett lokalt filsystem till Azure Blob-lagring
Det här exemplet visar hur du kopierar data från ett lokalt filsystem till Azure Blob-lagring. Exemplet har följande datafabrikentiteter:

* En länkad tjänst av typen [PåPremisesFileServer](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En [indatauppsättning](data-factory-create-datasets.md) av typen [FileShare](#dataset-properties).
* En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Följande exempel kopierar tidsseriedata från ett lokalt filsystem till Azure Blob-lagring varje timme. De JSON-egenskaper som används i dessa exempel beskrivs i avsnitten efter proverna.

Som ett första steg ställer du in Data Management Gateway enligt instruktionerna i [Flytta data mellan lokala källor och molnet med Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

**Lokal filserverlänkad tjänst:**

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

Vi rekommenderar att du använder **egenskapen encryptedCredential** i stället **egenskaperna userid** och **password.** Mer information om den länkade tjänsten finns i [den länkade tjänsten Filserver.](#linked-service-properties)

**Azure Storage-länkad tjänst:**

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

**Lokal filsysteminmatningsdatauppsättning:**

Data hämtas från en ny fil varje timme. Egenskaperna folderPath och fileName bestäms baserat på segmentets starttid.

Inställningen `"external": "true"` informerar Data Factory om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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

**Utdatauppsättning för Azure Blob-lagringsutdata:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delarna av år, månad, dag och timme i starttiden.

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

**En kopieringsaktivitet i en pipeline med filsystemkälla och Blob-mottagare:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **FileSystemSource**och **sink-typen** är inställd på **BlobSink**.

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
Följande exempel visar:

* En länkad tjänst av typen [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* En länkad tjänst av typen [PåPremisesFileServer](#linked-service-properties).
* En indatauppsättning av typen [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* En utdatauppsättning av typen [FileShare](#dataset-properties).
* En pipeline med en kopieringsaktivitet som använder [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) och [FileSystemSink](#copy-activity-properties).

Exemplet kopierar tidsseriedata från en Azure SQL-tabell till ett lokalt filsystem varje timme. De JSON-egenskaper som används i dessa exempel beskrivs i avsnitt efter proverna.

**Azure SQL Database-länkad tjänst:**

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

**Lokal filserverlänkad tjänst:**

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

Vi rekommenderar att du använder **egenskapen encryptedCredential** i stället för att använda **egenskaperna userid** och **lösenord.** Mer information om den länkade tjänsten finns i [filsystemlänkade tjänsten.](#linked-service-properties)

**Azure SQL-indatauppsättning:**

Exemplet förutsätter att du har skapat en tabell "MyTable" i Azure SQL, och den innehåller en kolumn som kallas "tidsstämpelpcolumn" för tidsseriedata.

Inställningen ``“external”: ”true”`` informerar Data Factory om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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

**Lokal datauppsättning för filsystemutdata:**

Data kopieras till en ny fil varje timme. MappPath och fileName för blobben bestäms baserat på segmentets starttid.

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

**En kopieringsaktivitet i en pipeline med SQL-käll- och filsystemmottagare:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I pipeline-JSON-definitionen är **källtypen** inställd på **SqlSource**och **sink-typen** är inställd på **FileSystemSink**. SQL-frågan som har angetts för egenskapen **SqlReaderQuery** väljer de data som ska kopieras under den senaste timmen.

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

Du kan också mappa kolumner från källdatauppsättning till kolumner från sink-datauppsättning i definitionen av kopieringsaktivitet. Mer information finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering
 Mer information om viktiga faktorer som påverkar prestanda för datarörelser (kopiera aktivitet) i Azure Data Factory och olika sätt att optimera den finns i [prestanda- och justeringsguiden för kopieringsaktivitet](data-factory-copy-activity-performance.md).

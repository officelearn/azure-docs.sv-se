---
title: Flytta data från en FTP-server med hjälp av Azure Data Factory
description: Lär dig mer om hur du flyttar data från en FTP-server med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eeeb122d240d8c3eae4ebe1650f67cf0e4b9dac6
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992053"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Flytta data från en FTP-server med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-ftp-connector.md)
> * [Version 2 (aktuell version)](../connector-ftp.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [FTP-anslutning i V2](../connector-ftp.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från en FTP-server. Den bygger på artikeln [Dataflyttningsaktiviteter,](data-factory-data-movement-activities.md) som ger en allmän översikt över dataflyttning med kopieringsaktiviteten.

Du kan kopiera data från en FTP-server till alla sink-datalager som stöds. En lista över datalager som stöds som mottagare av kopieringsaktiviteten finns i tabellen [datalager som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data Factory stöder för närvarande endast att flytta data från en FTP-server till andra datalager, men inte att flytta data från andra datalager till en FTP-server. Den stöder både lokala och moln FTP-servrar.

> [!NOTE]
> Kopieringsaktiviteten tar inte bort källfilen när den har kopierats till målet. Om du behöver ta bort källfilen efter en lyckad kopia skapar du en anpassad aktivitet för att ta bort filen och använder aktiviteten på pipelinen.

## <a name="enable-connectivity"></a>Aktivera anslutning
Om du flyttar data från en **lokal FTP-server** till ett molndatalager (till exempel till Azure Blob-lagring) installerar och använder du Data Management Gateway. Data Management Gateway är en klientagent som är installerad på din lokala dator och som gör att molntjänster kan ansluta till en lokal resurs. Mer information finns i [Data Management Gateway](data-factory-data-management-gateway.md). Stegvisa instruktioner om hur du konfigurerar gatewayen och använder den finns i [Flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md). Du använder gatewayen för att ansluta till en FTP-server, även om servern finns på en virtuell azure-infrastruktur som en virtuell tjänst (IaaS).

Det är möjligt att installera gatewayen på samma lokala dator eller IaaS VM som FTP-servern. Vi rekommenderar dock att du installerar gatewayen på en separat dator eller IaaS VM för att undvika resurskonkurrens och för bättre prestanda. När du installerar gatewayen på en separat dator bör datorn kunna komma åt FTP-servern.

## <a name="get-started"></a>Kom igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data från en FTP-källa med hjälp av olika verktyg eller API:er.

Det enklaste sättet att skapa en pipeline är att använda **guiden Kopia av Data Factory**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **PowerShell**, **Azure Resource Manager-mall**, **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna gör du följande för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen.
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg eller API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet. Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett FTP-datalager finns i [JSON-exemplet: Kopiera data från FTP-server till Azure blob-avsnittet](#json-example-copy-data-from-ftp-server-to-azure-blob) i den här artikeln.

> [!NOTE]
> Mer information om fil- och komprimeringsformat som stöds finns [i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för FTP.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
I följande tabell beskrivs JSON-element som är specifika för en FTP-länkad tjänst.

| Egenskap | Beskrivning | Krävs | Default |
| --- | --- | --- | --- |
| typ |Ställ in detta på FtpServer. |Ja |&nbsp; |
| värd |Ange namnet eller IP-adressen för FTP-servern. |Ja |&nbsp; |
| authenticationType |Ange autentiseringstypen. |Ja |Grundläggande, Anonym |
| användarnamn |Ange den användare som har åtkomst till FTP-servern. |Inga |&nbsp; |
| password |Ange lösenordet för användaren (användarnamn). |Inga |&nbsp; |
| krypteradKnuren |Ange den krypterade autentiseringsinformationen för att komma åt FTP-servern. |Inga |&nbsp; |
| gatewayName (gatewayName) |Ange namnet på gatewayen i Data Management Gateway för att ansluta till en lokal FTP-server. |Inga |&nbsp; |
| port |Ange den port som FTP-servern lyssnar på. |Inga |21 |
| enableSsl enableSsl enableSsl enableS |Ange om FTP ska användas via en SSL/TLS-kanal. |Inga |true |
| aktiveraServerCertificateValidation |Ange om du vill aktivera validering av TLS/SSL-certifikat för server när du använder FTP via SSL/TLS-kanal. |Inga |true |

>[!NOTE]
>FTP-anslutningen stöder åtkomst till FTP-server med antingen ingen kryptering eller explicit SSL/TLS-kryptering. Den stöder inte implicit SSL/TLS-kryptering.

### <a name="use-anonymous-authentication"></a>Använd anonym autentisering

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Använd användarnamn och lösenord i oformaterad text för grundläggande autentisering

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Använd port, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Använda krypteradkedreden för autentisering och gateway

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Skapa datauppsättningar](data-factory-create-datasets.md). Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper.

Avsnittet **typeProperties** är olika för varje typ av datauppsättning. Den innehåller information som är specifik för datauppsättningstypen. Avsnittet **typeProperties** för en datauppsättning av typen **FileShare** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Undersök till mappen. Använd escape-tecknet ' \ ' för specialtecken i strängen. Se Exempel på länkade tjänst- och datauppsättningsdefinitioner för exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** om du vill ha mappsökvägar baserat på segmentstart- och slutdatumtider. |Ja |
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När **filnamn** inte har angetts för en utdatauppsättning är namnet på den genererade filen i följande format: <br/><br/>`Data.<Guid>.txt`(Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Inga |
| filFilter |Ange ett filter som ska användas för att välja en delmängd av filer i **folderPath**i stället för alla filer.<br/><br/>Tillåtna värden `*` är: (flera tecken) och `?` (enstaka tecken).<br/><br/>Exempel 1:`"fileFilter": "*.log"`<br/>Exempel 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** gäller för en indatafildeldatauppsättning. Den här egenskapen stöds inte med Hadoop Distributed File System (HDFS). |Inga |
| partitioneradAv |Används för att ange en dynamisk **mappPath** och **fileName** för tidsseriedata. Du kan till exempel ange en **folderPath** som är parameteriserad för varje timme data. |Inga |
| format | Följande formattyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet Format.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Om du vill kopiera filer som de är mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitioner för både indata- och utdatadatauppsättningar. |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är **GZip,** **Deflate**, **BZip2**och **ZipDeflate**, och nivåerna som stöds är **optimala** och **snabbaste**. Mer information finns [i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |
| användaBinaryTransfer |Ange om det binära överföringsläget ska användas. Värdena är sanna för binärt läge (detta är standardvärdet) och falskt för ASCII. Den här egenskapen kan bara användas när den associerade länkade tjänsttypen är av typen: FtpServer. |Inga |

> [!NOTE]
> **fileName** och **fileFilter** kan inte användas samtidigt.

### <a name="use-the-partionedby-property"></a>Använd egenskapen partionedBy
Som nämnts i föregående avsnitt kan du ange en dynamisk **folderPath** och **fileName** för tidsseriedata med egenskapen **partitionedBy.**

Mer information om tidsseriedatauppsättningar, schemaläggning och segment finns i [Skapa datauppsättningar,](data-factory-create-datasets.md) [Schemaläggning och körning](data-factory-scheduling-and-execution.md)och Skapa [pipelines](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Exempel 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
I det här exemplet ersätts {Slice} med värdet för Data Factory systemvariabeln SliceStart, i det angivna formatet (YYYYMMDDHH). Segmentstart refererar till starttiden för segmentet. Mappsökvägen är olika för varje segment. (Till exempel wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104.)

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
I det här exemplet extraheras år, månad, dag och tid för SegmentStart i separata variabler som används av egenskaperna **folderPath** och **fileName.**

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Skapa pipelines](data-factory-create-pipelines.md). Egenskaper som namn, beskrivning, indata- och utdatatabeller och principer är tillgängliga för alla typer av aktiviteter.

Egenskaper som är tillgängliga i avsnittet typEgenskaper i aktiviteten **varierar** däremot med varje aktivitetstyp. För kopieringsaktiviteten varierar typegenskaperna beroende på vilka typer av källor och diskhoar som finns.

När källan är av typen **FileSystemSource**i kopieringsaktivitet är följande egenskap tillgänglig i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiv |Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. |Sant, Falskt (standard) |Inga |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON-exempel: Kopiera data från FTP-server till Azure Blob
Det här exemplet visar hur du kopierar data från en FTP-server till Azure Blob-lagring. Data kan dock kopieras direkt till någon av de diskhoar som anges i [de datalager och format som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats), med hjälp av kopieringsaktiviteten i Data Factory.

I följande exempel finns exempel på JSON-definitioner som du kan använda för att skapa en pipeline med Visual [Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)eller [PowerShell:](data-factory-copy-activity-tutorial-using-powershell.md)

* En länkad tjänst av typen [FtpServer](#linked-service-properties)
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* En [indatauppsättning](data-factory-create-datasets.md) av typen [FileShare](#dataset-properties)
* En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Exemplet kopierar data från en FTP-server till en Azure-blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

### <a name="ftp-linked-service"></a>FTP-länkad tjänst

I det här exemplet används grundläggande autentisering med användarnamn och lösenord i oformaterad text. Du kan också använda något av följande sätt:

* Anonym autentisering
* Grundläggande autentisering med krypterade autentiseringsuppgifter
* FTP över SSL/TLS (FTPS)

Se avsnittet [FTP-länkad tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```
### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
### <a name="ftp-input-dataset"></a>FTP-indatauppsättning

Den här datauppsättningen refererar `mysharedfolder` till `test.csv`FTP-mappen och filen . Pipelinen kopierar filen till målet.

Om du ställer **in externt** för **true** informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Utdatauppsättning för Azure-blobb

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt, baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>En kopieringsaktivitet i en pipeline med filsystemkälla och blob-mottagare

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **FileSystemSource**och **sink-typen** är inställd på **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Information om hur du mappar kolumner från källdatauppsättning till kolumner från sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* Mer information om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Data Factory och olika sätt att optimera den finns i [guiden Kopiera aktivitetsprestanda och justering](data-factory-copy-activity-performance.md).

* Stegvisa instruktioner för hur du skapar en pipeline med en kopieringsaktivitet finns i [självstudiekursen Kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

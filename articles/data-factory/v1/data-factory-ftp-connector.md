---
title: "Flytta data från en FTP-server med hjälp av Azure Data Factory | Microsoft Docs"
description: "Läs mer om hur du flyttar data från en FTP-server med hjälp av Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0452610e56294a19bab302d6df73dff2a70a2eeb
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Flytta data från en FTP-server med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-ftp-connector.md)
> * [Version 2 – förhandsversion](../connector-ftp.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [FTP-anslutningen i V2](../connector-ftp.md).

Den här artikeln förklarar hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från en FTP-server. Den bygger på den [Data movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet.

Du kan kopiera data från en FTP-server till alla stöds sink-datalagret. En lista över datakällor som stöds som sänkor av kopieringsaktiviteten, finns det [stöds datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Data Factory stöder för närvarande endast flytta data från en FTP-server till andra databaser, men inte flytta data från andra data lagras på en FTP-server. Den stöder både lokalt och molntjänster FTP-servrar.

> [!NOTE]
> Kopieringsaktiviteten tar inte bort källfilen när den har kopierats till målet. Om du vill ta bort källfilen efter en lyckad kopiering, skapa en anpassad aktivitet om du vill ta bort filen och använda aktiviteten i pipeline. 

## <a name="enable-connectivity"></a>Aktivera anslutning
Om du flyttar data från en **lokalt** FTP-servern till ett moln data lagra (till exempel till Azure Blob storage), installera och använda Data Management Gateway. Data Management Gateway är en klientagent som är installerad på den lokala datorn och ger molntjänster att ansluta till en lokal resurs. Mer information finns i [Data Management Gateway](data-factory-data-management-gateway.md). För stegvisa instruktioner om hur du konfigurerar gatewayen och använder den, se [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md). Du kan använda gatewayen för att ansluta till en FTP-server, även om servern är på en Azure-infrastruktur som en tjänst (IaaS) virtuell dator (VM).

Det är möjligt att installera gatewayen på samma lokala dator eller IaaS VM som FTP-servern. Vi rekommenderar dock att du installerar en gateway på en separat dator eller IaaS-VM att undvika resurskonflikter och bättre prestanda. När du installerar en gateway på en separat dator kan ska datorn kunna komma åt FTP-servern.

## <a name="get-started"></a>Kom igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data från en FTP-datakälla med hjälp av olika verktyg eller API: er.

Det enklaste sättet att skapa en pipeline är att använda den **Data Factory kopiera guiden**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er, utför följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare:

1. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory.
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen.
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata.

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder verktyg eller API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format. Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från en FTP-datalagret finns i [JSON-exempel: kopiera data från FTP-servern till Azure blob](#json-example-copy-data-from-ftp-server-to-azure-blob) i den här artikeln.

> [!NOTE]
> Mer information om format för stöds och komprimering ska användas finns [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter för FTP.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
I följande tabell beskrivs JSON-element som är specifika för en FTP-länkad tjänst.

| Egenskap | Beskrivning | Krävs | Standard |
| --- | --- | --- | --- |
| typ |Ange FtpServer. |Ja |&nbsp; |
| värden |Ange namn eller IP-adressen till FTP-servern. |Ja |&nbsp; |
| AuthenticationType |Ange vilken autentiseringstyp. |Ja |Grundläggande, anonyma |
| användarnamn |Ange den användare som har åtkomst till FTP-servern. |Nej |&nbsp; |
| lösenord |Ange lösenord för användaren (användarnamn). |Nej |&nbsp; |
| encryptedCredential |Ange krypterade autentiseringsuppgifter för åtkomst till FTP-servern. |Nej |&nbsp; |
| gatewayName |Ange namnet på gatewayen i Data Management Gateway för att ansluta till en lokal FTP-server. |Nej |&nbsp; |
| port |Ange den port som FTP-servern lyssnar. |Nej |21 |
| enableSsl |Ange om du använder FTP över SSL/TLS-kanalen. |Nej |SANT |
| enableServerCertificateValidation |Ange om du vill aktivera server SSL-certifikatsverifiering när du använder FTP över SSL/TLS-kanalen. |Nej |SANT |

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

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Använd användarnamn och lösenord i klartext för grundläggande autentisering

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

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Använda encryptedCredential för autentisering och gateway

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
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns [skapa datauppsättningar](data-factory-create-datasets.md). Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av datauppsättningen.

Den **typeProperties** avsnittet är olika för varje typ av datauppsättningen. Den innehåller information som är specifik för dataset-typen. Den **typeProperties** avsnittet för en dataset av typen **filresursen** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Underordnad sökväg till mappen. Använda escape-tecknet ' \ ' för specialtecken i strängen. Se [exempel länkad tjänst-och dataset](#sample-linked-service-and-dataset-definitions) exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** har mappsökvägar baserat på sektorn start-och sluttider datum. |Ja |
| fileName |Ange namnet på filen i den **folderPath** om du vill att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen tabellen pekar på alla filer i mappen.<br/><br/>När **fileName** har inte angetts för en datamängd för utdata, namnet på den genererade filen är i följande format: <br/><br/>Data. <Guid>.txt (exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nej |
| fileFilter |Ange ett filter som används för att välja en delmängd av filer i den **folderPath**, i stället för alla filer.<br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (valfritt tecken).<br/><br/>Exempel 1:`"fileFilter": "*.log"`<br/>Exempel 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** gäller för en inkommande filresursen datauppsättning. Den här egenskapen stöds inte med Hadoop Distributed File System (HDFS). |Nej |
| partitionedBy |Används för att ange en dynamisk **folderPath** och **fileName** för tid series-data. Du kan till exempel ange en **folderPath** som har fått parametrar för varje timme av data. |Nej |
| Format | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format ](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill kopiera filer som de är mellan filbaserade butiker (binär kopia) kan du hoppa över avsnittet format i både inkommande och utgående dataset-definitioner. |Nej |
| Komprimering | Ange typ och kompression för data. Typer som stöds är **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**, och stöds nivåerna **Optimal** och **snabbaste**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |
| useBinaryTransfer |Ange om du vill använda binär överföringsläget. Värdena är true för en binär (detta är standardvärdet) och false för ASCII. Den här egenskapen kan endast användas när den associera länkade tjänsttypen är av typen: FtpServer. |Nej |

> [!NOTE]
> **Filnamn** och **fileFilter** kan inte användas samtidigt.

### <a name="use-the-partionedby-property"></a>Använd egenskapen partionedBy
Som nämnts ovan kan du ange en dynamisk **folderPath** och **fileName** för tid series-data med den **partitionedBy** egenskapen.

Läs om tid serien datauppsättningar schemaläggning och segment i [skapa datauppsättningar](data-factory-create-datasets.md), [schemaläggning och körning](data-factory-scheduling-and-execution.md), och [skapar pipelines](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Exempel 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
I det här exemplet {segment} ersätts med värdet från Data Factory systemvariabeln SliceStart, i det format som specificerats (YYYYMMDDHH). SliceStart refererar till starttid av sektorn. Sökvägen till mappen är olika för varje segment. (Till exempel 2014100103-wikidatagateway/wikisampledataout eller 2014100104-wikidatagateway/wikisampledataout.)

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
I det här exemplet år, månad, dag och tid för SliceStart extraheras till olika variabler som används av den **folderPath** och **fileName** egenskaper.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter, se [skapar pipelines](data-factory-create-pipelines.md). Egenskaper som namn, beskrivning, ingående och utgående tabeller och principer är tillgängliga för alla typer av aktiviteter.

Egenskaper som är tillgängliga i den **typeProperties** avsnitt av aktivitet, å andra sidan varierar med varje aktivitetstyp. För kopieringsaktiviteten varierar Typegenskaper beroende på vilka typer av datakällor och sänkor.

I en Kopieringsaktivitet när källan är av typen **FileSystemSource**, av följande egenskap är tillgänglig i **typeProperties** avsnitt:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiva |Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. |SANT, FALSKT (standard) |Nej |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON-exempel: kopiera data från FTP-servern till Azure-Blob
Det här exemplet visas hur du kopierar data från en FTP-server till Azure Blob storage. Dock datan kan kopieras direkt till någon av sänkor som anges i den [datalager och format stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats), med hjälp av kopieringsaktiviteten i Data Factory.  

Följande exempel ger exempel JSON definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), eller [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* En länkad tjänst av typen [FtpServer](#linked-service-properties)
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Indata [dataset](data-factory-create-datasets.md) av typen [filresursen](#dataset-properties)
* Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* En [pipeline](data-factory-create-pipelines.md) med kopieringsaktiviteten som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Exemplet kopierar data från en FTP-server till en Azure blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

### <a name="ftp-linked-service"></a>Länkad FTP-tjänsten

Det här exemplet använder grundläggande autentisering med användarnamn och lösenord i klartext. Du kan också använda något av följande sätt:

* Anonym autentisering
* Grundläggande autentisering med krypterade autentiseringsuppgifter
* FTP över SSL/TLS (FTPS)

Finns det [FTP länkade tjänsten](#linked-service-properties) avsnittet för olika typer av autentisering som du kan använda.

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
### <a name="ftp-input-dataset"></a>FTP-inkommande dataset

Den här datamängden refererar till FTP-mappen `mysharedfolder` och `test.csv`. Pipelinen kopierar filen till målet.

Ange **externa** till **SANT** informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

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

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Sökvägen till mappen för blobben utvärderas dynamiskt, baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delar av starttiden.

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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>En kopia aktivitet i en pipeline med filen system käll- och blob sink

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **FileSystemSource**, och **sink** är inställd på **BlobSink**.

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
> Om du vill mappa kolumner från källan dataset till kolumner från sink dataset finns [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* Mer information om viktiga faktorer som påverkan prestanda för flytt av data (kopieringsaktiviteten) i Data Factory och olika sätt att optimera den finns i [kopiera aktivitet prestanda och prestandajustering guiden](data-factory-copy-activity-performance.md).

* Stegvisa instruktioner för att skapa en pipeline med kopiera aktiviteter finns i [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

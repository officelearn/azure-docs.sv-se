---
title: Flytta data från en FTP-server med hjälp av Azure Data Factory | Microsoft Docs
description: Läs mer om hur du flyttar data från en FTP-server med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 20f1d8ca67a38a9dc262845d87b77e2bc3fc9fb7
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810599"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Flytta data från en FTP-server med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-ftp-connector.md)
> * [Version 2 (aktuell version)](../connector-ftp.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [FTP-anslutningsappen i V2](../connector-ftp.md).

Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data från en FTP-server. Den bygger på den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten.

Du kan kopiera data från en FTP-server till alla datalager för mottagare som stöds. En lista över datalager som stöds som mottagare av Kopieringsaktivitet finns i den [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Data Factory stöder för närvarande endast flyttar data från en FTP-server till datalager, men inte flyttar data från andra data lagrar till en FTP-server. Den stöder både lokala och molnbaserade FTP-servrar.

> [!NOTE]
> Kopieringsaktivitet tar inte bort källfilen efter att den har kopierats till målet. Om du vill ta bort källfilen efter en lyckad kopiering kan skapa en anpassad aktivitet om du vill ta bort filen och använda aktiviteten i pipelinen.

## <a name="enable-connectivity"></a>Aktivera anslutning
Om du flyttar data från en **lokala** FTP-servern till ett moln data lagra (till exempel till Azure Blob storage), installera och använda Data Management Gateway. Data Management Gateway är en klientagent som är installerad på den lokala datorn och det gör att molntjänster för att ansluta till en lokal resurs. Mer information finns i [Data Management Gateway](data-factory-data-management-gateway.md). Stegvisa instruktioner om hur du upp gatewayen och använda det, finns i [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md). Du kan använda gatewayen för att ansluta till en FTP-server, även om servern är på en Azure-infrastruktur som en tjänst (IaaS)-dator (VM).

Det går att installera gatewayen på samma lokal dator eller IaaS VM som FTP-servern. Vi rekommenderar dock att du installerar gatewayen på en separat dator eller en IaaS-VM att undvika resurskonflikter och få bättre prestanda. När du installerar gatewayen på en separat dator kan ska datorn kunna få åtkomst till FTP-servern.

## <a name="get-started"></a>Kom igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data från en FTP-källa med hjälp av olika verktyg eller API: er.

Det enklaste sättet att skapa en pipeline är att använda den **Data Factory-Kopieringsguide**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er, utför du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen.
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder verktyg eller API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format. Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett datalager med FTP, finns det [JSON-exempel: Kopiera data från FTP-server till Azure-blob](#json-example-copy-data-from-ftp-server-to-azure-blob) i den här artikeln.

> [!NOTE]
> Mer information om fil- och komprimeringsformat de format som stöds ska användas finns [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för FTP.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
I följande tabell beskrivs JSON-element som är specifika för en FTP-länkad tjänst.

| Egenskap  | Beskrivning | Krävs | Standard |
| --- | --- | --- | --- |
| typ |Ange FtpServer. |Ja |&nbsp; |
| värd |Ange namn eller IP-adressen för FTP-servern. |Ja |&nbsp; |
| authenticationType |Ange vilken autentiseringstyp. |Ja |Basic-, anonym |
| användarnamn |Ange den användare som har åtkomst till FTP-servern. |Nej |&nbsp; |
| lösenord |Ange lösenordet för användaren (användarnamn). |Nej |&nbsp; |
| encryptedCredential |Ange de krypterade autentiseringsuppgifterna för åtkomst till FTP-servern. |Nej |&nbsp; |
| gatewayName |Ange namnet på gatewayen i Data Management Gateway för att ansluta till en lokal FTP-server. |Nej |&nbsp; |
| port |Ange den port som FTP-servern lyssnar. |Nej |21 |
| enableSsl |Ange om du använder FTP över SSL/TLS-kanalen. |Nej |true |
| enableServerCertificateValidation |Ange om du vill aktivera serververifiering SSL-certifikat när du använder FTP över SSL/TLS-kanalen. |Nej |true |

>[!NOTE]
>FTP-anslutningsappen stöder åtkomst till FTP-servern utan kryptering eller explicit SSL/TLS-kryptering; Det stöder inte implicit SSL/TLS-kryptering.

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
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [skapar datauppsättningar](data-factory-create-datasets.md). Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av.

Den **typeProperties** är olika för varje typ av datauppsättning. Den innehåller information som är specifik för typ av datauppsättning. Den **typeProperties** avsnittet för en datauppsättning av typen **filresursen** har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Underordnad sökväg innehavaradministratörens till mappen. Använd escape-tecknet ”\” för specialtecken i strängen. Se exemplet länkade tjänsten och datauppsättningen definitioner för exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappsökvägar baserat på sektorn start och avsluta datum / tid. |Ja |
| fileName |Ange namnet på filen i den **folderPath** om du vill att tabellen för att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen, tabellen pekar på alla filer i mappen.<br/><br/>När **fileName** har inte angetts för en utdatauppsättning, namnet på den genererade filen är i följande format: <br/><br/>Data. <Guid>.txt (exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nej |
| fileFilter |Ange ett filter som används för att välja en delmängd av filerna i den **folderPath**, i stället för alla filer.<br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (tecken).<br/><br/>Exempel 1: `"fileFilter": "*.log"`<br/>Exempel 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** gäller för en indatauppsättning filresursen. Den här egenskapen stöds inte med Hadoop Distributed File System (HDFS). |Nej |
| partitionedBy |Används för att ange en dynamisk **folderPath** och **fileName** för time series-data. Du kan till exempel ange en **folderPath** som är innehåller parametrar för varje timme som data. |Nej |
| Format | Följande formattyper av stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i den [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-Format ](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill kopiera filer som de är mellan filbaserade (binär kopia) kan du hoppa över avsnittet format i både inkommande och utgående datamängd definitioner. |Nej |
| Komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**, och stöds nivåer är **Optimal** och **snabbaste**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |
| useBinaryTransfer |Ange om du vill använda binär överföringsläge. Värdena är sanna för binärt läge (detta är standardvärdet), och false för ASCII. Den här egenskapen kan bara användas när den associera länkade tjänsttypen är av typen: FtpServer. |Nej |

> [!NOTE]
> **fileName** och **fileFilter** kan inte användas samtidigt.

### <a name="use-the-partionedby-property"></a>Använd egenskapen partionedBy
Som vi nämnde i föregående avsnitt, kan du ange en dynamisk **folderPath** och **fileName** för time series-data med den **partitionedBy** egenskapen.

Läs om time series datauppsättningar, schemaläggning och segment i [skapar datauppsättningar](data-factory-create-datasets.md), [schemaläggning och körning](data-factory-scheduling-and-execution.md), och [skapa pipelines](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Exempel 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
I det här exemplet {sektorn} ersätts med värdet för Data Factory systemvariabeln SliceStart, i det format som anges (YYYYMMDDHH). SliceStart refererar starttid för sektorn. Sökvägen till mappen är olika för varje segment. (Till exempel wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104.)

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
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapa pipelines](data-factory-create-pipelines.md). Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principer är tillgängliga för alla typer av aktiviteter.

Egenskaper som är tillgängliga i den **typeProperties** delen av aktiviteten, å andra sidan varierar med varje aktivitetstyp av. Typegenskaperna varierar beroende på vilka typer av källor och mottagare för kopieringsaktiviteten.

I kopieringsaktiviteten när källan är av typen **FileSystemSource**, följande egenskap är tillgänglig i **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiv |Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. |SANT, FALSKT (standard) |Nej |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON-exempel: Kopiera data från FTP-servern till Azure Blob
Detta exempel visar hur du kopierar data från en FTP-server till Azure Blob storage. Dock datan kan kopieras direkt till någon av de mottagare som anges i den [datalager och format som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats), med hjälp av kopieringsaktiviteten i Data Factory.

I följande exempel får exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), eller [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* En länkad tjänst av typen [FtpServer](#linked-service-properties)
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Indata [datauppsättning](data-factory-create-datasets.md) av typen [filresurs](#dataset-properties)
* Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Exemplet kopierar data från en FTP-server till en Azure-blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

### <a name="ftp-linked-service"></a>FTP-länkad tjänst

Det här exemplet använder grundläggande autentisering med användarnamn och lösenord i klartext. Du kan också använda något av följande sätt:

* Anonym autentisering
* Grundläggande autentisering med krypterade autentiseringsuppgifter
* FTP över SSL/TLS (FTPS)

Se den [FTP-länkade tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

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
### <a name="ftp-input-dataset"></a>FTP-datauppsättningen för indata

Denna datauppsättning refererar till FTP-mappen `mysharedfolder` och `test.csv`. Pipelinen kopierar filen till målet.

Ange **externa** till **SANT** informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

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

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1). Sökvägen till mappen för bloben utvärderas dynamiskt, baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>En Kopieringsaktivitet i en pipeline med filen system käll- och blob-mottagaren

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **FileSystemSource**, och **mottagare** är **BlobSink**.

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
> Om du vill mappa kolumner från datauppsättningen för källan till kolumner från en datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* Mer information om viktiga faktorer att påverka prestandan för dataflytt (Kopieringsaktivitet) i Data Factory och olika sätt att optimera den, finns det [kopiera aktivitet prestanda- och Justeringsguiden](data-factory-copy-activity-performance.md).

* Stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet finns i den [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

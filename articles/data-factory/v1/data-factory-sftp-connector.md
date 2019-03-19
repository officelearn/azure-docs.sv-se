---
title: Flytta data från SFTP-server med Azure Data Factory | Microsoft Docs
description: Läs mer om hur du flyttar data från en lokal eller en cloud SFTP-server med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 55a829f2ed32152d14ef637c8079f7af8c487bea
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097652"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Flytta data från en SFTP-server med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-sftp-connector.md)
> * [Version 2 (aktuell version)](../connector-sftp.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [SFTPconnector i V2](../connector-sftp.md).

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data från ett lokalt/i molnet SFTP-server till ett datalager för mottagare som stöds. Den här artikeln bygger vidare på den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som ger en allmän översikt över dataförflyttning Kopieringsaktivitet och en lista över datalager som stöds som källor/mottagare.

Data factory stöder för närvarande endast flyttar data från en SFTP-server till datalager, men inte för att flytta data från andra datalager till en SFTP-server. Den stöder både lokala och molnbaserade SFTP-servrar.

> [!NOTE]
> Kopieringsaktivitet tar inte bort källfilen efter att den har kopierats till målet. Om du vill ta bort källfilen efter en lyckad kopiering kan du skapa en anpassad aktivitet för att ta bort filen och använda aktiviteten i pipelinen.

## <a name="supported-scenarios-and-authentication-types"></a>Scenarier som stöds och typer av autentisering
Du kan använda den här SFTP-anslutningen för att kopiera data från **både i molnet SFTP- och lokala SFTP servrar**. **Grundläggande** och **SshPublicKey** autentiseringstyper som stöds när du ansluter till SFTP-servern.

När du kopierar data från en lokal SFTP-server, måste du installera en Gateway för datahantering i en lokal miljö/Azure VM. Se [Data Management Gateway](data-factory-data-management-gateway.md) mer information om gatewayen. Se [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln stegvisa instruktioner om hur du konfigurerar gatewayen och använda den.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data från en SFTP-källa med hjälp av olika verktyg/API: er.

- Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

- Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. JSON-exempel att kopiera data från SFTP-server till Azure Blob Storage, se [JSON-exempel: Kopiera data från SFTP-server till Azure-blob](#json-example-copy-data-from-sftp-server-to-azure-blob) i den här artikeln.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för FTP-länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ | Type-egenskapen måste anges till `Sftp`. |Ja |
| värd | Namn eller IP-adressen för SFTP-servern. |Ja |
| port |Porten som SFTP-servern lyssnar. Standardvärdet är: 21 |Nej |
| authenticationType |Ange autentiseringstyp. Tillåtna värden: **Basic**, **SshPublicKey**. <br><br> Referera till [använder grundläggande autentisering](#using-basic-authentication) och [med hjälp av SSH autentisering med offentlig nyckel](#using-ssh-public-key-authentication) respektive avsnitt på fler egenskaper och JSON-exempel. |Ja |
| skipHostKeyValidation | Ange om du vill hoppa över nyckelvalidering för värden. | Nej. Standard: FALSKT |
| ska hostKeyFingerprint | Ange fingeravtryck av serverns värdnyckel. | Ja om den `skipHostKeyValidation` är inställd på false.  |
| gatewayName |Namnet på Data Management Gateway att ansluta till en lokal SFTP-server. | Ja om du kopierar data från en lokal SFTP-server. |
| encryptedCredential | Krypterade autentiseringsuppgifter för åtkomst till SFTP-servern. Genereras automatiskt när du anger grundläggande autentisering (användarnamn och lösenord) eller SshPublicKey autentisering (användarnamn + privata Nyckelsökväg eller innehåll) i Kopieringsguiden eller ClickOnce popup-dialogruta. | Nej. Gäller endast när du kopierar data från en lokal SFTP-server. |

### <a name="using-basic-authentication"></a>Använder grundläggande autentisering

Om du vill använda grundläggande autentisering, ange `authenticationType` som `Basic`, och ange följande egenskaper förutom SFTP-anslutningsappen Allmänt som introducerades i det sista avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn | Användare som har åtkomst till SFTP-servern. |Ja |
| lösenord | Lösenordet för användaren (användarnamn). | Ja |

#### <a name="example-basic-authentication"></a>Exempel: Grundläggande autentisering
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exempel: Grundläggande autentisering med krypterade autentiseringsuppgifter

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>Med hjälp av SSH-autentisering för offentlig nyckel

Om du vill använda autentisering med SSH offentlig nyckel, ange `authenticationType` som `SshPublicKey`, och ange följande egenskaper förutom SFTP-anslutningsappen Allmänt som introducerades i det sista avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn |Användare som har åtkomst till SFTP-server |Ja |
| privateKeyPath | Ange absolut sökväg till filen för privat nyckel som gatewayen kan komma åt. | Ange antingen den `privateKeyPath` eller `privateKeyContent`. <br><br> Gäller endast när du kopierar data från en lokal SFTP-server. |
| privateKeyContent | En serialiserade sträng med privat nyckel innehållet. Kopieringsguiden kan läsa filen för privat nyckel och extrahera det privata nyckeln innehållet automatiskt. Om du använder några andra verktyg/SDK kan du använda egenskapen privateKeyPath i stället. | Ange antingen den `privateKeyPath` eller `privateKeyContent`. |
| Lösenfras | Ange pass frasen/lösenord för att dekryptera den privata nyckeln om nyckelfilen skyddas av en godkänd fras. | Ja om filen för privata nyckeln skyddas av en godkänd fras. |

> [!NOTE]
> SFTP-anslutningsappen har stöd för RSA/DSA OpenSSH-nyckel. Kontrollera att innehållet nyckelfil som börjar med ”---BEGIN [RSA/DSA] privata NYCKELN---”. Om den privata nyckelfilen är en fil för ppk-format, Använd Putty verktyget för att konvertera från .ppk till OpenSSH-format.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Exempel: SshPublicKey autentisering med hjälp av privat nyckel filePath

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exempel: SshPublicKey autentisering med hjälp av privat nyckel innehåll

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av.

Den **typeProperties** är olika för varje typ av datauppsättning. Den innehåller information som är specifik för typ av datauppsättning. TypeProperties avsnittet för en datauppsättning av typen **filresursen** datauppsättning har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sub sökvägen till mappen. Använd escape-tecknet ”\” för specialtecken i strängen. Se exemplet länkade tjänsten och datauppsättningen definitioner för exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappen sökvägarna baserat på sektorn start/slut datum / tid. |Ja |
| fileName |Ange namnet på filen i den **folderPath** om du vill att tabellen för att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen, tabellen pekar på alla filer i mappen.<br/><br/>När filnamn har angetts för en utdatauppsättning, namnet på den genererade filen vara i följande det här formatet: <br/><br/>Data. <Guid>.txt (exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| fileFilter |Ange ett filter som används för att välja en delmängd av filer i folderPath i stället för alla filer.<br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (tecken).<br/><br/>Exempel 1: `"fileFilter": "*.log"`<br/>Exempel 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter gäller för en indatauppsättning filresursen. Den här egenskapen stöds inte med HDFS. |Nej |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath filnamn för time series-data. Till exempel folderPath som innehåller parametrar för varje timme som data. |Nej |
| Format | Följande formattyper av stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia). |Nej |
| Komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |
| useBinaryTransfer |Ange om använda binär överföringsläge. Gäller för binärt läge och FALSKT ASCII. Standardvärde: SANT. Den här egenskapen kan bara användas när tillhörande länkade tjänsttypen är av typen: FtpServer. |Nej |

> [!NOTE]
> filnamn och fileFilter kan inte användas samtidigt.

### <a name="using-partionedby-property"></a>Använda partionedBy-egenskapen
Du kan ange en dynamisk folderPath filnamn för time series-data med partitionedBy som nämns i föregående avsnitt. Du kan göra det med Data Factory-makron och systemvariabeln SliceStart, SliceEnd som indikerar att den logiska tidsperioden för en viss datasektor.

Läs om time series datauppsättningar, schemaläggning och segment i [skapar datauppsättningar](data-factory-create-datasets.md), [schemaläggning och utförande](data-factory-scheduling-and-execution.md), och [skapa Pipelines](data-factory-create-pipelines.md) artiklar.

#### <a name="sample-1"></a>Exempel 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
I det här exemplet {sektorn} ersätts med det angivna värdet av Data Factory systemvariabeln SliceStart i formatet (YYYYMMDDHH). SliceStart refererar starttid för sektorn. FolderPath är olika för varje segment. Exempel: wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104.

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
I det här exemplet extraheras år, månad, dag och tid för SliceStart till olika variabler som används av egenskaper för folderPath och filnamn.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principer är tillgängliga för alla typer av aktiviteter.

Medan egenskaperna som är tillgängliga i avsnittet typeProperties aktivitetens varierar med varje aktivitetstyp av. Typegenskaperna varierar beroende på vilka typer av källor och mottagare för kopieringsaktiviteten.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimeringsformat de format som stöds
Se [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikeln på information.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON-exempel: Kopiera data från SFTP-server till Azure-blob
I följande exempel innehåller exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från SFTP källa till Azure Blob Storage. Men du kan kopiera data **direkt** från källor till någon av de mottagare som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.

> [!IMPORTANT]
> Det här exemplet innehåller JSON-kodfragment. Stegvisa instruktioner för att skapa data factory omfattas inte. Se [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln stegvisa instruktioner.

Exemplet har följande data factory-entiteter:

* En länkad tjänst av typen [sftp](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Indata [datauppsättning](data-factory-create-datasets.md) av typen [filresursen](#dataset-properties).
* Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en SFTP-server till en Azure-blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**SFTP-länkad tjänst**

Det här exemplet använder grundläggande autentisering med användarnamn och lösenord i klartext. Du kan också använda något av följande sätt:

* Grundläggande autentisering med krypterade autentiseringsuppgifter
* SSH-autentisering för offentlig nyckel

Se [FTP-länkade tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Länkad Azure Storage-tjänst**

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
**SFTP datauppsättningen för indata**

Denna datauppsättning refererar till SFTP-mapp `mysharedfolder` och `test.csv`. Pipelinen kopierar filen till målet.

Ange ”external”: ”true” informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Utdatauppsättning för Azure-blob**

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1). Sökvägen till mappen för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline med en Kopieringsaktivitet**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **FileSystemSource** och **mottagare** är **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
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
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* [Kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

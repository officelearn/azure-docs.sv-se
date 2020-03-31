---
title: Flytta data från SFTP-server med Azure Data Factory
description: Lär dig mer om hur du flyttar data från en lokal eller en SFTP-server i molnet med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3f78934fb11dd4f9e34bf27d565d471d47f250b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265810"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Flytta data från en SFTP-server med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-sftp-connector.md)
> * [Version 2 (aktuell version)](../connector-sftp.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [SFTPconnector i V2](../connector-sftp.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från en lokal/moln-SFTP-server till ett sink-datalager som stöds. Den här artikeln bygger på artikeln [om dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) som ger en allmän översikt över dataförflyttningar med kopieringsaktivitet och listan över datalager som stöds som källor/sänkor.

Data factory stöder för närvarande endast att flytta data från en SFTP-server till andra datalager, men inte för att flytta data från andra datalager till en SFTP-server. Den stöder både lokala och moln SFTP-servrar.

> [!NOTE]
> Kopieringsaktivitet tar inte bort källfilen när den har kopierats till målet. Om du behöver ta bort källfilen efter en lyckad kopia skapar du en anpassad aktivitet för att ta bort filen och använda aktiviteten i pipelinen.

## <a name="supported-scenarios-and-authentication-types"></a>Scenarier och autentiseringstyper som stöds
Du kan använda den här SFTP-anslutningen för att kopiera data från **både SFTP-servrar i molnet och lokala SFTP-servrar**. **Grundläggande** och **SshPublicKey-autentiseringstyper** stöds vid anslutning till SFTP-servern.

När du kopierar data från en lokal SFTP-server behöver du installera en datahanteringsgateway i den lokala miljön/Azure VM. Mer information om gatewayen finns i [Data Management Gateway.](data-factory-data-management-gateway.md) Se [flytta data mellan lokala platser och molnartikel](data-factory-move-data-between-onprem-and-cloud.md) för steg-för-steg-instruktioner om hur du konfigurerar gatewayen och använder den.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data från en SFTP-källa med hjälp av olika verktyg/API:er.

- Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.

- Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet. JSON-exempel för att kopiera data från SFTP-server till Azure Blob Storage finns i [JSON Exempel: Kopiera data från SFTP-server till Azure](#json-example-copy-data-from-sftp-server-to-azure-blob) blob-sektionen i den här artikeln.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
I följande tabell beskrivs JSON-element som är specifika för FTP-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ | Egenskapen type måste `Sftp`anges till . |Ja |
| värd | Namn eller IP-adress på SFTP-servern. |Ja |
| port |Port som SFTP-servern lyssnar på. Standardvärdet är: 21 |Inga |
| authenticationType |Ange autentiseringstyp. Tillåtna värden: **Basic**, **SshPublicKey**. <br><br> Se [Använda grundläggande autentisering](#using-basic-authentication) och använda autentiseringsavsnitt för [SSH-offentliga nyckel](#using-ssh-public-key-authentication) på fler egenskaper respektive JSON-exempel. |Ja |
| hoppa överHostKeyValidation | Ange om värdnyckelverifiering ska ska skipas. | Nej. Standardvärdet: falskt |
| hostKeyFingerprint | Ange värdnyckelns fingeravtryck. | Ja om `skipHostKeyValidation` den är inställd på false.  |
| gatewayName (gatewayName) |Namnet på datahanteringsgatewayen för att ansluta till en lokal SFTP-server. | Ja om du kopierar data från en lokal SFTP-server. |
| krypteradKnuren | Krypterad autentiseringsuppgifter för åtkomst till SFTP-servern. Genereras automatiskt när du anger grundläggande autentisering (användarnamn + lösenord) eller SshPublicKey-autentisering (användarnamn + sökväg eller innehåll för privata nycklar) i kopieringsguiden eller popup-dialogrutan ClickOnce. | Nej. Använd endast när du kopierar data från en lokal SFTP-server. |

### <a name="using-basic-authentication"></a>Använda grundläggande autentisering

Om du vill `authenticationType` använda `Basic`grundläggande autentisering anger du som och anger följande egenskaper förutom de generiska SFTP-anslutningsapparna som introducerades i det sista avsnittet:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn | Användare som har åtkomst till SFTP-servern. |Ja |
| password | Lösenord för användaren (användarnamn). | Ja |

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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exempel: Grundläggande autentisering med krypterad autentiseringsuppgifter

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

### <a name="using-ssh-public-key-authentication"></a>Använda autentisering av SSH-offentliga nyckel

Om du vill använda SSH-autentisering med offentliga nyckeler anger du `authenticationType` som och anger följande egenskaper förutom de allmänna SFTP-anslutningsapparna som `SshPublicKey`introducerades i det sista avsnittet:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn |Användare som har åtkomst till SFTP-servern |Ja |
| privateKeyPath (privatKeyPath) | Ange absolut sökväg till den privata nyckelfilen som gatewayen kan komma åt. | Ange antingen `privateKeyPath` `privateKeyContent`eller . <br><br> Använd endast när du kopierar data från en lokal SFTP-server. |
| privatKeyContent | En serialiserad sträng med det privata nyckelinnehållet. Kopieringsguiden kan läsa den privata nyckelfilen och extrahera innehållet i den privata nyckeln automatiskt. Om du använder något annat verktyg/SDK använder du egenskapen privateKeyPath i stället. | Ange antingen `privateKeyPath` `privateKeyContent`eller . |
| Lösenfras | Ange lösenfrasen/lösenordet för att dekryptera den privata nyckeln om nyckelfilen skyddas av en lösenfras. | Ja om den privata nyckelfilen skyddas av en lösenfras. |

> [!NOTE]
> SFTP-kontakt stöder RSA/DSA OpenSSH-nyckel. Kontrollera att ditt nyckelfilinnehåll börjar med "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Om den privata nyckelfilen är en ppk-format fil, använd Putty verktyg för att konvertera från PPK till OpenSSH-format.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Exempel: SshPublicKey-autentisering med hjälp av privata nyckelfilerPath

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exempel: SshPublicKey-autentisering med privat nyckelinnehåll

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
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper.

Avsnittet **typeProperties** är olika för varje typ av datauppsättning. Den innehåller information som är specifik för datauppsättningstypen. Avsnittet typeProperties för en datauppsättning av typen **FileShare-datauppsättning** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Undersökväg till mappen. Använd escape-tecknet ' \ ' för specialtecken i strängen. Se Exempel på länkade tjänst- och datauppsättningsdefinitioner för exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** om du vill ha mappsökvägar baserat på start-/slutdatumtider för segment. |Ja |
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När filnamn inte har angetts för en utdatauppsättning, skulle namnet på den genererade filen vara i följande format: <br/><br/>`Data.<Guid>.txt`(Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Inga |
| filFilter |Ange ett filter som ska användas för att välja en delmängd av filer i folderPath i stället för alla filer.<br/><br/>Tillåtna värden `*` är: (flera tecken) och `?` (enstaka tecken).<br/><br/>Exempel 1:`"fileFilter": "*.log"`<br/>Exempel 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter gäller för en indatafildeldatauppsättning. Den här egenskapen stöds inte med HDFS. |Inga |
| partitioneradAv |partitionedBy kan användas för att ange en dynamisk folderPath, filnamn för tidsseriedata. FolderPath har till exempel parameteriserats för varje timme data. |Inga |
| format | Följande formattyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet Format.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata. |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns [i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |
| användaBinaryTransfer |Ange om binärt överföringsläge ska användas. Sant för binärt läge och falskt ASCII. Standardvärde: Sant. Den här egenskapen kan bara användas när associerad länkad tjänsttyp är av typen: FtpServer. |Inga |

> [!NOTE]
> filnamn och filFilter kan inte användas samtidigt.

### <a name="using-partionedby-property"></a>Använda partionedBy egendom
Som nämnts i föregående avsnitt kan du ange en dynamisk folderPath, filnamn för tidsseriedata med partitioneradBy. Du kan göra det med datafabriksmakron och systemvariabeln SegmentStart, SegmentEnd som anger den logiska tidsperioden för ett visst datasegment.

Mer information om datauppsättningar, schemaläggning och segment i tidsserier finns i [Skapa datauppsättningar,](data-factory-create-datasets.md) [schemaläggning & körning](data-factory-scheduling-and-execution.md)och skapa [pipelines-artiklar.](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Prov 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
I det här exemplet ersätts {Slice} med värdet för Data Factory systemvariabeln SliceStart i det format (YYYYMMDDHH) som angetts. Segmentstart refererar till starttiden för segmentet. folderPath är olika för varje segment. Exempel: wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Prov 2:

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
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och principer är tillgängliga för alla typer av aktiviteter.

Medan de egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar med varje aktivitetstyp. För kopiera aktivitet varierar typegenskaperna beroende på vilka typer av källor och diskhoar.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimeringsformat som stöds
Mer information finns [i fil- och komprimeringsformat i](data-factory-supported-file-and-compression-formats.md) azure data factory-artikeln.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON Exempel: Kopiera data från SFTP-server till Azure blob
I följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från SFTP-källa till Azure Blob Storage. Data kan dock kopieras **direkt** från någon av källorna till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.

> [!IMPORTANT]
> Det här exemplet innehåller JSON-utdrag. Den innehåller inte steg-för-steg-instruktioner för att skapa datafabriken. Se [flytta data mellan lokala platser och molnartikel](data-factory-move-data-between-onprem-and-cloud.md) för steg-för-steg-instruktioner.

Exemplet har följande datafabriksenheter:

* En länkad tjänst av typen [sftp](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En [indatauppsättning](data-factory-create-datasets.md) av typen [FileShare](#dataset-properties).
* En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en SFTP-server till en Azure-blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

**SFTP-länkad tjänst**

I det här exemplet används den grundläggande autentiseringen med användarnamn och lösenord i oformaterad text. Du kan också använda något av följande sätt:

* Grundläggande autentisering med krypterade autentiseringsuppgifter
* SSH autentisering av offentliga nyckel

Se [avsnittet FTP-länkad tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

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
**Azure Storage-länkad tjänst**

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
**SFTP-indatauppsättning**

Den här datauppsättningen refererar till `mysharedfolder` SFTP-mappen och filen `test.csv`. Pipelinen kopierar filen till målet.

Inställningen "extern": "true" informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

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

**Pipeline med kopieringsaktivitet**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **FileSystemSource** och **sink-typen** är inställd på **BlobSink**.

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

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.

## <a name="next-steps"></a>Efterföljande moment
Se följande artiklar:

* [Kopiera självstudiekurs](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för aktivitet för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

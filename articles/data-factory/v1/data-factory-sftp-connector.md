---
title: "Flytta data från SFTP-servern med hjälp av Azure Data Factory | Microsoft Docs"
description: "Läs mer om hur du flyttar data från en lokal eller molnet SFTP server med hjälp av Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3485120347923dd291663962d528a1e5996b477f
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Flytta data från en SFTP-server med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-sftp-connector.md)
> * [Version 2 – förhandsversion](../connector-sftp.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [SFTPconnector i V2](../connector-sftp.md).

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data från en på lokalt/i molnet SFTP-server till ett dataarkiv som stöds sink. Den här artikeln bygger på den [data movement aktiviteter](data-factory-data-movement-activities.md) artikel som ger en allmän översikt över dataflyttning kopieringsaktiviteten och listan över datakällor som stöds som källor/sänkor.

Data factory stöder för närvarande endast flytta data från en SFTP-server till andra databaser, men inte för att flytta data från andra datalager till en SFTP-server. Den stöder både lokalt och molntjänster SFTP-servrar.

> [!NOTE]
> Kopieringsaktiviteten tar inte bort källfilen när den har kopierats till målet. Om du vill ta bort källfilen efter en lyckad kopiering kan du skapa en anpassad aktivitet för att ta bort filen och använda aktiviteten i pipelinen. 

## <a name="supported-scenarios-and-authentication-types"></a>Scenarier som stöds och typer av autentisering
Du kan använda den här SFTP-anslutningen för att kopiera data från **både molnet SFTP-servrar och lokala SFTP servrar**. **Grundläggande** och **SshPublicKey** autentiseringstyper som stöds när du ansluter till SFTP-servern.

När du kopierar data från en lokal SFTP-server, måste du installera en Data Management Gateway i lokal miljö/Azure VM. Se [Data Management Gateway](data-factory-data-management-gateway.md) information om gatewayen. Se [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikel stegvisa instruktioner för att konfigurera gatewayen och använder den.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data från en SFTP-datakälla med hjälp av olika verktyg/API: er.

- Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

- Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. JSON-exempel att kopiera data från SFTP-servern till Azure Blob Storage finns [JSON-exempel: kopiera data från SFTP-server till Azure blob](#json-example-copy-data-from-sftp-server-to-azure-blob) i den här artikeln.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för FTP-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- | --- |
| typ | Egenskapen type måste anges till `Sftp`. |Ja |
| värd | Namn eller IP-adress till SFTP-server. |Ja |
| port |Port som avlyssnas SFTP-servern. Standardvärdet är: 21 |Nej |
| AuthenticationType |Ange autentiseringstypen. Tillåtna värden: **grundläggande**, **SshPublicKey**. <br><br> Referera till [använder grundläggande autentisering](#using-basic-authentication) och [med hjälp av SSH autentisering med offentlig nyckel](#using-ssh-public-key-authentication) respektive avsnitt på fler egenskaper och JSON-exempel. |Ja |
| skipHostKeyValidation | Ange om du vill hoppa över värden viktiga validering. | Nej. Standardvärde: false |
| hostKeyFingerprint | Ange fingeravtryck för värdnyckeln. | Ja om den `skipHostKeyValidation` är inställd på false.  |
| gatewayName |Namnet på Data Management Gateway för att ansluta till en lokal SFTP-server. | Ja om du kopierar data från en lokal SFTP-server. |
| encryptedCredential | Krypterade autentiseringsuppgifter för åtkomst till SFTP-servern. Genereras automatiskt när du anger grundläggande autentisering (användarnamn och lösenord) eller SshPublicKey autentisering (användarnamn + privat sökväg eller innehåll) i guiden Kopiera eller ClickOnce popup-dialogruta. | Nej. Gäller bara när du kopierar data från en lokal SFTP-server. |

### <a name="using-basic-authentication"></a>Med grundläggande autentisering

Om du vill använda grundläggande autentisering, `authenticationType` som `Basic`, och ange följande egenskaper förutom SFTP kopplingen generiska som introducerades i det sista avsnittet:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- | --- |
| användarnamn | Användare som har åtkomst till SFTP-servern. |Ja |
| lösenord | Lösenord för användare (användarnamn). | Ja |

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

### <a name="using-ssh-public-key-authentication"></a>Med hjälp av autentisering med SSH offentlig nyckel

Om du vill använda autentisering med SSH offentlig nyckel, `authenticationType` som `SshPublicKey`, och ange följande egenskaper förutom SFTP kopplingen generiska som introducerades i det sista avsnittet:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- | --- |
| användarnamn |Användare som har åtkomst till SFTP-server |Ja |
| privateKeyPath | Ange absolut sökväg till filen för privat nyckel som gateway kan komma åt. | Ange antingen det `privateKeyPath` eller `privateKeyContent`. <br><br> Gäller bara när du kopierar data från en lokal SFTP-server. |
| privateKeyContent | En serialiserad sträng av privat nyckel innehållet. Guiden Kopiera kan läsa filen för privat nyckel och extrahera privata nyckel innehållet automatiskt. Om du använder någon annan verktyget/SDK, använder du egenskapen privateKeyPath. | Ange antingen det `privateKeyPath` eller `privateKeyContent`. |
| passPhrase | Ange pass frasen/lösenord för att dekryptera den privata nyckeln om nyckelfilen skyddas av ett lösenord. | Ja om filen för privata nyckeln skyddas av ett lösenord. |

> [!NOTE]
> SFTP-anslutningen har endast stöd för OpenSSH-nyckel. Kontrollera att din nyckelfilen är i rätt format. Du kan använda Putty för att konvertera från .ppk till OpenSSH-format.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Exempel: SshPublicKey autentisering med hjälp av privat nyckel filsökväg

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
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av datauppsättningen.

Den **typeProperties** avsnittet är olika för varje typ av datauppsättningen. Den innehåller information som är specifik för dataset-typen. TypeProperties avsnittet för en dataset av typen **filresursen** datamängden har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sub sökvägen till mappen. Använda escape-tecknet ' \ ' för specialtecken i strängen. Se [exempel länkad tjänst-och dataset](#sample-linked-service-and-dataset-definitions) exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappen sökvägar baserat på sektorn börja/sluta datum gånger. |Ja |
| fileName |Ange namnet på filen i den **folderPath** om du vill att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen tabellen pekar på alla filer i mappen.<br/><br/>Om filnamnet inte anges för en datamängd för utdata är namnet på den genererade filen i följande det här formatet: <br/><br/>Data.<Guid>.txt (Example: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| fileFilter |Ange ett filter som används för att välja en delmängd av filer i mappsökvägen i stället för alla filer.<br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (valfritt tecken).<br/><br/>Exempel 1:`"fileFilter": "*.log"`<br/>Exempel 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter gäller för en inkommande filresursen datauppsättning. Den här egenskapen stöds inte med HDFS. |Nej |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath filnamn för tid series-data. Till exempel folderPath som innehåller parametrar för varje timme av data. |Nej |
| format | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner. |Nej |
| Komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbast**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |
| useBinaryTransfer |Ange om använder binära överföringsläge. True för en binär och FALSKT ASCII. Standardvärde: True. Den här egenskapen kan endast användas när associerade linked service-typen är av typen: FtpServer. |Nej |

> [!NOTE]
> filnamnet och fileFilter kan inte användas samtidigt.

### <a name="using-partionedby-property"></a>Med hjälp av egenskapen partionedBy
Du kan ange en dynamisk folderPath filnamn för tidsdata serien med partitionedBy som nämns i föregående avsnitt. Du kan göra det med Data Factory-makron och systemvariabeln SliceStart, SliceEnd som indikerar den logiska tidsperioden för en viss datasektorn.

Läs om tid serien datauppsättningar schemaläggning och segment i [skapa datauppsättningar](data-factory-create-datasets.md), [schemaläggning och utförande](data-factory-scheduling-and-execution.md), och [skapar Pipelines](data-factory-create-pipelines.md) artiklar.

#### <a name="sample-1"></a>Exempel 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
I det här exemplet {segment} ersättas med det angivna värdet för Data Factory systemvariabel SliceStart i formatet (YYYYMMDDHH). SliceStart refererar till starttid av sektorn. FolderPath är olika för varje segment. Exempel: 2014100103-wikidatagateway/wikisampledataout eller 2014100104-wikidatagateway/wikisampledataout.

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
I det här exemplet extraheras år, månad, dag och tidpunkt för SliceStart till olika variabler som används av egenskaperna folderPath och filnamn.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, ingående och utgående tabeller och principer är tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitetstyp. För Kopieringsaktivitet kan variera Typegenskaper beroende på vilka typer av datakällor och sänkor.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimering format som stöds
Se [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikeln för information.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON-exempel: Kopiera data från SFTP-server till Azure blob
I följande exempel innehåller exempel JSON definitioner som du kan använda för att skapa en pipeline med [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från SFTP källa till Azure Blob Storage. Dock datan kan kopieras **direkt** från alla källor till någon av sänkor anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.

> [!IMPORTANT]
> Det här exemplet innehåller JSON kodavsnitt. Stegvisa instruktioner för att skapa datafabriken inkluderas inte. Se [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikel stegvisa instruktioner.

Exemplet har följande data factory enheter:

* En länkad tjänst av typen [sftp](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Indata [dataset](data-factory-create-datasets.md) av typen [filresursen](#dataset-properties).
* Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med Kopieringsaktiviteten som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en SFTP-server till en Azure blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**SFTP länkad tjänst**

Det här exemplet använder grundläggande autentisering med användarnamn och lösenord i klartext. Du kan också använda något av följande sätt:

* Grundläggande autentisering med krypterade autentiseringsuppgifter
* Autentisering med SSH offentlig nyckel

Se [FTP länkade tjänsten](#linked-service-properties) avsnittet för olika typer av autentisering som du kan använda.

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
**SFTP inkommande dataset**

Den här datamängden refererar till mappen SFTP `mysharedfolder` och `test.csv`. Pipelinen kopierar filen till målet.

Inställningen ”externa”: ”true” informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

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

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Sökvägen till mappen för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delar av starttiden.

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

**Pipeline med kopieringsaktiviteten**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **FileSystemSource** och **sink** är inställd på **BlobSink**.

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

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* [Kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) stegvisa instruktioner för att skapa en pipeline med en kopia-aktivitet.

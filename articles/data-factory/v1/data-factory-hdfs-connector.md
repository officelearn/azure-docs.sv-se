---
title: "Flytta data från lokala HDFS | Microsoft Docs"
description: "Läs mer om hur du flyttar data från lokala HDFS med hjälp av Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 85a3b1b96effb716b8a33da8ad37309462042a44
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Flytta data från lokala HDFS med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-hdfs-connector.md)
> * [Version 2 – förhandsversion](../connector-hdfs.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [HDFS-anslutningen i V2](../connector-hdfs.md).

Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data från en lokal HDFS. Den bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet.

Du kan kopiera data från HDFS till alla stöds sink-datalagret. En lista över datakällor som stöds som sänkor av kopieringsaktiviteten, finns det [stöds datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Data factory stöder för närvarande endast flytta data från en lokal HDFS till andra databaser, men inte för att flytta data från andra datalager till en lokal HDFS.

> [!NOTE]
> Kopieringsaktiviteten tar inte bort källfilen när den har kopierats till målet. Om du vill ta bort källfilen efter en lyckad kopiering kan du skapa en anpassad aktivitet för att ta bort filen och använda aktiviteten i pipelinen. 

## <a name="enabling-connectivity"></a>Aktivera anslutning
Data Factory-tjänsten stöder anslutning till lokala HDFS med Data Management Gateway. Se [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikeln innehåller information om Data Management Gateway och stegvisa instruktioner om hur du konfigurerar en gateway. Använd en gateway för att ansluta till HDFS även om den finns i en Azure IaaS-VM.

> [!NOTE]
> Se till att Data Management Gateway har åtkomst till **alla** [nod namnservern]: [name nod port] och [nod dataservrar]: [data nod port] i Hadoop-kluster. Standard [namn på noden port] är 50070 och standardvärdet [data nod port] är 50075.

Medan du kan installera gatewayen på samma lokala dator eller virtuell Azure-dator som HDFS, rekommenderar vi att du installerar en gateway på en separat dator/Azure IaaS-VM. Med gatewayen på en separat dator minskar resurskonflikter och förbättrar prestandan. När du installerar en gateway på en separat dator ska datorn kunna få åtkomst till datorn med HDFS.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data från en källa för HDFS med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare:

1. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory.
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen.
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata.

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett HDFS-datalager finns [JSON-exempel: kopiera data från lokala HDFS till Azure Blob](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter till HDFS:

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
En länkad tjänst länkar ett datalager till en data factory. Du skapar en länkad tjänst av typen **Hdfs** att länka en lokala HDFS till din data factory. Följande tabell innehåller beskrivning för JSON-element som är specifika för HDFS länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **Hdfs** |Ja |
| URL |URL till HDFS |Ja |
| AuthenticationType |Anonym, eller Windows. <br><br> Att använda **Kerberos-autentisering** HDFS-anslutningen finns i [i det här avsnittet](#use-kerberos-authentication-for-hdfs-connector) därefter konfigurera din lokala miljö. |Ja |
| Användarnamn |Användarnamn för Windows-autentisering. Kerberos-autentisering, ange `<username>@<domain>.com`. |Ja (för Windows-autentisering) |
| lösenord |Lösenordet för Windows-autentisering. |Ja (för Windows-autentisering) |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till HDFS. |Ja |
| encryptedCredential |[Nya AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) utdata för autentiseringsuppgifterna för åtkomst. |Nej |

### <a name="using-anonymous-authentication"></a>Använder anonym autentisering

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Med Windows-autentisering

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av dataset (Azure SQL Azure blob, Azure-tabellen, osv.).

Den **typeProperties** avsnitt är olika för varje typ av dataset och innehåller information om placeringen av data i datalagret. TypeProperties avsnittet för dataset av typen **filresursen** (som omfattar HDFS dataset) har följande egenskaper

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökvägen till mappen. Exempel:`myfolder`<br/><br/>Använda escape-tecknet ' \ ' för specialtecken i strängen. Till exempel: Ange mapp för folder\subfolder,\\\\undermapp och ange d: för d:\samplefolder,\\\\Exempelmapp.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappen sökvägar baserat på sektorn börja/sluta datum gånger. |Ja |
| fileName |Ange namnet på filen i den **folderPath** om du vill att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen tabellen pekar på alla filer i mappen.<br/><br/>Om filnamnet inte anges för en datamängd för utdata är namnet på den genererade filen i följande det här formatet: <br/><br/>Data. <Guid>.txt (till exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath filnamn för tid series-data. Exempel: folderPath parametriserade varje timme av data. |Nej |
| Format | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner. |Nej |
| Komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbast**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

> [!NOTE]
> filnamnet och fileFilter kan inte användas samtidigt.

### <a name="using-partionedby-property"></a>Med hjälp av egenskapen partionedBy
Som nämnts ovan, du kan ange en dynamisk folderPath och ett filnamn för tid series-data med den **partitionedBy** egenskapen [Data Factory-funktioner och systemvariablerna](data-factory-functions-variables.md).

Läs mer om tid serien datauppsättningar, schemaläggning och segment i [skapa datauppsättningar](data-factory-create-datasets.md), [schemaläggning och utförande](data-factory-scheduling-and-execution.md), och [skapar Pipelines](data-factory-create-pipelines.md) artiklar.

#### <a name="sample-1"></a>Exempel 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
I det här exemplet {segment} ersättas med det angivna värdet för Data Factory systemvariabel SliceStart i formatet (YYYYMMDDHH). SliceStart refererar till starttid av sektorn. FolderPath är olika för varje segment. Till exempel: 2014100103-wikidatagateway/wikisampledataout eller 2014100104-wikidatagateway/wikisampledataout.

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
I det här exemplet extraheras år, månad, dag och tidpunkt för SliceStart till olika variabler som används av egenskaperna folderPath och filnamn.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, ingående och utgående tabeller och principer är tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

För Kopieringsaktiviteten när datakällan är av typen **FileSystemSource** följande egenskaper finns i avsnittet typeProperties:

**FileSystemSource** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiva |Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. |SANT, FALSKT (standard) |Nej |

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimering format som stöds
Se [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikeln för information.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>JSON-exempel: kopiera data från lokala HDFS till Azure-Blob
Det här exemplet visas hur du kopierar data från en lokal HDFS till Azure Blob Storage. Dock datan kan kopieras **direkt** till någon av sänkor anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.  

Exemplet innehåller JSON definitioner för följande Data Factory-enheter. Du kan använda dessa definitioner för att skapa en pipeline för att kopiera data från HDFS till Azure Blob Storage med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. En länkad tjänst av typen [OnPremisesHdfs](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [filresursen](#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med Kopieringsaktiviteten som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en lokal HDFS till en Azure blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

Som ett första steg bör du ställa in data management gateway. Anvisningarna i den [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikel.

**HDFS länkade tjänsten:** det här exemplet använder Windows-autentisering. Se [HDFS länkade tjänsten](#linked-service-properties) avsnittet för olika typer av autentisering som du kan använda.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure Storage länkade tjänsten:**

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

**HDFS indatauppsättning:** den här datamängden refererar till mappen HDFS DataTransfer/UnitTest /. Pipelinen kopierar alla filer i den här mappen till målet.

Inställningen ”externa”: ”true” informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Azure Blob utdatauppsättningen:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Sökvägen till mappen för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delar av starttiden.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kopieringsaktiviteten i en pipeline med filsystemet källa och mottagare för Blob:**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda dessa indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **FileSystemSource** och **sink** är inställd på **BlobSink**. SQL-frågan som angetts för den **frågan** egenskapen väljer vilka data under den senaste timmen att kopiera.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Använda Kerberos-autentisering för HDFS-anslutningen
Det finns två alternativ för att konfigurera den lokala miljön för att använda Kerberos-autentisering i HDFS-koppling. Du kan välja den bättre passar ditt ärende.
* Alternativ 1: [anslutning till gateway-datorn i Kerberos-sfär](#kerberos-join-realm)
* Alternativ 2: [aktivera ömsesidigt förtroende mellan Windows-domän och Kerberos-sfär](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Alternativ 1: Ansluta till gateway-datorn i Kerberos-sfär

#### <a name="requirement"></a>Krav:

* Gateway-datorn måste ansluta till Kerberos-sfären och kan inte ansluta till en Windows-domän.

#### <a name="how-to-configure"></a>Så här konfigurerar du:

**På gateway-datorn:**

1.  Kör den **Ksetup** verktyg för att konfigurera Kerberos KDC-servern och sfären.

    Datorn måste konfigureras som en medlem i en arbetsgrupp, eftersom en Kerberos-sfär skiljer sig från en Windows-domän. Detta kan uppnås genom att Kerberos-sfären och lägga till en KDC-server på följande sätt. Ersätt *REALM.COM* med din egen respektive sfär efter behov.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Starta om** datorn efter körning kommandona 2.

2.  Verifiera konfigurationen med **Ksetup** kommando. Utdata ska vara som:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**I Azure Data Factory:**

* Konfigurera anslutningen HDFS med **Windows-autentisering** tillsammans med dina Kerberos-huvudnamn och ett lösenord för att ansluta till HDFS-datakälla. Kontrollera [HDFS länkade tjänstegenskaper](#linked-service-properties) avsnittet konfigurationsinformation.

### <a name="kerberos-mutual-trust"></a>Alternativ 2: Aktivera ömsesidigt förtroende mellan Windows-domän och Kerberos-sfär

#### <a name="requirement"></a>Krav:
*   Gateway-datorn måste ansluta till en Windows-domän.
*   Du behöver behörighet att uppdatera inställningarna för domänkontrollanten.

#### <a name="how-to-configure"></a>Så här konfigurerar du:

> [!NOTE]
> Ersätt REALM.COM och AD.COM i följande självstudier med respektive sfär och domänkontrollern efter behov.

**På KDC-server:**

1.  Redigera KDC-konfigurationen i **krb5.conf** filen så att KDC litar på Windows-domän som refererar till följande konfigurationsmall. Som standard konfigurationen finns på **/etc/krb5.conf**.

            [logging]
             default = FILE:/var/log/krb5libs.log
             kdc = FILE:/var/log/krb5kdc.log
             admin_server = FILE:/var/log/kadmind.log

            [libdefaults]
             default_realm = REALM.COM
             dns_lookup_realm = false
             dns_lookup_kdc = false
             ticket_lifetime = 24h
             renew_lifetime = 7d
             forwardable = true

            [realms]
             REALM.COM = {
              kdc = node.REALM.COM
              admin_server = node.REALM.COM
             }
            AD.COM = {
             kdc = windc.ad.com
             admin_server = windc.ad.com
            }

            [domain_realm]
             .REALM.COM = REALM.COM
             REALM.COM = REALM.COM
             .ad.com = AD.COM
             ad.com = AD.COM

            [capaths]
             AD.COM = {
              REALM.COM = .
             }

  **Starta om** KDC-tjänsten efter konfigurationen.

2.  Förbereda en huvudansvarig med namnet  **krbtgt/REALM.COM@AD.COM**  i KDC-server med följande kommando:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  I **hadoop.security.auth_to_local** HDFS tjänstkonfiguration lägger du till `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**På domänkontrollanten:**

1.  Kör följande **Ksetup** kommandon för att lägga till en sfär post:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Upprätta förtroende från Windows-domän till Kerberos-sfär. [lösenord] är lösenordet för objektet  **krbtgt/REALM.COM@AD.COM** .

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Välj krypteringsalgoritm som används i Kerberos.

    1. Gå till Serverhanteraren > hantering av Grupprincip > domän > grupprincipobjekt > standard eller aktiva domänprincip och redigera.

    2. I den **Redigeraren för Grupprinciphantering** popup-fönster, gå till Datorkonfiguration > Principer > Windows-inställningar > säkerhetsinställningar > lokala principer > säkerhetsalternativ, och konfigurera **Nätverkssäkerhet: Konfigurera krypteringstyper tillåts för Kerberos**.

    3. Välj den krypteringsalgoritm som du vill använda när du ansluter till KDC. Ofta, kan du bara välja alla alternativ.

        ![Config krypteringstyper för Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Använd **Ksetup** kommando för att ange krypteringsalgoritmen som ska användas på den specifika SFÄREN.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Skapa mappning mellan domänkontot och Kerberos-huvudnamn för att kunna använda Kerberos-huvudnamn i Windows-domän.

    1. Starta administrativa verktyg > **Active Directory-användare och datorer**.

    2. Konfigurera avancerade funktioner genom att klicka på **visa** > **avancerade funktioner**.

    3. Leta upp det konto som du vill skapa mappningar och högerklicka om du vill visa **namnmappningar** > klickar du på **Kerberos-namn** fliken.

    4. Lägga till en huvudansvarig från domänen.

        ![Mappa säkerhetsidentitet](media/data-factory-hdfs-connector/map-security-identity.png)

**På gateway-datorn:**

* Kör följande **Ksetup** kommandon för att lägga till en domän-post.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**I Azure Data Factory:**

* Konfigurera anslutningen HDFS med **Windows-autentisering** tillsammans med din domänkonto eller Kerberos-huvudnamn att ansluta till HDFS-datakälla. Kontrollera [HDFS länkade tjänstegenskaper](#linked-service-properties) avsnittet konfigurationsinformation.

> [!NOTE]
> Om du vill mappa kolumner från källan dataset till kolumner från sink dataset finns [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.

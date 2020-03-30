---
title: Flytta data från lokala HDFS
description: Läs mer om hur du flyttar data från lokala HDFS med Hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7652ab72fb972230d98913c2d7e2601737982532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924351"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Flytta data från lokala HDFS med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-hdfs-connector.md)
> * [Version 2 (aktuell version)](../connector-hdfs.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [HDFS-anslutning i V2](../connector-hdfs.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från en lokal HDFS. Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

Du kan kopiera data från HDFS till alla sink-datalager som stöds. En lista över datalager som stöds som mottagare av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Datafabriken stöder för närvarande endast att flytta data från en lokal HDFS till andra datalager, men inte för att flytta data från andra datalager till en lokal HDFS.

> [!NOTE]
> Kopieringsaktivitet tar inte bort källfilen när den har kopierats till målet. Om du behöver ta bort källfilen efter en lyckad kopia skapar du en anpassad aktivitet för att ta bort filen och använda aktiviteten i pipelinen. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Aktivera anslutning
Data Factory-tjänsten stöder anslutning till lokala HDFS med hjälp av Data Management Gateway. Se [flytta data mellan lokala platser och molnartikel](data-factory-move-data-between-onprem-and-cloud.md) om du vill veta mer om Data Management Gateway och steg-för-steg-instruktioner om hur du konfigurerar gatewayen. Använd gatewayen för att ansluta till HDFS även om den finns i en Virtuell Azure IaaS-dator.

> [!NOTE]
> Kontrollera att datahanteringsgatewayen kan komma åt **alla** [namnnodsservern]:[namnnodport] och [datanodservrar]:[datanodport] i Hadoop-klustret. Standard [namnnodport] är 50070 och standard [datanodport] är 50075.

Även om du kan installera gateway på samma lokala dator eller Den virtuella Azure-datorn som HDFS, rekommenderar vi att du installerar gatewayen på en separat dator/Azure IaaS VM. Att ha gateway på en separat dator minskar resurskonkurrensen och förbättrar prestanda. När du installerar gatewayen på en separat dator bör maskinen kunna komma åt maskinen med HDFS.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data från en HDFS-källa med hjälp av olika verktyg/API:er.

Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure portal**, **Visual Studio**, Azure **PowerShell**, **Azure Resource Manager-mall**, **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen.
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet.  Ett exempel på ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett HDFS-datalager finns i [JSON-exempel: Kopiera data från lokala HDFS till Azure Blob-delen](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för HDFS:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
En länkad tjänst länkar ett datalager till en datafabrik. Du skapar en länkad tjänst av typen **Hdfs** för att länka en lokal HDFS till din datafabrik. Följande tabell innehåller en beskrivning av JSON-element som är specifika för HDFS-länkade tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste ställas in på: **HDfs** |Ja |
| url |URL till HDFS |Ja |
| authenticationType |Anonym, eller Windows. <br><br> Om du vill använda **Kerberos-autentisering** för HDFS-anslutning läser du [det här avsnittet](#use-kerberos-authentication-for-hdfs-connector) för att konfigurera den lokala miljön i enlighet med detta. |Ja |
| userName |Användarnamn för Windows-autentisering. För Kerberos-autentisering anger du `<username>@<domain>.com`. |Ja (för Windows-autentisering) |
| password |Lösenord för Windows-autentisering. |Ja (för Windows-autentisering) |
| gatewayName (gatewayName) |Namnet på den gateway som datafabrikstjänsten ska använda för att ansluta till HDFS. |Ja |
| krypteradKnuren |[Ny-AzDataFactoryEncryptVärdevärdeutdata](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) för åtkomstautentiseringsuppgifterna. |Inga |

### <a name="using-anonymous-authentication"></a>Använda anonym autentisering

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

### <a name="using-windows-authentication"></a>Använda Windows-autentisering

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
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell osv.).

Avsnittet **typeProperties** är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet typeProperties för datauppsättning av typen **FileShare** (som innehåller HDFS-datauppsättning) har följande egenskaper

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökväg till mappen. Exempel: `myfolder`<br/><br/>Använd escape-tecknet ' \ ' för specialtecken i strängen. Till exempel: för mapp\undermapp\\\\anger du mappundermapp och för d:\samplefolder anger du d:\\\\samplefolder.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** om du vill ha mappsökvägar baserat på start-/slutdatumtider för segment. |Ja |
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När filnamn inte har angetts för en utdatauppsättning, skulle namnet på den genererade filen vara i följande format: <br/><br/>`Data.<Guid>.txt`(till exempel: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Inga |
| partitioneradAv |partitionedBy kan användas för att ange en dynamisk folderPath, filnamn för tidsseriedata. Exempel: folderPath parameteriserad för varje timme data. |Inga |
| format | Följande formattyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet Format.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata. |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns [i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

> [!NOTE]
> filnamn och filFilter kan inte användas samtidigt.

### <a name="using-partionedby-property"></a>Använda partionedBy egendom
Som nämnts i föregående avsnitt kan du ange en dynamisk folderPath och filnamn för tidsseriedata med egenskapen **partitionedBy,** [Data Factory-funktioner och systemvariablerna](data-factory-functions-variables.md).

Mer information om tidsseriedatauppsättningar, schemaläggning och segment finns i [Skapa datauppsättningar,](data-factory-create-datasets.md) [schemaläggning & körning](data-factory-scheduling-and-execution.md)och Skapa [pipelines-artiklar.](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Prov 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
I det här exemplet ersätts {Slice} med värdet för Data Factory systemvariabeln SliceStart i det format (YYYYMMDDHH) som angetts. Segmentstart refererar till starttiden för segmentet. folderPath är olika för varje segment. Till exempel: wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104.

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
I det här exemplet extraheras år, månad, dag och tid för SegmentStart i separata variabler som används av egenskaperna folderPath och fileName.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och principer är tillgängliga för alla typer av aktiviteter.

Medan egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar med varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

För kopieringsaktivitet, när källan är av typen **FileSystemSource,** är följande egenskaper tillgängliga i avsnittet typeProperties:

**FileSystemSource** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiv |Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. |Sant, Falskt (standard) |Inga |

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimeringsformat som stöds
Mer information finns [i fil- och komprimeringsformat i](data-factory-supported-file-and-compression-formats.md) azure data factory-artikeln.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>JSON-exempel: Kopiera data från lokala HDFS till Azure Blob
Det här exemplet visar hur du kopierar data från en lokal HDFS till Azure Blob Storage. Data kan dock kopieras **direkt** till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.  

Exemplet innehåller JSON-definitioner för följande Data Factory-entiteter. Du kan använda dessa definitioner för att skapa en pipeline för att kopiera data från HDFS till Azure Blob Storage med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. En länkad tjänst av typen [OnPremisesHdfs](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [FileShare](#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en lokal HDFS till en Azure-blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

Som ett första steg ställer du in datahanteringsgatewayen. Instruktionerna i [flyttdata mellan lokala platser och molnartikel.](data-factory-move-data-between-onprem-and-cloud.md)

**HDFS länkade tjänst:** I det här exemplet används Windows-autentiseringen. Se [avsnittet HDFS-länkade tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

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

**Azure Storage-länkad tjänst:**

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

**HDFS indatauppsättning:** Den här datauppsättningen refererar till HDFS-mappen DataTransfer/UnitTest/. Pipelinen kopierar alla filer i den här mappen till målet.

Inställningen "extern": "true" informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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

**Azure Blob-utdatauppsättning:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

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

**En kopieringsaktivitet i en pipeline med filsystemkälla och Blob-mottagare:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda dessa in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **FileSystemSource** och **sink-typen** är inställd på **BlobSink**. Den SQL-fråga som **query** angetts för frågeegenskapen väljer de data som ska kopieras under den senaste timmen.

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

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Använda Kerberos-autentisering för HDFS-anslutning
Det finns två alternativ för att ställa in den lokala miljön för att använda Kerberos-autentisering i HDFS-anslutningsappen. Du kan välja en bättre passar ditt fall.
* Alternativ 1: [Gå med i gateway-maskin i Kerberos-sfären](#kerberos-join-realm)
* Alternativ 2: [Aktivera ömsesidigt förtroende mellan Windows-domän och Kerberos-sfär](#kerberos-mutual-trust)

### <a name="option-1-join-gateway-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Alternativ 1: Gå med i gateway-maskin i Kerberos-sfären

#### <a name="requirement"></a>Krav:

* Gateway-datorn måste ansluta till Kerberos-sfären och kan inte ansluta till någon Windows-domän.

#### <a name="how-to-configure"></a>Konfigurerar du:

**På gateway-maskin:**

1.  Kör **Ksetup-verktyget** för att konfigurera Kerberos KDC-servern och sfären.

    Datorn måste konfigureras som medlem i en arbetsgrupp eftersom en Kerberos-sfär skiljer sig från en Windows-domän. Detta kan uppnås genom att ange Kerberos-sfären och lägga till en KDC-server enligt följande. Ersätt *REALM.COM* med din egen respektive värld efter behov.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Starta om** maskinen efter att ha kört dessa 2 kommandon.

2.  Verifiera konfigurationen med **kommandot Ksetup.** Utgången ska vara som:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**I Azure Data Factory:**

* Konfigurera HDFS-kontakten med **Windows-autentisering** tillsammans med Kerberos huvudnamn och lösenord för att ansluta till HDFS-datakällan. Kontrollera avsnittet [egenskaper för HDFS-länkade tjänster](#linked-service-properties) om konfigurationsinformation.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Alternativ 2: Aktivera ömsesidigt förtroende mellan Windows-domän och Kerberos-sfär

#### <a name="requirement"></a>Krav:
*   Gateway-datorn måste ansluta till en Windows-domän.
*   Du behöver behörighet att uppdatera domänkontrollantens inställningar.

#### <a name="how-to-configure"></a>Konfigurerar du:

> [!NOTE]
> Ersätt REALM.COM och AD.COM i följande självstudie med din egen respektive sfär och domänkontrollant efter behov.

**På KDC-server:**

1. Redigera KDC-konfigurationen i **filen krb5.conf** så att KDC kan lita på Windows-domänen som refererar till följande konfigurationsmall. Som standard finns konfigurationen på **/etc/krb5.conf**.

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

2. Förbered ett huvudnamn med namnet **\@krbtgt/REALM.COM AD.COM** i KDC-servern med följande kommando:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. I **konfigurationsfilen för hadoop.security.auth_to_local** HDFS-tjänst lägger du till `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**På domänkontrollant:**

1.  Kör följande **Ksetup-kommandon** för att lägga till en sfärpost:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Skapa förtroende från Windows-domän till Kerberos Realm. [lösenord] är lösenordet för huvudbeloppet **\@krbtgt/REALM.COM AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Välj krypteringsalgoritm som används i Kerberos.

    1. Gå till Serverhanteraren > grupprinciphantering > domän > grupprincipobjekt > standard- eller active domain-princip och redigera.

    2. I popup-fönstret **Redigerare för redigerare för grupprinciphantering** går du till Datorkonfiguration > principer > Windows-inställningar > säkerhetsinställningar > lokala principer > säkerhetsalternativ och konfigurera **Nätverkssäkerhet: Konfigurera krypteringstyper som tillåts för Kerberos**.

    3. Välj den krypteringsalgoritm som du vill använda när du ansluter till KDC. Vanligtvis kan du helt enkelt välja alla alternativ.

        ![Konfigurationskrypteringstyper för Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Använd **kommandot Ksetup** för att ange den krypteringsalgoritm som ska användas på den specifika REALM.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Skapa mappningen mellan domänkontot och Kerberos-huvudmannen för att använda Kerberos-huvudnamn i Windows-domänen.

    1. Starta administrationsverktygen > **Active Directory – användare och datorer**.

    2. Konfigurera avancerade funktioner genom att klicka på **Visa** > **avancerade funktioner**.

    3. Leta reda på det konto som du vill skapa mappningar på och högerklicka för att visa **namnmappningar** > klicka på fliken **Kerberos-namn.**

    4. Lägg till en huvudman från riket.

        ![Karta säkerhetsidentitet](media/data-factory-hdfs-connector/map-security-identity.png)

**På gateway-maskin:**

* Kör följande **Ksetup-kommandon** för att lägga till en sfärpost.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**I Azure Data Factory:**

* Konfigurera HDFS-kontakten med **Windows-autentisering** tillsammans med antingen ditt domänkonto eller Kerberos-huvudnamn för att ansluta till HDFS-datakällan. Kontrollera avsnittet [egenskaper för HDFS-länkade tjänster](#linked-service-properties) om konfigurationsinformation.

> [!NOTE]
> Information om hur du mappar kolumner från källdatauppsättning till kolumner från sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.

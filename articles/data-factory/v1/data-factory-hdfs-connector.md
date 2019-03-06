---
title: Flytta data från den lokala HDFS | Microsoft Docs
description: Läs mer om hur du flyttar data från lokala HDFS med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 45b3149c0d546be201412567041ab1c5a86036e6
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455980"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Flytta data från den lokala HDFS med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-hdfs-connector.md)
> * [Version 2 (aktuell version)](../connector-hdfs.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [HDFS-anslutning i V2](../connector-hdfs.md).

Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data från ett lokalt HDFS. Den bygger på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten.

Du kan kopiera data från HDFS till alla datalager för mottagare som stöds. En lista över datalager som stöds som mottagare av Kopieringsaktivitet finns i den [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Data factory stöder för närvarande endast flyttar data från ett lokalt HDFS till datalager, men inte för att flytta data från andra datalager till ett lokalt HDFS.

> [!NOTE]
> Kopieringsaktivitet tar inte bort källfilen efter att den har kopierats till målet. Om du vill ta bort källfilen efter en lyckad kopiering kan du skapa en anpassad aktivitet för att ta bort filen och använda aktiviteten i pipelinen. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Aktivera anslutning
Data Factory-tjänsten stöder anslutning till den lokala HDFS med hjälp av Data Management Gateway. Se [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) du lär dig om Data Management Gateway och stegvisa instruktioner om hur du konfigurerar gatewayen. Använda gateway för att ansluta till HDFS, även om den finns i en Azure IaaS-VM.

> [!NOTE]
> Kontrollera att Data Management Gateway har åtkomst till **alla** [nod namnservern]: [name noden port] och [data nodservrar]: [nod dataporten] av Hadoop-kluster. Standard [namn noden port] är 50070 och standardvärdet [data noden port] är 50075.

Medan du kan installera gatewayen på samma lokala dator eller virtuell Azure-dator som med HDFS, rekommenderar vi att du installerar gatewayen på en separat dator/Azure IaaS VM. Att ha gatewayen på en separat dator minskar resurskonkurrens och förbättrar prestandan. När du installerar gatewayen på en separat dator kan ska datorn kunna få åtkomst till datorn med med HDFS.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data från en källa för HDFS med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen.
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett HDFS-datalager, se [JSON-exempel: Kopiera data från lokala HDFS till Azure Blob](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för HDFS:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
En länkad tjänst länkar ett datalager till en data factory. Du skapar en länkad tjänst av typen **Hdfs** att länka ett lokalt HDFS till din datafabrik. Följande tabell innehåller beskrivning för JSON-element som är specifika för HDFS-länkade tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen måste anges till: **Hdfs** |Ja |
| URL |URL: en med HDFS |Ja |
| authenticationType |Anonym, eller Windows. <br><br> Att använda **Kerberos-autentisering** HDFS-anslutningstjänsten finns i [i det här avsnittet](#use-kerberos-authentication-for-hdfs-connector) att ställa in din lokala miljö på lämpligt sätt. |Ja |
| Användarnamn |Användarnamn för Windows-autentisering. Kerberos-autentisering, ange `<username>@<domain>.com`. |Ja (för Windows-autentisering) |
| lösenord |Lösenordet för Windows-autentisering. |Ja (för Windows-autentisering) |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till med HDFS. |Ja |
| encryptedCredential |[Ny AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) utdata för åtkomst-autentiseringsuppgift. |Nej |

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

### <a name="using-windows-authentication"></a>Med hjälp av Windows-autentisering

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
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av (Azure SQL, Azure-blob, Azure-tabell osv.).

Den **typeProperties** avsnittet är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. TypeProperties avsnittet för datauppsättningen av typen **filresursen** (som innehåller HDFS datauppsättning) har följande egenskaper

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökvägen till mappen. Exempel: `myfolder`<br/><br/>Använd escape-tecknet ”\” för specialtecken i strängen. Till exempel: Ange mapp för folder\subfolder,\\\\undermapp och d:\samplefolder, ange d:\\\\Exempelmapp.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappen sökvägarna baserat på sektorn start/slut datum / tid. |Ja |
| fileName |Ange namnet på filen i den **folderPath** om du vill att tabellen för att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen, tabellen pekar på alla filer i mappen.<br/><br/>När filnamn har angetts för en utdatauppsättning, namnet på den genererade filen vara i följande det här formatet: <br/><br/>Data. <Guid>.txt (exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath filnamn för time series-data. Exempel: folderPath innehåller parametrar för varje timme som data. |Nej |
| Format | Följande formattyper av stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia). |Nej |
| Komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

> [!NOTE]
> filnamn och fileFilter kan inte användas samtidigt.

### <a name="using-partionedby-property"></a>Använda partionedBy-egenskapen
Som tidigare nämnts ovan kan du ange en dynamisk folderPath och ett filnamn för time series-data med den **partitionedBy** egenskapen [Data Factory-funktioner och systemvariablerna](data-factory-functions-variables.md).

Läs mer om time series datauppsättningar, schemaläggning och segment i [skapar datauppsättningar](data-factory-create-datasets.md), [schemaläggning och utförande](data-factory-scheduling-and-execution.md), och [skapa Pipelines](data-factory-create-pipelines.md) artiklar.

#### <a name="sample-1"></a>Exempel 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
I det här exemplet {sektorn} ersätts med det angivna värdet av Data Factory systemvariabeln SliceStart i formatet (YYYYMMDDHH). SliceStart refererar starttid för sektorn. FolderPath är olika för varje segment. Till exempel: wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104.

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
I det här exemplet extraheras år, månad, dag och tid för SliceStart till olika variabler som används av egenskaper för folderPath och filnamn.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principer är tillgängliga för alla typer av aktiviteter.

Medan egenskaper som är tillgängliga i avsnittet typeProperties aktivitetens varierar med varje aktivitetstyp av. För kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare.

För Kopieringsaktiviteten, när källan är av typen **FileSystemSource** följande egenskaper är tillgängliga i avsnittet typeProperties:

**FileSystemSource** har stöd för följande egenskaper:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiv |Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. |SANT, FALSKT (standard) |Nej |

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimeringsformat de format som stöds
Se [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikeln på information.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>JSON-exempel: Kopiera data från lokala HDFS till Azure Blob
Detta exempel visar hur du kopierar data från ett lokalt HDFS till Azure Blob Storage. Men du kan kopiera data **direkt** till någon av de mottagare som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.  

Det innehåller JSON-definitioner för följande Data Factory-entiteter. Du kan använda dessa definitioner för att skapa en pipeline för att kopiera data från HDFS till Azure Blob Storage med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. En länkad tjänst av typen [OnPremisesHdfs](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [filresursen](#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från ett lokalt HDFS till en Azure-blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

Som ett första steg att konfigurera data management gateway. Anvisningarna i den [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln.

**HDFS länkad tjänst:** Det här exemplet använder Windows-autentisering. Se [HDFS länkad tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

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

**Länkad Azure Storage-tjänst:**

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

**HDFS datauppsättningen för indata:** Denna datauppsättning refererar till mappen HDFS DataTransfer/UnitTest /. Pipelinen kopierar alla filer i den här mappen till målet.

Ange ”external”: ”true” informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

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

**Utdatauppsättning för Azure Blob:**

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1). Sökvägen till mappen för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

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

**En Kopieringsaktivitet i en pipeline med filsystemet käll- och Blob-mottagare:**

Pipelinen innehåller en Kopieringsaktivitet som är konfigurerad för att använda dessa in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **FileSystemSource** och **mottagare** är **BlobSink**. SQL-frågan som angetts för den **fråga** egenskapen väljer vilka data under den senaste timmen att kopiera.

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

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Använda Kerberos-autentisering för HDFS-anslutningstjänsten
Det finns två alternativ för att konfigurera den lokala miljön för att använda Kerberos-autentisering i HDFS-anslutningsappen. Du kan välja som passar bättre ditt ärende.
* Alternativ 1: [Ansluta till gateway-datorn i Kerberos-sfär](#kerberos-join-realm)
* Alternativ 2: [Aktivera ömsesidigt förtroende mellan Windows-domän och Kerberos-sfär](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Alternativ 1: Ansluta till gateway-datorn i Kerberos-sfär

#### <a name="requirement"></a>Krav:

* Gateway-datorn måste ansluta till Kerberos-sfären och det går inte att ansluta till en Windows-domän.

#### <a name="how-to-configure"></a>Så här konfigurerar du:

**På gateway-datorn:**

1.  Kör den **Ksetup** verktyg för att konfigurera Kerberos KDC-servern och sfär.

    Datorn måste konfigureras som en medlem i en arbetsgrupp, eftersom en Kerberos-sfär skiljer sig från en Windows-domän. Detta kan uppnås genom att Kerberos-sfären och lägga till en KDC-server på följande sätt. Ersätt *REALM.COM* med din egen respektive sfär vid behov.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Starta om** datorn när du kör kommandona 2.

2.  Verifiera konfigurationen med **Ksetup** kommando. Resultatet bör se ut som:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**I Azure Data Factory:**

* Konfigurera HDFS anslutningsapp med **Windows-autentisering** tillsammans med dina Kerberos-huvudnamn och lösenord för att ansluta till HDFS-datakälla. Kontrollera [HDFS länkade tjänstegenskaper](#linked-service-properties) avsnittet på konfigurationsinformation.

### <a name="kerberos-mutual-trust"></a>Alternativ 2: Aktivera ömsesidigt förtroende mellan Windows-domän och Kerberos-sfär

#### <a name="requirement"></a>Krav:
*   Gateway-datorn måste ansluta till en Windows-domän.
*   Du behöver behörighet att uppdatera inställningarna för den domänkontrollanten.

#### <a name="how-to-configure"></a>Så här konfigurerar du:

> [!NOTE]
> Ersätt REALM.COM och AD.COM i följande självstudie med dina egna respektive sfär och domänkontrollern efter behov.

**På KDC-servern:**

1.  Redigera KDC-konfigurationen i **krb5.conf** filen så att KDC förtroende för Windows-domänen för följande konfiguration. Som standard konfigurationen finns i **/etc/krb5.conf**.

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

2.  Förbereda ett huvudnamn med namnet **krbtgt/REALM.COM@AD.COM** i KDC-server med följande kommando:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  I **hadoop.security.auth_to_local** HDFS-tjänstkonfigurationen Lägg till `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**På domänkontrollanten:**

1.  Kör följande **Ksetup** kommandon för att lägga till en sfär post:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Upprätta förtroende från Windows-domän att Kerberos-sfär. [lösenord] är lösenordet för huvudnamn **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Välj krypteringsalgoritmen som används i Kerberos.

    1. Gå till Server Manager > hantering av Grupprincip > domän > grupprincipobjekt > standard eller aktiva domänprincip och redigera.

    2. I den **Redigeraren för Grupprinciphantering** popup-fönster, går du till Datorkonfiguration > Principer > Windows-inställningar > säkerhetsinställningar > lokala principer > säkerhetsalternativ, och konfigurera **nätverk säkerhet: Konfigurera krypteringstyper som tillåts för Kerberos**.

    3. Välj den krypteringsalgoritm som du vill använda när du ansluter till KDC. Ofta, kan du helt enkelt välja alla alternativ.

        ![Config-krypteringstyperna för Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Använd **Ksetup** kommando för att ange krypteringsalgoritmen som ska användas på specifika området.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Skapa mappningen mellan domänkontot och Kerberos-huvudnamn för att kunna använda Kerberos-huvudnamn i Windows-domän.

    1. Starta administrativa verktyg > **Active Directory-användare och datorer**.

    2. Konfigurera avancerade funktioner genom att klicka på **visa** > **avancerade funktioner**.

    3. Leta upp det konto som du vill skapa mappningar och högerklicka för att visa **namnmappningar** > klickar du på **Kerberos-namn** fliken.

    4. Lägg till ett huvudnamn för från området.

        ![Mappa säkerhetsidentitet](media/data-factory-hdfs-connector/map-security-identity.png)

**På gateway-datorn:**

* Kör följande **Ksetup** kommandon för att lägga till en sfär-post.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**I Azure Data Factory:**

* Konfigurera HDFS anslutningsapp med **Windows-autentisering** tillsammans med dina domänkonto eller Kerberos-huvudnamn att ansluta till HDFS-datakälla. Kontrollera [HDFS länkade tjänstegenskaper](#linked-service-properties) avsnittet på konfigurationsinformation.

> [!NOTE]
> Om du vill mappa kolumner från datauppsättningen för källan till kolumner från en datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.

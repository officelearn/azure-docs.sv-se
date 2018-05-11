---
title: Kopiera data från HDFS med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från en källa för molnet eller lokala HDFS till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: be60e79d9e5a57cd93a4aa57efdbb3e4c034d064
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Kopiera data från HDFS med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-hdfs-connector.md)
> * [Version 2 – förhandsversion](connector-hdfs.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från HDFS. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [HDFS-anslutningen i V1](v1/data-factory-hdfs-connector.md).


## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från HDFS till alla stöds sink-datalagret. En lista över datakällor som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här HDFS-anslutningen:

- Kopiera filer med **Windows** (Kerberos) eller **anonym** autentisering.
- Kopiera filer med **webhdfs** protokoll eller **inbyggda DistCp** stöd.
- Filer som kopieras-är eller parsning/genererar filer med den [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill kopiera data från ett HDFS som inte är offentligt tillgänglig, måste du konfigurera en Self-hosted integrering Runtime. Se [Self-hosted integrering Runtime](concepts-integration-runtime.md) artikel mer information.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till HDFS.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för HDFS länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **Hdfs**. | Ja |
| url |URL till HDFS |Ja |
| AuthenticationType | Tillåtna värden är: **anonym**, eller **Windows**. <br><br> Att använda **Kerberos-autentisering** HDFS-anslutningen finns i [i det här avsnittet](#use-kerberos-authentication-for-hdfs-connector) därefter konfigurera din lokala miljö. |Ja |
| Användarnamn |Användarnamn för Windows-autentisering. Kerberos-autentisering, ange `<username>@<domain>.com`. |Ja (för Windows-autentisering) |
| lösenord |Lösenordet för Windows-autentisering. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja (för Windows-autentisering) |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Self-hosted integrering Runtime eller Azure Integration Runtime (om datalager är offentligt tillgänglig). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel: använder anonym autentisering**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: med Windows-autentisering**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av HDFS dataset.

Ange typegenskapen för dataset för att kopiera data från HDFS, **filresursen**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **filresursen** |Ja |
| folderPath | Sökvägen till mappen. Wildcard-filter stöds inte. Till exempel: mappen/undermappen / |Ja |
| fileName |  **Namn eller jokertecken-filtret** för alla filer under den angivna ”folderPath”. Om du inte anger ett värde för den här egenskapen dataset pekar på alla filer i mappen. <br/><br/>För filter som tillåts är jokertecken: `*` (flera tecken) och `?` (valfritt tecken).<br/>-Exempel 1: `"fileName": "*.csv"`<br/>-Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` att undanta om din faktiska filnamnet innehåller jokertecken eller den här escape-tecken i. |Nej |
| Format | Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner.<br/><br/>Om du vill tolka filer med ett specifikt format format för följande filtyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](supported-file-formats-and-compression-codecs.md#text-format), [Json-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), och [parkettgolv Format](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binära kopiera scenario) |
| Komprimering | Ange typ och kompression för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbast**. |Nej |

>[!TIP]
>Kopiera alla filer i en mapp genom att ange **folderPath** endast.<br>Om du vill kopiera en enstaka fil med ett angivet namn, ange **folderPath** med Mappdel och **fileName** med filnamn.<br>Om du vill kopiera en delmängd av filer på en mapp, ange **folderPath** med Mappdel och **fileName** med jokerteckenfiltret.

**Exempel:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av HDFS-källa.

### <a name="hdfs-as-source"></a>HDFS som källa

Om du vill kopiera data från HDFS, anger du källa i kopieringsaktiviteten till **HdfsSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **HdfsSource** |Ja |
| Rekursiva | Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. Obs när rekursiv är inställd på true och mottagare är filbaserad lagring, tom mapp/underåtgärder-folder kommer inte att kopieras/skapas på mottagare.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT** | Nej |
| distcpSettings | Egenskapsgrupp när du använder HDFS DistCp. | Nej |
| resourceManagerEndpoint | Yarn ResourceManager-slutpunkten | Ja om du använder DistCp |
| tempScriptPath | En mappsökväg som används för att lagra temporära DistCp kommandoskript. Skriptfilen genereras av Data Factory och kommer att tas bort när kopieringsjobbet klar. | Ja om du använder DistCp |
| distcpOptions | Ytterligare alternativ som finns på DistCp kommando. | Nej |

**Exempel: HDFS källa i kopieringsaktiviteten med bort från MINNET**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Mer information om hur du använder DistCp för att kopiera data från HDFS effektivt från nästa avsnitt.

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Använd DistCp för att kopiera data från HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) är ett Hadoop interna kommandoradsverktyg sköta distribuerade kopian i ett Hadoop-kluster. När du kör kommandot Distcp, kommer den först lista över alla filer kopieras, skapa flera jobb på kartan i Hadoop-kluster och Jobbens karta gör binära kopia från källan till mottagare.

Kopiera aktivitet stöd med DistCp för kopiering av filer som – i Azure Blob (inklusive [mellanlagrad kopia](copy-activity-performance.md) eller Azure Data Lake Store då ditt kluster power i stället för körs på Self-hosted integrering Runtime fullständigt kan utnyttja . Det ger bättre copy genomströmning särskilt om klustret är en mycket kraftfull. Baserat på konfigurationen i Azure Data Factory kopieringsaktiviteten automatiskt skapa ett distcp kommando, skicka till Hadoop-kluster och övervaka kopian status.

### <a name="prerequsites"></a>Prerequsites

Använd DistCp för att kopiera filer som-är från HDFS till Azure Blob (inklusive mellanlagrade kopia) eller Azure Data Lake Store, kontrollera att Hadoop-kluster uppfyller nedan krav:

1. MapReduce och Yarn tjänster är aktiverade.
2. Yarn-versionen är 2.5 eller senare.
3. HDFS-server är integrerat med datalager mål - Azure Blob- eller Azure Data Lake Store:

    - Azure Blob-filsystem stöds internt sedan Hadoop 2.7. Du behöver bara ange jar-sökväg i Hadoop env config.
    - Azure Data Lake Store-filsystem paketeras från Hadoop 3.0.0-alpha1. Om Hadoop-kluster är lägre än den versionen, måste du manuellt importera ADLS relaterade jar-paket (azure-datalake-store.jar) i klustret från [här](https://hadoop.apache.org/releases.html), och anger jar-sökväg i Hadoop env config.

4. Förbered en tillfällig mapp för i HDFS. Den här tillfällig mapp används för att lagra DistCp shell-skript, så att den tar upp diskutrymme i KB-nivå.
5. Kontrollera att användarkontot i HDFS länkade tjänst har behörighet att en) skickar programmet i Yarn; b) har behörighet att skapa undermapp, läsning och skrivning filer under ovan tillfällig mapp.

### <a name="configurations"></a>Konfigurationer

Nedan visas ett exempel på Kopiera aktivitet konfiguration för att kopiera data från HDFS till Azure-Blob med DistCp:

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromHDFSToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "HDFSDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HdfsSource",
                "distcpSettings": {
                    "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                    "tempScriptPath": "/usr/hadoop/tempscript",
                    "distcpOptions": "-strategy dynamic -map 100"
                }
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Använda Kerberos-autentisering för HDFS-anslutningen

Det finns två alternativ för att konfigurera den lokala miljön för att använda Kerberos-autentisering i HDFS-koppling. Du kan välja den bättre passar ditt ärende.
* Alternativ 1: [koppling Self-hosted integrering Runtime-dator i Kerberos-sfär](#kerberos-join-realm)
* Alternativ 2: [aktivera ömsesidigt förtroende mellan Windows-domän och Kerberos-sfär](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Alternativ 1: Ansluta Self-hosted integrering Runtime datorn i Kerberos-sfär

#### <a name="requirements"></a>Krav

* Self-hosted integrering Runtime-dator måste ansluta till Kerberos-sfären och kan inte ansluta till en Windows-domän.

#### <a name="how-to-configure"></a>Så här konfigurerar du

**På Self-hosted integrering Runtime datorn:**

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

#### <a name="requirements"></a>Krav

*   Self-hosted integrering Runtime-dator måste ansluta till en Windows-domän.
*   Du behöver behörighet att uppdatera inställningarna för domänkontrollanten.

#### <a name="how-to-configure"></a>Så här konfigurerar du

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

2.  Förbereda en huvudansvarig med namnet **krbtgt/REALM.COM@AD.COM** i KDC-server med följande kommando:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  I **hadoop.security.auth_to_local** HDFS tjänstkonfiguration lägger du till `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**På domänkontrollanten:**

1.  Kör följande **Ksetup** kommandon för att lägga till en sfär post:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Upprätta förtroende från Windows-domän till Kerberos-sfär. [lösenord] är lösenordet för objektet **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Välj krypteringsalgoritm som används i Kerberos.

    1. Gå till Serverhanteraren > hantering av Grupprincip > domän > grupprincipobjekt > standard eller aktiva domänprincip och redigera.

    2. I den **Redigeraren för Grupprinciphantering** popup-fönster, gå till Datorkonfiguration > Principer > Windows-inställningar > säkerhetsinställningar > lokala principer > säkerhetsalternativ, och konfigurera **Nätverkssäkerhet: Konfigurera krypteringstyper tillåts för Kerberos**.

    3. Välj den krypteringsalgoritm som du vill använda när du ansluter till KDC. Ofta, kan du bara välja alla alternativ.

        ![Config krypteringstyper för Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Använd **Ksetup** kommando för att ange krypteringsalgoritmen som ska användas på den specifika SFÄREN.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Skapa mappning mellan domänkontot och Kerberos-huvudnamn för att kunna använda Kerberos-huvudnamn i Windows-domän.

    1. Starta administrativa verktyg > **Active Directory-användare och datorer**.

    2. Konfigurera avancerade funktioner genom att klicka på **visa** > **avancerade funktioner**.

    3. Leta upp det konto som du vill skapa mappningar och högerklicka om du vill visa **namnmappningar** > klickar du på **Kerberos-namn** fliken.

    4. Lägga till en huvudansvarig från domänen.

        ![Mappa säkerhetsidentitet](media/connector-hdfs/map-security-identity.png)

**På Self-hosted integrering Runtime datorn:**

* Kör följande **Ksetup** kommandon för att lägga till en domän-post.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**I Azure Data Factory:**

* Konfigurera anslutningen HDFS med **Windows-autentisering** tillsammans med din domänkonto eller Kerberos-huvudnamn att ansluta till HDFS-datakälla. Kontrollera [HDFS länkade tjänstegenskaper](#linked-service-properties) avsnittet konfigurationsinformation.


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).
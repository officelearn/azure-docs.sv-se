---
title: Kopiera data från HDFS med Azure Data Factory
description: Lär dig hur du kopierar data från en moln- eller lokal HDFS-källa till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 2cd76afa9412e89c57cfb6c357eb164ce5d3d1c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75830436"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Kopiera data från HDFS med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-hdfs-connector.md)
> * [Aktuell version](connector-hdfs.md)

I den här artikeln beskrivs hur du kopierar data från HDFS-servern. Mer information om Azure Data Factory finns i den [inledande artikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här HDFS-kontakten stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Denna HDFS-kontakt stöder:

- Kopiera filer med **Windows** (Kerberos) eller **Anonym** autentisering.
- Kopiera filer med **webhdfs-protokollet** eller **inbyggt DistCp-stöd.**
- Kopiera filer som de är eller tolka/generera filer med [filformat och komprimeringskodicer som stöds](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Kontrollera att integrationskörningen kan komma åt **alla** [namnnodsservern]:[namnnodport] och [datanodservrar]:[datanodport] i Hadoop-klustret. Standard [namnnodport] är 50070 och standard [datanodport] är 50075.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för HDFS.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för HDFS-länkade tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste ställas in på: **HDfs**. | Ja |
| url |URL till HDFS |Ja |
| authenticationType | Tillåtna värden är: **Anonyma**eller **Windows**. <br><br> Om du vill använda **Kerberos-autentisering** för HDFS-anslutning läser du [det här avsnittet](#use-kerberos-authentication-for-hdfs-connector) för att konfigurera den lokala miljön i enlighet med detta. |Ja |
| userName |Användarnamn för Windows-autentisering. För Kerberos-autentisering anger du `<username>@<domain>.com`. |Ja (för Windows-autentisering) |
| password |Lösenord för Windows-autentisering. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja (för Windows-autentisering) |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om det inte anges används standardkörningen för Azure Integration. |Inga |

**Exempel: använda anonym autentisering**

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

**Exempel: använda Windows-autentisering**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för HDFS under `location` inställningar i formatbaserad datauppsättning:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen type `location` under in dataset måste anges till **HdfsLocation**. | Ja      |
| folderPath | Sökvägen till mappen. Om du vill använda jokertecken för att filtrera mappen hoppar du över den här inställningen och anger i inställningarna för aktivitetskällan. | Inga       |
| fileName   | Filnamnet under den angivna folderPath. Om du vill använda jokertecken för att filtrera filer hoppar du över den här inställningen och anger i inställningarna för aktivitetskällan. | Inga       |

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av HDFS-källa.

### <a name="hdfs-as-source"></a>HDFS som källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för HDFS under `storeSettings` inställningar i formatbaserad kopieringskälla:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Egenskapen type `storeSettings` under måste vara inställd på **HdfsReadSettings**. | Ja                                           |
| Rekursiv                | Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. Observera att när rekursiv är inställd på true och diskhon är ett filbaserat arkiv kopieras eller skapas inte en tom mapp eller undermapp i diskhon. Tillåtna värden är **sanna** (standard) och **falska**. | Inga                                            |
| jokerteckenMappspath       | Mappsökvägen med jokertecken för att filtrera källmappar. <br>Tillåtna jokertecken `*` är: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken); används `^` för att fly om ditt faktiska mappnamn har jokertecken eller den här flyktteckenet inuti. <br>Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). | Inga                                            |
| jokerteckenFileName         | Filnamnet med jokertecken under den angivna mappenPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken `*` är: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken); används `^` för att fly om ditt faktiska mappnamn har jokertecken eller den här flyktteckenet inuti.  Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). | Ja `fileName` om inte anges i datauppsättningen |
| modifiedDatetimeStart    | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas. | Inga                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Inga                                            |
| distcpSettings | Egenskapsgrupp när HDFS DistCp använder. | Inga |
| resursManagerEndpoint | Slutpunkten för Yarn Resource Manager | Ja om du använder DistCp |
| tempScriptPath (tempScriptPath) | En mappsökväg som används för att lagra kommandott Temp DistCp. Skriptfilen genereras av Data Factory och tas bort när kopiera jobbet är klart. | Ja om du använder DistCp |
| distcpOptions | Ytterligare alternativ som tillhandahålls till DistCp-kommandot. | Inga |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till lagringsarkivet samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga                                            |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp- och filfilter

I det här avsnittet beskrivs det resulterande beteendet för mappsökvägen och filnamnet med jokerteckenfilter.

| folderPath | fileName             | Rekursiv | Källmappstruktur och filterresultat (filer i **fetstil** hämtas) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (tom, använd standard) | false     | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `Folder*`  | (tom, använd standard) | true      | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `Folder*`  | `*.csv`              | false     | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `Folder*`  | `*.csv`              | true      | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Använda DistCp för att kopiera data från HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) är ett Inbyggt kommandoradsverktyg för Hadoop för att göra distribuerad kopia i ett Hadoop-kluster. När du kör ett Distcp-kommando visas först alla filer som ska kopieras, skapa flera kartjobb i Hadoop-klustret och varje kartjobb gör binär kopia från källa till diskho.

Kopiera aktivitetsstöd med DistCp för att kopiera filer som de är i Azure Blob (inklusive [stegvis kopia)](copy-activity-performance.md)eller Azure Data Lake Store, i vilket fall det kan utnyttja klustrets kraft i stället för att köras på den självvärderade integrationskörningen. Det kommer att ge bättre kopieringsdataflöde, särskilt om klustret är mycket kraftfullt. Baserat på din konfiguration i Azure Data Factory konstruerar copy-aktivitet automatiskt ett distcp-kommando, skickar till ditt Hadoop-kluster och övervakar kopieringsstatusen.

### <a name="prerequisites"></a>Krav

Om du vill använda DistCp för att kopiera filer enligt HDFS till Azure Blob (inklusive mellanlagringskopia) eller Azure Data Lake Store kontrollerar du att Ditt Hadoop-kluster uppfyller nedanstående krav:

1. MapReduce- och Yarn-tjänster är aktiverade.
2. Garn versionen är 2,5 eller högre.
3. HDFS-servern är integrerad med ditt måldatalager – Azure Blob eller Azure Data Lake Store:

    - Azure Blob FileSystem stöds internt sedan Hadoop 2.7. Du behöver bara ange jar path i Hadoop env config.
    - Azure Data Lake Store FileSystem är paketerat från Hadoop 3.0.0-alpha1. Om Hadoop-klustret är lägre än den versionen måste du manuellt importera ADLS-relaterade jar-paket (azure-datalake-store.jar) till klustret [härifrån](https://hadoop.apache.org/releases.html)och ange jar path i Hadoop env config.

4. Förbered en temp-mapp i HDFS. Den här tempmappen används för att lagra DistCp-skalskript, så det upptar utrymme på KB-nivå.
5. Kontrollera att användarkontot som tillhandahålls i HDFS Linked Service har behörighet att a) skicka in en ansökan i Yarn; b) har behörighet att skapa undermapp, läsa / skriva filer under ovanstående temp mapp.

### <a name="configurations"></a>Konfigurationer

Se DistCp-relaterade konfigurationer och exempel i [HDFS som källavsnitt.](#hdfs-as-source)

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Använda Kerberos-autentisering för HDFS-anslutning

Det finns två alternativ för att ställa in den lokala miljön för att använda Kerberos-autentisering i HDFS-anslutningsappen. Du kan välja en bättre passar ditt fall.
* Alternativ 1: [Gå med i självvärd för integrationskörningsdator i Kerberos-sfären](#kerberos-join-realm)
* Alternativ 2: [Aktivera ömsesidigt förtroende mellan Windows-domän och Kerberos-sfär](#kerberos-mutual-trust)

### <a name="option-1-join-self-hosted-integration-runtime-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Alternativ 1: Gå med i självvärd för integrationskörningsdator i Kerberos-sfären

#### <a name="requirements"></a>Krav

* Den självvärderade integrationskörningsdatorn måste ansluta till Kerberos-sfären och kan inte ansluta till någon Windows-domän.

#### <a name="how-to-configure"></a>Konfigurerar du

**På självvärdförd integrationskörningsmaskin:**

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

#### <a name="requirements"></a>Krav

*   Den självvärderade integrationskörningsdatorn måste ansluta till en Windows-domän.
*   Du behöver behörighet att uppdatera domänkontrollantens inställningar.

#### <a name="how-to-configure"></a>Konfigurerar du

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

        ![Konfigurationskrypteringstyper för Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Använd **kommandot Ksetup** för att ange den krypteringsalgoritm som ska användas på den specifika REALM.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Skapa mappningen mellan domänkontot och Kerberos-huvudmannen för att använda Kerberos-huvudnamn i Windows-domänen.

    1. Starta administrationsverktygen > **Active Directory – användare och datorer**.

    2. Konfigurera avancerade funktioner genom att klicka på **Visa** > **avancerade funktioner**.

    3. Leta reda på det konto som du vill skapa mappningar på och högerklicka för att visa **namnmappningar** > klicka på fliken **Kerberos-namn.**

    4. Lägg till en huvudman från riket.

        ![Karta säkerhetsidentitet](media/connector-hdfs/map-security-identity.png)

**På självvärdförd integrationskörningsmaskin:**

* Kör följande **Ksetup-kommandon** för att lägga till en sfärpost.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**I Azure Data Factory:**

* Konfigurera HDFS-kontakten med **Windows-autentisering** tillsammans med antingen ditt domänkonto eller Kerberos-huvudnamn för att ansluta till HDFS-datakällan. Kontrollera avsnittet [egenskaper för HDFS-länkade tjänster](#linked-service-properties) om konfigurationsinformation.

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Äldre modeller

>[!NOTE]
>Följande modeller stöds fortfarande i sig för bakåtkompatibilitet. Du föreslås använda den nya modellen som nämns i ovanstående avsnitt framöver, och ADF-redigeringsgränssnittet har bytt till att generera den nya modellen.

### <a name="legacy-dataset-model"></a>Äldre datauppsättningsmodell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste anges till: **FileShare** |Ja |
| folderPath | Sökväg till mappen. Jokerteckenfilter stöds, tillåtna jokertecken `*` är: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken); används `^` för att fly om ditt faktiska filnamn har jokertecken eller den här flyktteckenet inuti. <br/><br/>Exempel: rootfolder/subfolder/, se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). |Ja |
| fileName |  **Namn eller jokerteckenfilter** för filen/filerna under den angivna "folderPath". Om du inte anger något värde för den här egenskapen pekar datauppsättningen på alla filer i mappen. <br/><br/>För filter är tillåtna `*` jokertecken: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken).<br/>- Exempel 1:`"fileName": "*.csv"`<br/>- Exempel 2:`"fileName": "???20180427.txt"`<br/>Används `^` för att fly om ditt faktiska mappnamn har jokertecken eller den här flyktteckenet inuti. |Inga |
| modifiedDatetimeStart | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning kommer att påverkas genom att aktivera den här inställningen när du vill göra filfilter från stora mängder filer. <br/><br/> Egenskaperna kan vara NULL som innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas.| Inga |
| modifiedDatetimeEnd | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning kommer att påverkas genom att aktivera den här inställningen när du vill göra filfilter från stora mängder filer. <br/><br/> Egenskaperna kan vara NULL som innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas.| Inga |
| format | Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata.<br/><br/>Om du vill tolka filer med ett visst format stöds följande filformattyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Json Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parquet Format.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nej (endast för binärt kopieringsscenario) |
| komprimering | Ange typ och komprimeringsnivå för data. Mer information finns i [Filformat och komprimeringskodicer som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbaste**. |Inga |

>[!TIP]
>Om du vill kopiera alla filer under en mapp anger du endast **folderPath.**<br>Om du vill kopiera en enskild fil med ett förnamn anger du **folderPath** med mappdel och **fileName** med filnamn.<br>Om du vill kopiera en delmängd filer under en mapp anger du **folderPath** med mappdel och **fileName** med jokerteckenfilter.

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
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
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

### <a name="legacy-copy-activity-source-model"></a>Källmodell för äldre kopieringsaktivitet

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type property för kopians aktivitet måste ställas in på: **HdfsSource** |Ja |
| Rekursiv | Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. Observera att när rekursiv är inställd på true och sink är filbaserad butik, kopieras/skapas inte den tomma mappen/undermappen vid diskhon.<br/>Tillåtna värden är: **sant** (standard), **falskt** | Inga |
| distcpSettings | Egenskapsgrupp när HDFS DistCp använder. | Inga |
| resursManagerEndpoint | Slutpunkten för Yarn Resource Manager | Ja om du använder DistCp |
| tempScriptPath (tempScriptPath) | En mappsökväg som används för att lagra kommandott Temp DistCp. Skriptfilen genereras av Data Factory och tas bort när kopiera jobbet är klart. | Ja om du använder DistCp |
| distcpOptions | Ytterligare alternativ som tillhandahålls till DistCp-kommandot. | Inga |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till lagringsarkivet samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga |

**Exempel: HDFS-källa i kopieringsaktivitet med DistCp**

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

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

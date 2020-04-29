---
title: Kopiera data från HDFS med Azure Data Factory
description: Lär dig hur du kopierar data från en moln-eller lokal HDFS-källa till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
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
ms.openlocfilehash: 09c39c41b2d31f88fe2b19d8f20cd19e182c9214
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417272"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Kopiera data från HDFS med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-hdfs-connector.md)
> * [Aktuell version](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du kopierar data från HDFS-servern. Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här HDFS-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Mer specifikt stöder den här HDFS-anslutningen:

- Kopiera filer med **Windows** (Kerberos) eller **Anonym** autentisering.
- Kopiera filer med **webhdfs** -protokoll eller **inbyggt DistCp** -stöd.
- Kopiera filer som-är eller parsa/generera filer med de [fil format och komprimerings-codec som stöds](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Se till att Integration Runtime har åtkomst till **alla** [namnserver]: [namn Node port] och [datanode-servrar]: [datanode port] för Hadoop-klustret. Standard [Name Node port] är 50070 och standardvärdet för [datanode-port] är 50075.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för HDFS.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten HDFS:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **HDFS**. | Ja |
| url |URL till HDFS |Ja |
| authenticationType | Tillåtna värden är: **anonyma**eller **Windows**. <br><br> Om du vill använda **Kerberos-autentisering** för HDFS Connector läser du [det här avsnittet](#use-kerberos-authentication-for-hdfs-connector) för att konfigurera din lokala miljö. |Ja |
| userName |Användar namn för Windows-autentisering. För Kerberos-autentisering anger `<username>@<domain>.com`du. |Ja (för Windows-autentisering) |
| password |Lösen ord för Windows-autentisering. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja (för Windows-autentisering) |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för HDFS under `location` inställningar i format-baserad data mängd:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Typ egenskapen under `location` i data mängden måste anges till **HdfsLocation**. | Ja      |
| folderPath | Sökvägen till mappen. Om du vill använda jokertecken för att filtrera mappar hoppar du över den här inställningen och anger i aktivitets källans inställningar. | Nej       |
| fileName   | Fil namnet under den aktuella folderPath. Om du vill använda jokertecken för att filtrera filer, hoppar du över den här inställningen och anger i aktivitets källans inställningar. | Nej       |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av HDFS-källan.

### <a name="hdfs-as-source"></a>HDFS som källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för HDFS under `storeSettings` inställningar i format-baserad kopierings Källa:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Typ egenskapen under `storeSettings` måste anges till **HdfsReadSettings**. | Ja                                           |
| rekursiva                | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursivt har angetts till true och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. Tillåtna värden är **True** (standard) och **false**. | Nej                                            |
| wildcardFolderPath       | Mappsökvägen med jokertecken för att filtrera källmappen. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller enstaka tecken). används `^` för att kringgå om det faktiska mappnamnet har jokertecken eller detta escape-tecken inuti. <br>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Nej                                            |
| wildcardFileName         | Fil namnet med jokertecken under den aktuella folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller enstaka tecken). används `^` för att kringgå om det faktiska mappnamnet har jokertecken eller detta escape-tecken inuti.  Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Ja om `fileName` inte anges i data uppsättningen |
| modifiedDatetimeStart    | Filter för filer baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och. `modifiedDatetimeEnd` Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har datetime-värde `modifiedDatetimeEnd` men är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime väljs.  När `modifiedDatetimeEnd` har ett datetime- `modifiedDatetimeStart` värde men är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime väljs. | Nej                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                            |
| distcpSettings | Egenskaps grupp vid användning av HDFS DistCp. | Nej |
| resourceManagerEndpoint | Garn Resource Manager-slutpunkt | Ja om du använder DistCp |
| tempScriptPath | En mappsökväg som används för att lagra Temp DistCp-kommandoskriptet. Skript filen genereras av Data Factory och tas bort när kopierings jobbet är klart. | Ja om du använder DistCp |
| distcpOptions | Ytterligare alternativ har angetts för DistCp-kommandot. | Nej |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej                                            |

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

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp-och fil filter

I det här avsnittet beskrivs det resulterande beteendet hos mappsökvägen och fil namnet med filter för jokertecken.

| folderPath | fileName             | rekursiva | Källans mappstruktur och filter resultat (filer i **fetstil** hämtas) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (tom, Använd standard) | falskt     | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fil2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | (tom, Använd standard) | true      | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fil2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | `*.csv`              | falskt     | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | `*.csv`              | true      | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Använda DistCp för att kopiera data från HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) är ett internt Hadoop-kommando rads verktyg som gör distribuerad kopia i ett Hadoop-kluster. När du kör ett Distcp-kommando, visar den först alla filer som ska kopieras, skapar flera kart jobb i Hadoop-klustret och varje kart jobb kommer att göra binär kopia från källa till mottagare.

Kopiera aktivitets stöd med DistCp för att kopiera filer som-är i Azure Blob (inklusive [mellanlagrad kopia](copy-activity-performance.md)) eller Azure Data Lake Store, och i så fall kan den utnyttja klustrets ström i stället för att köras på den lokala integration Runtime. Det ger bättre kopiering av data genom att i synnerhet om klustret är mycket kraftfullt. Baserat på din konfiguration i Azure Data Factory konstruerar kopierings aktiviteten automatiskt ett Distcp-kommando, skickar till ditt Hadoop-kluster och övervakar kopierings statusen.

### <a name="prerequisites"></a>Krav

Om du vill använda DistCp för att kopiera filer från HDFS till Azure Blob (inklusive mellanlagrad kopia) eller Azure Data Lake Store, se till att ditt Hadoop-kluster uppfyller nedanstående krav:

1. MapReduce-och garn tjänster är aktiverade.
2. Garn versionen är 2,5 eller högre.
3. HDFS-servern är integrerad med mål data lagret – Azure Blob eller Azure Data Lake Store:

    - Azure Blob-filsystem stöds internt sedan Hadoop 2,7. Du behöver bara ange jar-sökväg i Hadoop-kuvert config.
    - Azure Data Lake Store FileSystem paketeras från Hadoop 3.0.0-alpha1. Om ditt Hadoop-kluster är lägre än den versionen måste du manuellt importera ADLS-relaterade jar-paket (Azure-datalake-Store. jar) till klustret [härifrån och ange jar-sökväg](https://hadoop.apache.org/releases.html)i Hadoop-kuvert config.

4. Förbered en Temp-mapp i HDFS. Den här Temp-mappen används för att lagra DistCp Shell-skript, så den kommer att uppta utrymme på KB-nivå.
5. Se till att det användar konto som anges i den länkade tjänsten HDFS har behörighet att skicka ett program i garn, b) har behörighet att skapa undermappar, läsa/skriva filer under ovan Temp-mappen.

### <a name="configurations"></a>Konfigurationer

Se DistCp-relaterade konfigurationer och exempel i [HDFS som käll](#hdfs-as-source) avsnitt.

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Använd Kerberos-autentisering för HDFS-anslutning

Det finns två alternativ för att konfigurera den lokala miljön så att den använder Kerberos-autentisering i HDFS-anslutningen. Du kan välja den som passar bäst för ditt ärende.
* Alternativ 1: [Anslut egen värd integration runtime datorn i Kerberos-sfären](#kerberos-join-realm)
* Alternativ 2: [Aktivera ömsesidigt förtroende mellan Windows-domän och Kerberos-sfär](#kerberos-mutual-trust)

### <a name="option-1-join-self-hosted-integration-runtime-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Alternativ 1: Anslut egen värd Integration Runtime datorn i Kerberos-sfären

#### <a name="requirements"></a>Krav

* Datorn med egen värd Integration Runtime måste ansluta till Kerberos-sfären och kan inte ansluta till en Windows-domän.

#### <a name="how-to-configure"></a>Så här konfigurerar du

**På datorn med egen värd Integration Runtime:**

1.  Kör **Ksetup** -verktyget för att konfigurera Kerberos KDC-servern och sfären.

    Datorn måste vara konfigurerad som medlem i en arbets grupp eftersom en Kerberos-sfär skiljer sig från en Windows-domän. Detta kan uppnås genom att du ställer in Kerberos-sfären och lägger till en KDC-server på följande sätt. Ersätt *REALM.com* med egen respektive sfär efter behov.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Starta om** datorn när du har kört dessa två kommandon.

2.  Verifiera konfigurationen med kommandot **Ksetup** . Utdata bör vara som:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**I Azure Data Factory:**

* Konfigurera HDFS-anslutningen med **Windows-autentisering** tillsammans med ditt Kerberos-huvud namn och lösen ord för att ansluta till HDFS-datakällan. Kontrol lera [HDFS-avsnittet länkade tjänst egenskaper](#linked-service-properties) i konfigurations information.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Alternativ 2: Aktivera ömsesidigt förtroende mellan Windows-domän och Kerberos-sfär

#### <a name="requirements"></a>Krav

*   Datorn med egen värd Integration Runtime måste ansluta till en Windows-domän.
*   Du måste ha behörighet att uppdatera inställningarna för domänkontrollanten.

#### <a name="how-to-configure"></a>Så här konfigurerar du

> [!NOTE]
> Ersätt REALM.COM och AD.COM i följande självstudie med din egen domän och domänkontrollant efter behov.

**På KDC-Server:**

1. Redigera KDC-konfigurationen i **krb5. conf** -filen för att tillåta att KDC-förtroende Windows-domänen refererar till följande konfigurations mall. Konfigurationen finns som standard på **/etc/krb5.conf**.

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

2. Förbered ett huvud namn med namnet **KRBTGT/REALM\@. com-AD.com** i KDC-servern med följande kommando:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. I filen **Hadoop. Security. auth_to_local** HDFS-tjänsten lägger du `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`till.

**På domänkontrollant:**

1.  Kör följande **Ksetup** -kommandon för att lägga till en sfär post:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Upprätta förtroende från Windows-domän till Kerberos-sfär. [Password] är lösen ordet för det primära **KRBTGT/REALM. COM\@-AD.com**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Välj krypteringsalgoritm som används i Kerberos.

    1. Gå till Serverhanteraren > grupprincip hantering > domän > grupprincip objekt > standard princip eller aktiv domän princip och redigera.

    2. I popup-fönstret **redigeraren Grupprinciphantering** går du till dator konfiguration > principer > Windows-inställningar > säkerhets inställningar > lokala principer > säkerhets alternativ och konfigurerar **nätverks säkerhet: Konfigurera krypterings typer som tillåts för Kerberos**.

    3. Välj den krypteringsalgoritm som du vill använda när du ansluter till KDC. Vanligt vis kan du bara välja alla alternativ.

        ![Konfigurations krypterings typer för Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Använd **Ksetup** -kommandot för att ange vilken krypteringsalgoritm som ska användas för den aktuella sfären.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Skapa mappningen mellan domän kontot och Kerberos-huvudobjektet för att använda Kerberos-huvudobjektet i Windows-domänen.

    1. Starta administrations verktygen > **Active Directory användare och datorer**.

    2. Konfigurera avancerade funktioner genom att klicka på **Visa** > **avancerade funktioner**.

    3. Leta upp det konto som du vill skapa mappningar för och högerklicka för att visa **namn mappningar** > Klicka på fliken **Kerberos-namn** .

    4. Lägg till ett huvud konto från sfären.

        ![Mappa säkerhets identitet](media/connector-hdfs/map-security-identity.png)

**På datorn med egen värd Integration Runtime:**

* Kör följande **Ksetup** -kommandon för att lägga till en resurs post.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**I Azure Data Factory:**

* Konfigurera HDFS-anslutningen med **Windows-autentisering** tillsammans med antingen ditt domän konto eller Kerberos-huvudobjektet för att ansluta till HDFS-datakällan. Kontrol lera [HDFS-avsnittet länkade tjänst egenskaper](#linked-service-properties) i konfigurations information.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Äldre modeller

>[!NOTE]
>Följande modeller stöds fortfarande för bakåtkompatibilitet. Du rekommenderar att du använder den nya modellen som anges ovan och fortsätter, och redigerings gränssnittet för ADF har växlat till att generera den nya modellen.

### <a name="legacy-dataset-model"></a>Äldre data uppsättnings modell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **fileshare** |Ja |
| folderPath | Sökväg till mappen. Wildcard-filtret stöds, tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller enstaka tecken). används `^` för att kringgå om det faktiska fil namnet har jokertecken eller detta escape-tecken inuti. <br/><br/>Exempel: RootFolder/undermapp/, se fler exempel i [mapp-och fil filter exempel](#folder-and-file-filter-examples). |Ja |
| fileName |  **Namn eller Wildcard-filter** för filen/filerna under den angivna "folderPath". Om du inte anger ett värde för den här egenskapen pekar data uppsättningen på alla filer i mappen. <br/><br/>För filter är tillåtna jokertecken: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken).<br/>– Exempel 1:`"fileName": "*.csv"`<br/>– Exempel 2:`"fileName": "???20180427.txt"`<br/>Används `^` för att kringgå om det faktiska mappnamnet har jokertecken eller detta escape-tecken inuti. |Nej |
| modifiedDatetimeStart | Filter för filer baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och. `modifiedDatetimeEnd` Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning påverkas genom att aktivera den här inställningen när du vill göra fil filter från enorma mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filter för filattribut används för data uppsättningen.  När `modifiedDatetimeStart` har datetime-värde `modifiedDatetimeEnd` men är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime väljs.  När `modifiedDatetimeEnd` har ett datetime- `modifiedDatetimeStart` värde men är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime väljs.| Nej |
| modifiedDatetimeEnd | Filter för filer baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och. `modifiedDatetimeEnd` Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning påverkas genom att aktivera den här inställningen när du vill göra fil filter från enorma mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filter för filattribut används för data uppsättningen.  När `modifiedDatetimeStart` har datetime-värde `modifiedDatetimeEnd` men är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime väljs.  När `modifiedDatetimeEnd` har ett datetime- `modifiedDatetimeStart` värde men är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime väljs.| Nej |
| format | Om du vill **Kopiera filer som är** mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar.<br/><br/>Om du vill parsa filer med ett speciellt format stöds följande fil format **typer: text**format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i [text format](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), Orc- [format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) -avsnitt. |Nej (endast för binär kopierings scenario) |
| komprimering | Ange typ och nivå för komprimeringen för data. Mer information finns i [fil format och komprimerings-codecar som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>De typer som stöds är: **gzip**, **DEFLATE**, **BZip2**och **ZipDeflate**.<br/>De nivåer som stöds är: **optimalt** och **snabbast**. |Nej |

>[!TIP]
>Om du vill kopiera alla filer under en mapp anger du endast **folderPath** .<br>Om du vill kopiera en enskild fil med ett visst namn anger du **folderPath** med en **mapp och ett fil namn** med fil namnet.<br>Om du vill kopiera en delmängd av filer under en mapp anger du **folderPath** med en mapp och ett **fil namns** filter med jokertecken.

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

### <a name="legacy-copy-activity-source-model"></a>Käll modell för äldre kopierings aktiviteter

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **HdfsSource** |Ja |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. OBS! om rekursivt har angetts till true och Sink är ett filbaserat Arkiv, kopieras inte den tomma mappen/undermappen till mottagaren.<br/>Tillåtna värden är: **Sant** (standard), **falskt** | Nej |
| distcpSettings | Egenskaps grupp vid användning av HDFS DistCp. | Nej |
| resourceManagerEndpoint | Garn Resource Manager-slutpunkt | Ja om du använder DistCp |
| tempScriptPath | En mappsökväg som används för att lagra Temp DistCp-kommandoskriptet. Skript filen genereras av Data Factory och tas bort när kopierings jobbet är klart. | Ja om du använder DistCp |
| distcpOptions | Ytterligare alternativ har angetts för DistCp-kommandot. | Nej |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej |

**Exempel: HDFS-källa i kopierings aktivitet med DistCp**

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
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

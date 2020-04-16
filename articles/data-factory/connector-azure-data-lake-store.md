---
title: Kopiera data till eller från Azure Data Lake Storage Gen1
description: Lär dig hur du kopierar data från källdatalager som stöds till Azure Data Lake Store eller från DataSjölagret till sink-butiker som stöds med hjälp av Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 61bb8fe950de8cd9be91bc76bd24aa0151f3fb79
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415416"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Kopiera data till eller från Azure Data Lake Storage Gen1 med Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-azure-datalake-connector.md)
> * [Aktuell version](connector-azure-data-lake-store.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du kopierar data till och från Azure Data Lake Storage Gen1. Mer information om Azure Data Factory finns i den [inledande artikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Data Lake Storage Gen1-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md) 
- [Mappa dataflöde](concepts-data-flow-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

Med den här kontakten kan du:

- Kopiera filer med hjälp av någon av följande autentiseringsmetoder: tjänstens huvudnamn eller hanterade identiteter för Azure-resurser.
- Kopiera filer som de är eller tolka eller generera filer med [de filformat och komprimeringskoderna som stöds](supported-file-formats-and-compression-codecs.md).
- [Bevara ACL:er](#preserve-acls-to-data-lake-storage-gen2) när du kopierar till Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> Om du kopierar data med hjälp av den självvärderade integrationskörningen `<ADLS account name>.azuredatalakestore.net` `login.microsoftonline.com/<tenant>/oauth2/token` konfigurerar du företagsbrandväggen så att utgående trafik tillåts till och på port 443. Den senare är Azure Security Token Service som integrationen körtid måste kommunicera med för att få åtkomsttoken.

## <a name="get-started"></a>Kom igång

> [!TIP]
> En genomgång av hur du använder Azure Data Lake Store-kopplingen finns [i Läsa in data i Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure Data Lake Store.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade azure datasjölagringstjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen `type` måste anges till **AzureDataLakeStore**. | Ja |
| dataLakeStoreUri | Information om Azure Data Lake Store-kontot. Den här informationen tar något `https://[accountname].azuredatalakestore.net/webhdfs/v1` av `adl://[accountname].azuredatalakestore.net/`följande format: eller . | Ja |
| subscriptionId | Det Azure-prenumerations-ID som Data Lake Store-kontot tillhör. | Krävs för diskbänk |
| resourceGroupName | Namnet på Azure-resursgruppen som datasjölagringskontot tillhör. | Krävs för diskbänk |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure-integreringskörningen eller en självvärderad integrationskörning om ditt datalager finns i ett privat nätverk. Om den här egenskapen inte anges används standardkörningen för Azure-integrering. |Inga |

### <a name="use-service-principal-authentication"></a>Använd autentisering av tjänstens huvudnamn

Så här använder du autentisering av tjänstens huvudnamn.

1. Registrera en programentitet i Azure Active Directory och ge den åtkomst till DataSjö store. Detaljerade steg finns i [Autentisering](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)från tjänst till tjänst . Anteckna följande värden, som du använder för att definiera den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. Ge tjänstens huvudnamn rätt tillstånd. Se exempel på hur behörighet fungerar i Data Lake Storage Gen1 från [Åtkomstkontroll i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Som källa:** I **Data Explorer** > **Access**, bevilja åtminstone **Kör** behörighet för alla uppströms mappar inklusive roten, tillsammans med läsbehörighet för filerna att kopiera. **Read** Du kan välja att lägga till **i den här mappen och alla underordnade** för rekursiv, och lägga till som en **åtkomstbehörighet och en standardbehörighetspost**. Det finns inget krav på åtkomstkontroll på kontonivå (IAM).
    - **Som mottagare:** I **Data explorer** > **Access**, bevilja åtminstone **Kör** behörighet för alla uppströmsmappar, inklusive roten, tillsammans med **skrivbehörighet** för sink-mappen. Du kan välja att lägga till **i den här mappen och alla underordnade** för rekursiv, och lägga till som en **åtkomstbehörighet och en standardbehörighetspost**. Om du använder en Azure-integreringskörning för att kopiera (både källa och diskho finns i molnet) bevilja du åtminstone **reader-rollen** i IAM för att låta Data Factory identifiera regionen för DataSjölagring. Om du vill undvika den här IAM-rollen skapar du uttryckligen [en Azure-integreringskörning](create-azure-integration-runtime.md#create-azure-ir) med platsen för Data Lake Store. Om ditt DataSjölag till exempel finns i Västeuropa skapar du en Azure-integreringskörning med plats inställd på "Västeuropa". Associera dem i den länkade tjänsten DataSjölager enligt följande exempel.

Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här `SecureString` fältet som en för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Ange klientinformation, till exempel domännamn eller klient-ID, som programmet finns under. Du kan hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Ja |

**Exempel:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Använda hanterade identiteter för Azure-resursautentisering

En datafabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md), som representerar den här specifika datafabriken. Du kan direkt använda den här hanterade identiteten för DataSjölagringsautentisering, på samma sätt som med ditt eget tjänsthuvudnamn. Det gör att denna utsedda fabrik för att komma åt och kopiera data till eller från Data Lake Store.

Så här använder du hanterade identiteter för Azure-resursautentisering.

1. [Hämta datafabrikens hanterade identitetsinformation](data-factory-service-identity.md#retrieve-managed-identity) genom att kopiera värdet för "Service Identity Application ID" som genereras tillsammans med din fabrik.

2. Bevilja den hanterade identitetsåtkomsten till Data Lake Store. Se exempel på hur behörighet fungerar i Data Lake Storage Gen1 från [Åtkomstkontroll i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Som källa:** I **Data Explorer** > **Access**, bevilja åtminstone **Kör** behörighet för alla uppströms mappar inklusive roten, tillsammans med läsbehörighet för filerna att kopiera. **Read** Du kan välja att lägga till **i den här mappen och alla underordnade** för rekursiv, och lägga till som en **åtkomstbehörighet och en standardbehörighetspost**. Det finns inget krav på åtkomstkontroll på kontonivå (IAM).
    - **Som mottagare:** I **Data explorer** > **Access**, bevilja åtminstone **Kör** behörighet för alla uppströmsmappar, inklusive roten, tillsammans med **skrivbehörighet** för sink-mappen. Du kan välja att lägga till **i den här mappen och alla underordnade** för rekursiv, och lägga till som en **åtkomstbehörighet och en standardbehörighetspost**. Om du använder en Azure-integreringskörning för att kopiera (både källa och diskho finns i molnet) bevilja du åtminstone **reader-rollen** i IAM för att låta Data Factory identifiera regionen för DataSjölagring. Om du vill undvika den här IAM-rollen skapar du uttryckligen [en Azure-integreringskörning](create-azure-integration-runtime.md#create-azure-ir) med platsen för Data Lake Store. Associera dem i den länkade tjänsten DataSjölager enligt följande exempel.

I Azure Data Factory behöver du inte ange några egenskaper förutom den allmänna datasjölagringsinformationen i den länkade tjänsten.

**Exempel:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
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

Följande egenskaper stöds för Azure Data Lake `location` Store Gen1 under inställningar i den formatbaserade datauppsättningen:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen type `location` under i datauppsättningen måste anges till **AzureDataLakeStoreLocation**. | Ja      |
| folderPath | Sökvägen till en mapp. Om du vill använda ett jokertecken för att filtrera mappar hoppar du över den här inställningen och anger den i aktivitetskällans inställningar. | Inga       |
| fileName   | Filnamnet under den angivna folderPath. Om du vill använda ett jokertecken för att filtrera filer hoppar du över den här inställningen och anger den i aktivitetskällans inställningar. | Inga       |

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Data Lake Store-källa och diskho.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store som källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Azure Data Lake `storeSettings` Store Gen1 under inställningar i den formatbaserade kopieringskällan:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Typegenskapen under `storeSettings` måste anges till **AzureDataLakeStoreReadSettings**. | Ja                                           |
| Rekursiv                | Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. När rekursiv är inställd på true och diskhon är ett filbaserat arkiv kopieras eller skapas inte en tom mapp eller undermapp i diskhon. Tillåtna värden är **sanna** (standard) och **falska**. | Inga                                            |
| jokerteckenMappspath       | Mappsökvägen med jokertecken för att filtrera källmappar. <br>Tillåtna jokertecken `*` är (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken). Används `^` för att fly om ditt faktiska mappnamn har ett jokertecken eller den här flykttecknet inuti. <br>Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). | Inga                                            |
| jokerteckenFileName         | Filnamnet med jokertecken under den angivna mappenPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken `*` är (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken). Används `^` för att fly om ditt faktiska mappnamn har ett jokertecken eller den här flykttecknet inuti. Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). | Ja `fileName` om inte anges i datauppsättningen |
| modifiedDatetimeStart    | Filfilter baserat på attributet Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` `modifiedDatetimeEnd`ligger inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet är markerade. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet är markerade. | Inga                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Inga                                            |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till lagringsarkivet samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga                                            |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store som diskho

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Azure Data Lake `storeSettings` Store Gen1 under inställningar i den formatbaserade kopian sink:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Typegenskapen under `storeSettings` måste anges till **AzureDataLakeStoreWriteSettings**. | Ja      |
| kopieraBehavior             | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>- PreserveHierarchy (standard):</b>Bevarar filhierarkin i målmappen. Källfilens relativa sökväg till källmappen är identisk med målfilens relativa sökväg till målmappen.<br/><b>- FlattenHierarchy:</b>Alla filer från källmappen är i den första nivån i målmappen. Målfilerna har automatiskt urartade namn. <br/><b>- MergeFiles:</b>Sammanfogar alla filer från källmappen till en fil. Om filnamnet anges är det kopplade filnamnet det angivna namnet. Annars är det ett autogenererat filnamn. | Inga       |
| expiryDateTime | Anger utgångstiden för de skrivna filerna. Tiden tillämpas på UTC-tiden i formatet "2020-03-01T08:00:00Z". Som standard är det NULL, vilket innebär att de skrivna filerna aldrig har upphört att gälla. | Inga |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till datalagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga       |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureDataLakeStoreWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp- och filfilter

I det här avsnittet beskrivs det resulterande beteendet för mappsökvägen och filnamnet med jokerteckenfilter.

| folderPath | fileName | Rekursiv | Källmappstruktur och filterresultat (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Tom, använd standard) | false | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `Folder*` | (Tom, använd standard) | true | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `Folder*` | `*.csv` | false | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `Folder*` | `*.csv` | true | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exempel på kopieringsbeteende

I det här avsnittet beskrivs kopieringens resulterande beteende för olika kombinationer av `recursive` och `copyBehavior` värden.

| Rekursiv | kopieraBehavior | Källmappstruktur | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |bevaraHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmapp1 skapas med samma struktur som källan:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5. |
| true |plattaHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File5 |
| true |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 innehåll slås samman till en fil, med ett autogenererat filnamn. |
| false |bevaraHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmapp1 skapas med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |
| false |plattaHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmapp1 skapas med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File2<br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |
| false |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmapp1 skapas med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + File2-innehåll sammanfogas till en fil med automatiskt uppargat filnamn. autogenererat namn för Arkiv1<br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Bevara ACL:er till DataSjölagring Gen2

>[!TIP]
>Information om hur du kopierar data från Azure Data Lake Storage Gen1 till Gen2 i allmänhet finns i [Kopiera data från Azure Data Lake Storage Gen1 till Gen2 med Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) för en genomgång och metodtips.

Om du vill replikera åtkomstkontrollistorna (ACL: er) tillsammans med datafiler när du uppgraderar från Data Lake Storage Gen1 till Data Lake Storage Gen2 läser [du Bevara ACL:er från DataSjölagring gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Mappa dataflödesegenskaper

När du omvandlar data i mappningsdataflödet kan du läsa och skriva filer från Azure Data Lake Storage Gen1 i JSON-, Avro-, avgränsat text- eller parkettformat. Mer information finns i [källomvandling](data-flow-source.md) och [sink-omvandling](data-flow-sink.md) i funktionen för mappningsdataflöde.

### <a name="source-transformation"></a>Omvandling av källa

I källomvandlingen kan du läsa från en behållare, mapp eller enskild fil i Azure Data Lake Storage Gen1. På fliken **Källalternativ** kan du hantera hur filerna läss. 

![Alternativ för källa](media/data-flow/sourceOptions1.png "Alternativ för källa")

**Jokerteckens sökväg:** Med hjälp av ett jokerteckenmönster instruerar ADF att loopa igenom varje matchande mapp och fil i en enda källomvandling. Detta är ett effektivt sätt att bearbeta flera filer inom ett enda flöde. Lägg till flera matchningsmönster med +-tecknet som visas när du hovrar över ditt befintliga jokerteckenmönster.

Välj en serie filer som matchar ett mönster från källbehållaren. Endast behållare kan anges i datauppsättningen. Jokertecknets sökväg måste därför också innehålla mappsökvägen från rotmappen.

Exempel på jokertecken:

* ```*```Representerar alla teckenuppsättningar
* ```**```Representerar rekursiv katalogkapsling
* ```?```Ersätter ett tecken
* ```[]```Matchar ett av flera tecken inom parenteserna

* ```/data/sales/**/*.csv```Hämtar alla csv-filer under /data/sales
* ```/data/sales/20??/**/```Hämtar alla filer på 1900-talet
* ```/data/sales/*/*/*.csv```Hämtar csv-filer två nivåer under /data/sales
* ```/data/sales/2004/*/12/[XY]1?.csv```Hämtar alla csv-filer under 2004 i december med början med X eller Y som föregås av ett tvåsiffrigt nummer

**Sökvägen till partitionsrot:** Om du har partitionerade mappar i ```key=value``` filkällan med ett format (till exempel year=2019) kan du tilldela den översta nivån i det partitionsmappträdet till ett kolumnnamn i dataflödesdataströmmen.

Ange först ett jokertecken så att alla sökvägar som är de partitionerade mapparna plus lövfilerna som du vill läsa.

![Inställningar för partitionskälla](media/data-flow/partfile2.png "Partitionsfilinställning")

Använd inställningen Partition Root Path för att definiera vad den översta nivån i mappstrukturen är. När du visar innehållet i dina data via en förhandsgranskning av data ser du att ADF lägger till de lösta partitionerna som finns i var och en av mappnivåerna.

![Partitionsrotsökväg](media/data-flow/partfile1.png "Förhandsgranskning av partitionsrotsökväg")

**Lista över filer:** Det här är en filuppsättning. Skapa en textfil som innehåller en lista över relativa sökvägsfiler som ska bearbetas. Peka på den här textfilen.

**Kolumn för att lagra filnamn:** Lagra namnet på källfilen i en kolumn i dina data. Ange ett nytt kolumnnamn här för att lagra filnamnssträngen.

**Efter avslutad:** Välj att inte göra något med källfilen när dataflödet har körts, källfilen tas bort eller flyttar källfilen. Sökvägarna för flytten är relativa.

Om du vill flytta källfiler till en annan plats efterbearbetningen väljer du först "Flytta" för filåtgärd. Ställ sedan in katalogen "från". Om du inte använder några jokertecken för sökvägen kommer inställningen "från" att vara samma mapp som källmappen.

Om du har en källsökväg med jokertecken ser syntaxen ut så här nedan:

```/data/sales/20??/**/*.csv```

Du kan ange "från" som

```/data/sales```

Och "till" som

```/backup/priorSales```

I det här fallet flyttas alla filer som har hämtats under /data/sales till /backup/priorSales.

> [!NOTE]
> Filåtgärder körs bara när du startar dataflödet från en pipeline-körning (en pipeline-felsökning eller körningskörning) som använder aktiviteten Kör dataflöde i en pipeline. Filåtgärder körs *inte* i felsökningsläge för dataflöde.

**Filtrera efter senast ändrad:** Du kan filtrera vilka filer du bearbetar genom att ange ett datumintervall för när de senast ändrades. Alla datumtider finns i UTC. 

### <a name="sink-properties"></a>Sink-egenskaper

I sink-omvandlingen kan du skriva till antingen en behållare eller mapp i Azure Data Lake Storage Gen1. På fliken **Inställningar** kan du hantera hur filerna skrivs.

![sink alternativ](media/data-flow/file-sink-settings.png "sink alternativ")

**Rensa mappen:** Avgör om målmappen rensas innan data skrivs eller inte.

**Alternativet Filnamn:** Bestämmer hur målfilerna namnges i målmappen. Alternativen för filnamn är:
   * **Standard**: Tillåt Spark att namnge filer baserat på DEL-standardvärden.
   * **Mönster**: Ange ett mönster som räknar upp dina utdatafiler per partition. Till exempel, **lån [n].csv** kommer att skapa loans1.csv, loans2.csv, och så vidare.
   * **Per partition**: Ange ett filnamn per partition.
   * **Som data i kolumnen**: Ange utdatafilen till värdet för en kolumn. Sökvägen är relativ till datamängdsbehållaren, inte målmappen. Om du har en mappsökväg i datauppsättningen åsidosätts den.
   * **Utdata till en enda fil**: Kombinera de partitionerade utdatafilerna till en enda namngiven fil. Sökvägen är relativ till datauppsättningsmappen. Tänk på att åtgärden för kopplad te kan eventuellt misslyckas baserat på nodstorlek. Det här alternativet rekommenderas inte för stora datauppsättningar.

**Citera alla:** Bestämmer om alla värden ska omslutas inom citationstecken

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Aktivitetsegenskaper för GetMetadata

Om du vill veta mer om egenskaperna kontrollerar du [GetMetadata-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Ta bort aktivitetsegenskaper

Om du vill veta mer om egenskaperna kontrollerar du [Ta bort aktivitet](delete-activity.md)

## <a name="legacy-models"></a>Äldre modeller

>[!NOTE]
>Följande modeller stöds fortfarande i sig för bakåtkompatibilitet. Du föreslås använda den nya modellen som nämns i ovanstående avsnitt framöver, och ADF-redigeringsgränssnittet har bytt till att generera den nya modellen.

### <a name="legacy-dataset-model"></a>Äldre datauppsättningsmodell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för datauppsättningen måste anges till **AzureDataLakeStoreFile**. |Ja |
| folderPath | Sökväg till mappen i DataSjö store. Om det inte anges pekar den på roten. <br/><br/>Jokerteckenfilter stöds. Tillåtna jokertecken `*` är (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken). Används `^` för att fly om ditt faktiska mappnamn har ett jokertecken eller den här flykttecknet inuti. <br/><br/>Till exempel: rootfolder/subfolder/. Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). |Inga |
| fileName | Namn eller jokerteckenfilter för filerna under den angivna "folderPath". Om du inte anger något värde för den här egenskapen pekar datauppsättningen på alla filer i mappen. <br/><br/>För filter är `*` de tillåtna jokerteckenen (matchar `?` noll eller fler tecken) och (matchar noll eller ett tecken).<br/>- Exempel 1:`"fileName": "*.csv"`<br/>- Exempel 2:`"fileName": "???20180427.txt"`<br/>Används `^` för att fly om ditt faktiska filnamn har ett jokertecken eller den här flykttecknet inuti.<br/><br/>När filnamn inte anges för en utdatauppsättning och **bevaraHierarchy** inte anges i aktivitetsmottagaren, genererar kopieringsaktiviteten automatiskt filnamnet med följande mönster: "*Data.[ aktivitetskörning ID GUID]. [GUID om FlattenHierarchy]. [format om det är konfigurerat]. [komprimering om den är konfigurerad]*", till exempel "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Om du kopierar från en tabellkälla med hjälp av ett tabellnamn i stället för en fråga, är namnmönstret "*[tabellnamn].[ format]. [komprimering om den är konfigurerad]*", till exempel "MyTable.csv". |Inga |
| modifiedDatetimeStart | Filfilter baserat på attributet Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` `modifiedDatetimeEnd`ligger inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Den övergripande prestandan för data förflyttning påverkas genom att aktivera den här inställningen när du vill göra filfilter med stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet är markerade. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet är markerade.| Inga |
| modifiedDatetimeEnd | Filfilter baserat på attributet Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` `modifiedDatetimeEnd`ligger inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Den övergripande prestandan för data förflyttning påverkas genom att aktivera den här inställningen när du vill göra filfilter med stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet är markerade. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet är markerade.| Inga |
| format | Om du vill kopiera filer som mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata.<br/><br/>Om du vill tolka eller generera filer med ett visst format stöds följande filformattyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**och **ParquetFormat**. Ange **type** typegenskapen under **format** till ett av dessa värden. Mer information finns i avsnitten [Textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc-format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parkettformat.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nej (endast för binärt kopieringsscenario) |
| komprimering | Ange typ och komprimeringsnivå för data. Mer information finns i [Filformat och komprimeringskodicer som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Typer som stöds är **GZip**, **Deflate**, **BZip2**och **ZipDeflate**.<br/>Nivåer som stöds är **optimala** och **snabbaste.** |Inga |

>[!TIP]
>Om du vill kopiera alla filer under en mapp anger du endast **folderPath.**<br>Om du vill kopiera en enskild fil med ett visst namn anger du **folderPath** med en mappdel och **filnamn** med ett filnamn.<br>Om du vill kopiera en delmängd filer under en mapp anger du **folderPath** med en mappdel och **filnamn** med ett jokerteckenfilter. 

**Exempel:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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
| typ | Egenskapen `type` för kopiera aktivitetskällan måste anges till **AzureDataLakeStoreSource**. |Ja |
| Rekursiv | Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. När `recursive` värdet är true och handfatet är ett filbaserat arkiv kopieras eller skapas inte en tom mapp eller undermapp i diskhon. Tillåtna värden är **sanna** (standard) och **falska**. | Inga |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till datalagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Äldre modell för referensmodell för kopierad aktivitet

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen `type` för kopiera aktivitetsmottagaren måste anges till **AzureDataLakeStoreSink**. |Ja |
| kopieraBehavior | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>- PreserveHierarchy (standard):</b>Bevarar filhierarkin i målmappen. Källfilens relativa sökväg till källmappen är identisk med målfilens relativa sökväg till målmappen.<br/><b>- FlattenHierarchy:</b>Alla filer från källmappen är i den första nivån i målmappen. Målfilerna har automatiskt urartade namn. <br/><b>- MergeFiles:</b>Sammanfogar alla filer från källmappen till en fil. Om filnamnet anges är det kopplade filnamnet det angivna namnet. Annars skapas filnamnet automatiskt. | Inga |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till datalagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg

En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

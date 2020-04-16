---
title: Kopiera och omvandla data i Azure Blob-lagring
description: Lär dig hur du kopierar data till och från Blob-lagring och omvandlar data i Blob-lagring med hjälp av Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: b04ff409c95980a1569a2709a475dd8ec74d59b3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415489"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Kopiera och omvandla data i Azure Blob-lagring med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-blob-connector.md)
> * [Aktuell version](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att kopiera data från och till Azure Blob-lagring och använda Dataflöde för att omvandla data i Azure Blob-lagring. Mer information om Azure Data Factory finns i den [inledande artikeln](introduction.md).

>[!TIP]
>För datasjö- eller informationslagermigreringsscenario kan du läsa mer från [Använd Azure Data Factory för att migrera data från datasjöen eller informationslager till Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Blob-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Mappa dataflöde](concepts-data-flow-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

För kopieringsaktivitet stöder den här Blob-lagringsappen:

- Kopiera blobbar till och från azure-lagringskonton för allmänt ändamål och frekvent/lågbloblagring. 
- Kopiera blobbar med hjälp av kontonyckel, tjänstsignatur för delad åtkomst, tjänsthuvudnamn eller hanterade identiteter för Azure-resurser autentiseringar.
- Kopiera blobbar från block-, tilläggs- eller sidblobar och kopiera data till endast blockera blobbar.
- Kopiera blobbar som är eller tolka eller generera blobbar med [filformat som stöds och komprimeringskodifier .](supported-file-formats-and-compression-codecs.md)
- [Bevara filmetadata under kopieringen](#preserve-metadata-during-copy).

>[!IMPORTANT]
>Om du aktiverar alternativet **Tillåt betrodda Microsoft-tjänster för att komma åt det här lagringskontot** på Azure Storage-brandväggsinställningar och vill använda Azure-integreringskörning för att ansluta till din Blob Storage, måste du använda [autentisering av hanterad identitet](#managed-identity).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för Blob-lagring.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Azure Blob connector stöder följande autentiseringstyper, se motsvarande avsnitt om information:

- [Autentisering av kontonyckel](#account-key-authentication)
- [Autentisering av signatur för delad åtkomst](#shared-access-signature-authentication)
- [Autentisering av tjänstens huvudnamn](#service-principal-authentication)
- [Hanterade identiteter för Azure-resursautentisering](#managed-identity)

>[!NOTE]
>När du använder PolyBase för att läsa in data i SQL Data Warehouse, om din källa eller mellanlagring Blob-lagring är konfigurerad med slutpunkten för virtuellt nätverk, måste du använda hanterad identitetsautentisering enligt polybasens sida och använda Självvärdförd integrationskörning med version 3.18 eller högre. Se avsnittet [autentisering av hanterad identitet](#managed-identity) med fler konfigurationsförutnämningar.

>[!NOTE]
>HDInsights- och Azure Machine Learning-aktiviteter stöder endast azure blob-lagringskontonyckelautentisering.

### <a name="account-key-authentication"></a>Autentisering av kontonyckel

Så här använder du autentisering av lagringskontonyckel:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till **AzureBlobStorage** (föreslaget) eller **AzureStorage** (se anteckningar nedan). |Ja |
| Connectionstring | Ange den information som behövs för att ansluta till lagring för egenskapen connectionString. <br/> Du kan också placera kontonyckeln i `accountKey` Azure Key Vault och hämta konfigurationen ur anslutningssträngen. Mer information finns i följande exempel och [Store-autentiseringsuppgifter i](store-credentials-in-key-vault.md) azure key vault-artikeln. |Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime (om ditt datalager finns i ett privat nätverk). Om det inte anges används standardkörningen för Azure Integration. |Inga |

>[!NOTE]
>Slutpunkt för sekundär blob-tjänst stöds inte när du använder autentisering av kontonyckel. Du kan använda andra autentiseringstyper.

>[!NOTE]
>Om du använde "AzureStorage" typ länkad tjänst, stöds den fortfarande som den är, medan du föreslås använda den här nya "AzureBlobStorage"-länkade tjänsttypen framöver.

**Exempel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: store-kontonyckel i Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Autentisering av signatur för delad åtkomst

En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagringskonto. Du kan använda en signatur för delad åtkomst för att ge en klient begränsade behörigheter till objekt i ditt lagringskonto under en angiven tid. Du behöver inte dela dina kontoåtkomstnycklar. Signaturen för delad åtkomst är en URI som i frågeparametrarna omfattar all information som krävs för autentiserat åtkomst till en lagringsresurs. För att komma åt lagringsresurser med signaturen för delad åtkomst behöver klienten bara skicka in signaturen för delad åtkomst till lämplig konstruktor eller metod. Mer information om signaturer för delad åtkomst finns i Signaturer för [delad åtkomst: Förstå signaturmodellen för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory stöder nu både **signaturer för delad åtkomst till tjänsten** och **signaturer för delad åtkomst till kontot**. Mer information om signaturer för delad åtkomst finns i [Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SAS (Shared Access Signatures).](../storage/common/storage-sas-overview.md)
>- I senare datauppsättningskonfiguration är mappsökvägen den absoluta sökvägen från behållarnivå. Du måste konfigurera en som är justerad med sökvägen i SAS URI.

Om du vill använda autentisering av signatur för delad åtkomst stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till **AzureBlobStorage** (föreslaget) eller **AzureStorage** (se anteckningar nedan). |Ja |
| sasuri (samma sak) | Ange signatur-URI för delad åtkomst till lagringsresurserna, till exempel blob/container. <br/>Markera det här fältet som en SecureString för att lagra det säkert i Data Factory. Du kan också placera SAS-token i Azure Key Vault för att utnyttja automatisk rotation och ta bort tokendelen. Mer information finns i följande exempel och [Store-autentiseringsuppgifter i](store-credentials-in-key-vault.md) azure key vault-artikeln. |Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller self-hosted Integration Runtime (om ditt datalager finns i ett privat nätverk). Om det inte anges används standardkörningen för Azure Integration. |Inga |

>[!NOTE]
>Om du använde "AzureStorage" typ länkad tjänst, stöds den fortfarande som den är, medan du föreslås använda den här nya "AzureBlobStorage"-länkade tjänsttypen framöver.

**Exempel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: store-kontonyckel i Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

När du skapar en URI för delad åtkomst-signatur bör du tänka på följande:

- Ange lämpliga läs-/skrivbehörigheter för objekt baserat på hur den länkade tjänsten (läsa, skriva, läsa/skriva) används i din datafabrik.
- Ställ **in utgångstid** på lämpligt sätt. Kontrollera att åtkomsten till Lagringsobjekt inte upphör att gälla inom den aktiva perioden för pipelinen.
- URI ska skapas i rätt behållare/blob baserat på behovet. En signatur-URI för delad åtkomst till en blob gör det möjligt för Data Factory att komma åt just den bloben. En uri för delad åtkomstsignatur till en Blob-lagringsbehållare gör att Data Factory kan iterera via blobbar i den behållaren. Om du vill ge åtkomst till fler eller färre objekt senare, eller för att uppdatera signaturen URI för delad åtkomst, kom ihåg att uppdatera den länkade tjänsten med den nya URI:n.

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

För principautentisering av Azure Storage-tjänsten i allmänhet, se [Autentisera åtkomst till Azure Storage med Azure Active Directory](../storage/common/storage-auth-aad.md).

Så här använder du autentisering av tjänstens huvudnamn:

1. Registrera en programentitet i Azure Active Directory (Azure AD) genom att följa [Registrera ditt program med en Azure AD-klientorganisation](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden, som du använder för att definiera den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. Bevilja tjänstens huvudnamn rätt behörighet i Azure Blob-lagring. Se [Hantera åtkomsträttigheter till Azure Storage-data med RBAC](../storage/common/storage-auth-aad-rbac.md) med mer information om rollerna.

    - **Som källa**, i Access Control (IAM), bevilja minst **Storage Blob Data Reader** roll.
    - **Som mottagare**, i Åtkomstkontroll (IAM), bevilja minst **Storage Blob Data Contributor** roll.

Dessa egenskaper stöds för en Azure Blob storage-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AzureBlobStorage**. |Ja |
| serviceEndpoint | Ange slutpunkten för Azure Blob storage-tjänsten med mönstret `https://<accountName>.blob.core.windows.net/`. |Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en **SecureString** för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime (om ditt datalager finns i ett privat nätverk). Om det inte anges används standardkörningen för Azure Integration. |Inga |

>[!NOTE]
>Tjänsthuvudnamnsautentisering stöds endast av "AzureBlobStorage" typlänkad tjänst men inte tidigare "AzureStorage"-typlänkad tjänst.

**Exempel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Hanterade identiteter för Azure-resursautentisering

En datafabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md), som representerar den här specifika datafabriken. Du kan direkt använda den här hanterade identiteten för Blob-lagringsautentisering som liknar att använda ditt eget tjänsthuvudnamn. Det gör att denna utsedda fabrik för att komma åt och kopiera data från / till din Blob lagring.

Se [Autentisera åtkomst till Azure Storage med Azure Active Directory](../storage/common/storage-auth-aad.md) för Azure Storage-autentisering i allmänhet. Så här använder du hanterade identiteter för Azure-resursautentisering:

1. [Hämta data fabrikshanterad identitetsinformation](data-factory-service-identity.md#retrieve-managed-identity) genom att kopiera värdet för **hanterat identitetsobjekt-ID** som genereras tillsammans med din fabrik.

2. Bevilja den hanterade identiteten rätt behörighet i Azure Blob-lagring. Se [Hantera åtkomsträttigheter till Azure Storage-data med RBAC](../storage/common/storage-auth-aad-rbac.md) med mer information om rollerna.

    - **Som källa**, i Access Control (IAM), bevilja minst **Storage Blob Data Reader** roll.
    - **Som mottagare**, i Åtkomstkontroll (IAM), bevilja minst **Storage Blob Data Contributor** roll.

>[!IMPORTANT]
>Om du använder PolyBase för att läsa in data från Blob (som källa eller som mellanlagring) i SQL Data Warehouse, när du använder hanterad identitetsautentisering för Blob, kontrollerar du att du också följer steg 1 och 2 i [den här vägledningen](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) till 1) registrera SQL Database-servern med Azure Active Directory (Azure AD) och 2) tilldela rollen Storage Blob Data Contributor till sql Database-servern. resten hanteras av Data Factory. Om din Blob-lagring är konfigurerad med en Azure Virtual Network-slutpunkt, för att använda PolyBase för att läsa in data från den, måste du använda autentisering av hanterad identitet som krävs av PolyBase.

Dessa egenskaper stöds för en Azure Blob storage-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AzureBlobStorage**. |Ja |
| serviceEndpoint | Ange slutpunkten för Azure Blob storage-tjänsten med mönstret `https://<accountName>.blob.core.windows.net/`. |Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime (om ditt datalager finns i ett privat nätverk). Om det inte anges används standardkörningen för Azure Integration. |Inga |

> [!NOTE]
> Hanterade identiteter för Azure-resursautentisering stöds endast av "AzureBlobStorage" typlänkad tjänst men inte tidigare "AzureStorage"-typlänkad tjänst. 

**Exempel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
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

Följande egenskaper stöds för Azure `location` Blob under inställningar i formatbaserad datauppsättning:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen Type property för platsen i datauppsättningen måste anges till **AzureBlobStorageLocation**. | Ja      |
| container  | Blob-behållaren.                                          | Ja      |
| folderPath | Sökvägen till mappen under den angivna behållaren. Om du vill använda jokertecken för att filtrera mappen hoppar du över den här inställningen och anger i inställningarna för aktivitetskällan. | Inga       |
| fileName   | Filnamnet under den angivna behållaren + folderPath. Om du vill använda jokertecken för att filtrera filer hoppar du över den här inställningen och anger i inställningarna för aktivitetskällan. | Inga       |

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Blob-lagringskällan och diskhon.

### <a name="blob-storage-as-a-source-type"></a>Blob-lagring som källtyp

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Azure `storeSettings` Blob under inställningar i formatbaserad kopieringskälla:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Egenskapen type `storeSettings` under måste anges till **AzureBlobStorageReadSettings**. | Ja                                           |
| Rekursiv                | Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. Observera att när rekursiv är inställd på true och diskhon är ett filbaserat arkiv kopieras eller skapas inte en tom mapp eller undermapp i diskhon. Tillåtna värden är **sanna** (standard) och **falska**. | Inga                                            |
| Prefix                   | Prefix för blob-namnet under den angivna behållaren som konfigurerats i datauppsättningen för att filtrera källblobar. Blobbar vars namn börjar med det här prefixet är markerade. <br>Gäller endast `wildcardFolderPath` `wildcardFileName` när och egenskaper inte har angetts. | Inga                                                          |
| jokerteckenMappspath       | Mappsökvägen med jokertecken under den angivna behållaren som konfigurerats i datauppsättningen för att filtrera källmappar. <br>Tillåtna jokertecken `*` är: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken); används `^` för att fly om ditt faktiska mappnamn har jokertecken eller den här flyktteckenet inuti. <br>Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). | Inga                                            |
| jokerteckenFileName         | Filnamnet med jokertecken under den angivna behållaren + mappPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken `*` är: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken); används `^` för att fly om ditt faktiska mappnamn har jokertecken eller den här flyktteckenet inuti.  Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). | Ja `fileName` om inte anges i datauppsättningen |
| modifiedDatetimeStart    | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas. | Inga                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Inga                                            |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till lagringsarkivet samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga                                            |

> [!NOTE]
> För parett/avgränsat textformat stöds fortfarande kopiaaktivitetskälla för **BlobSource** som nämns i nästa avsnitt, eftersom den är för bakåtkompatibilitet. Du föreslås använda den nya modellen framöver och ADF-utvecklingsgränssnittet har bytt till att generera dessa nya typer.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSettings",
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

### <a name="blob-storage-as-a-sink-type"></a>Blob-lagring som en sink-typ

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Azure `storeSettings` Blob under inställningar i formatbaserad kopia sink:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Typegenskapen under `storeSettings` måste anges till **AzureBlobStorageWriteSettings**. | Ja      |
| kopieraBehavior             | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>- PreserveHierarchy (standard):</b>Bevarar filhierarkin i målmappen. Den relativa sökvägen för källfilen till källmappen är identisk med målfilens relativa sökväg till målmappen.<br/><b>- FlattenHierarchy:</b>Alla filer från källmappen är i den första nivån i målmappen. Målfilerna har automatiskt urartade namn. <br/><b>- MergeFiles:</b>Sammanfogar alla filer från källmappen till en fil. Om fil- eller blobnamnet anges är det kopplade filnamnet det angivna namnet. Annars är det ett autogenererat filnamn. | Inga       |
| blockSizeInMB | Ange blockstorleken i MB som används för att skriva data för att blockera blobbar. Läs mer [om Block Blobbar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Tillåtet värde är **mellan 4 och 100 MB**. <br/>Som standard bestämmer ADF automatiskt blockstorleken baserat på källlagringstypen och data. För icke-binär kopia till Blob är standardblockstorleken 100 MB för att få plats med högst 4,95 TB-data. Det kanske inte är optimalt när dina data inte är stora, särskilt när du använder Självvärdförd integrationskörningstid med dålig nätverkstimeout eller prestandaproblem. Du kan uttryckligen ange en blockstorlek, medan se till att blockSizeInMB*50000 är tillräckligt stor för att lagra data, annars misslyckas kopieringsaktivitetskörningen. | Inga |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till lagringsarkivet samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga       |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSettings",
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
| `container/Folder*` | (tom, använd standard) | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `container/Folder*` | (tom, använd standard) | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `container/Folder*` | `*.csv` | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `container/Folder*` | `*.csv` | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Några rekursiva och copyBehavior exempel

I det här avsnittet beskrivs det resulterande beteendet för kopieringsåtgärden för olika kombinationer av rekursiva värden och copyBehavior-värden.

| Rekursiv | kopieraBehavior | Källmappstruktur | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |bevaraHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmappen Folder1 skapas med samma struktur som källan:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 |
| true |plattaHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File5 |
| true |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 innehåll slås samman till en fil med ett autogenererat filnamn. |
| false |bevaraHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmappen Folder1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |
| false |plattaHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmappen Folder1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File2<br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |
| false |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmappen Folder1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + File2-innehåll sammanfogas till en fil med ett filnamn som skapas automatiskt. autogenererat namn för Arkiv1<br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |

## <a name="preserve-metadata-during-copy"></a>Bevara metadata under kopiering

När du kopierar filer från Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 till Azure Data Lake Storage Gen2/Azure Blob kan du välja att bevara filmetadata tillsammans med data. Läs mer från [Bevara metadata](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Mappa dataflödesegenskaper

När du omvandlar data i mappningsdataflödet kan du läsa och skriva filer från Azure Blob Storage i JSON-, Avro-, avgränsat text- eller parkettformat. Mer information finns i [källomvandling](data-flow-source.md) och [sink-omvandling](data-flow-sink.md) i funktionen för mappningsdataflöde.

### <a name="source-transformation"></a>Omvandling av källa

I källomvandlingen kan du läsa från en behållare, mapp eller enskild fil i Azure Blob Storage. På fliken **Källalternativ** kan du hantera hur filerna läss. 

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

I sink-omvandlingen kan du skriva till antingen en behållare eller mapp i Azure Blob Storage. På fliken **Inställningar** kan du hantera hur filerna skrivs.

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
| typ | Egenskapen Type property för datauppsättningen måste anges till **AzureBlob**. |Ja |
| folderPath | Sökväg till behållaren och mappen i blob-lagringen. <br/><br/>Jokerteckenfiltret stöds för sökvägen exklusive behållarnamn. Tillåtna jokertecken `*` är: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken); används `^` för att fly om ditt faktiska mappnamn har jokertecken eller den här flyktteckenet inuti. <br/><br/>Exempel: myblobcontainer/myblobfolder/, se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). |Ja för kopierings-/uppslagsaktivitet, Nej för GetMetadata-aktivitet |
| fileName | **Namn eller jokerteckenfilter** för blobben under den angivna "folderPath". Om du inte anger något värde för den här egenskapen pekar datauppsättningen på alla blobbar i mappen. <br/><br/>För filter är tillåtna `*` jokertecken: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken).<br/>- Exempel 1:`"fileName": "*.csv"`<br/>- Exempel 2:`"fileName": "???20180427.txt"`<br/>Används `^` för att fly om ditt faktiska filnamn har jokertecken eller den här flyktteckenet inuti.<br/><br/>När filnamn inte anges för en utdatauppsättning och **bevaraHierarchy** inte anges i aktivitetsmottagaren, genererar kopieringsaktiviteten automatiskt blobnamnet med följande mönster: "*Data.[ aktivitetskörning ID GUID]. [GUID om FlattenHierarchy]. [format om det är konfigurerat]. [komprimering om den är konfigurerad]*", t.ex. Om du kopierar från tabellkälla med tabellnamn i stället för fråga är namnmönstret "*[tabellnamn].[ format]. [komprimering om den är konfigurerad]*", t.ex. |Inga |
| modifiedDatetimeStart | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning kommer att påverkas genom att aktivera den här inställningen när du vill göra filfilter från stora mängder filer. <br/><br/> Egenskaperna kan vara NULL som innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas.| Inga |
| modifiedDatetimeEnd | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning kommer att påverkas genom att aktivera den här inställningen när du vill göra filfilter från stora mängder filer. <br/><br/> Egenskaperna kan vara NULL som innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas.| Inga |
| format | Om du vill kopiera filer som mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i både indata- och utdatauppsättningsdefinitionerna.<br/><br/>Om du vill tolka eller generera filer med ett visst format stöds följande filformattyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**och **ParquetFormat**. Ange **type** typegenskapen under **format** till ett av dessa värden. Mer information finns i avsnitten [Textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc-format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parkettformat.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nej (endast för binärt kopieringsscenario) |
| komprimering | Ange typ och komprimeringsnivå för data. Mer information finns i [Filformat och komprimeringskodicer som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Typer som stöds är **GZip**, **Deflate**, **BZip2**och **ZipDeflate**.<br/>Nivåer som stöds är **optimala** och **snabbaste.** |Inga |

>[!TIP]
>Om du vill kopiera alla blobbar under en mapp anger du endast **folderPath.**<br>Om du vill kopiera en enda blob med ett förnamn anger du **folderPath** med mappdel och **filnamn** med filnamn.<br>Om du vill kopiera en delmängd blobbar under en mapp anger du **folderPath** med mappdel och **filNamn** med jokerteckenfilter. 

**Exempel:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till **BlobSource**. |Ja |
| Rekursiv | Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. Observera att när rekursiv är inställd på true och diskhon är ett filbaserat arkiv kopieras eller skapas inte en tom mapp eller undermapp i diskhon.<br/>Tillåtna värden är **sanna** (standard) och **falska**. | Inga |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till lagringsarkivet samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
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
| typ | Egenskapen Type property för copy activity sink måste anges till **BlobSink**. |Ja |
| kopieraBehavior | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>- PreserveHierarchy (standard):</b>Bevarar filhierarkin i målmappen. Den relativa sökvägen för källfilen till källmappen är identisk med målfilens relativa sökväg till målmappen.<br/><b>- FlattenHierarchy:</b>Alla filer från källmappen är i den första nivån i målmappen. Målfilerna har automatiskt urartade namn. <br/><b>- MergeFiles:</b>Sammanfogar alla filer från källmappen till en fil. Om fil- eller blobnamnet anges är det kopplade filnamnet det angivna namnet. Annars är det ett autogenererat filnamn. | Inga |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till lagringsarkivet samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg

En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns i [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

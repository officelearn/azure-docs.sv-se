---
title: Kopiera data till eller från Azure Blob Storage med hjälp av Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från käll data lager som stöds till Azure Blob Storage eller från Blob Storage till mottagar data lager som stöds med hjälp av Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: da8b4ebd5cf1e7a57842a116e5d9e21e3c3f7874
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387304"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Kopiera data till eller från Azure Blob Storage med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-blob-connector.md)
> * [Aktuell version](connector-azure-blob-storage.md)

Den här artikeln beskriver hur du kopierar data till och från Azure Blob Storage. Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Funktioner som stöds

Azure Blob Connector stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

Mer specifikt stöder denna Blob Storage Connector:

- Kopiera blobbar till och från allmänna Azure Storage-konton och varmt/cool Blob Storage. 
- Kopiera blobbar med hjälp av konto nyckel, signatur för delad åtkomst för tjänst, tjänstens huvud namn eller hanterade identiteter för Azure-resursers autentiseringar.
- Kopiera blobbar från block-, tilläggs-eller sid-blobar och kopiera data till endast block-blobar.
- Att kopiera blobbar som är eller parsa eller generera blobbar med [fil format och komprimerings-codec som stöds](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Om du aktiverar alternativet _"Tillåt att betrodda Microsoft-tjänster får åtkomst till det här lagrings kontot"_ på Azure Storage brand Väggs inställningar Miss Miss kan du använda Azure integration runtime för att ansluta till Blob Storage, eftersom ADF inte behandlas som ett betrott värde Microsoft-tjänst. Anslut via en egen värd Integration Runtime i stället.

## <a name="get-started"></a>Kom i gång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Blob Storage.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Azure Blob Connector stöder följande autentiseringstyper: Mer information finns i motsvarande avsnitt:

- [Autentisering av konto nyckel](#account-key-authentication)
- [Autentisering av signatur för delad åtkomst](#shared-access-signature-authentication)
- [Autentisering av tjänstens huvudnamn](#service-principal-authentication)
- [Hanterade identiteter för Azure-resurser-autentisering](#managed-identity)

>[!NOTE]
>När du använder PolyBase för att läsa in data i SQL Data Warehouse, om din källa eller mellanlagring av blob-lagring har kon figurer ATS med Virtual Network slut punkt, måste du använda hanterad identitetsautentisering som krävs av PolyBase, och använda sig av självbetjänings Integration Runtime med version 3,18 eller senare. Se avsnittet [hanterad identitets autentisering](#managed-identity) med fler konfigurations krav.

>[!NOTE]
>HDInsights-och Azure Machine Learning-aktiviteter stöder endast autentisering med Azure Blob Storage-konto nycklar.

### <a name="account-key-authentication"></a>Autentisering av konto nyckel

Följande egenskaper stöds för att använda autentisering av lagrings konto nycklar:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen måste anges till **AzureBlobStorage** (föreslagen) eller **AzureStorage** (se OBS! nedan). |Ja |
| Begär | Ange den information som behövs för att ansluta till lagringen för egenskapen connectionString. <br/>Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i Data Factory. Du kan också ange konto nyckeln i Azure Key Vault och hämta @no__t 0-konfigurationen från anslutnings strängen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. |Ja |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime (om ditt data lager finns i ett privat nätverk). Om inget värde anges används standard Azure Integration Runtime. |Nej |

>[!NOTE]
>Sekundär BLOB service-slutpunkt stöds inte när autentisering med konto nycklar används. Du kan använda andra typer av autentisering.

>[!NOTE]
>Om du använder den länkade tjänsten "AzureStorage"-typ, stöds den fortfarande som den är, men du rekommenderas att använda den här nya länkade tjänst typen "AzureBlobStorage".

**Exempel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: lagrings konto nyckel i Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
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

En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagrings konto. Du kan använda en signatur för delad åtkomst för att ge en klient begränsad behörighet till objekt i ditt lagrings konto under en angiven tid. Du behöver inte dela dina konto åtkomst nycklar. Signaturen för delad åtkomst är en URI som omfattar den information som krävs för autentiserad åtkomst till en lagrings resurs i dess frågeparametrar. För att få åtkomst till lagrings resurser med signaturen för delad åtkomst måste klienten bara skicka in signaturen för delad åtkomst till lämplig konstruktor eller metod. Mer information om signaturer för delad åtkomst finns i signaturer för delad åtkomst [: förstå signatur modellen för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory stöder nu både signaturer för **delad åtkomst för tjänsten** och **kontots delade åtkomst-signaturer**. Mer information om signaturer för delad åtkomst finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../storage/common/storage-sas-overview.md).
>- I senare data uppsättnings konfiguration är mappsökvägen den absoluta sökvägen som börjar från container nivå. Du måste konfigurera en anpassad med sökvägen i SAS-URI: n.

> [!TIP]
> Om du vill skapa en signatur för delad åtkomst för tjänsten för ditt lagrings konto kan du köra följande PowerShell-kommandon. Ersätt plats hållarna och bevilja nödvändig behörighet.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Följande egenskaper stöds för att använda autentisering med signaturen för delad åtkomst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen måste anges till **AzureBlobStorage** (föreslagen) eller **AzureStorage** (se OBS! nedan). |Ja |
| sasUri | Ange signatur-URI för delad åtkomst till lagrings resurserna, till exempel BLOB/container. <br/>Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i Data Factory. Du kan också ställa in SAS-token i Azure Key Vault för att dra nytta av automatisk rotation och ta bort token-delen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. |Ja |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller den lokala Integration Runtime (om ditt data lager finns i ett privat nätverk). Om inget värde anges används standard Azure Integration Runtime. |Nej |

>[!NOTE]
>Om du använder den länkade tjänsten "AzureStorage"-typ, stöds den fortfarande som den är, men du rekommenderas att använda den här nya länkade tjänst typen "AzureBlobStorage".

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

**Exempel: lagrings konto nyckel i Azure Key Vault**

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

När du skapar en signatur-URI för delad åtkomst bör du tänka på följande:

- Ange lämpliga Läs-/skriv behörigheter för objekt baserat på hur den länkade tjänsten (läsa, skriva, läsa/skriva) används i din data fabrik.
- Ange **förfallo tid** på lämpligt sätt. Se till att åtkomsten till lagrings objekt upphör att gälla inom den aktiva perioden för pipelinen.
- URI: n ska skapas i rätt behållare/BLOB baserat på behovet. En signatur-URI för delad åtkomst till en BLOB ger Data Factory åtkomst till just denna blob. En signatur-URI för delad åtkomst till en Blob Storage-behållare gör att Data Factory kan iterera igenom blobbar i den behållaren. Kom ihåg att uppdatera den länkade tjänsten med den nya URI: n för att ge åtkomst till fler eller färre objekt senare, eller för att uppdatera signatur-URI för delad åtkomst.

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

För Azure Storage tjänstens huvud namns autentisering i allmänhet, se [autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory](../storage/common/storage-auth-aad.md).

Följ dessa steg om du vill använda autentisering av tjänstens huvud namn:

1. Registrera en program enhet i Azure Active Directory (Azure AD) genom [att följa registrera ditt program med en Azure AD-klient](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

    - Program-ID:t
    - Program nyckel
    - Klient-ID:t

2. Bevilja tjänstens huvud namn rätt behörighet i Azure Blob Storage. Se [Hantera åtkomst rättigheter för att Azure Storage data med RBAC](../storage/common/storage-auth-aad-rbac.md) med mer information om rollerna.

    - **Som källa**, i åtkomst kontroll (IAM), beviljas minst **Storage BLOB data Reader** -roll.
    - **Som mottagare**, i åtkomst kontroll (IAM), beviljar du minst rollen **Storage BLOB data Contributor** .

Dessa egenskaper stöds för en länkad Azure Blob Storage-tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AzureBlobStorage**. |Ja |
| serviceEndpoint | Ange Azure Blob Storage-tjänstens slut punkt med mönstret `https://<accountName>.blob.core.windows.net/`. |Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| innehav | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime (om ditt data lager finns i ett privat nätverk). Om inget värde anges används standard Azure Integration Runtime. |Nej |

>[!NOTE]
>Autentisering av tjänstens huvud namn stöds endast av "AzureBlobStorage"-typen länkad tjänst men inte tidigare "AzureStorage"-typ länkad tjänst.

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

### <a name="managed-identity"></a>Hanterade identiteter för Azure-resurser-autentisering

En data fabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md)som representerar den aktuella data fabriken. Du kan använda den här hanterade identiteten direkt för Blob Storage-autentisering som liknar att använda ditt eget tjänst huvud namn. Det gör att den här fabriken kan komma åt och kopiera data från/till Blob Storage.

Se [autentisera åtkomst till Azure Storage med Azure Active Directory](../storage/common/storage-auth-aad.md) för Azure Storage autentisering i allmänhet. Följ dessa steg om du vill använda hanterade identiteter för autentisering med Azure-resurser:

1. [Hämta Data Factory-hanterad identitets information](data-factory-service-identity.md#retrieve-managed-identity) genom att kopiera värdet "ID för tjänst identitet" som genererats tillsammans med din fabrik.

2. Ge behörigheten hanterad identitet korrekt i Azure Blob Storage. Se [Hantera åtkomst rättigheter för att Azure Storage data med RBAC](../storage/common/storage-auth-aad-rbac.md) med mer information om rollerna.

    - **Som källa**, i åtkomst kontroll (IAM), beviljas minst **Storage BLOB data Reader** -roll.
    - **Som mottagare**, i åtkomst kontroll (IAM), beviljar du minst rollen **Storage BLOB data Contributor** .

>[!IMPORTANT]
>Om du använder PolyBase för att läsa in data från BLOB (som källa eller som mellanlagring) till SQL Data Warehouse, ska du, när du använder hanterad identitetsautentisering för BLOB, se till att du även följer steg 1 och 2 i [den här vägledningen](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) till 1) registrera din SQL Database Server med Azure Active Directory (Azure AD) och 2) tilldela rollen Storage BLOB data Contributor till din SQL Database-Server. resten hanteras av Data Factory. Om blob-lagringen har kon figurer ATS med en Azure Virtual Network-slutpunkt, för att använda PolyBase för att läsa in data från den, måste du använda hanterad identitetsautentisering som krävs av PolyBase.

Dessa egenskaper stöds för en länkad Azure Blob Storage-tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AzureBlobStorage**. |Ja |
| serviceEndpoint | Ange Azure Blob Storage-tjänstens slut punkt med mönstret `https://<accountName>.blob.core.windows.net/`. |Ja |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime (om ditt data lager finns i ett privat nätverk). Om inget värde anges används standard Azure Integration Runtime. |Nej |

> [!NOTE]
> Hanterade identiteter för Azure-resurser stöds endast av "AzureBlobStorage"-typen länkad tjänst men inte tidigare "AzureStorage"-typ länkad tjänst. 

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

## <a name="dataset-properties"></a>Egenskaper för data mängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . 

- För **Parquet, avgränsade text-, JSON-, Avro-och binärformat**, se [Parquet, delimited text-, JSON-, Avro-och binära format-datauppsättning](#format-based-dataset) .
- Andra format som **Orc/JSON-format**finns i avsnittet [annan format data uppsättning](#other-format-dataset) .

### <a name="format-based-dataset"></a>Data uppsättning för Parquet, avgränsad text, JSON, Avro och binärt format

Om du vill kopiera data till och från Blob Storage i Parquet, avgränsad text, Avro eller binärt format, se [Parquet format](format-parquet.md), [avgränsat text format](format-delimited-text.md), [Avro format](format-avro.md) och [binärt format](format-binary.md) artikel i format-baserad data uppsättning och inställningar som stöds. Följande egenskaper stöds för Azure-Blob under `location`-inställningar i format-baserad data mängd:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen Type för platsen i data mängden måste anges till **AzureBlobStorageLocation**. | Ja      |
| container  | BLOB-behållaren.                                          | Ja      |
| folderPath | Sökvägen till mappen under den angivna behållaren. Om du vill använda jokertecken för att filtrera mappar hoppar du över den här inställningen och anger i aktivitets källans inställningar. | Nej       |
| fileName   | Fil namnet under den aktuella containern + folderPath. Om du vill använda jokertecken för att filtrera filer, hoppar du över den här inställningen och anger i aktivitets källans inställningar. | Nej       |

> [!NOTE]
>
> **AzureBlob** Type-datauppsättning med Parquet/text format som nämns i nästa avsnitt stöds fortfarande som-är för kopierings-/Sök-/getMetaData-aktivitet för bakåtkompatibilitet, men den fungerar inte med mappnings data flödet. Du rekommenderas att använda den här nya modellen för att vidarebefordra och användar gränssnittet för ADF-redigering har växlat till att generera dessa nya typer.

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

### <a name="other-format-dataset"></a>Data uppsättning för andra format

Om du vill kopiera data till och från Blob Storage i ORC/JSON-format anger du egenskapen type för data uppsättningen till **AzureBlob**. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till **AzureBlob**. |Ja |
| folderPath | Sökväg till behållaren och mappen i blob-lagringen. <br/><br/>Wildcard-filtret stöds för sökvägen exklusive behållar namn. Tillåtna jokertecken är: `*` (matchar inga eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Använd `^` om du vill undvika att det faktiska mappnamnet har jokertecken eller det här Escape-char-namnet inuti. <br/><br/>Exempel: myblobcontainer/myblobfolder/, se fler exempel i [mapp-och fil filter exempel](#folder-and-file-filter-examples). |Ja för kopiera/Sök-aktivitet, nej för GetMetadata-aktivitet |
| fileName | **Namn eller Wildcard-filter** för Blobbarna under den angivna "folderPath". Om du inte anger ett värde för den här egenskapen pekar data uppsättningen på alla blobbar i mappen. <br/><br/>För filter är tillåtna jokertecken: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken).<br/>-Exempel 1: `"fileName": "*.csv"`<br/>-Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` för att kringgå om det faktiska fil namnet har jokertecken eller detta escape-tecken inuti.<br/><br/>När fil namnet inte har angetts för en data uppsättning för utdata och **preserveHierarchy** inte har angetts i aktivitets mottagaren genererar kopierings aktiviteten automatiskt BLOB-namnet med följande mönster: "*data. [ aktivitetens körnings-ID GUID]. [GUID om FlattenHierarchy]. [format om det är konfigurerat]. [komprimering om konfigurerad]* ", t. ex." data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz "; Om du kopierar från tabell källa med tabell namn i stället för fråga, är namn mönstret " *[tabell namn]. [ format]. [komprimering om konfigurerad]* ", t. ex." Table. csv ". |Nej |
| modifiedDatetimeStart | Filter för filer baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning påverkas genom att aktivera den här inställningen när du vill göra fil filter från enorma mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filter för filattribut används för data uppsättningen.  När `modifiedDatetimeStart` har datetime-värdet men `modifiedDatetimeEnd` är NULL, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime väljs.  När `modifiedDatetimeEnd` har datetime-värdet men `modifiedDatetimeStart` är NULL, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime väljs.| Nej |
| modifiedDatetimeEnd | Filter för filer baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning påverkas genom att aktivera den här inställningen när du vill göra fil filter från enorma mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filter för filattribut används för data uppsättningen.  När `modifiedDatetimeStart` har datetime-värdet men `modifiedDatetimeEnd` är NULL, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime väljs.  När `modifiedDatetimeEnd` har datetime-värdet men `modifiedDatetimeStart` är NULL, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime väljs.| Nej |
| formatering | Om du vill kopiera filer som är mellan filbaserade butiker (binär kopia) hoppar du över avsnittet format i både indata och utdata-datauppsättnings definitionerna.<br/><br/>Om du vill parsa eller generera filer med ett särskilt format stöds följande fil format **typer: text**format, **JsonFormat**, **AvroFormat**, **OrcFormat**och **ParquetFormat**. Ange egenskapen **Type** under **format** till något av dessa värden. Mer information finns i avsnitten [text format](supported-file-formats-and-compression-codecs.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs.md#json-format), [Avro format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs.md#orc-format)och [Parquet format](supported-file-formats-and-compression-codecs.md#parquet-format) . |Nej (endast för binär kopierings scenario) |
| komprimering | Ange typ och nivå för komprimeringen för data. Mer information finns i [fil format och komprimerings-codecar som stöds](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är **gzip**, **DEFLATE**, **BZip2**och **ZipDeflate**.<br/>De nivåer som stöds är **optimala** och **snabbaste**. |Nej |

>[!TIP]
>Om du vill kopiera alla blobbar under en mapp anger du endast **folderPath** .<br>Om du vill kopiera en enda BLOB med ett visst namn anger du **folderPath** med en **mapp och ett fil namn** med fil namnet.<br>Om du vill kopiera en delmängd av blobbar under en mapp anger du **folderPath** med en mapp och ett **fil namns** filter med jokertecken. 

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

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Blob Storage-källan och mottagare.

### <a name="blob-storage-as-a-source-type"></a>Blob-lagring som käll typ

- Om du vill kopiera från **Parquet, avgränsade text-, JSON-, Avro-och binärformat**, se avsnittet [Parquet, avgränsad text, JSON, Avro och binärt format](#format-based-source) .
- Om du vill kopiera från andra format som **Orc-format**, se avsnittet [annan format källa](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, avgränsad text, JSON, Avro och binär format källa

Om du vill kopiera data till och från Blob Storage i **Parquet, avgränsade text-, JSON-, Avro-och binärformat**, referera till [Parquet-format](format-parquet.md), [avgränsat text format](format-delimited-text.md), [Avro format](format-avro.md) och [binärt format](format-binary.md) -artikel i format-baserad data uppsättning och inställningar som stöds. Följande egenskaper stöds för Azure-Blob under `storeSettings`-inställningar i format-baserad kopierings Källa:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Egenskapen Type under `storeSettings` måste anges till **AzureBlobStorageReadSetting**. | Ja                                           |
| rekursiva                | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursivt har angetts till true och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. Tillåtna värden är **True** (standard) och **false**. | Nej                                            |
| wildcardFolderPath       | Mappsökvägen med jokertecken under den angivna behållaren som kon figurer ATS i data uppsättningen för att filtrera källmappen. <br>Tillåtna jokertecken är: `*` (matchar inga eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Använd `^` om du vill undvika att det faktiska mappnamnet har jokertecken eller det här Escape-char-namnet inuti. <br>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Nej                                            |
| wildcardFileName         | Fil namnet med jokertecken under den aktuella containern + folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken är: `*` (matchar inga eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Använd `^` om du vill undvika att det faktiska mappnamnet har jokertecken eller det här Escape-char-namnet inuti.  Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Ja om `fileName` inte anges i data uppsättningen |
| modifiedDatetimeStart    | Filter för filer baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har datetime-värdet men `modifiedDatetimeEnd` är NULL, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime väljs.  När `modifiedDatetimeEnd` har datetime-värdet men `modifiedDatetimeStart` är NULL, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime väljs. | Nej                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                            |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej                                            |

> [!NOTE]
> För Parquet/avgränsat text format stöds **BlobSource** typ kopierings aktivitet som anges i nästa avsnitt fortfarande som-är för bakåtkompatibilitet. Du rekommenderas att använda den här nya modellen för att vidarebefordra och användar gränssnittet för ADF-redigering har växlat till att generera dessa nya typer.

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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobStorageReadSetting",
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

#### <a name="other-format-source"></a>Annan format källa

Om du vill kopiera data från Blob Storage i **Orc-format**anger du käll typen i kopierings aktiviteten till **BlobSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** .

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till **BlobSource**. |Ja |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursivt har angetts till true och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren.<br/>Tillåtna värden är **True** (standard) och **false**. | Nej |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej |

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

### <a name="blob-storage-as-a-sink-type"></a>Blob-lagring som mottagar typ

- Om du vill kopiera från **Parquet, avgränsade text-, JSON-, Avro-och binärformat**, se avsnittet [Parquet, avgränsad text, JSON, Avro och binärt format](#format-based-source) .
- Om du vill kopiera från andra format som **Orc-format**, se avsnittet [annan format källa](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, avgränsad text, JSON, Avro och binär format källa

Om du vill kopiera data från Blob Storage i **Parquet, avgränsade text-, JSON-, Avro-och binärformat**, referera till [Parquet-format](format-parquet.md), [avgränsat text format](format-delimited-text.md), [Avro format](format-avro.md) och [binära format](format-binary.md) -artikel i den formatbaserade kopierings aktivitets källan och inställningar som stöds. Följande egenskaper stöds för Azure-Blob under `storeSettings`-inställningar i format-baserad kopierings mottagare:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Egenskapen Type under `storeSettings` måste anges till **AzureBlobStorageWriteSetting**. | Ja      |
| copyBehavior             | Definierar kopierings beteendet när källan är filer från ett filbaserat data lager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen är på den första nivån i målmappen. Filerna har automatiskt genererade namn. <br/><b>-MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om filen eller BLOB-namnet anges, är det sammanslagna fil namnet det angivna namnet. Annars är det ett automatiskt genererat fil namn. | Nej       |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej       |

> [!NOTE]
> För Parquet/avgränsat text format stöds **BlobSink** typ kopierings aktivitet som anges i nästa avsnitt fortfarande som-är för bakåtkompatibilitet. Du rekommenderas att använda den här nya modellen för att vidarebefordra och användar gränssnittet för ADF-redigering har växlat till att generera dessa nya typer.

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
                    "type": "AzureBlobStorageWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Övrigt format mottagare

Om du vill kopiera data till Blob Storage i **Orc-format**anger du mottagar typen i kopierings aktiviteten till **BlobSink**. Följande egenskaper stöds i avsnittet **mottagare** .

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till **BlobSink**. |Ja |
| copyBehavior | Definierar kopierings beteendet när källan är filer från ett filbaserat data lager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen är på den första nivån i målmappen. Filerna har automatiskt genererade namn. <br/><b>-MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om filen eller BLOB-namnet anges, är det sammanslagna fil namnet det angivna namnet. Annars är det ett automatiskt genererat fil namn. | Nej |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej |

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

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp-och fil filter

I det här avsnittet beskrivs det resulterande beteendet hos mappsökvägen och fil namnet med filter för jokertecken.

| folderPath | fileName | rekursiva | Källans mappstruktur och filter resultat (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (tom, Använd standard) | false | container<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3FolderA<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**fil1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**fil2. JSON**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File3.csv<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File4.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File5.csv<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File6.csv |
| `container/Folder*` | (tom, Använd standard) | sant | container<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3FolderA<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**fil1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**fil2. JSON**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**File3. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**File4. JSON**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**File5. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | container<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3FolderA<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**fil1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File2.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File3.csv<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File4.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File5.csv<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | sant | container<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3FolderA<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**fil1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File2.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**File3. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10&nbsp;1File4.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11**File5. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Några rekursiva och copyBehavior-exempel

I det här avsnittet beskrivs det resulterande beteendet för kopierings åtgärden för olika kombinationer av rekursiva och copyBehavior värden.

| rekursiva | copyBehavior | Källans mappstruktur | Resulterande mål |
|:--- |:--- |:--- |:--- |
| sant |preserveHierarchy | Mapp1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | Målmappen Mapp1 skapas med samma struktur som källan:<br/><br/>Mapp1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 |
| sant |flattenHierarchy | Mapp1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | Mål-Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3autogenerated namn för fil1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3autogenerated namn för Fil2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3autogenerated namn för File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3autogenerated namn för File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3autogenerated namn för File5 |
| sant |mergeFiles | Mapp1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | Mål-Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1 + Fil2 + File3 + File4 + File5-innehållet sammanfogas till en fil med ett automatiskt genererat fil namn. |
| false |preserveHierarchy | Mapp1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | Målmappen Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |
| false |flattenHierarchy | Mapp1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | Målmappen Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3autogenerated namn för fil1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3autogenerated namn för Fil2<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |
| false |mergeFiles | Mapp1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1 + Fil2-innehåll sammanfogas till en fil med ett automatiskt genererat fil namn. automatiskt genererat namn för fil1<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

Lär dig mer om omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i mappnings data flödet.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Ta bort aktivitets egenskaper

Om du vill veta mer om egenskaperna kontrollerar du [ta bort aktivitet](delete-activity.md)

## <a name="next-steps"></a>Nästa steg

En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Data Factory finns i [data lager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).

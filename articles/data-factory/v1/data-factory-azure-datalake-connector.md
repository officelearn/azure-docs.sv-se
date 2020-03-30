---
title: Kopiera data till och från Azure Data Lake Storage Gen1
description: Lär dig hur du kopierar data till och från DataSjölagret med hjälp av Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b6a60536bae6fbedf01eda7aa340e90ced58e004
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281605"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Kopiera data till och från DataSjölagring Gen1 med hjälp av Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-azure-datalake-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Azure Data Lake Storage Gen1-anslutningsappen i V2](../connector-azure-data-lake-store.md).

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att flytta data till och från Azure Data Lake Storage Gen1 (tidigare känt som Azure Data Lake Store). Den bygger på artikeln [Dataflyttningsaktiviteter,](data-factory-data-movement-activities.md) en översikt över dataflyttning med Kopiera aktivitet.

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure Data Lake Store** till följande datalager:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till Azure Data Lake Store:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Skapa ett DataSjölagringskonto innan du skapar en pipeline med Kopiera aktivitet. Mer information finns i [Komma igång med Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Autentiseringstyper som stöds
DataSjölagringskopplingen stöder dessa autentiseringstyper:
* Autentisering av tjänstens huvudnamn
* OAuth-autentisering (OAuth) autentisering (användarautentiseringsuppgifter)

Vi rekommenderar att du använder autentisering av tjänstens huvudnamn, särskilt för en schemalagd datakopia. Tokenförfallobeteende kan uppstå med autentiseringsuppgifter för användare. Konfigurationsinformation finns i avsnittet [Egenskaper för länkade tjänster.](#linked-service-properties)

## <a name="get-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data till/från ett Azure Data Lake Store med hjälp av olika verktyg/API:er.

Det enklaste sättet att skapa en pipeline för att kopiera data är att använda **kopieringsguiden**. En självstudiekurs om hur du skapar en pipeline med hjälp av kopieringsguiden finns i [Självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md).

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera pipelines.
2. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken. Om du till exempel kopierar data från en Azure-bloblagring till ett Azure Data Lake Store skapar du två länkade tjänster för att länka ditt Azure-lagringskonto och Azure Data Lake Store till din datafabrik. För länkade tjänstegenskaper som är specifika för Azure Data Lake Store finns i avsnittet [länkade tjänstegenskaper.](#linked-service-properties)
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen. I exemplet som nämns i det sista steget skapar du en datauppsättning för att ange blob-behållaren och mappen som innehåller indata. Och du skapar en annan datauppsättning för att ange mapp- och filsökvägen i datasjöarkivet som innehåller data som kopierats från blob-lagringen. För datauppsättningsegenskaper som är specifika för Azure Data Lake Store finns i avsnittet [egenskaper för datauppsättning.](#dataset-properties)
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. I det tidigare exemplet använder du BlobSource som källa och AzureDataLakeStoreSink som en mottagare för kopieringsaktiviteten. Om du kopierar från Azure Data Lake Store till Azure Blob Storage använder du AzureDataLakeStoreSource och BlobSink i kopieringsaktiviteten. För kopieringsaktivitetsegenskaper som är specifika för Azure Data Lake Store finns i avsnittet [kopiera aktivitetsegenskaper.](#copy-activity-properties) Om du vill ha information om hur du använder ett datalager som källa eller en diskbänk klickar du på länken i föregående avsnitt för datalagret.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet. För exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från ett Azure Data Lake Store finns i avsnittet [JSON-exempel](#json-examples-for-copying-data-to-and-from-data-lake-store) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för DataSjölagring.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
En länkad tjänst länkar ett datalager till en datafabrik. Du skapar en länkad tjänst av typen **AzureDataLakeStore** för att länka data från Data Lake Store till din datafabrik. I följande tabell beskrivs JSON-element som är specifika för datasjölagringsrelaterade tjänster. Du kan välja mellan tjänstens huvudnamn och autentiseringsuppgifter för användare.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **Typ** | Typegenskapen måste anges till **AzureDataLakeStore**. | Ja |
| **dataLakeStoreUri** | Information om Azure Data Lake Store-kontot. Den här informationen tar något `https://[accountname].azuredatalakestore.net/webhdfs/v1` av `adl://[accountname].azuredatalakestore.net/`följande format: eller . | Ja |
| **subscriptionId** | Azure-prenumerations-ID som DataSjölagringskontot tillhör. | Krävs för diskbänk |
| **resursGroupName** | Azure-resursgruppsnamn som datasjölagringskontot tillhör. | Krävs för diskbänk |

### <a name="service-principal-authentication-recommended"></a>Autentisering av tjänstens huvudnamn (rekommenderas)
Om du vill använda tjänsthuvudbehörighetsautentisering registrerar du en programentitet i Azure Active Directory (Azure AD) och ger den åtkomst till DataSjö store. Detaljerade steg finns i [Autentisering](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)från tjänst till tjänst . Anteckna följande värden, som du använder för att definiera den länkade tjänsten:
* Program-ID:t
* Programnyckel
* Klient-ID:t

> [!IMPORTANT]
> Se till att du ger tjänstens huvudnamn rätt behörighet i Azure Data Lake Store:
>- **Om du vill använda DataSjölagring som källa**ger du minst behörighet att läsa **+ köra** data för att lista och kopiera innehållet i en mapp, eller **Läs** behörighet att kopiera en enskild fil. Inget krav på åtkomstkontroll på kontonivå.
>- **Om du vill använda DataSjölagring som mottagare**ger du minst **behörigheten Skriv + Kör** data för att skapa underordnade objekt i mappen. Och om du använder Azure IR för att ge kopiera (både källa och diskho finns i molnet), för att låta Data Factory identifiera Data Lake Stores region, ge åtminstone **Reader-roll** i kontoåtkomstkontroll (IAM). Om du vill undvika den här IAM-rollen [anger du körningPlats](data-factory-data-movement-activities.md#global) med platsen för datasjöarkivet i kopieringsaktivitet.
>- Om du **använder Kopiera guide för att skapa pipelines**ger du åtminstone **reader-rollen** i IAM (Account Access Control). Bevilja också minst **läs + kör** behörighet till din DataSjölagringsrot ("/") och dess underordnade. Annars kan meddelandet "Autentiseringsuppgifterna är ogiltiga".

Använd autentisering av tjänstens huvudnamn genom att ange följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **servicePrincipalId** | Ange programmets klient-ID. | Ja |
| **servicePrincipalKey** | Ange programmets nyckel. | Ja |
| **Hyresgästen** | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Du kan hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Ja |

**Exempel: Autentisering av tjänstens huvudnamn**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Autentisering av användarautentiseringsuppgifter
Du kan också använda autentiseringsuppgifter för användarautentiseringsuppgifter för att kopiera från eller till Datasjölagret genom att ange följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **Tillstånd** | Klicka på knappen **Auktorisera** i Data Factory Editor och ange autentiseringsuppgifterna som tilldelar den automatiskt skapade auktoriseringsadressen till den här egenskapen. | Ja |
| **Sessionid** | OAuth-sessions-ID från OAuth-auktoriseringssessionen. Varje sessions-ID är unikt och kan endast användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory Editor. | Ja |

> [!IMPORTANT]
> Se till att du ger användaren rätt behörighet i Azure Data Lake Store:
>- **Om du vill använda DataSjölagring som källa**ger du minst behörighet att läsa **+ köra** data för att lista och kopiera innehållet i en mapp, eller **Läs** behörighet att kopiera en enskild fil. Inget krav på åtkomstkontroll på kontonivå.
>- **Om du vill använda DataSjölagring som mottagare**ger du minst **behörigheten Skriv + Kör** data för att skapa underordnade objekt i mappen. Och om du använder Azure IR för att ge kopiera (både källa och diskho finns i molnet), för att låta Data Factory identifiera Data Lake Stores region, ge åtminstone **Reader-roll** i kontoåtkomstkontroll (IAM). Om du vill undvika den här IAM-rollen [anger du körningPlats](data-factory-data-movement-activities.md#global) med platsen för datasjöarkivet i kopieringsaktivitet.
>- Om du **använder Kopiera guide för att skapa pipelines**ger du åtminstone **reader-rollen** i IAM (Account Access Control). Bevilja också minst **läs + kör** behörighet till din DataSjölagringsrot ("/") och dess underordnade. Annars kan meddelandet "Autentiseringsuppgifterna är ogiltiga".

**Exempel: Autentisering av användarautentiseringsuppgifter**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Token förfallodatum
Auktoriseringskoden som du genererar med knappen **Auktorisera** upphör att gälla efter en viss tid. Följande meddelande innebär att autentiseringstoken har upphört att gälla:

Åtgärdsfel för autentiseringsuppgifter: invalid_grant - AADSTS70002: Fel uppstod när autentiseringsuppgifterna inte validerades. AADSTS70008: Förutsatt åtkomstbidrag har upphört att gälla eller återkallas. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timepstam: 2015-12-15 21-09-31Z.

I följande tabell visas förfallotider för olika typer av användarkonton:

| Användartyp | Upphör att gälla efter |
|:--- |:--- |
| Användarkonton som *inte* hanteras av @hotmail.com Azure @live.comActive Directory (till exempel eller ) |12 timmar |
| Användarkonton som hanteras av Azure Active Directory |14 dagar efter det sista segmentet <br/><br/>90 dagar, om ett segment baserat på en OAuth-baserad länkad tjänst körs minst en gång var 14:e dag |

Om du ändrar ditt lösenord före tokens utgångstid upphör token att gälla omedelbart. Du kommer att se meddelandet som nämns tidigare i det här avsnittet.

Du kan återauktorisera kontot med knappen **Auktorisera** när token upphör att distribuera om den länkade tjänsten. Du kan också generera värden för **sessionsId-** och **auktoriseringsegenskaperna** programmässigt med hjälp av följande kod:


```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Mer information om de datafabriksklasser som används i koden finns i avsnitten [AzureDataLakeStoreLinkedService Class,](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx) [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)och [AuthorizationSessionGetResponse Class.](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) Lägg till en `2.9.10826.1824` `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` referens `WindowsFormsWebAuthenticationDialog` till versionen av för den klass som används i koden.

## <a name="troubleshooting-tips"></a>Felsökningstips

**Symptom:** När du kopierar data **till** Azure Data Lake Store, om din kopieringsaktivitet misslyckas med följande fel:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Grundorsak:** Det finns 2 möjliga orsaker:

1. Den `resourceGroupName` och/eller `subscriptionId` som anges i Azure Data Lake Store-tjänsten är felaktig.
2. Användaren eller tjänstens huvudnamn har inte den behörighet som krävs.

**Upplösning:**

1. Kontrollera att `subscriptionId` `resourceGroupName` och du anger `typeProperties` i den länkade tjänsten är verkligen de som ditt datasjökonto tillhör.

2. Se till att du ger användaren eller tjänstens huvudnamn minst **reader-rollen** på datasjökontot. Så här gör du det:

    1. Gå till Azure-portalen -> ditt DataSjö store-konto
    2. Klicka på **Åtkomstkontroll (IAM)** på bladet i DataSjöarkivet
    3. Klicka på **Lägg till rolltilldelning**
    4. Ange **roll** som **läsare**och välj den användare eller det tjänsthuvudnamn som du använder för att kopiera för att bevilja åtkomst

3. Om du inte vill ge **reader-rollen** till användarens eller tjänstens huvudnamn är alternativet att [uttryckligen ange en körningsplats](data-factory-data-movement-activities.md#global) i kopieringsaktivitet med platsen för datasjöarkivet. Exempel:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>Egenskaper för datamängd
Om du vill ange en datauppsättning som ska representera indata i ett DataSjölag anger du **egenskapen type** för datauppsättningen till **AzureDataLakeStore**. Ange egenskapen **linkedServiceName** för datauppsättningen till namnet på den länkade tjänsten DataSjölager. En fullständig lista över JSON-avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt i en datauppsättning i JSON, till exempel **struktur,** **tillgänglighet**och **princip**, är liknande för alla datauppsättningstyper (Azure SQL-databas, Azure blob och Azure-tabell, till exempel). Avsnittet **typeProperties** är olika för varje typ av datauppsättning och innehåller information som plats och format för data i datalagret.

Avsnittet **typeProperties** för en datauppsättning av typen **AzureDataLakeStore** innehåller följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **folderPath** |Sökväg till behållaren och mappen i DataSjö store. |Ja |
| **fileName** |Filens namn i Azure Data Lake Store. Egenskapen **fileName** är valfri och skiftlägeskänslig. <br/><br/>Om du anger **filnamn**fungerar aktiviteten (inklusive Kopiera) på den specifika filen.<br/><br/>När **filnamn** inte har angetts innehåller Copy alla filer i **folderPath** i indatauppsättningen.<br/><br/>När **filnamn** inte har angetts för en utdatauppsättning och **bevaraHierarchy** inte anges i aktivitetsmottagaren, är namnet på den genererade filen i formatet `Data._Guid_.txt`. Till exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Inga |
| **partitioneradAv** |Egenskapen **partitionedBy** är valfri. Du kan använda den för att ange en dynamisk sökväg och ett filnamn för tidsseriedata. **FolderPath** kan till exempel parameteriseras för varje timme data. Mer information och exempel finns i egenskapen partitionedBy. |Inga |
| **Format** | Följande formattyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**och **ParquetFormat**. Ange **type** typegenskapen under **format** till ett av dessa värden. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), [ORC-format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parkettformat](data-factory-supported-file-and-compression-formats.md#parquet-format) i artikel [fil- och komprimeringsformat som stöds av](data-factory-supported-file-and-compression-formats.md) azure data factory. <br><br> Om du vill kopiera filer "i det vill du", mellan `format` filbaserade butiker (binär kopia), hoppa över avsnittet i både indata- och utdatauppsättningsdefinitioner. |Inga |
| **Komprimering** | Ange typ och komprimeringsnivå för data. Typer som stöds är **GZip**, **Deflate**, **BZip2**och **ZipDeflate**. Nivåer som stöds är **optimala** och **snabbaste.** Mer information finns i [Fil- och komprimeringsformat som stöds av Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

### <a name="the-partitionedby-property"></a>Egenskapen partitionedBy
Du kan ange dynamiska **egenskaper för sökvägar** och **filnamn** för tidsseriedata med egenskapen **partitionedBy,** Data Factory-funktioner och systemvariabler. Mer information finns i artikeln [Azure Data Factory - funktioner och systemvariabler.](data-factory-functions-variables.md)


I följande exempel `{Slice}` ersätts värdet för systemvariabeln `SliceStart` Data Factory`yyyyMMddHH`i det angivna formatet ( ). Namnet `SliceStart` refererar till segmentets starttid. Egenskapen `folderPath` är olika för varje `wikidatagateway/wikisampledataout/2014100103` `wikidatagateway/wikisampledataout/2014100104`segment, som i eller .

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

I följande exempel extraheras år, månad, `SliceStart` dag och tid för i separata `folderPath` variabler som används av egenskaperna och: `fileName`
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
Mer information om tidsseriedatauppsättningar, schemaläggning och segment finns [i datauppsättningarna i Azure Data Factory](data-factory-create-datasets.md) och Data Factory schemaläggning och [körning](data-factory-scheduling-and-execution.md) artiklar.


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och princip är tillgängliga för alla typer av aktiviteter.

Vilka egenskaper som är tillgängliga i avsnittet **typEgenskaper i** en aktivitet varierar beroende på varje aktivitetstyp. För en kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

**AzureDataLakeStoreSource** stöder följande egenskap i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| **Rekursiv** |Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. |Sant (standardvärde), Falskt |Inga |

**AzureDataLakeStoreSink** stöder följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| **kopieraBehavior** |Anger kopieringsbeteendet. |<b>PreserveHierarchy</b>: Bevarar filhierarkin i målmappen. Den relativa sökvägen för källfilen till källmappen är identisk med målfilens relativa sökväg till målmappen.<br/><br/><b>FlattenHierarchy</b>: Alla filer från källmappen skapas på den första nivån i målmappen. Målfilerna skapas med autogenererade namn.<br/><br/><b>MergeFiles</b>: Sammanfogar alla filer från källmappen till en fil. Om fil- eller blobnamnet anges är det kopplade filnamnet det angivna namnet. Annars skapas filnamnet automatiskt. |Inga |

### <a name="recursive-and-copybehavior-examples"></a>rekursiva och copyBehavior exempel
I det här avsnittet beskrivs det resulterande beteendet för kopieringsåtgärden för olika kombinationer av rekursiva värden och copyBehavior-värden.

| Rekursiv | kopieraBehavior | Resulterande beteende |
| --- | --- | --- |
| true |bevaraHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmappen Folder1 skapas med samma struktur som källan<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5. |
| true |plattaHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File5 |
| true |mergeFiles |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 innehåll slås samman till en fil med automatiskt genererat filnamn |
| false |bevaraHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmappen Folder1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/><br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |
| false |plattaHierarchy |För en källmapp Mapp1 med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmappen Folder1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File2<br/><br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |
| false |mergeFiles |För en källmapp Mapp1 med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5<br/><br/>målmappen Folder1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + File2-innehåll sammanfogas till en fil med automatiskt genererat filnamn. automatiskt genererat namn för Arkiv1<br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimeringsformat som stöds
Mer information finns i artikeln [Fil och komprimering i](data-factory-supported-file-and-compression-formats.md) Azure Data Factory.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>JSON-exempel för kopiering av data till och från Data Lake Store
Följande exempel innehåller exempel på JSON-definitioner. Du kan använda dessa exempeldefinitioner för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Exemplen visar hur du kopierar data till och från DataSjölagring och Azure Blob-lagring. Data kan dock kopieras _direkt_ från någon av källorna till någon av de diskhoar som stöds. Mer information finns i avsnittet "Datalager och format som stöds" i artikeln Flytta data med hjälp av artikel [Kopiera aktivitet.](data-factory-data-movement-activities.md)

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Exempel: Kopiera data från Azure Blob Storage till Azure Data Lake Store
Exempelkoden i det här avsnittet visar:

* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En länkad tjänst av typen [AzureDataLakeStore](#linked-service-properties).
* En [indatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureDataLakeStore](#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med en kopieringsaktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [AzureDataLakeStoreSink](#copy-activity-properties).

Exemplen visar hur tidsseriedata från Azure Blob Storage kopieras till Data Lake Store varje timme.

**Azure Storage-länkad tjänst**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Azure Data Lake Store-länkad tjänst**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Konfigurationsinformation finns i avsnittet [Egenskaper för länkade tjänster.](#linked-service-properties)
>

**Azure blob indatauppsättning**

I följande exempel hämtas data från en ny`"frequency": "Hour", "interval": 1`blob varje timme ( ). Mappsökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder starttidens år, månad och dag. Filnamnet använder timdelen av starttiden. Inställningen `"external": true` informerar datafabrikstjänsten om att tabellen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Utdatauppsättning för Azure Data Lake Store**

I följande exempel kopieras data till DataSjö store. Nya data kopieras till DataSjölagre varje timme.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopiera aktivitet i en pipeline med en blob-källa och en datasjölagringsmottagare**

I följande exempel innehåller pipelinen en kopieringsaktivitet som är konfigurerad för att använda indata- och utdatauppsättningar. Kopieringsaktiviteten är schemalagd att köras varje timme. I JSON-definitionen för `source` pipelinen `BlobSource`är typen `sink` inställd på `AzureDataLakeStoreSink`, och typen är inställd på .

```json
{
    "name":"SamplePipeline",
    "properties":
    {
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [
            {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Exempel: Kopiera data från Azure Data Lake Store till en Azure-blob
Exempelkoden i det här avsnittet visar:

* En länkad tjänst av typen [AzureDataLakeStore](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En [indatauppsättning](data-factory-create-datasets.md) av typen [AzureDataLakeStore](#dataset-properties).
* En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med en kopieringsaktivitet som använder [AzureDataLakeStoreSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Koden kopierar tidsseriedata från Data Lake Store till en Azure-blob varje timme.

**Azure Data Lake Store-länkad tjänst**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Konfigurationsinformation finns i avsnittet [Egenskaper för länkade tjänster.](#linked-service-properties)
>

**Azure Storage-länkad tjänst**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Indatauppsättning för Azure Data Lake**

I det här `"external"` `true` exemplet informerar du datafabrikstjänsten om att tabellen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

```json
{
    "name": "AzureDataLakeStoreInput",
    "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
**Azure blob utdatauppsättning**

I följande exempel skrivs data till en ny`"frequency": "Hour", "interval": 1`blob varje timme ( ). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delen år, månad, dag och timmar av starttiden.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**En kopieringsaktivitet i en pipeline med en Azure Data Lake Store-källa och en blob-diskho**

I följande exempel innehåller pipelinen en kopieringsaktivitet som är konfigurerad för att använda indata- och utdatauppsättningar. Kopieringsaktiviteten är schemalagd att köras varje timme. I JSON-definitionen för `source` pipelinen `AzureDataLakeStoreSource`är typen `sink` inställd på `BlobSink`, och typen är inställd på .

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureDataLakeStoreInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
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
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

I definitionen för kopieringsaktivitet kan du också mappa kolumner från källdatauppsättningen till kolumner i sink-datauppsättningen. Mer information finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Mer information om de faktorer som påverkar prestanda för kopiera aktivitet och hur du optimerar den finns i artikeln [Kopiera aktivitetsprestanda och justeringsguide.](data-factory-copy-activity-performance.md)

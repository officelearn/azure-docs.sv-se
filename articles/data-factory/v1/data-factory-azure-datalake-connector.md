---
title: Kopiera data till och från Azure Data Lake Storage Gen1 | Microsoft Docs
description: Lär dig hur du kopierar data till och från Data Lake Store med hjälp av Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3bb372c4c3ddb79429df20c24c691c847e927e2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567358"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Kopiera data till och från Data Lake Storage Gen1 med Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-azure-datalake-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Azure Data Lake Storage Gen1 connector i V2](../connector-azure-data-lake-store.md).

Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data till och från Azure Data Lake Storage Gen1 (kallades tidigare Azure Data Lake Store). Den bygger på den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel, en översikt över dataförflyttning med en Kopieringsaktivitet.

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure Data Lake Store** till följande data lagras:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Skapa ett Data Lake Store-konto innan du skapar en pipeline med en Kopieringsaktivitet. Mer information finns i [Kom igång med Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Typer av autentisering stöds
Data Lake Store-anslutningen har stöd för dessa typer av autentisering:
* Autentisering av tjänstens huvudnamn
* Användarautentisering för autentiseringsuppgifter (OAuth)

Vi rekommenderar att du använder autentisering av tjänstens huvudnamn, särskilt för en schemalagd kopia. Giltighetstid för token kan inträffa med användarautentisering för autentiseringsuppgifter. Konfigurationsinformation finns i den [länkade tjänstegenskaper](#linked-service-properties) avsnittet.

## <a name="get-started"></a>Kom igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data till/från ett Azure Data Lake Store med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline för att kopiera data är att använda den **Kopieringsguiden**. En självstudiekurs om hur du skapar en pipeline med hjälp av guiden Kopiera finns i [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md).

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera pipelines.
2. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik. Till exempel om du kopierar data från Azure blob storage till en Azure Data Lake Store skapa du två länkade tjänster för att länka ditt Azure storage-konto och Azure Data Lake store till din datafabrik. Länkade tjänstegenskaper som är specifika för Azure Data Lake Store, se [länkade tjänstegenskaper](#linked-service-properties) avsnittet.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen. I det tidigare exemplet i det sista steget, skapar du en datauppsättning för att ange blobbehållare och mapp som innehåller indata. Och du skapar en annan datauppsättning för att ange mappen och filsökvägen i Data Lake store som innehåller de data som kopieras från blob storage. Egenskaper för datamängd som är specifika för Azure Data Lake Store, se [egenskaper för datamängd](#dataset-properties) avsnittet.
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. I exemplet som tidigare nämnts, använder du BlobSource som en källa och AzureDataLakeStoreSink som mottagare för kopieringsaktiviteten. På samma sätt, om du kopierar från Azure Data Lake Store till Azure Blob Storage, använder du AzureDataLakeStoreSource och BlobSink i kopieringsaktiviteten. Kopiera Aktivitetsegenskaper som är specifika för Azure Data Lake Store, se [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnittet. Mer information om hur du använder ett datalager som en källa eller mottagare klickar du på länken i föregående avsnitt för ditt datalager.

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format. Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från ett Azure Data Lake Store finns [JSON-exempel](#json-examples-for-copying-data-to-and-from-data-lake-store) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för Data Lake Store.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
En länkad tjänst länkar ett datalager till en data factory. Du skapar en länkad tjänst av typen **AzureDataLakeStore** att länka dina Data Lake Store-data till din datafabrik. I följande tabell beskrivs JSON-element som är specifika för Data Lake Store-länkade tjänster. Du kan välja mellan tjänstens huvudnamn och användarautentisering för autentiseringsuppgifter.

| Egenskap  | Beskrivning | Obligatoriskt |
|:--- |:--- |:--- |
| **typ** | Type-egenskapen måste anges till **AzureDataLakeStore**. | Ja |
| **dataLakeStoreUri** | Information om Azure Data Lake Store-konto. Den här informationen antar ett av följande format: `https://[accountname].azuredatalakestore.net/webhdfs/v1` eller `adl://[accountname].azuredatalakestore.net/`. | Ja |
| **Prenumerations-ID** | Azure-prenumerations-ID som Data Lake Store-kontot tillhör. | Krävs för mottagare |
| **resourceGroupName** | Azure resursgruppens namn som det Data Lake Store-kontot tillhör. | Krävs för mottagare |

### <a name="service-principal-authentication-recommended"></a>Autentisering av tjänstens huvudnamn (rekommenderas)
Registrera en entitet för program i Azure Active Directory (Azure AD) för att använda autentisering av tjänstens huvudnamn, och ge det åtkomst till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst-autentisering](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:
* Program-ID:t
* Programnyckel
* Klient-ID:t

> [!IMPORTANT]
> Kontrollera att du ge tjänstens huvudnamn rätt behörighet i Azure Data Lake Store:
>- **Du använder Data Lake Store som källa**, ge minst **Läs + kör** dataåtkomst med behörighet att visa och kopiera innehållet i en mapp eller **Läs** tillstånd att kopiera en fil. Inga krav på administratörsnivå kontroll.
>- **Du använder Data Lake Store som mottagare**, ge minst **skriva + köra** dataåtkomst med behörighet att skapa underordnade objekt i mappen. Och om du använder Azure IR möjligheter för kopia (både källa och mottagare finns i molnet), för att låta Data Factory identifiera Data Lake Store-region, bevilja minst **läsare** roll i konto-åtkomstkontroll (IAM). Om du vill undvika den här IAM-rollen [ange executionLocation](data-factory-data-movement-activities.md#global) med platsen för ditt Data Lake Store i kopieringsaktiviteten.
>- Om du **använda guiden Kopiera för att skapa en pipeline**, ge minst **läsare** roll i konto-åtkomstkontroll (IAM). Dessutom ge minst **Läs + kör** behörighet att din Data Lake Store-roten (”/”) och dess underordnade. Annars visas meddelandet ”autentiseringsuppgifterna är ogiltiga”.

Använd autentisering av tjänstens huvudnamn genom att ange följande egenskaper:

| Egenskap  | Beskrivning | Obligatoriskt |
|:--- |:--- |:--- |
| **servicePrincipalId** | Ange programmets klient-ID. | Ja |
| **servicePrincipalKey** | Ange programmets nyckel. | Ja |
| **klient** | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Ja |

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

### <a name="user-credential-authentication"></a>Användarautentisering för autentiseringsuppgifter
Du kan också använda användarautentisering för autentiseringsuppgifter för att kopiera från eller till Data Lake Store genom att ange följande egenskaper:

| Egenskap  | Beskrivning | Obligatoriskt |
|:--- |:--- |:--- |
| **Auktorisering** | Klicka på den **auktorisera** i Data Factory-redigeraren och ange dina autentiseringsuppgifter som tilldelar automatiskt genererade auktorisering URL: en till den här egenskapen. | Ja |
| **sessionId** | OAuth sessions-ID från OAuth-auktorisering sessionen. Varje sessions-ID är unik och kan bara användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory-redigeraren. | Ja |

> [!IMPORTANT]
> Kontrollera att du bevilja användaren rätt behörighet i Azure Data Lake Store:
>- **Du använder Data Lake Store som källa**, ge minst **Läs + kör** dataåtkomst med behörighet att visa och kopiera innehållet i en mapp eller **Läs** tillstånd att kopiera en fil. Inga krav på administratörsnivå kontroll.
>- **Du använder Data Lake Store som mottagare**, ge minst **skriva + köra** dataåtkomst med behörighet att skapa underordnade objekt i mappen. Och om du använder Azure IR möjligheter för kopia (både källa och mottagare finns i molnet), för att låta Data Factory identifiera Data Lake Store-region, bevilja minst **läsare** roll i konto-åtkomstkontroll (IAM). Om du vill undvika den här IAM-rollen [ange executionLocation](data-factory-data-movement-activities.md#global) med platsen för ditt Data Lake Store i kopieringsaktiviteten.
>- Om du **använda guiden Kopiera för att skapa en pipeline**, ge minst **läsare** roll i konto-åtkomstkontroll (IAM). Dessutom ge minst **Läs + kör** behörighet att din Data Lake Store-roten (”/”) och dess underordnade. Annars visas meddelandet ”autentiseringsuppgifterna är ogiltiga”.

**Exempel: Användarautentisering för autentiseringsuppgifter**
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

#### <a name="token-expiration"></a>Giltighetstid för token
Auktoriseringskod som du genererar med hjälp av den **auktorisera** knappen upphör att gälla efter en viss tidsperiod. Följande meddelande innebär att autentiseringstoken har upphört att gälla:

Åtgärdsfel för autentiseringsuppgift: invalid_grant - AADSTS70002: Fel vid verifiering av autentiseringsuppgifter. AADSTS70008: Det tillhandahållna åtkomst beviljandet har upphört att gälla eller återkallats. Trace-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 tidsstämpel: 2015-12-15 21-09-31Z.

I följande tabell visas de upphör att gälla tiderna för olika typer av användarkonton:

| Användartyp | Upphör att gälla efter |
|:--- |:--- |
| Användarkonton *inte* hanteras av Azure Active Directory (till exempel @hotmail.com eller @live.com) |12 timmar |
| Användarkonton som hanteras av Azure Active Directory |Kör 14 dagar efter det sista segmentet <br/><br/>90 dagar, om ett segment baserat på en OAuth-baserad länkad tjänst körs minst en gång var fjortonde dag |

Om du ändrar ditt lösenord innan de token upphör att gälla, token upphör att gälla omedelbart. Du ser meddelandet som nämnts tidigare i det här avsnittet.

Du kan auktorisera kontot med hjälp av den **auktorisera** knappen när token upphör att distribuera den länkade tjänsten. Du kan också skapa värden för den **sessionId** och **auktorisering** egenskaper via programmering med hjälp av följande kod:


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
Mer information om Data Factory-klasser som används i koden, finns det [AzureDataLakeStoreLinkedService klass](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService klass](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), och [ AuthorizationSessionGetResponse klassen](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) ämnen. Lägg till en referens till version `2.9.10826.1824` av `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` för den `WindowsFormsWebAuthenticationDialog` klass som används i koden.

## <a name="troubleshooting-tips"></a>Felsökningstips

**Symptom:** När du kopierar data **till** Azure Data Lake Store, om Kopieringsaktivitet misslyckas med följande fel:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Rotorsak:** Det finns 2 möjliga orsaker:

1. Den `resourceGroupName` och/eller `subscriptionId` anges i Azure Data Lake Store länkad felaktiga;
2. Användaren eller tjänstens huvudnamn har inte behörigheten som krävs.

**Lösning:**

1. Kontrollera att den `subscriptionId` och `resourceGroupName` du anger i den länkade tjänsten `typeProperties` verkligen är de som ditt data lake-konto som hör till.

2. Kontrollera att du ger minst **läsare** roll till användaren eller tjänstens huvudnamn på data lake-konto. Nedan visas hur du gör det:

    1. Gå till Azure Portal -> ditt Data Lake Store-konto
    2. Klicka på **åtkomstkontroll (IAM)** på bladet i Data Lake Store
    3. Klicka på **Lägg till rolltilldelning**
    4. Ange **rollen** som **läsare**, och Välj användaren eller tjänstens huvudnamn som du använder för Kopiera för att bevilja åtkomst

3. Om du inte vill att bevilja **läsare** roll till användaren eller tjänstens huvudnamn, alternativ är att [uttryckligen ange en plats för körning](data-factory-data-movement-activities.md#global) i kopia activitywith platsen för ditt Data Lake Store. Exempel:

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
Om du vill ange en datauppsättning som representerar indata i ett Data Lake Store som du anger den **typ** egenskapen på datauppsättningen till **AzureDataLakeStore**. Ange den **linkedServiceName** egenskap med namnet på Data Lake Store-länkade tjänst. En fullständig lista över JSON-avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt i en datauppsättning i JSON, till exempel **struktur**, **tillgänglighet**, och **princip**, är liknande för alla datauppsättningstyper av (Azure SQL database, Azure-blob och Azure-tabell för exempel). Den **typeProperties** avsnittet är olika för varje typ av datauppsättning och tillhandahåller information som plats och formatet för data i datalagret.

Den **typeProperties** avsnittet för en datauppsättning av typen **AzureDataLakeStore** innehåller följande egenskaper:

| Egenskap  | Beskrivning | Obligatoriskt |
|:--- |:--- |:--- |
| **folderPath** |Sökvägen till behållaren och mappen i Data Lake Store. |Ja |
| **fileName** |Namnet på filen i Azure Data Lake Store. Den **fileName** egenskapen är valfri och skiftlägeskänsliga. <br/><br/>Om du anger **fileName**, aktiviteten (inklusive kopia) fungerar på den specifika filen.<br/><br/>När **fileName** inte har angetts, kopiera innehåller alla filer i **folderPath** i datauppsättningen för indata.<br/><br/>När **fileName** har inte angetts för en utdatauppsättning och **preserveHierarchy** har inte angetts i aktiviteten mottagare, namnet på den genererade filen är i formatet `Data._Guid_.txt`. Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Nej |
| **partitionedBy** |Den **partitionedBy** egenskapen är valfri. Du kan använda den för att ange en dynamisk sökväg och filnamn för time series-data. Till exempel **folderPath** kan parameteriseras för varje timme som data. Information och exempel finns i egenskapen partitionedBy. |Nej |
| **Format** | Följande formattyper av stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, och **ParquetFormat**. Ange den **typ** egenskapen under **format** till någon av dessa värden. Mer information finns i den [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [ORC-format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-Format ](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitten i den [format och komprimering stöds av Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikeln. <br><br> Om du vill kopiera filer ”som – är” mellan filbaserade (binär kopia), hoppar du över den `format` avsnittet i både inkommande och utgående datamängd definitioner. |Nej |
| **compression** | Ange typ och komprimeringsnivå för data. Typer som stöds är **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Stöds nivåer **Optimal** och **snabbast**. Mer information finns i [format och komprimering stöds av Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

### <a name="the-partitionedby-property"></a>Egenskapen partitionedBy
Du kan ange dynamiska **folderPath** och **fileName** egenskaper för time series-data med den **partitionedBy** egenskap, Data Factory-funktioner och systemvariabler. Mer information finns i [Azure Data Factory - funktioner och systemvariabler](data-factory-functions-variables.md) artikeln.


I följande exempel `{Slice}` ersätts med värdet för variabeln för Data Factory-system `SliceStart` i det format som anges (`yyyyMMddHH`). Namnet `SliceStart` avser starttiden för sektorn. Den `folderPath` egenskapen är olika för varje sektor som i `wikidatagateway/wikisampledataout/2014100103` eller `wikidatagateway/wikisampledataout/2014100104`.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

I följande exempel, år, månad, dag och tid för `SliceStart` extraheras till olika variabler som används av den `folderPath` och `fileName` egenskaper:
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
Mer information om time series-datauppsättningar, schemaläggning och sektorer finns i den [datauppsättningar i Azure Data Factory](data-factory-create-datasets.md) och [Data Factory schemaläggning och körning](data-factory-scheduling-and-execution.md) artiklar.


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principen är tillgängliga för alla typer av aktiviteter.

Egenskaperna som är tillgängliga i den **typeProperties** avsnittet för en aktivitet som varierar med varje aktivitetstyp av. För en Kopieringsaktivitet variera de beroende på vilka typer av källor och mottagare.

**AzureDataLakeStoreSource** stöder följande egenskap i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Obligatoriskt |
| --- | --- | --- | --- |
| **recursive** |Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. |SANT (standard), FALSKT |Nej |

**AzureDataLakeStoreSink** har stöd för följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Obligatoriskt |
| --- | --- | --- | --- |
| **copyBehavior** |Anger kopieringsbeteendet. |<b>PreserveHierarchy</b>: Bevarar filen hierarkin i målmappen. Den relativa sökvägen för källfilen för källmappen är identisk med den relativa sökvägen för målfilen till målmappen.<br/><br/><b>FlattenHierarchy</b>: Alla filer från källmappen skapas i den första nivån i målmappen. Målfiler som skapas med automatiskt skapade namn.<br/><br/><b>MergeFiles</b>: Slår samman alla filer från källmappen till en fil. Om namnet på filen / bloben har angetts, är sammanfogade filnamnet det angivna namnet. Annars är filnamnet automatiskt genererade. |Nej |

### <a name="recursive-and-copybehavior-examples"></a>rekursiva och copyBehavior exempel
Det här avsnittet beskrivs kopieringsåtgärden för olika kombinationer av värden för rekursiv och copyBehavior resulterande beteende.

| rekursiv | copyBehavior | Resulterande beteende |
| --- | --- | --- |
| true |preserveHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med samma struktur som källa<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatiskt genererade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File5 |
| true |mergeFiles |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 + fil3 + File4 + filen 5 innehållet slås samman i en fil med autogenererade filnamn |
| false |preserveHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/><br/>Subfolder1 med fil3, File4 och File5 plockas inte upp. |
| false |flattenHierarchy |För en källmapp Mapp1 med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatiskt genererade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/><br/><br/>Subfolder1 med fil3, File4 och File5 plockas inte upp. |
| false |mergeFiles |För en källmapp Mapp1 med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 innehållet slås samman i en fil med automatiskt genererade namnet. Automatiskt genererade namn på File1<br/><br/>Subfolder1 med fil3, File4 och File5 plockas inte upp. |

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimeringsformat de format som stöds
Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikeln.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>JSON-exempel för att kopiera data till och från Data Lake Store
I följande exempel får exempel JSON-definitioner. Du kan använda dessa exempel definitioner för att skapa en pipeline med hjälp av den [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Exemplen visar hur du kopierar data till och från Data Lake Store och Azure Blob storage. Men du kan kopiera data _direkt_ från källor till någon av de angivna egenskaperna. Mer information finns i avsnittet ”datalager som stöds och format” i den [flytta data med hjälp av Kopieringsaktiviteten](data-factory-data-movement-activities.md) artikeln.

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Exempel: Kopiera data från Azure Blob Storage till Azure Data Lake Store
Exempelkoden i det här avsnittet visar:

* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En länkad tjänst av typen [AzureDataLakeStore](#linked-service-properties).
* Indata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureDataLakeStore](#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [AzureDataLakeStoreSink](#copy-activity-properties).

Exemplen visar hur time series-data från Azure Blob Storage är kopieras till Data Lake Store varje timme.

**Länkad Azure Storage-tjänst**

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

**Azure Data Lake Store-länkade tjänst**

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
> Konfigurationsinformation finns i den [länkade tjänstegenskaper](#linked-service-properties) avsnittet.
>

**Indatauppsättning för Azure-blobb**

I följande exempel data hämtas från en ny blob varje timme (`"frequency": "Hour", "interval": 1`). Mappnamn för sökvägen och filnamnet för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad och dagsdelen av starttiden. Namnet på filen använder timandelen i starttiden. Den `"external": true` inställningen informerar Data Factory-tjänsten att tabellen är extern till datafabriken och inte kommer från en aktivitet i data factory.

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

I följande exempel kopierar data till Data Lake Store. Nya data kopieras till Data Lake Store varje timme.

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

**Kopiera aktivitet i en pipeline med en blobbkällan och en Data Lake Store-mottagare**

I följande exempel innehåller pipelinen en Kopieringsaktivitet som är konfigurerad för användning av indata och utdata datauppsättningar. Kopieringsaktivitet är schemalagd att köras varje timme. I pipeline-JSON-definitionen i `source` är `BlobSource`, och `sink` är `AzureDataLakeStoreSink`.

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
* Indata [datauppsättning](data-factory-create-datasets.md) av typen [AzureDataLakeStore](#dataset-properties).
* Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [AzureDataLakeStoreSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Koden kopierar time series-data från Data Lake Store till en Azure-blob varje timme.

**Azure Data Lake Store-länkade tjänst**

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
> Konfigurationsinformation finns i den [länkade tjänstegenskaper](#linked-service-properties) avsnittet.
>

**Länkad Azure Storage-tjänst**

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

I det här exemplet, ställa in `"external"` till `true` informerar Data Factory-tjänsten att tabellen är extern till datafabriken och inte kommer från en aktivitet i data factory.

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
**Utdatauppsättning för Azure-blobb**

I följande exempel data skrivs till en ny blob varje timme (`"frequency": "Hour", "interval": 1`). Sökvägen till mappen för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delen av starttiden.

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

**En Kopieringsaktivitet i en pipeline med en Azure Data Lake Store-källa och mottagare blob**

I följande exempel innehåller pipelinen en Kopieringsaktivitet som är konfigurerad för användning av indata och utdata datauppsättningar. Kopieringsaktivitet är schemalagd att köras varje timme. I pipeline-JSON-definitionen i `source` är `AzureDataLakeStoreSource`, och `sink` är `BlobSink`.

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

Du kan också mappa kolumner från datauppsättningen för källan till kolumner i datauppsättning för mottagare i aktivitetsdefinitionen kopia. Mer information finns i [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Mer information om de faktorer som påverkar prestanda för Kopieringsaktiviteten och hur du optimerar den, finns det [Kopieringsaktiviteten prestanda- och Justeringsguiden](data-factory-copy-activity-performance.md) artikeln.

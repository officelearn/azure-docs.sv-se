---
title: Kopiera data till och från Azure Data Lake Store | Microsoft Docs
description: Lär dig att kopiera data till och från Data Lake Store med hjälp av Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ce5909bd522ab7af77846af598506ea69058bd5c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
# <a name="copy-data-to-and-from-data-lake-store-by-using-data-factory"></a>Kopiera data till och från Data Lake Store med hjälp av Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-azure-datalake-connector.md)
> * [Version 2 – förhandsversion](../connector-azure-data-lake-store.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Azure Data Lake Store-anslutningen i V2](../connector-azure-data-lake-store.md).

Den här artikeln förklarar hur du använder Kopieringsaktiviteten i Azure Data Factory för att flytta data till och från Azure Data Lake Store. Den bygger på den [Data movement aktiviteter](data-factory-data-movement-activities.md) artikel, en översikt över dataflyttning med Kopieringsaktiviteten.

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure Data Lake Store** till följande data lagras:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Skapa ett Data Lake Store-konto innan du skapar en pipeline med Kopieringsaktiviteten. Mer information finns i [Kom igång med Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Typer av autentiseringsmetoder som stöds
Data Lake Store-kopplingen har stöd för dessa typer av autentisering:
* Autentisering av tjänstens huvudnamn
* Användarautentisering autentiseringsuppgifter (OAuth) 

Vi rekommenderar att du använder service principal autentisering, särskilt för en schemalagd datauppsättning kopia. Giltighetstid för token kan inträffa med autentiseringsuppgifter för användarautentisering. Konfigurationsinformation finns i [länkade tjänstegenskaper](#linked-service-properties) avsnitt.

## <a name="get-started"></a>Kom igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data till och från ett Azure Data Lake Store med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline för att kopiera data är att använda den **guiden Kopiera**. En självstudiekurs om hur du skapar en pipeline med hjälp av guiden Kopiera finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare:

1. Skapa en **datafabriken**. En datafabrik kan innehålla en eller flera pipelines. 
2. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory. Om du kopierar data från en Azure blobblagring till Azure Data Lake Store, skapa till exempel två länkade tjänster för att länka ditt Azure storage-konto och Azure Data Lake store till din data factory. Länkad tjänst-egenskaper som är specifika för Azure Data Lake Store finns i [länkade tjänstegenskaper](#linked-service-properties) avsnitt. 
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. I det tidigare exemplet i det sista steget, skapar du en datauppsättning för att ange blob-behållaren och mappen som innehåller indata. Och du skapar en annan dataset för att ange mappen och filsökvägen i Data Lake store som innehåller de data som kopieras från blob storage. Egenskaper för datamängd som är specifika för Azure Data Lake Store, se [egenskaper för datamängd](#dataset-properties) avsnitt.
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. I exemplet ovan kan använda du BlobSource som en källa och AzureDataLakeStoreSink som en mottagare för kopieringsaktiviteten. På samma sätt om du kopierar från Azure Data Lake Store till Azure Blob Storage, använder du AzureDataLakeStoreSource och BlobSink i kopieringsaktiviteten. Kopiera Aktivitetsegenskaper som är specifika för Azure Data Lake Store, se [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnitt. Mer information om hur du använder ett dataarkiv som en källa eller en mottagare klickar du på länken i föregående avsnitt för datalager.  

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från ett Azure Data Lake Store finns [JSON-exempel](#json-examples-for-copying-data-to-and-from-data-lake-store) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter till Data Lake Store.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
En länkad tjänst länkar ett datalager till en data factory. Du skapar en länkad tjänst av typen **AzureDataLakeStore** länka dina Data Lake Store-data till din data factory. I följande tabell beskrivs JSON-element som är specifika för Data Lake Store länkade tjänster. Du kan välja mellan tjänstens huvudnamn och autentiseringsuppgifter för användarautentisering.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **typ** | Egenskapen type måste anges till **AzureDataLakeStore**. | Ja |
| **dataLakeStoreUri** | Information om Azure Data Lake Store-konto. Den här informationen har någon av följande format: `https://[accountname].azuredatalakestore.net/webhdfs/v1` eller `adl://[accountname].azuredatalakestore.net/`. | Ja |
| **prenumerations-ID** | ID för Azure-prenumeration som Data Lake Store-kontot tillhör. | Krävs för sink |
| **resourceGroupName** | Azure resursgruppens namn som Data Lake Store-kontot tillhör. | Krävs för sink |

### <a name="service-principal-authentication-recommended"></a>Tjänstens huvudnamn autentisering (rekommenderas)
Om du vill använda huvudnamn autentiseringen av tjänsten registrera en entitet för program i Azure Active Directory (AD Azure) och bevilja åtkomst till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst autentisering](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:
* Program-ID:t
* Nyckeln för programmet 
* Klient-ID:t

> [!IMPORTANT]
> Kontrollera att du ger service principal rätt behörighet i Azure Data Lake Store:
>- **Att använda Data Lake Store som datakälla**, bevilja minst **Läs + Execute** data behörighet att visa och kopiera innehållet i en mapp eller **Läs** tillstånd att kopiera en fil. Inga krav på administratörsnivå kontroll.
>- **Att använda Data Lake Store som mottagare**, bevilja minst **skriva + köra** data behörighet att skapa underordnade objekt i mappen. Och om du använder Azure IR för att ge kopia (både källa och mottagare är i molnet), för att låta Data Factory identifiera Data Lake Store region, bevilja minst **Reader** roll i kontot åtkomstkontroll (IAM). Om du vill undvika den här IAM-rollen [ange executionLocation](data-factory-data-movement-activities.md#global) med platsen för din Data Lake Store i en Kopieringsaktivitet.
>- Om du **använder guiden Kopiera för att redigera pipelines**, bevilja minst **Reader** roll i kontot åtkomstkontroll (IAM). Dessutom ger minst **Läs + Execute** behörighet till din Data Lake Store-rot (”/”) och dess underordnade. Annars kan meddelandet ”autentiseringsuppgifterna är ogiltiga”.

Använd service principal autentisering genom att ange följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **servicePrincipalId** | Ange programmets klient-ID. | Ja |
| **servicePrincipalKey** | Ange programmets nyckeln. | Ja |
| **Klient** | Ange information om klient (domain name eller klient ID) under där programmet finns. Du kan hämta den hovrar muspekaren i det övre högra hörnet i Azure-portalen. | Ja |

**Exempel: Service principal autentisering**
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

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **Auktorisering** | Klicka på den **auktorisera** i den Data Factory-redigeraren och ange dina autentiseringsuppgifter som tilldelar automatiskt genererade auktorisering URL till den här egenskapen. | Ja |
| **Sessions-ID** | OAuth sessions-ID från OAuth-auktorisering sessionen. Varje sessions-ID är unikt och kan bara användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory-redigeraren. | Ja |

> [!IMPORTANT]
> Kontrollera att du bevilja användaren rätt behörighet i Azure Data Lake Store:
>- **Att använda Data Lake Store som datakälla**, bevilja minst **Läs + Execute** data behörighet att visa och kopiera innehållet i en mapp eller **Läs** tillstånd att kopiera en fil. Inga krav på administratörsnivå kontroll.
>- **Att använda Data Lake Store som mottagare**, bevilja minst **skriva + köra** data behörighet att skapa underordnade objekt i mappen. Och om du använder Azure IR för att ge kopia (både källa och mottagare är i molnet), för att låta Data Factory identifiera Data Lake Store region, bevilja minst **Reader** roll i kontot åtkomstkontroll (IAM). Om du vill undvika den här IAM-rollen [ange executionLocation](data-factory-data-movement-activities.md#global) med platsen för din Data Lake Store i en Kopieringsaktivitet.
>- Om du **använder guiden Kopiera för att redigera pipelines**, bevilja minst **Reader** roll i kontot åtkomstkontroll (IAM). Dessutom ger minst **Läs + Execute** behörighet till din Data Lake Store-rot (”/”) och dess underordnade. Annars kan meddelandet ”autentiseringsuppgifterna är ogiltiga”.

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

#### <a name="token-expiration"></a>Token upphör att gälla
Auktoriseringskoden som du skapar med hjälp av den **auktorisera** knappen upphör att gälla efter en viss tid. Följande meddelande innebär att autentiseringstoken har upphört att gälla:

Autentiseringsuppgifter fel: invalid_grant - AADSTS70002: fel vid verifiering av autentiseringsuppgifter. AADSTS70008: Bevilja beviljade åtkomsten har upphört att gälla eller återkallats. Trace-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 tidsstämpel: 2015-12-15 21-09-31Z.

I följande tabell visas förfallodatum gånger med olika typer av användarkonton:

| Användartyp | Upphör att gälla efter |
|:--- |:--- |
| Användarkonton *inte* hanteras av Azure Active Directory (till exempel @hotmail.com eller @live.com) |12 timmar |
| Användarkonton som hanteras av Azure Active Directory |14 dagar efter det sista segmentet som kör <br/><br/>90 dagar, om ett segment baserat på en länkad OAuth-tjänst körs på minst en gång var fjortonde dag |

Om du ändrar ditt lösenord innan token förfallotiden token upphör att gälla omedelbart. Meddelandet som nämnts tidigare i det här avsnittet visas.

Du kan omauktorisera kontot med hjälp av den **auktorisera** knappen när token upphör att distribuera om den länkade tjänsten. Du kan också generera värden för den **sessionId** och **auktorisering** egenskaper programmatiskt genom att använda följande kod:


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
Mer information om Data Factory-klasser som används i koden finns i [AzureDataLakeStoreLinkedService klassen](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService klassen](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), och [AuthorizationSessionGetResponse klassen](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) avsnitt. Lägg till en referens till version `2.9.10826.1824` av `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` för den `WindowsFormsWebAuthenticationDialog` klass som används i koden.

## <a name="troubleshooting-tips"></a>Felsökningstips

**Symptom:** vid kopiering av data **till** Azure Data Lake Store, om din kopieringsaktiviteten misslyckas med följande fel:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Orsak:** finns 2 möjliga orsaker:

1. Den `resourceGroupName` och/eller `subscriptionId` anges i Azure Data Lake Store länkade tjänsten är felaktig.
2. Användaren eller tjänstens huvudnamn har inte behörigheten som krävs.

**Lösning:**

1. Kontrollera att den `subscriptionId` och `resourceGroupName` du anger i den länkade tjänsten `typeProperties` verkligen är de som ditt data lake-konto som tillhör.

2. Kontrollera att du har gett minst ”**Reader**” roll till användaren eller tjänstens huvudnamn på data lake-konto. Här är att göra det:

    1. Gå till Azure Portal -> ditt Data Lake Store-konto
    2. Klicka på ”Access Control (IAM)” i bladet Data Lake Store
    3. Klicka på ”Lägg till” i bladet för ”åtkomst kontroll (IAM)”
    4. Ange ”roll” som ”läsare” och Välj användaren eller huvudnamn för tjänsten som du använder för att bevilja åtkomst

3. Om du inte vill ge rollen ”Läsare” för användare eller tjänstens huvudnamn alernative är att [uttryckligen ange en plats för körning av](data-factory-data-movement-activities.md#global) i Kopiera activitywith platsen för din Data Lake Store. Exempel:

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
Om du vill ange en datamängd som representerar indata i ett Data Lake Store som du anger den **typen** -egenskapen för datamängden som **AzureDataLakeStore**. Ange den **linkedServiceName** länkad egenskap på dataset till namnet på Data Lake Store-tjänsten. En fullständig lista över JSON-avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt i en dataset i JSON, t.ex **struktur**, **tillgänglighet**, och **princip**, är liknande för alla typer av datauppsättningen (Azure SQL-databasen, Azure blob och Azure-tabellen, till exempel). Den **typeProperties** avsnitt är olika för varje typ av dataset och ger information, till exempel plats och formatet på data i datalagret. 

Den **typeProperties** avsnittet för en dataset av typen **AzureDataLakeStore** innehåller följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **folderPath** |Sökvägen till behållaren och mappen i Data Lake Store. |Ja |
| **fileName** |Namnet på filen i Azure Data Lake Store. Den **fileName** egenskapen är valfri och är skiftlägeskänsliga. <br/><br/>Om du anger **fileName**, aktiviteten (inklusive kopia) fungerar på filen.<br/><br/>När **fileName** anges kopia innehåller alla filer i **folderPath** i inkommande datamängden.<br/><br/>När **fileName** har inte angetts för en datamängd för utdata och **preserveHierarchy** har inte angetts i aktiviteten sink namnet på den genererade filen har formatet Data. _GUID_.txt'. Till exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Nej |
| **partitionedBy** |Den **partitionedBy** egenskapen är valfri. Du kan använda den för att ange en dynamisk sökväg och filnamn för time series-data. Till exempel **folderPath** kan parameteriseras för varje timme av data. Mer information och exempel finns [egenskapen partitionedBy](#using-partitionedby-property). |Nej |
| **Format** | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, och **ParquetFormat**. Ange den **typen** egenskap under **format** till någon av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [ORC format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitten i den [format och komprimering stöds av Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikel. <br><br> Om du vill kopiera filer ”som-är” mellan filbaserade butiker (binär kopia) kan du hoppa över den `format` avsnitt i både inkommande och utgående dataset-definitioner. |Nej |
| **Komprimering** | Ange typ och kompression för data. Typer som stöds är **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Stöds nivåerna **Optimal** och **snabbast**. Mer information finns i [format och komprimering stöds av Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

### <a name="the-partitionedby-property"></a>Egenskapen partitionedBy
Du kan ange dynamiska **folderPath** och **fileName** egenskaper för time series-data med den **partitionedBy** egenskapen, Data Factory-funktioner och systemvariabler. Mer information finns i [Azure Data Factory - funktioner och systemvariabler](data-factory-functions-variables.md) artikel.


I följande exempel `{Slice}` ersätts med värdet för variabeln Data Factory system `SliceStart` i det format som specificerats (`yyyyMMddHH`). Namnet `SliceStart` refererar till starttiden för sektorn. Den `folderPath` egenskapen är olika för varje segment som i `wikidatagateway/wikisampledataout/2014100103` eller `wikidatagateway/wikisampledataout/2014100104`.

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
Mer information om tidsserier datauppsättningar, schemaläggning och segment, finns det [datauppsättningar i Azure Data Factory](data-factory-create-datasets.md) och [Data Factory schemaläggning och körning av](data-factory-scheduling-and-execution.md) artiklar. 


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, ingående och utgående tabeller och principen är tillgängliga för alla typer av aktiviteter.

Egenskaper som är tillgängliga i den **typeProperties** avsnitt i en aktivitet varierar med varje aktivitetstyp. För en Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

**AzureDataLakeStoreSource** stöder följande egenskap i den **typeProperties** avsnitt:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| **Rekursiva** |Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. |SANT (standardvärdet), FALSKT |Nej |


**AzureDataLakeStoreSink** stöder följande egenskaper på den **typeProperties** avsnitt:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| **copyBehavior** |Anger beteendet kopia. |<b>PreserveHierarchy</b>: bevarar fil hierarkin i målmappen. Den relativa sökvägen för källfilen till källmappen är identisk med den relativa sökvägen för filen till målmappen.<br/><br/><b>FlattenHierarchy</b>: alla filer från källmappen skapas i den första nivån i målmappen. Mål-filer som skapas med namn som genererats automatiskt.<br/><br/><b>MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om fil-eller blob anges är kopplade filnamnet det angivna namnet. Annars är filnamnet automatiskt genererade. |Nej |

### <a name="recursive-and-copybehavior-examples"></a>rekursiva och copyBehavior exempel
Det här avsnittet beskriver resultatet av kopieringen för olika kombinationer av värden rekursiv och copyBehavior.

| Rekursiva | copyBehavior | Resultatet |
| --- | --- | --- |
| true |preserveHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med samma struktur som källa<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File5 |
| true |mergeFiles |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 + fil3 + File4 + filen 5 innehållet slås samman till en fil med automatiskt genererade namnet |
| false |preserveHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 har skapats med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/><br/>Subfolder1 med fil3, File4 och File5 har inte plockats. |
| false |flattenHierarchy |För en källmapp Mapp1 med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 har skapats med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil2<br/><br/><br/>Subfolder1 med fil3, File4 och File5 har inte plockats. |
| false |mergeFiles |För en källmapp Mapp1 med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 har skapats med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 innehållet slås samman till en fil med automatiskt genererade namnet. automatiskt genererade namnet på File1<br/><br/>Subfolder1 med fil3, File4 och File5 har inte plockats. |

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimering format som stöds
Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikel.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>JSON-exempel för att kopiera data till och från Data Lake Store
Följande exempel ger exempel JSON-definitioner. Du kan använda dessa exempel definitioner för att skapa en pipeline med hjälp av den [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Exemplen visar hur du kopierar data till och från Data Lake Store och Azure Blob storage. Dock datan kan kopieras _direkt_ från någon av källorna till någon av de stöds egenskaperna. Mer information finns i avsnittet ”stöds datalager och format” i den [flytta data med hjälp av Kopieringsaktiviteten](data-factory-data-movement-activities.md) artikel.  

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Exempel: Kopiera data från Azure Blobblagring till Azure Data Lake Store
Exempelkoden i det här avsnittet visar:

* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En länkad tjänst av typen [AzureDataLakeStore](#linked-service-properties).
* Indata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Utdata [dataset](data-factory-create-datasets.md) av typen [AzureDataLakeStore](#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med en kopia-aktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [AzureDataLakeStoreSink](#copy-activity-properties).

Exemplen visar hur tidsserier data från Azure Blob Storage är kopieras till Data Lake Store varje timme. 

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

**Azure Data Lake Store länkade tjänsten**

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
> Konfigurationsinformation finns i [länkade tjänstegenskaper](#linked-service-properties) avsnitt.
>

**Indatauppsättning för Azure-blobb**

I följande exempel data hämtas från en ny blob varje timme (`"frequency": "Hour", "interval": 1`). Mappen sökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad och dagsdelen av starttiden. Namnet på filen använder timdelen av starttiden. Den `"external": true` inställningen informerar Data Factory-tjänsten att tabellen är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

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

**Azure Data Lake Store utdatauppsättningen**

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


**Kopiera aktivitet i en pipeline med en blob-källa och mottagare ett Data Lake Store**

I följande exempel innehåller pipeline kopieringsaktiviteten som är konfigurerad att använda indata och utdata datauppsättningar. Kopiera aktiviteten ska köras varje timme. I pipeline-JSON-definitionen av `source` är inställd på `BlobSource`, och `sink` är inställd på `AzureDataLakeStoreSink`.

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

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Exempel: Kopiera data från Azure Data Lake Store till en Azure blob
Exempelkoden i det här avsnittet visar:

* En länkad tjänst av typen [AzureDataLakeStore](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Indata [dataset](data-factory-create-datasets.md) av typen [AzureDataLakeStore](#dataset-properties).
* Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med en kopia-aktivitet som använder [AzureDataLakeStoreSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Koden kopieras time series-data från Data Lake Store till en Azure blob varje timme. 

**Azure Data Lake Store länkade tjänsten**

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
> Konfigurationsinformation finns i [länkade tjänstegenskaper](#linked-service-properties) avsnitt.
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
**Azure Data Lake-indatauppsättningen**

I det här exemplet anger `"external"` till `true` informerar Data Factory-tjänsten att tabellen är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

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

I följande exempel data skrivs till en ny blob varje timme (`"frequency": "Hour", "interval": 1`). Sökvägen till mappen för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delen av starttiden.

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

**En kopia aktivitet i en pipeline med ett Azure Data Lake Store-källa och en blob-mottagare**

I följande exempel innehåller pipeline kopieringsaktiviteten som är konfigurerad att använda indata och utdata datauppsättningar. Kopiera aktiviteten ska köras varje timme. I pipeline-JSON-definitionen av `source` är inställd på `AzureDataLakeStoreSource`, och `sink` är inställd på `BlobSink`.

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

Du kan mappa kolumner från käll-dataset till kolumner i datauppsättning för sink i aktivitetsdefinitionen kopia. Mer information finns i [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Läs om de faktorer som påverkar Kopieringsaktiviteten prestanda och hur du optimerar den i den [prestandajustering guide och Kopieringsaktivitet prestanda](data-factory-copy-activity-performance.md) artikel.

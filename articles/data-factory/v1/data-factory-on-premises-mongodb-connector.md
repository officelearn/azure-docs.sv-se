---
title: Flytta data från MongoDB med Data Factory | Microsoft Docs
description: Läs mer om hur du flyttar data från MongoDB-databas med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 433a8b2f9fb1f4c4599afbb807e9270992a98a52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824192"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Flytta data från MongoDB med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-on-premises-mongodb-connector.md)
> * [Version 2 (aktuell version)](../connector-mongodb.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [MongoDB-anslutningsappen i V2](../connector-mongodb.md).


Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data från en lokal MongoDB-databas. Den bygger på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten.

Du kan kopiera data från ett datalager för lokal MongoDB till alla datalager för mottagare som stöds. En lista över datalager som stöds som mottagare av Kopieringsaktivitet finns i den [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Data factory stöder för närvarande endast flyttar data från en MongoDB-datalager till datalager, men inte för att flytta data från andra datalager till en MongoDB-databasen.

## <a name="prerequisites"></a>Nödvändiga komponenter
För Azure Data Factory-tjänsten ska kunna ansluta till din lokala MongoDB-databas, måste du installera följande komponenter:

- MongoDB-versioner som stöds är: 2.4, 2.6, 3.0, 3.2, 3.4 och 3.6.
- Data Management Gateway på samma dator som är värd för databasen eller på en separat dator att undvika konkurrerar om resurser med databasen. Data Management Gateway är en programvara som ansluter till lokala datakällor till molntjänster på ett säkert och hanterat sätt. Se [Data Management Gateway](data-factory-data-management-gateway.md) nedan för information om Data Management Gateway. Se [flytta data från lokal plats till molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln stegvisa instruktioner om hur du konfigurerar gatewayen en datapipeline att flytta data.

    När du installerar gatewayen installeras automatiskt en Microsoft MongoDB ODBC-drivrutin som används för att ansluta till MongoDB.

    > [!NOTE]
    > Du måste använda gatewayen för att ansluta till MongoDB, även om den är värd för virtuella Azure IaaS-datorer. Om du vill ansluta till en instans av MongoDB som finns i molnet, kan du även installera gateway-instans i IaaS-VM.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data från ett datalager för lokal MongoDB med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen.
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett datalager för lokal MongoDB hittar [JSON-exempel: Kopiera data från MongoDB till Azure Blob](#json-example-copy-data-from-mongodb-to-azure-blob) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för MongoDB-källa:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för **OnPremisesMongoDB** länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| type |Type-egenskapen måste anges till: **OnPremisesMongoDb** |Ja |
| server |IP-adressen eller värdnamnet namnet på MongoDB-servern. |Ja |
| port |TCP-port som MongoDB-servern använder för att lyssna efter klientanslutningar. |Valfritt, standardvärde: 27017 |
| authenticationType |Grundläggande eller anonym. |Ja |
| användarnamn |Användarkonto för att få åtkomst till MongoDB. |Ja (om du använder grundläggande autentisering). |
| password |Lösenordet för användaren. |Ja (om du använder grundläggande autentisering). |
| authSource |Namnet på MongoDB-databasen som du vill använda för att kontrollera dina autentiseringsuppgifter för autentisering. |Valfritt (om du använder grundläggande autentisering). standard: använder administratörskontot och databasen som anges med egenskapen databaseName. |
| databaseName |Namnet på MongoDB-databasen som du vill komma åt. |Ja |
| gatewayName |Namnet på den gateway som ansluter till datalagret. |Ja |
| encryptedCredential |Autentiseringsuppgifter har krypterats av gateway. |Valfri |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av (Azure SQL, Azure-blob, Azure-tabell osv.).

Den **typeProperties** avsnittet är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. TypeProperties avsnittet för datauppsättningen av typen **MongoDbCollection** har följande egenskaper:

| Egenskap  | Beskrivning | Obligatoriskt |
| --- | --- | --- |
| Samlingsnamn |Namnet på samlingen i MongoDB-databas. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principen är tillgängliga för alla typer av aktiviteter.

Egenskaper som är tillgängliga i den **typeProperties** avsnittet aktivitetens å andra sidan varierar med varje aktivitetstyp av. För kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare.

När källan är av typen **MongoDbSource** följande egenskaper är tillgängliga i avsnittet typeProperties:

| Egenskap  | Beskrivning | Tillåtna värden | Obligatoriskt |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-92 frågesträngen. Till exempel: Välj * från MyTable. |Nej (om **collectionName** av **datauppsättning** har angetts) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>JSON-exempel: Kopiera data från MongoDB till Azure Blob
Det här exemplet innehåller exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Den visar hur du kopierar data från en lokal MongoDB till Azure Blob Storage. Dock datan kan kopieras till någon av de mottagare som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.

Exemplet har följande data factory-entiteter:

1. En länkad tjänst av typen [OnPremisesMongoDb](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [MongoDbCollection](#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [MongoDbSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från ett frågeresultat i MongoDB-databas till en blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

Som ett första steg att konfigurera data management gateway enligt anvisningarna i den [Data Management Gateway](data-factory-data-management-gateway.md) artikeln.

**MongoDB-länkad tjänst:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
        }
    }
}
```

**Länkad Azure Storage-tjänst:**

```json
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

**MongoDB datauppsättningen för indata:** Ange ”external”: ”true” informerar Data Factory-tjänsten att tabellen är extern till datafabriken och inte kommer från en aktivitet i data factory.

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Utdatauppsättning för Azure Blob:**

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1). Sökvägen till mappen för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kopiera aktivitet i en pipeline med MongoDB käll- och Blob-mottagare:**

Pipelinen innehåller en Kopieringsaktivitet som är konfigurerad att använda ovanstående indata och utdata datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **MongoDbSource** och **mottagare** är **BlobSink**. SQL-frågan som angetts för den **fråga** egenskapen väljer vilka data under den senaste timmen att kopiera.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Schemat av Data Factory
Azure Data Factory-tjänsten skapar schema från en MongoDB-samling med hjälp av de senaste 100 dokumenten i samlingen. Om dokumenten 100 inte innehåller fullständig schemat, att vissa kolumner ignoreras under kopieringen.

## <a name="type-mapping-for-mongodb"></a>Mappning för MongoDB
Som vi nämnde i den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikeln kopieringsaktiviteten utför automatisk konverteringar från typer av datakällor till mottagare typer med följande metod i steg 2:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare

När data flyttas till MongoDB används följande mappningar från MongoDB-typer till .NET-typer.

| MongoDB-typ | .NET framework-typ |
| --- | --- |
| Binär |Byte[] |
| Boolean |Boolean |
| Date |Datetime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectId |String |
| Sträng |String |
| UUID |Guid |
| Object |Renormalized till att platta ut kolumner med ”_” som kapslade avgränsare |

> [!NOTE]
> Mer information om stöd för matriser med virtuella tabeller, som avser [stöd för komplexa typer med hjälp av virtuella tabeller](#support-for-complex-types-using-virtual-tables) nedan.

För närvarande stöds inte följande datatyper för MongoDB: DBPointer, JavaScript, Max per minut nyckel, reguljära uttryck, symboler, tidsstämpel, Odefinierad

## <a name="support-for-complex-types-using-virtual-tables"></a>Stöd för komplexa typer med hjälp av virtuella tabeller
Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från MongoDB-databasen. För komplexa typer, till exempel matriser eller objekt med olika typer i dokumenten normaliserar drivrutinen igen data till motsvarande virtuella tabeller. Om en tabell innehåller sådana kolumner, genererar drivrutinen mer specifikt kan följande virtuella tabeller:

* En **bastabellen**, som innehåller samma data som den verkliga tabellen utom de komplexa typen kolumnerna. Bastabellen använder samma namn som den verkliga tabell som representerar.
* En **virtuella tabellen** för varje kolumn för komplex typ, vilket utökar kapslade data. Virtuella tabeller namnges med namnet på tabellen verkliga, avgränsare ”_” och namnet på den matris eller ett objekt.

Virtuella tabellerna hänvisar till data i tabellen verkliga aktiverar drivrutinen att komma åt den Avnormaliserade data. Se avsnittet nedan. Du kan komma åt innehållet i MongoDB-matriser genom att fråga och ansluta till virtuella tabeller.

Du kan använda den [Kopieringsguiden](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) att visa en lista över tabeller i MongoDB-databas, inklusive virtuella tabeller intuitivt och förhandsgranska data i. Du kan också skapa en fråga i guiden Kopiera och validera om du vill se resultatet.

### <a name="example"></a>Exempel
Till exempel är ”ExampleTable” nedan en MongoDB-tabell som har en kolumn med en matris med objekt i varje cell – fakturor och en kolumn med en matris med skalära typer – betyg.

| _id | Kundens namn | Fakturor | Servicenivå | Klassificeringar |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:”123”, item:”toaster”, price:”456”, discount:”0.2”}, {invoice_id:”124”, item:”oven”, price: ”1235”, discount: ”0.2”}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:”135”, item:”fridge”, price: ”12543”, discount: ”0.0”}] |Guld |[1,2] |

Drivrutinen skulle generera flera virtuella tabeller som representerar en enda tabell. Den första virtuella tabellen är bastabellen med namnet ”ExampleTable” visas nedan. Bastabellen innehåller alla data för den ursprungliga tabellen, men data från matriser har utelämnats och utökas i virtuella tabeller.

| _id | Kundens namn | Servicenivå |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Guld |

Följande tabeller visar virtuella tabeller som representerar de ursprungliga matriserna i det här exemplet. Dessa tabeller innehåller följande:

* En referens tillbaka till den ursprungliga primärnyckelkolumnen som motsvarar raden i den ursprungliga matrisen (via kolumnen _id)
* En indikation om placeringen av data inom den ursprungliga matrisen
* Utökade data för varje element i matrisen

Tabell ”ExampleTable_Invoices”:

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | Objekt | price | Rabatt |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Toaster |456 |0.2 |
| 1111 |1 |124 |vara |1235 |0.2 |
| 2222 |0 |135 |kylskåp |12543 |0.0 |

Tabell ”ExampleTable_Ratings”:

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Kartkälla till kolumner för mottagare
Mer information om mappning av kolumner i datauppsättningen för källan till kolumner i datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor
Kom ihåg att undvika oväntade resultat repeterbarhet när kopiera data från relationsdata lagras. I Azure Data Factory kan du köra en sektor manuellt. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att en sektor som körs när ett fel uppstår. När ett segment ska köras på nytt på något sätt, måste du se till att samma data läses oavsett hur många gånger som en sektor körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.

## <a name="next-steps"></a>Nästa steg
Se [flytta data mellan lokala och molnbaserade](data-factory-move-data-between-onprem-and-cloud.md) artikel stegvisa instruktioner för att skapa en datapipeline som flyttar data från ett lokalt datalager till ett datalager med Azure.

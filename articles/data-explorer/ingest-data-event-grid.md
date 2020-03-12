---
title: Mata in Azure-blobar i Azure Data Explorer
description: I den här artikeln får du lära dig hur du skickar lagrings konto data till Azure Datautforskaren att använda en Event Grid-prenumeration.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 47870410741cf96e289014fab5a9c2eab26759b1
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096413"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Mata in blobbar i Azure Datautforskaren genom att prenumerera på Event Grid meddelanden

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-mall](data-connection-event-grid-resource-manager.md)

Azure Datautforskaren är en snabb och skalbar data utforsknings tjänst för logg-och telemetridata. Den erbjuder kontinuerlig inmatning (data inläsning) från blobbar skrivna till BLOB-behållare. 

I den här artikeln får du lära dig hur du ställer in en [Azure Event Grid](/azure/event-grid/overview) prenumeration och dirigerar händelser till Azure datautforskaren via en Event Hub. Innan du börjar bör du ha ett lagrings konto med en Event Grid-prenumeration som skickar meddelanden till Azure Event Hubs. Sedan skapar du en Event Grid data anslutning och ser data flödet i systemet.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/).
* [Ett kluster och en databas](create-cluster-database-portal.md).
* [Ett lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [En Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Skapa en Event Grid-prenumeration på ditt lagringskonto

1. Hitta ditt lagrings konto i Azure Portal.
1. Välj **händelser** > **händelse prenumeration**.

    ![Frågeprogramlänk](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. I fönstret **Skapa händelseprenumeration** på fliken **Grundläggande** anger du följande värden:

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Namn | *test-grid-connection* | Namnet på det händelse rutnät som du vill skapa.|
    | Händelseschema | *Event Grid schema* | Det schema som ska användas för Event Grid. |
    | Typ av ämne | *Lagringskonto* | Typ av Event Grid-ämne. |
    | Ämnesresurs | *gridteststorage* | Namnet på ditt lagringskonto. |
    | Prenumerera på alla händelsetyper | *Rensa* | Få inte meddelanden vid alla händelser. |
    | Definierade händelsetyper | *BLOB skapad* | Vilka specifika händelser du ska meddelas om. |
    | Typ av slutpunkt | *Event Hubs* | Typ av slutpunkt som du skickar händelserna till. |
    | Slutpunkt | *test-hub* | Händelsehubben som du skapade. |
    | | |

1. Välj fliken **filter** om du vill spåra filer från en speciell behållare. Ange filter för meddelanden på följande sätt:
    * Fältet **subject börjar med** är det *litterala* prefixet för BLOB-behållaren. När mönstret som används är *StartsWith*kan det sträcka sig över flera behållare. Jokertecken får inte användas.
     Det *måste* anges enligt följande: *`/blobServices/default/containers/`* [containerprefix]
    * Fältet **Subject Ends With** (Ämnet slutar med) är det *literala* blobsuffixet. Jokertecken får inte användas.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Skapa en måltabell i Azure Data Explorer

Skapa en tabell i Azure Datautforskaren där Event Hubs ska skicka data. Skapa tabellen i klustret och databasen för beredd i kraven.

1. Välj **Fråga** under klustret på Azure-portalen.

    ![Frågeprogramlänk](media/ingest-data-event-grid/query-explorer-link.png)

1. Kopiera följande kommando till fönstret och välj **Kör** för att skapa den tabell (TestTable) som ska ta emot inmatade data.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Kör genereringsfråga](media/ingest-data-event-grid/run-create-table.png)

1. Kopiera följande kommando till fönstret och välj **Kör** för att mappa inkommande JSON-data till kolumnnamnen och datatyperna i tabellen (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Skapa en Event Grid-dataanslutning i Azure Data Explorer

Anslut nu till Event Grid från Azure Datautforskaren så att data som flödar till BLOB-behållaren strömmas till test tabellen. 

1. Välj **Meddelanden** i verktygsfältet för att kontrollera att distributionen av händelsehubben lyckades.

1. Under klustret som du har skapat väljer du **databaser** > **TestDatabase**.

    ![Välj testdatabas](media/ingest-data-event-grid/select-test-database.png)

1. Välj **data inmatning** > **Lägg till data anslutning**.

    ![Datainhämtning](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Välj Anslutnings typ: **Blob Storage**.

1. Fyll i formuläret med följande information och välj **skapa**.

    ![Händelsehubbanslutning](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Datakälla:

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Namn på dataanslutning | *test-hub-connection* | Namnet på den anslutning som du vill skapa i Azure Datautforskaren.|
    | Lagringskontoprenumeration | Ditt prenumerations-ID | Prenumerations-ID:t där lagringskontot finns.|
    | Lagringskonto | *gridteststorage* | Namnet på det lagrings konto som du skapade tidigare.|
    | Event Grid | *test-grid-connection* | Namnet på det händelse rutnät som du har skapat. |
    | Namn på händelsehubb | *test-hub* | Händelsehubben som du skapade. Det här fältet fylls i automatiskt när du väljer ett event-rutnät. |
    | Konsumentgrupp | *test-group* | Konsument gruppen som definierats i den händelsehubben som du skapade. |
    | | |

    Måltabell:

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Tabell | *TestTable* | Tabellen som du skapade i **TestDatabase**. |
    | Dataformat | *JSON* | Format som stöds är Avro, CSV, JSON, Multiline JSON, PSV, SOH, SCSV, TSV, RAW och TXT. Komprimerings alternativ som stöds: zip och GZip |
    | Kolumnmappning | *TestMapping* | Den mappning som du skapade i **TestDatabase**, som mappar inkommande JSON-data till kolumnnamnen och datatyperna i **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Generera exempeldata

Nu när Azure Datautforskaren och lagrings kontot är anslutna kan du skapa exempel data och ladda upp det till blob-lagringen.

Vi kommer att arbeta med ett litet kommandoskript som utfärdar några grundläggande Azure CLI-kommandon för att interagera med Azure Storage-resurser. Det här skriptet skapar en ny behållare i ditt lagrings konto, laddar upp en befintlig fil (som en BLOB) till behållaren och listar sedan blobarna i behållaren. Du kan använda [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) för att köra skriptet direkt i portalen.

Spara data i en fil och ladda upp den med det här skriptet:

```Json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```bash
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> För att uppnå bästa möjliga inmatnings prestanda *måste den* okomprimerade bloben som skickas för inmatningen förmedlas. Eftersom Event Grid-meddelanden bara innehåller grundläggande information, måste storleks informationen förmedlas uttryckligen. Du kan ange information om okomprimerad storlek genom att ange egenskapen `rawSizeBytes` i BLOB-metadata med den *okomprimerade* data storleken i byte.

### <a name="ingestion-properties"></a>Inmatnings egenskaper

Du kan ange inmatnings [egenskaperna](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#ingestion-properties) för BLOB-inmatningen via BLOB-metadata.

Dessa egenskaper kan anges:

|**Egenskap** | **Egenskaps Beskrivning**|
|---|---|
| `rawSizeBytes` | Storlek på rå data (okomprimerad). För Avro/ORC/Parquet är detta storleken innan format-Specific Compression används.|
| `kustoTable` |  Namnet på den befintliga mål tabellen. Åsidosätter `Table` som angetts på bladet `Data Connection`. |
| `kustoDataFormat` |  Data format. Åsidosätter `Data format` som angetts på bladet `Data Connection`. |
| `kustoIngestionMappingReference` |  Namnet på den befintliga inmatnings mappningen som ska användas. Åsidosätter `Column mapping` som angetts på bladet `Data Connection`.|
| `kustoIgnoreFirstRecord` | Om värdet är `true`ignorerar Kusto den första raden i blobben. Använd i tabell format data (CSV, TSV eller liknande) för att ignorera huvuden. |
| `kustoExtentTags` | Sträng som representerar [taggar](/azure/kusto/management/extents-overview#extent-tagging) som ska bifogas i den resulterande omfattningen. |
| `kustoCreationTime` |  Åsidosätter [$IngestionTime](/azure/kusto/query/ingestiontimefunction?pivots=azuredataexplorer) för blobben, formaterade som en ISO 8601-sträng. Används för Autofyll. |

> [!NOTE]
> Azure Datautforskaren tar inte bort blobar efter inmatning.
> Behåll Blobbarna för thrre till fem dagar.
> Använd [Azure Blob Storage-livscykeln](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) för att hantera BLOB-borttagning. 

## <a name="review-the-data-flow"></a>Granska dataflödet

> [!NOTE]
> Azure Datautforskaren har en agg regerings princip (batch) för data inmatning som är utformad för att optimera inmatnings processen.
Som standard konfigureras principen till 5 minuter.
Du kommer att kunna ändra principen vid ett senare tillfälle om det behövs. I den här artikeln kan du vänta en fördröjning på några minuter.

1. Under ditt händelserutnät på Azure-portalen ser du en aktivitetstopp när appen körs.

    ![Event Grid-graf](media/ingest-data-event-grid/event-grid-graph.png)

1. För att kontrollera hur många meddelanden som nått databasen hittills kör du följande fråga i testdatabasen.

    ```Kusto
    TestTable
    | count
    ```

1. Kör följande fråga i testdatabasen för att se innehållet i meddelandena.

    ```Kusto
    TestTable
    ```

    Resultatet bör se ut så här.

    ![Meddelanderesultat](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda händelserutnätet igen rensar du **test-hub-rg** för att undvika kostnader.

1. Välj **Resursgrupper** i Azure Portal längst till vänster och välj sedan den resursgrupp du skapat.  

    Om den vänstra menyn döljs väljer du ![Knappen Expandera](media/ingest-data-event-grid/expand.png) för att expandera den.

   ![Välj den resursgrupp som ska tas bort](media/ingest-data-event-grid/delete-resources-select.png)

1. Under **test-resource-group** väljer du **Ta bort resursgrupp**.

1. I det nya fönstret anger du namnet på den resurs grupp som ska tas bort (*test-Hub-RG*) och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Fråga efter data i Azure Datautforskaren](web-query-data.md)

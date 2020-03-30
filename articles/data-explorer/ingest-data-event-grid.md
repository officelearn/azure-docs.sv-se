---
title: Mata in Azure-blobar i Azure Data Explorer
description: I den här artikeln får du lära dig hur du skickar lagringskontodata till Azure Data Explorer med en Event Grid-prenumeration.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ec218b1638183db463ff09488c988cad64d78c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370448"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Ingest blobbar i Azure Data Explorer genom att prenumerera på Event Grid-meddelanden

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-mall](data-connection-event-grid-resource-manager.md)

Azure Data Explorer är en snabb och skalbar datautforskningstjänst för logg- och telemetridata. Det erbjuder kontinuerligt inmatning (datainläsning) från blobbar skrivna till blob-behållare. 

I den här artikeln får du lära dig hur du anger en [Azure Event Grid-prenumeration](/azure/event-grid/overview) och dirigerar händelser till Azure Data Explorer via en händelsehubb. Till att börja med bör du ha ett lagringskonto med en prenumeration på eventrutnät som skickar meddelanden till Azure Event Hubs. Sedan skapar du en händelserutnätsdataanslutning och ser dataflödet i hela systemet.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).
* [Ett kluster och en databas](create-cluster-database-portal.md).
* [Ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [En händelsehubb](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Skapa en Event Grid-prenumeration på ditt lagringskonto

1. Hitta ditt lagringskonto i Azure-portalen.
1. Välj > **händelsehändelseprenumeration**. **Events**

    ![Frågeprogramlänk](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. I fönstret **Skapa händelseprenumeration** på fliken **Grundläggande** anger du följande värden:

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Namn | *test-grid-connection* | Namnet på det händelserutnät som du vill skapa.|
    | Händelseschema | *Event Grid-schema* | Schemat som ska användas för händelserutnätet. |
    | Typ av ämne | *Lagringskonto* | Typ av Event Grid-ämne. |
    | Ämnesresurs | *gridteststorage* | Namnet på ditt lagringskonto. |
    | Prenumerera på alla händelsetyper | *Tydlig* | Få inte meddelanden vid alla händelser. |
    | Definierade händelsetyper | *Blob skapas* | Vilka specifika händelser du ska meddelas om. |
    | Typ av slutpunkt | *Event Hubs* | Typ av slutpunkt som du skickar händelserna till. |
    | Slutpunkt | *test-hub* | Händelsehubben som du skapade. |
    | | |

1. Välj fliken **Filter** om du vill spåra filer från en viss behållare. Ange filter för meddelanden på följande sätt:
    * **Ämne börjar med** fältet är det *bokstavliga* prefixet för blob-behållaren. När mönstret som tillämpas *börjar med*kan det sträcka sig över flera behållare. Jokertecken får inte användas.
     Det *måste* anges enligt följande: *`/blobServices/default/containers/`*[containerprefix]
    * Fältet **Subject Ends With** (Ämnet slutar med) är det *literala* blobsuffixet. Jokertecken får inte användas.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Skapa en måltabell i Azure Data Explorer

Skapa en tabell i Azure Data Explorer där eventhubbar skickar data. Skapa tabellen i klustret och databasen som förberetts i förutsättningarna.

1. Välj **Fråga** under klustret på Azure-portalen.

    ![Frågeprogramlänk](media/ingest-data-event-grid/query-explorer-link.png)

1. Kopiera följande kommando till fönstret och välj **Kör** för att skapa tabellen (TestTable) som tar emot de intjesterade data.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Kör genereringsfråga](media/ingest-data-event-grid/run-create-table.png)

1. Kopiera följande kommando till fönstret och välj **Kör** för att mappa inkommande JSON-data till kolumnnamnen och datatyperna i tabellen (TestTable).

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Skapa en Event Grid-dataanslutning i Azure Data Explorer

Anslut nu till händelserutnätet från Azure Data Explorer, så att data som flödar in i blob-behållaren strömmas till testtabellen. 

1. Välj **Meddelanden** i verktygsfältet för att kontrollera att distributionen av händelsehubben lyckades.

1. Under **klustret** > som du skapade väljer du Databaser**TestDatabase**.

    ![Välj testdatabas](media/ingest-data-event-grid/select-test-database.png)

1. Välj **Datainmatning** > **Lägg till dataanslutning**.

    ![Datainhämtning](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Välj anslutningstyp: **Blob Storage**.

1. Fyll i formuläret med följande information och välj **Skapa**.

    ![Händelsehubbanslutning](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Datakälla:

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Namn på dataanslutning | *test-hub-connection* | Namnet på den anslutning som du vill skapa i Azure Data Explorer.|
    | Lagringskontoprenumeration | Ditt prenumerations-ID | Prenumerations-ID:t där lagringskontot finns.|
    | Lagringskonto | *gridteststorage* | Namnet på det lagringskonto som du skapade tidigare.|
    | Event Grid | *test-grid-connection* | Namnet på det händelserutnät som du skapade. |
    | Namn på händelsehubb | *test-hub* | Händelsehubben som du skapade. Det här fältet fylls automatiskt när du väljer ett händelserutnät. |
    | Konsumentgrupp | *test-group* | Konsumentgruppen som definierats i händelsehubben som du skapade. |
    | | |

    Måltabell:

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Tabell | *TestTable* | Tabellen som du skapade i **TestDatabase**. |
    | Dataformat | *Json* | Format som stöds är Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV, RAW och TXT. Komprimeringsalternativ som stöds: Zip och GZip |
    | Kolumnmappning | *TestMapping* | Den mappning som du skapade i **TestDatabase**, som mappar inkommande JSON-data till kolumnnamnen och datatyperna i **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Generera exempeldata

Nu när Azure Data Explorer och lagringskontot är anslutna kan du skapa exempeldata och överföra dem till blob-lagringen.

Vi kommer att arbeta med ett litet kommandoskript som utfärdar några grundläggande Azure CLI-kommandon för att interagera med Azure Storage-resurser. Skriptet skapar en ny behållare i ditt lagringskonto, överför en befintlig fil (som en blob) till den behållaren och visar sedan blobbar i behållaren. Du kan använda [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) för att köra skriptet direkt i portalen.

Spara data i en fil och ladda upp den med det här skriptet:

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
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
> För att uppnå bästa intag prestanda, måste den *okomprimerade* storleken på komprimerade blobbar som skickas för intag meddelas. Eftersom meddelanden om händelserutnät endast innehåller grundläggande information måste storleksinformationen uttryckligen meddelas. Information om okomprimerad storlek kan `rawSizeBytes` tillhandahållas genom att ange egenskapen på blobmetadata med den *okomprimerade* datastorleken i byte.

### <a name="ingestion-properties"></a>Egenskaper för intag

Du kan ange [egenskaperna för inmatning](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#ingestion-properties) av blob-inmatning via blobmetadata.

Dessa egenskaper kan ställas in:

|**Egenskap** | **Beskrivning av egenskapen**|
|---|---|
| `rawSizeBytes` | Storleken på råa (okomprimerade) data. För Avro/ORC/Parkett är detta storleken innan formatspecifik komprimering appliceras.|
| `kustoTable` |  Namn på den befintliga måltabellen. Åsidosätter `Table` bladets `Data Connection` uppsättning. |
| `kustoDataFormat` |  Dataformat. Åsidosätter `Data format` bladets `Data Connection` uppsättning. |
| `kustoIngestionMappingReference` |  Namnet på den befintliga inmatningsmappningen som ska användas. Åsidosätter `Column mapping` bladets `Data Connection` uppsättning.|
| `kustoIgnoreFirstRecord` | Om den `true`är inställd på ignorerar Kusto den första raden i blobben. Använd i tabellformatdata (CSV, TSV eller liknande) för att ignorera rubriker. |
| `kustoExtentTags` | Sträng som representerar [taggar](/azure/kusto/management/extents-overview#extent-tagging) som ska kopplas till resulterande utsträckning. |
| `kustoCreationTime` |  Åsidosätter [$IngestionTime](/azure/kusto/query/ingestiontimefunction?pivots=azuredataexplorer) för blobben, formaterad som en ISO 8601-sträng. Använd för återfyllning. |

> [!NOTE]
> Azure Data Explorer tar inte bort blobbar efter inmatning.
> Behåll blobbar i tre dagar.
> Använd [Azure Blob storage lifecycle](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) för att hantera blob-borttagning. 

## <a name="review-the-data-flow"></a>Granska dataflödet

> [!NOTE]
> Azure Data Explorer har en aggregeringsprincip (batchning) för datainmatning som utformats för att optimera inmatningsprocessen.
Som standard konfigureras principen till 5 minuter.
Du kan ändra principen vid ett senare tillfälle om det behövs. I den här artikeln kan du förvänta dig en latens på några minuter.

1. Under ditt händelserutnät på Azure-portalen ser du en aktivitetstopp när appen körs.

    ![Event Grid-graf](media/ingest-data-event-grid/event-grid-graph.png)

1. För att kontrollera hur många meddelanden som nått databasen hittills kör du följande fråga i testdatabasen.

    ```kusto
    TestTable
    | count
    ```

1. Kör följande fråga i testdatabasen för att se innehållet i meddelandena.

    ```kusto
    TestTable
    ```

    Resultatet bör se ut så här.

    ![Meddelanderesultat](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda händelserutnätet igen rensar du **test-hub-rg** för att undvika kostnader.

1. Välj **Resursgrupper** i Azure Portal längst till vänster och välj sedan den resursgrupp du skapat.  

    Om den vänstra menyn är dold väljer du ![Knappen Expandera](media/ingest-data-event-grid/expand.png) för att expandera den.

   ![Välj den resursgrupp som ska tas bort](media/ingest-data-event-grid/delete-resources-select.png)

1. Under **test-resource-group** väljer du **Ta bort resursgrupp**.

1. I det nya fönstret anger du namnet på den resursgrupp som ska tas bort (*test-hub-rg*) och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Fråga efter data i Azure Data Explorer](web-query-data.md)

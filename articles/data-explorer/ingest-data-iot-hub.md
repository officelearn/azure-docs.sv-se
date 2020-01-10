---
title: Mata in data från IoT Hub i Azure Datautforskaren
description: I den här artikeln får du lära dig hur du matar in data i Azure Datautforskaren från IoT Hub.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1052ef799949550ddaf492b8aa5b77ab9526d092
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780291"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer-preview"></a>Mata in data från IoT Hub till Azure Datautforskaren (förhands granskning)

> [!div class="op_single_selector"]
> * [Portalen](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager-mall](data-connection-iot-hub-resource-manager.md)

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Datautforskaren erbjuder inmatning (data inläsning) från IoT Hub, en Big data strömnings plattform och IoT-inmatnings tjänst.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Skapa [ett test kluster och en databas](create-cluster-database-portal.md) med databas namnet *testdb*.
* [Ett exempel på en app](https://github.com/Azure-Samples/azure-iot-samples-csharp) och dokumentation för att simulera en enhet.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) för att köra exempel appen.

## <a name="create-an-iot-hub"></a>Skapa en IoT-hubb

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Registrera en enhet i IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Skapa en måltabell i Azure Data Explorer

Nu skapar du en tabell i Azure Datautforskaren som IoT-hubbar ska skicka data till. Du skapar tabellen i klustret och databasen etablerad i [**krav**](#prerequisites).

1. På Azure-portalen går du till ditt kluster och väljer **Fråga**.

    ![ADX-fråga i portalen](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Kopiera följande kommando till fönstret och välj **Kör** för att skapa den tabell (TestTable) som tar emot insamlade data.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Kör genereringsfråga](media/ingest-data-iot-hub/run-create-query.png)

1. Kopiera följande kommando till fönstret och välj **Kör** för att mappa inkommande JSON-data till kolumnnamnen och datatyperna i tabellen (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Anslut Azure Datautforskaren-tabellen till IoT Hub

Nu ansluter du till IoT Hub från Azure Datautforskaren. När den här anslutningen är klar strömmas data som flödar till IoT Hub-strömmar till [mål tabellen som du skapade](#create-a-target-table-in-azure-data-explorer).

1. Välj **meddelanden** i verktygsfältet för att kontrol lera att distributionen av IoT Hub har slutförts.

1. Under klustret som du har skapat väljer du **databaser** och väljer sedan den databas som du skapade **testdb**.
    
    ![Välj testdatabas](media/ingest-data-iot-hub/select-database.png)

1. Välj **Datainmatning** och **Lägg till dataanslutning**. Fyll sedan i formuläret med följande information. Välj **skapa** när du är klar.

    ![IoT Hub anslutning](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Data källa**:

    **Inställning** | **Fältbeskrivning**
    |---|---|
    | Namn på dataanslutning | Namnet på den anslutning som du vill skapa i Azure Datautforskaren
    | IoT Hub | IoT Hub namn |
    | Princip för delad åtkomst | Namnet på principen för delad åtkomst. Måste ha Läs behörighet |
    | Konsumentgrupp |  Konsument gruppen som definierats i den IoT Hub inbyggda slut punkten |
    | Egenskaper för händelse system | [System egenskaperna för IoT Hub Event](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages). När du lägger till system egenskaper [skapar](/azure/kusto/management/tables#create-table) eller [uppdaterar](/azure/kusto/management/tables#alter-table-and-alter-merge-table) du tabell schema och [mappning](/azure/kusto/management/mappings) för att inkludera de valda egenskaperna. | | | 

    > [!NOTE]
    > Om du vill [redundansväxla manuellt](/azure/iot-hub/iot-hub-ha-dr#manual-failover)måste du återskapa data anslutningen.

    **Mål tabell**:

    Det finns två alternativ för att dirigera inmatade data: *statiska* och *dynamiska*. 
    I den här artikeln använder du statisk routning, där du anger tabell namn, data format och mappning. Låt därför **My data includes routing info** (Mina data innehåller routningsinformation) vara avmarkerat.

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Tabell | *TestTable* | Den tabell som du skapade i **testdb**. |
    | Dataformat | *JSON* | Format som stöds är Avro, CSV, JSON, Multiline JSON, PSV, SOHSV, SCSV, TSV, TSVE och TXT. |
    | Kolumnmappning | *TestMapping* | [Mappningen](/azure/kusto/management/mappings) som du skapade i **testdb**, som mappar inkommande JSON-data till kolumn namn och data typer för **testdb**. Krävs för JSON, Multiline-JSON och AVRO, och valfritt för andra format.|
    | | |

    > [!NOTE]
    > * Välj **mina data innehåller routningsinformation** för att använda dynamisk routning, där dina data innehåller den routningsinformation som krävs som visas i exempel kommentaren för [appen](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) . Om både statiska och dynamiska egenskaper anges åsidosätter de dynamiska egenskaperna de statiska. 
    > * Endast händelser som står i kö efter att du har skapat data anslutningen matas in.

[!INCLUDE [data-explorer-container-system-properties](../../includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>Generera exempel data för testning

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar simulerad telemetri om temperatur och luftfuktighet.

1. Ladda ned exempelprojektet för C# från https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip och extrahera ZIP-arkivet.

1. Navigera till C#-exempelprojektets rotmapp i ett lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device**.

1. Öppna filen **SimulatedDevice.cs** i en valfri textredigerare.

    Ersätt värdet för variabeln `s_connectionString` med enhets anslutnings strängen från [Registrera en enhet till IoT Hub](#register-a-device-to-the-iot-hub). Spara dina ändringar i filen **SimulatedDevice.cs**.

1. Installera de paket som krävs för programmet för simulerad enhet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    dotnet restore
    ```

1. Kör programmet för simulerad enhet genom att skapa och köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    dotnet run
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>Granska dataflödet

När appen genererar data kan du nu se data flödet från IoT-hubben till tabellen i klustret.

1. I Azure Portal, under din IoT-hubb, kan du se insamling i aktivitet medan appen körs.

    ![IoT Hub mått](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. För att kontrollera hur många meddelanden som nått databasen hittills kör du följande fråga i testdatabasen.

    ```Kusto
    TestTable
    | count
    ```

1. Kör följande fråga för att se innehållet i meddelandena:

    ```Kusto
    TestTable
    ```

    Resultat uppsättningen:
    
    ![Visa inmatade data resultat](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure Data Explorer har en sammansättningsprincip (batchbearbetning) för datainmatning som är utformad för att optimera inmatningsprocessen. Principen är konfigurerad till 5 minuter eller 500 MB data som standard, så att du kan uppleva en fördröjning. Se [batch-principen](/azure/kusto/concepts/batchingpolicy) för sammansättnings alternativ. 
    > * Konfigurera din tabell så att den stöder strömning och ta bort fördröjningen i svars tiden. Se [strömmande princip](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda din IoT Hub igen kan du rensa **test-Hub-RG**för att undvika att kostnader uppkommer.

1. Välj **Resursgrupper** i Azure Portal längst till vänster och välj sedan den resursgrupp du skapat.  

    Om den vänstra menyn döljs väljer du ![Knappen Expandera](media/ingest-data-event-hub/expand.png) för att expandera den.

   ![Välj den resursgrupp som ska tas bort](media/ingest-data-event-hub/delete-resources-select.png)

1. Under **test-resource-group** väljer du **Ta bort resursgrupp**.

1. I det nya fönstret skriver du namnet på resursgruppen som ska tas bort (*test-hub-rg*) och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Fråga efter data i Azure Datautforskaren](web-query-data.md)

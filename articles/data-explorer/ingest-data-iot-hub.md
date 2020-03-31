---
title: Övra data från IoT Hub till Azure Data Explorer
description: I den här artikeln får du lära dig hur du intänder (läser in) data i Azure Data Explorer från IoT Hub.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 78455c90bab694b77a5e4a56d0b40518867d8d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188354"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>Övra data från IoT Hub till Azure Data Explorer 

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager-mall](data-connection-iot-hub-resource-manager.md)

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Data Explorer erbjuder inmatning (datainläsning) från IoT Hub, en plattform för direktuppspelning av stordata och IoT-inmatningstjänst.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Skapa [ett testkluster och en databas](create-cluster-database-portal.md) med *testtestdb*för databasnamn .
* [En exempelapp](https://github.com/Azure-Samples/azure-iot-samples-csharp) och dokumentation för att simulera en enhet.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) för att köra exempelappen.

## <a name="create-an-iot-hub"></a>Skapa en Iot Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Registrera en enhet i IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Skapa en måltabell i Azure Data Explorer

Nu skapar du en tabell i Azure Data Explorer som IoT Hubs skickar data till. Du skapar tabellen i klustret och databasen som etablerats i [**Förutsättningar**](#prerequisites).

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

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Ansluta Azure Data Explorer-tabell till IoT-hubb

Nu ansluter du till IoT Hub från Azure Data Explorer. När den här anslutningen är klar strömmas data som flödar till iot-hubben till [måltabellen som du skapade](#create-a-target-table-in-azure-data-explorer).

1. Välj **Meddelanden** i verktygsfältet för att kontrollera att IoT Hub-distributionen lyckades.

1. Under klustret som du skapade väljer du **Databaser** och väljer sedan den databas som du skapade **testdb**.
    
    ![Välj testdatabas](media/ingest-data-iot-hub/select-database.png)

1. Välj **Datainmatning** och **Lägg till dataanslutning**. Fyll sedan i formuläret med följande information. Välj **Skapa** när du är klar.

    ![IoT Hub-anslutning](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Datakälla:**

    **Inställning** | **Fältbeskrivning**
    |---|---|
    | Namn på dataanslutning | Namnet på den anslutning som du vill skapa i Azure Data Explorer
    | IoT Hub | IoT Hub-namn |
    | Princip för delad åtkomst | Namnet på principen för delad åtkomst. Måste ha läsbehörighet |
    | Konsumentgrupp |  Konsumentgruppen som definieras i den inbyggda slutpunkten för IoT Hub |
    | Egenskaper för händelsesystem | Egenskaperna [för IoT Hub-händelsesystem](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages). När du lägger till systemegenskaper skapar eller [uppdaterar](/azure/kusto/management/alter-table-command) [du](/azure/kusto/management/create-table-command) tabellschema och [mappning](/azure/kusto/management/mappings) för att inkludera de valda egenskaperna. | | | 

    > [!NOTE]
    > Vid manuell [redundans](/azure/iot-hub/iot-hub-ha-dr#manual-failover)måste du återskapa dataanslutningen.

    **Måltabell:**

    Det finns två alternativ för routning av intövrade data: *statisk och* *dynamisk*. 
    I den här artikeln använder du statisk routning, där du anger tabellnamn, dataformat och mappning. Låt därför **My data includes routing info** (Mina data innehåller routningsinformation) vara avmarkerat.

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Tabell | *TestTable* | Tabellen som du skapade i **testdb**. |
    | Dataformat | *Json* | Format som stöds är Avro, CSV, JSON, MULTILINE JSON, PSV, SOHSV, SCSV, TSV, TSVE och TXT. |
    | Kolumnmappning | *TestMapping* | [Mappningen](/azure/kusto/management/mappings) som du skapade i **testdb**, som mappar inkommande JSON-data till kolumnnamn och datatyper för **testdb**. Krävs för JSON, MULTILINE JSON och AVRO, och som tillval för andra format.|
    | | |

    > [!NOTE]
    > * Välj **Mina data innehåller routningsinformation** om du vill använda dynamisk routning, där dina data innehåller nödvändig routningsinformation som visas i [exempelappkommentarerna.](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) Om både statiska och dynamiska egenskaper har angetts åsidosätter de dynamiska egenskaperna de statiska egenskaperna. 
    > * Endast händelser som gäller när du har skapat dataanslutningen intas.

[!INCLUDE [data-explorer-container-system-properties](../../includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>Generera exempeldata för testning

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar simulerad telemetri om temperatur och luftfuktighet.

1. Ladda ned exempelprojektet för C# från https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip och extrahera ZIP-arkivet.

1. Navigera till C#-exempelprojektets rotmapp i ett lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device**.

1. Öppna filen **SimulatedDevice.cs** i en valfri textredigerare.

    Ersätt variabelns `s_connectionString` värde med enhetsanslutningssträngen från [Registrera en enhet till IoT Hub](#register-a-device-to-the-iot-hub). Spara dina ändringar i filen **SimulatedDevice.cs**.

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

När appen genererar data kan du nu se dataflödet från IoT-hubben till tabellen i klustret.

1. I Azure-portalen, under din IoT-hubb, ser du den ökade aktiviteten medan appen körs.

    ![IoT Hub-mått](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. För att kontrollera hur många meddelanden som nått databasen hittills kör du följande fråga i testdatabasen.

    ```Kusto
    TestTable
    | count
    ```

1. Kör följande fråga för att se innehållet i meddelandena:

    ```Kusto
    TestTable
    ```

    Resultatuppsättningen:
    
    ![Visa intvalda dataresultat](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure Data Explorer har en sammansättningsprincip (batchbearbetning) för datainmatning som är utformad för att optimera inmatningsprocessen. Principen är konfigurerad till 5 minuter eller 500 MB data, som standard, så att du kan uppleva en svarstid. Se [batchprincip](/azure/kusto/concepts/batchingpolicy) för aggregeringsalternativ. 
    > * Konfigurera tabellen så att den stöder direktuppspelning och ta bort fördröjningen i svarstiden. Se [streamingprincipen](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda IoT Hub igen rensar du **testhubb-rg**för att undvika kostnader.

1. Välj **Resursgrupper** i Azure Portal längst till vänster och välj sedan den resursgrupp du skapat.  

    Om den vänstra menyn är dold väljer du ![Knappen Expandera](media/ingest-data-event-hub/expand.png) för att expandera den.

   ![Välj den resursgrupp som ska tas bort](media/ingest-data-event-hub/delete-resources-select.png)

1. Under **test-resource-group** väljer du **Ta bort resursgrupp**.

1. I det nya fönstret skriver du namnet på resursgruppen som ska tas bort (*test-hub-rg*) och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Fråga efter data i Azure Data Explorer](web-query-data.md)

---
title: Implementera Azure Databricks med en Cosmos DB-slutpunkt
description: Den här självstudien beskrivs hur du implementerar Azure Databricks i ett virtuellt nätverk med en tjänstslutpunkt aktiverad för Cosmos DB.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: 0d5442a63680227f3a6186330502666c92dc3129
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013205"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Självstudier: Implementera Azure Databricks med en Cosmos DB-slutpunkt

Den här självstudien beskrivs hur du implementerar ett virtuellt nätverk inmatade Databricks-miljö med en tjänstslutpunkt aktiverad för Cosmos DB.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en Azure Databricks-arbetsyta i ett virtuellt nätverk
> * Skapa en tjänstslutpunkt för Cosmos DB
> * Skapa ett Cosmos DB-konto och importera data
> * Skapa ett Azure Databricks-kluster
> * Fråga Cosmos DB från en Azure Databricks-anteckningsbok

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar måste du göra följande:

* Skapa en [Azure Databricks-arbetsytan i ett virtuellt nätverk](quickstart-create-databricks-workspace-vnet-injection.md).

* Ladda ned den [Spark-anslutningsappen](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Hämta exempeldata från den [noaa: S National Lagringsprotokollet för miljöinformation](https://www.ncdc.noaa.gov/stormevents/). Välj en delstat eller område och välj **Search**. På nästa sida, accepterar du standardvärdena och välj **Search**. Välj sedan **CSV hämta** till vänster på sidan för att hämta resultaten.

* Ladda ned den [förkompilerad binär](https://aka.ms/csdmtool) av Migreringsverktyget för Azure Cosmos DB Data.

## <a name="create-a-cosmos-db-service-endpoint"></a>Skapa en tjänstslutpunkt för Cosmos DB

1. När du har distribuerat en Azure Databricks-arbetsyta till ett virtuellt nätverk, går du till det virtuella nätverket i den [Azure-portalen](https://portal.azure.com). Lägg märke till de offentliga och privata undernät som har skapats genom Databricks-distributionen.

   ![Undernät för virtuella nätverk](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Välj den *offentliga undernät* och skapa en tjänstslutpunkt för Cosmos DB. Sedan **spara**.
   
   ![Lägg till en Cosmos DB-tjänstslutpunkt](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Skapa ett Cosmos DB-konto

1. Öppna Azure Portal. Längst upp till vänster på skärmen Välj **skapa en resurs > databaser > Azure Cosmos DB**.

2. Fyll i **instansinformation** på den **grunderna** fliken med följande inställningar:

   |Inställning|Value|
   |-------|-----|
   |Prenumeration|*din prenumeration*|
   |Resursgrupp|*din resursgrupp*|
   |Kontonamn|db-vnet-service-endpoint|
   |API|Core (SQL)|
   |Location|Västra USA|
   |Geo-redundans|Inaktivera|
   |Skrivningar i flera regioner|Aktivera|

   ![Lägg till en Cosmos DB-tjänstslutpunkt](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Välj den **nätverk** fliken och konfigurera ditt virtuella nätverk. 

   a. Välj det virtuella nätverket som du skapade som ett krav och välj sedan *offentliga undernät*. Observera att *privat undernät* har Anmärkning *slutpunkten för ”Microsoft AzureCosmosDB” saknas ”*. Detta beror på att Cosmos DB-tjänstslutpunkt aktiveras endast på den *offentliga undernät*.

   b. Kontrollera att du har **tillåta åtkomst från Azure-portalen** aktiverat. Den här inställningen kan du komma åt ditt Cosmos DB-konto från Azure-portalen. Om det här alternativet är inställt på **neka**, du får fel vid försök att komma åt ditt konto. 

   > [!NOTE]
   > Det är inte nödvändigt för den här självstudien, men du kan också aktivera *tillåter åtkomst från Min IP-adress* om du vill kunna komma åt ditt Cosmos DB-konto från din lokala dator. Om du ansluter till ditt konto med Cosmos DB SDK, måste du aktivera den här inställningen. Om det har inaktiverats felmeddelandet ”åtkomst nekad”-fel.

   ![Nätverksinställningar för cosmos DB-konto](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Välj **granska + skapa**, och sedan **skapa** att skapa Cosmos DB-kontot i virtuella nätverk.

5. När ditt Cosmos DB-konto har skapats går du till **nycklar** under **inställningar**. Kopiera primär anslutningssträng och spara den i en textredigerare för senare användning.

    ![Sidan för cosmos DB-konto nycklar](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Välj **Datautforskaren** och **ny samling** att lägga till en ny databas och samling till ditt Cosmos DB-konto.

    ![Ny cosmos DB-samling](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Ladda upp data till Cosmos DB

1. Öppna den grafiska gränssnitt-versionen av den [datamigreringsverktyget för Cosmos DB](https://aka.ms/csdmtool), **Dtui.exe**.

    ![Cosmos DB-Datamigreringsverktyg](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. På den **källinformation** fliken **CSV-filerna** i den **importera från** listrutan. Välj sedan **Lägg till filer** och lägga till storm data CSV som du har hämtat som ett krav.

    ![Cosmos DB Datamigreringsverktyget källinformation](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. På den **målinformation** fliken, ange anslutningssträngen. Formatet för anslutningssträngen är `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`. AccountEndpoint och AccountKey ingår i den primära anslutningssträngen som du sparade i föregående avsnitt. Lägg till `Database=<your database name>` i slutet av anslutningssträngen och väljer **Kontrollera**. Lägg sedan till nyckeln samling namn och en partition.

    ![Cosmos DB Datamigreringsverktyget målinformation](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Välj **nästa** tills du kommer till sidan Översikt. Välj **Import**.

## <a name="create-a-cluster-and-add-library"></a>Skapa ett kluster och lägga till bibliotek

1. Gå till din Azure Databricks-tjänst i den [Azure-portalen](https://portal.azure.com) och välj **Starta arbetsyta**.

   ![Starta Databricks-arbetsyta](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Skapa ett nytt kluster. Välj ett klusternamn och acceptera resten av standardinställningarna.

   ![Ny klusterinställningar](./media/service-endpoint-cosmosdb/create-cluster.png)

3. När klustret skapas, gå till sidan kluster och väljer den **bibliotek** fliken. Välj **installera nya** och ladda upp Spark connector jar-filen om du vill installera biblioteket.

    ![Installera Spark connector-biblioteket](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Du kan kontrollera att biblioteket har installerats på den **bibliotek** fliken.

    ![Biblioteksfliken i Databricks-kluster](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Fråga Cosmos DB från en Databricks notebook

1. Gå till Azure Databricks-arbetsytan och skapa en ny python notebook.

    ![Skapa ny anteckningsbok i Databricks](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Kör följande python-kod för att ställa in konfigurationen för Cosmos DB-anslutning. Ändra den **Endpoint**, **Masterkey**, **databasen**, och **samling** därefter.

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. Använd följande python-kod för att läsa in data och skapa en tillfällig vy.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Använd kommandot magic för att köra en SQL-instruktion som returnerar data.

    ```python
    %sql
    select * from storm
    ```

    Du har anslutit din VNet-installerade Databricks-arbetsyta till en tjänsts slutpunkt-aktiverade Cosmos DB-resurs. Att läsa mer om hur du ansluter till Cosmos DB i [Azure Cosmos DB-Anslutningsapp för Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, Azure Databricks-arbetsytan och alla relaterade resurser när de inte längre behövs. Tar bort jobbet undviker onödiga fakturering. Om du planerar att använda Azure Databricks-arbetsytan i framtiden kan du stoppa klustret och sedan starta det igen. Om du inte tänker fortsätta att använda den här Azure Databricks-arbetsytan, ta bort alla resurser som du skapade i den här självstudien med hjälp av följande steg:

1. I den vänstra menyn i Azure portal, klickar du på **resursgrupper** och klicka sedan på namnet på resursgruppen som du skapade.

2. På sidan med resursgrupper, väljer **ta bort**, skriver du namnet på resursen som ska tas bort i textrutan och välj sedan **ta bort** igen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du distribuerat en Azure Databricks-arbetsyta till ett virtuellt nätverk och används Cosmos DB Spark-anslutningsappen för att fråga Cosmos DB-data från Databricks. Om du vill veta mer om att arbeta med Azure Databricks i ett virtuellt nätverk kan du fortsätta till självstudien för att använda SQL Server med Azure Databricks.

> [!div class="nextstepaction"]
> [Självstudier: Fråga en SQL Server Linux Docker-behållare i ett virtuellt nätverk från en Azure Databricks-anteckningsbok](vnet-injection-sql-server.md)
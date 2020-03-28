---
title: Självstudiekurs - Implementera Azure Databricks med en Cosmos DB-slutpunkt
description: Den här självstudien beskriver hur du implementerar Azure Databricks i ett virtuellt nätverk med en tjänstslutpunkt aktiverad för Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: 4ac8c01e986cf1f3158c615a0791ba476e5bf1bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706153"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Självstudiekurs: Implementera Azure Databricks med en Cosmos DB-slutpunkt

Den här självstudien beskriver hur du implementerar en VNet-insprutad Databricks-miljö med en tjänstslutpunkt aktiverad för Cosmos DB.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa en Azure Databricks-arbetsyta i ett virtuellt nätverk
> * Skapa en slutpunkt för Cosmos DB-tjänst
> * Skapa ett Cosmos DB-konto och importera data
> * Skapa ett Azure Databricks-kluster
> * Fråga Cosmos DB från en Azure Databricks-anteckningsbok

## <a name="prerequisites"></a>Krav

Gör följande innan du börjar:

* Skapa en [Azure Databricks-arbetsyta i ett virtuellt nätverk](quickstart-create-databricks-workspace-vnet-injection.md).

* Ladda ner [Spark-kontakten](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Ladda ner exempeldata från [NOAA National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/). Markera ett tillstånd eller område och välj **Sök**. På nästa sida godkänner du standardinställningarna och väljer **Sök**. Välj sedan **CSV Download** till vänster på sidan för att hämta resultaten.

* Hämta den [förkompilerade binärfilen](https://aka.ms/csdmtool) för Azure Cosmos DB Data Migration Tool.

## <a name="create-a-cosmos-db-service-endpoint"></a>Skapa en slutpunkt för Cosmos DB-tjänst

1. När du har distribuerat en Azure Databricks-arbetsyta till ett virtuellt nätverk navigerar du till det virtuella nätverket i [Azure-portalen](https://portal.azure.com). Lägg märke till de offentliga och privata undernät som skapades via Databricks-distributionen.

   ![Virtuella nätverksundernät](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Välj det *offentliga undernätet* och skapa en Cosmos DB-tjänstslutpunkt. Spara **Save**sedan .
   
   ![Lägga till en cosmos DB-tjänstslutpunkt](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Skapa ett Cosmos DB-konto

1. Öppna Azure Portal. På skärmens övre vänstra sida väljer du **Skapa en resurs > databaser > Azure Cosmos DB**.

2. Fyll i **instansinformationen** på fliken **Grunderna** med följande inställningar:

   |Inställning|Värde|
   |-------|-----|
   |Prenumeration|*din prenumeration*|
   |Resursgrupp|*din resursgrupp*|
   |Kontonamn|db-vnet-service-slutpunkt|
   |API|Core (SQL)|
   |Location|USA, västra|
   |Geo-redundans|Inaktivera|
   |Skrivåtgärder för flera regioner|Aktivera|

   ![Lägga till en cosmos DB-tjänstslutpunkt](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Välj fliken **Nätverk** och konfigurera det virtuella nätverket. 

   a. Välj det virtuella nätverk som du skapade som en förutsättning och välj sedan *public-subnät*. Observera att *private-subnet* har anteckningen *"Microsoft AzureCosmosDB" slutpunkt saknas'*. Detta beror på att du bara har aktiverat Cosmos DB-tjänstslutpunkten i det *offentliga undernätet*.

   b. Se till att du har **Tillåt åtkomst från Azure-portalen** aktiverad. Med den här inställningen kan du komma åt ditt Cosmos DB-konto från Azure-portalen. Om det här alternativet är inställt **på Neka**får du felmeddelanden när du försöker komma åt ditt konto. 

   > [!NOTE]
   > Det är inte nödvändigt för den här guiden, men du kan också aktivera *Tillåt åtkomst från min IP* om du vill ha möjlighet att komma åt ditt Cosmos DB-konto från din lokala dator. Om du till exempel ansluter till ditt konto med Cosmos DB SDK måste du aktivera den här inställningen. Om den är inaktiverad visas felmeddelanden "Åtkomst nekad".

   ![Nätverksinställningar för Cosmos DB-konto](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Välj **Granska + Skapa**och **skapa** sedan för att skapa ditt Cosmos DB-konto i det virtuella nätverket.

5. När ditt Cosmos DB-konto har skapats navigerar du till **Nycklar** under **Inställningar**. Kopiera den primära anslutningssträngen och spara den i en textredigerare för senare användning.

    ![Sidan Produktförslagsnycklar för KOSmos DB-konto](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Välj **Data Explorer** och Ny **samling** om du vill lägga till en ny databas och samling i ditt Cosmos DB-konto.

    ![Cosmos DB ny kollektion](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Ladda upp data till Cosmos DB

1. Öppna den grafiska gränssnittsversionen av [datamigreringsverktyget för Cosmos DB](https://aka.ms/csdmtool), **Dtui.exe**.

    ![Azure Cosmos DB-datamigreringsverktyget](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Välj **CSV-filer** i **listrutan Importera från** på fliken **Källinformation.** Välj sedan **Lägg till filer** och lägg till stormdata CSV som du hämtade som en förutsättning.

    ![Källa till källa till källa till källa till cosmos DB-datamigreringsverktyg](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Mata in anslutningssträngen på fliken **Målinformation.** Anslutningssträngformatet `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`är . AccountEndpoint och AccountKey ingår i den primära anslutningssträngen som du sparade i föregående avsnitt. Lägg `Database=<your database name>` till i slutet av anslutningssträngen och välj **Verifiera**. Lägg sedan till samlingsnamn och partitionsnyckel.

    ![Målinformation om målinformation för Cosmos DB Data Migration Tool](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Välj **Nästa** tills du kommer till sammanfattningssidan. Välj sedan **Importera**.

## <a name="create-a-cluster-and-add-library"></a>Skapa ett kluster och lägga till bibliotek

1. Navigera till din Azure Databricks-tjänst i [Azure-portalen](https://portal.azure.com) och välj **Starta arbetsyta**.

   ![Starta Databricks arbetsyta](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Skapa ett nytt kluster. Välj ett klusternamn och acceptera de återstående standardinställningarna.

   ![Nya klusterinställningar](./media/service-endpoint-cosmosdb/create-cluster.png)

3. När klustret har skapats navigerar du till klustersidan och väljer fliken **Bibliotek.** Välj **Installera ny** och ladda upp Spark-anslutningsburkfilen för att installera biblioteket.

    ![Installera Spark-anslutningsbibliotek](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Du kan kontrollera att biblioteket har installerats på fliken **Bibliotek.**

    ![Fliken Databricks-klusterbibliotek](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Fråga Cosmos DB från en Databricks-anteckningsbok

1. Navigera till din Azure Databricks-arbetsyta och skapa en ny python-anteckningsbok.

    ![Skapa nya databricks-anteckningsbok](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Kör följande python-kod för att ställa in Cosmos DB-anslutningskonfigurationen. Ändra **slutpunkt**, **Masterkey**, **Databas**och **samling** därefter.

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

4. Använd följande magiska kommando för att köra ett SQL-uttryck som returnerar data.

    ```python
    %sql
    select * from storm
    ```

    Du har anslutit din VNet-injicerade Databricks-arbetsyta till en tjänstslutpunkt aktiverad Cosmos DB-resurs. Mer information om hur du ansluter till Cosmos DB finns i [Azure Cosmos DB Connector för Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen, Azure Databricks-arbetsytan och alla relaterade resurser. Om du tar bort jobbet undviks onödig fakturering. Om du planerar att använda Azure Databricks-arbetsytan i framtiden kan du stoppa klustret och starta om det senare. Om du inte ska fortsätta att använda den här Azure Databricks-arbetsytan tar du bort alla resurser som du har skapat i den här självstudien med hjälp av följande steg:

1. Klicka på **Resursgrupper** på menyn till vänster i Azure-portalen och klicka sedan på namnet på resursgruppen som du skapade.

2. På resursgruppssidan väljer du **Ta bort,** skriver namnet på den resurs som ska tas bort i textrutan och väljer sedan **Ta bort** igen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du distribuerat en Azure Databricks-arbetsyta till ett virtuellt nätverk och använt Cosmos DB Spark-kopplingen för att fråga Cosmos DB-data från Databricks. Om du vill veta mer om hur du arbetar med Azure Databricks i ett virtuellt nätverk fortsätter du till självstudien för att använda SQL Server med Azure Databricks.

> [!div class="nextstepaction"]
> [Självstudiekurs: Fråga en SQL Server Linux Docker-behållare i ett virtuellt nätverk från en Azure Databricks-anteckningsbok](vnet-injection-sql-server.md)

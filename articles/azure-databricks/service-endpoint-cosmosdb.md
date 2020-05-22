---
title: Självstudie – implementera Azure Databricks med en Cosmos DB-slutpunkt
description: I den här självstudien beskrivs hur du implementerar Azure Databricks i ett virtuellt nätverk med en tjänst slut punkt aktive rad för Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: e18c2b0f03f9ac2155c441580d62d6085581de12
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779603"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Självstudie: implementera Azure Databricks med en Cosmos DB-slutpunkt

I den här självstudien beskrivs hur du implementerar en virtuell VNet-Databricks-miljö med en tjänst slut punkt aktive rad för Cosmos DB.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa en Azure Databricks arbets yta i ett virtuellt nätverk
> * Skapa en tjänst slut punkt för Cosmos DB
> * Skapa ett Cosmos DB konto och importera data
> * Skapa ett Azure Databricks-kluster
> * Fråga Cosmos DB från en Azure Databricks Notebook

## <a name="prerequisites"></a>Krav

Innan du börjar gör du följande:

* Skapa en [Azure Databricks arbets yta i ett virtuellt nätverk](quickstart-create-databricks-workspace-vnet-injection.md).

* Hämta [Spark-anslutaren](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Hämta exempel data från [NOAA National Centers för miljö information](https://www.ncdc.noaa.gov/stormevents/). Välj ett tillstånd eller område och välj **Sök**. På nästa sida godkänner du standardinställningarna och väljer **Sök**. Sedan väljer du **CSV-hämtning** på vänster sida av sidan för att hämta resultatet.

* Ladda ned den [förkompilerade binärfilen](https://aka.ms/csdmtool) för verktyget Azure Cosmos DB datamigrering.

## <a name="create-a-cosmos-db-service-endpoint"></a>Skapa en tjänst slut punkt för Cosmos DB

1. När du har distribuerat en Azure Databricks arbets yta till ett virtuellt nätverk, navigerar du till det virtuella nätverket i [Azure Portal](https://portal.azure.com). Lägg märke till offentliga och privata undernät som har skapats via Databricks-distributionen.

   ![Undernät för virtuella nätverk](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Välj *offentlig-undernät* och skapa en Cosmos DB tjänst slut punkt. **Spara**sedan.
   
   ![Lägga till en tjänst slut punkt för Cosmos DB](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Skapa ett Cosmos DB-konto

1. Öppna Azure Portal. På den övre vänstra sidan av skärmen väljer du **skapa en resurs > databaser > Azure Cosmos DB**.

2. Fyll i **instans informationen** på fliken **grundläggande** med följande inställningar:

   |Inställningen|Värde|
   |-------|-----|
   |Prenumeration|*din prenumeration*|
   |Resource Group|*din resurs grupp*|
   |Account Name|DB-VNet-tjänst-slut punkt|
   |API|Core (SQL)|
   |Plats|USA, västra|
   |Geo-redundans|Inaktivera|
   |Skrivåtgärder för flera regioner|Aktivera|

   ![Lägga till en tjänst slut punkt för Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Välj fliken **nätverk** och konfigurera ditt virtuella nätverk. 

   a. Välj det virtuella nätverk som du skapade som en förutsättning och välj sedan *offentligt-undernät*. Observera att det *privata under nätet* *saknar "Microsoft AzureCosmosDB"-slut punkt saknas*. Detta beror på att du bara har aktiverat Cosmos DB tjänstens slut punkt på *offentliga-undernät*.

   b. Se till att du har **Tillåt åtkomst från Azure Portal** aktive rad. Med den här inställningen kan du komma åt ditt Cosmos DB konto från Azure Portal. Om det här alternativet är inställt på **neka**får du fel meddelanden när du försöker komma åt ditt konto. 

   > [!NOTE]
   > Det är inte nödvändigt för den här självstudien, men du kan också aktivera *Tillåt åtkomst från min IP* om du vill få åtkomst till ditt Cosmos DB konto från den lokala datorn. Om du till exempel ansluter till ditt konto med hjälp av Cosmos DB SDK måste du aktivera den här inställningen. Om den är inaktive rad visas fel meddelandet "åtkomst nekad".

   ![Nätverks inställningar för Cosmos DB konto](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Välj **Granska + skapa**och sedan **skapa** för att skapa ditt Cosmos DB-konto i det virtuella nätverket.

5. När du har skapat ditt Cosmos DB konto går du till **nycklar** under **Inställningar**. Kopiera den primära anslutnings strängen och spara den i en text redigerare för senare användning.

    ![Sidan Cosmos DB konto nycklar](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Välj **datautforskaren** och **ny samling** för att lägga till en ny databas och samling till ditt Cosmos DB-konto.

    ![Cosmos DB ny samling](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Ladda upp data till Cosmos DB

1. Öppna den grafiska gränssnitts versionen av [verktyget datamigrering för Cosmos DB](https://aka.ms/csdmtool), **Dtui. exe**.

    ![Azure Cosmos DB-datamigreringsverktyget](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. På fliken **käll information** väljer du **CSV-fil (er)** i list rutan **Importera från** . Välj sedan **Lägg till filer** och Lägg till den storm data CSV som du laddade ned som en förutsättning.

    ![Käll information för Cosmos DB Data Migration Tool](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Ange din anslutnings sträng på fliken **mål information** . Formatet för anslutnings strängen är `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>` . AccountEndpoint och AccountKey ingår i den primära anslutnings sträng som du sparade i föregående avsnitt. Lägg `Database=<your database name>` till i slutet av anslutnings strängen och välj **Verifiera**. Lägg sedan till samlings namnet och partitionsnyckel.

    ![Cosmos DB mål information för verktyget för datamigrering](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Välj **Nästa** tills du kommer till sidan Sammanfattning. Välj sedan **Importera**.

## <a name="create-a-cluster-and-add-library"></a>Skapa ett kluster och Lägg till bibliotek

1. Gå till din Azure Databricks-tjänst i [Azure Portal](https://portal.azure.com) och välj **Starta arbets yta**.

2. Skapa ett nytt kluster. Välj ett kluster namn och godkänn resten av standardinställningarna.

   ![Nya kluster inställningar](./media/service-endpoint-cosmosdb/create-cluster.png)

3. När klustret har skapats går du till kluster sidan och väljer fliken **bibliotek** . Välj **installera ny** och överför Spark Connector jar-filen för att installera biblioteket.

    ![Installera Spark Connector-bibliotek](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Du kan kontrol lera att biblioteket har installerats på fliken **bibliotek** .

    ![Fliken kluster bibliotek i Databricks](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Fråga Cosmos DB från en Databricks Notebook

1. Navigera till din Azure Databricks arbets yta och skapa en ny python-anteckningsbok.

    ![Skapa ny Databricks Notebook](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Kör följande python-kod för att ange Cosmos DB anslutnings konfiguration. Ändra **slut punkten**, **MasterKey**, **databasen**och **samlingen** enligt detta.

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

4. Använd följande Magic-kommando för att köra en SQL-instruktion som returnerar data.

    ```python
    %sql
    select * from storm
    ```

    Du har anslutit din VNet-inmatade Databricks-arbetsyta till en tjänst slut punkt som är aktive rad Cosmos DB resurs. Om du vill läsa mer om hur du ansluter till Cosmos DB, se [Azure Cosmos DB Connector för Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen, Azure Databricks arbets ytan och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviks onödig fakturering. Om du planerar att använda arbets ytan Azure Databricks i framtiden kan du stoppa klustret och starta om det senare. Om du inte kommer att fortsätta att använda den här Azure Databricks arbets ytan tar du bort alla resurser som du skapade i den här självstudien med hjälp av följande steg:

1. Klicka på **resurs grupper** på den vänstra menyn i Azure Portal och klicka sedan på namnet på den resurs grupp som du skapade.

2. På sidan resurs grupp väljer du **ta bort**, skriver in namnet på resursen som ska tas bort i text rutan och väljer sedan **ta bort** igen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du distribuerat en Azure Databricks-arbetsyta till ett virtuellt nätverk och använt Cosmos DB Spark-anslutningen för att fråga Cosmos DB data från Databricks. Om du vill veta mer om hur du arbetar med Azure Databricks i ett virtuellt nätverk kan du fortsätta till självstudien för att använda SQL Server med Azure Databricks.

> [!div class="nextstepaction"]
> [Självstudie: fråga en SQL Server Linux Docker-behållare i ett virtuellt nätverk från en Azure Databricks Notebook](vnet-injection-sql-server.md)

---
title: 'Snabbstart: Köra ett Spark-jobb på Azure Databricks med Azure Portal'
description: Snabbstarten visar hur du använder Azure-portalen för att skapa en arbetsyta för Azure Databricks och ett Apache Spark-kluster och kör ett Spark-jobb.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 43133810c6f8b7cb9fdacb2503103e09f345acfc
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551194"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Snabbstart: Köra ett Spark-jobb på Azure Databricks med Azure Portal

Den här snabbstarten visar hur du skapar en arbetsyta för Azure Databricks och ett Apache Spark-kluster i den arbetsytan. Dessutom får du lära dig hur du kör ett Spark-jobb på Databricks-klustret. Mer information om Azure Databricks finns i [Vad är Azure Databricks?](what-is-azure-databricks.md)

I den här snabbstarten analysera som en del av Spark-jobbet du Boston säkerhet data för att få insikter om olika rapporteringsmetoder.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

I det här avsnittet skapar du en Azure Databricks-arbetsyta med Azure-portalen.

1. Välj **Skapa en resurs** > **Analys** > **Azure Databricks** i Azure-portalen.

    ![Databricks på Azure-portalen](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks på Azure-portalen")

2. Under **Azure Databricks-tjänst** anger du värden för att skapa en Databricks-arbetsyta.

    ![Skapa en arbetsyta för Azure Databricks](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Skapa en arbetsyta för Azure Databricks")

    Ange följande värden:
    
    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Namn på arbetsyta**     | Ange ett namn för Databricks-arbetsytan        |
    |**Prenumeration**     | I listrutan väljer du din Azure-prenumeration.        |
    |**Resursgrupp**     | Ange om du vill skapa en ny resursgrupp eller använda en befintlig. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i [översikten över Azure-resursgrupper](../azure-resource-manager/resource-group-overview.md). |
    |**Plats**     | Välj **USA, västra 2**. För andra tillgängliga regioner läser du informationen om [Azure-tjänsttillgänglighet per region](https://azure.microsoft.com/regions/services/).        |
    |**Prisnivå**     |  Välj mellan **Standard**, **Premium**, eller **utvärderingsversion**. Mer information om de här nivåerna finns på [prissättningssidan för Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Markera **Fäst på instrumentpanelen** och klicka sedan på **Skapa**.

4. Det tar några minuter att skapa arbetsytan. När arbetsytan skapas, kan du visa Distributionsstatus i **meddelanden**.

    ![Distributionspanel för Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Distributionspanel för Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Skapa ett Spark-kluster i Databricks

> [!NOTE]
> Om du vill använda ett kostnadsfritt konto för att skapa Azure Databricks-klustret ska du innan du skapar klustret gå till din profil och ändra prenumerationen till **betala per användning**. Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

1. I Azure Portal går du till arbetsytan Databricks som du skapade och klickar sedan på **Starta arbetsyta**.

2. Du omdirigeras till Azure Databricks-portalen. I portalen klickar du på **nytt kluster**.

    ![Databricks på Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks på Azure")

3. På sidan **Nytt kluster** anger du värdena för att skapa ett kluster.

    ![Skapa Databricks Spark-kluster på Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Skapa Databricks Spark-kluster på Azure")

    Godkänn alla övriga standardvärden, förutom följande:

   * Ange ett namn för klustret.
   * Den här artikeln är att skapa ett kluster med **5.2** runtime.
   * Se till att markera kryssrutan **Avsluta efter \_\_ minuters inaktivitet**. Ange en varaktighet (i minuter) för att avsluta klustret om klustret inte används.
    
     Välj **Skapa kluster**. När klustret körs kan du ansluta anteckningsböcker till klustret och köra Spark-jobb.

Mer information om att skapa kluster finns i [Skapa ett Spark-kluster i Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="run-a-spark-sql-job"></a>Köra ett Spark SQL-jobb

Utför följande uppgifter för att skapa en anteckningsbok i Databricks, konfigurera den för att läsa data från en öppen datauppsättningar i Azure och kör ett Spark SQL-jobb på data.

1. I den vänstra rutan väljer **Azure Databricks**. Från den **vanliga uppgifter**väljer **ny anteckningsbok**.

    ![Skapa anteckningsbok i Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Skapa anteckningsbok i Databricks")

2. I den **Skapa anteckningsbok** dialogrutan rutan, ange ett namn, Välj **Python** som språk och väljer det Spark-kluster som du skapade tidigare.

    ![Skapa anteckningsbok i Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Skapa anteckningsbok i Databricks")

    Välj **Skapa**.

3. I det här steget skapar du en Spark DataFrame med Boston säkerhet Data från [Azure öppna datauppsättningar](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks), och använda det för att fråga efter data.

   Följande kommando anger information för Azure storage-åtkomst. Klistra in den här PySpark-kod i den första cellen och använda **SKIFT + RETUR** att köra koden.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   Följande kommando gör att Spark kan läsa från Blob storage via en fjärranslutning. Klistra in den här PySpark-kod i nästa cells och använda **SKIFT + RETUR** att köra koden.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   Följande kommando skapar en dataram. Klistra in den här PySpark-kod i nästa cells och använda **SKIFT + RETUR** att köra koden.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Köra ett SQL-uttryck som returnerar de översta 10 raderna med data från den tillfälliga vyn kallas **källa**. Klistra in den här PySpark-kod i nästa cells och använda **SKIFT + RETUR** att köra koden.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. Du ser en tabellvy som i följande skärmbild (endast vissa kolumner visas):

    ![Exempeldata](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "exempel-JSON-data")

6. Nu kan du skapa en visuell representation av dessa data att visa hur många säkerhet händelser rapporteras med medborgare Anslut App och Stad Worker App i stället för andra källor. Längst ned i tabellvyn, Välj den **stapeldiagram** ikon och klicka sedan på **ritalternativ**.

    ![Skapa stapeldiagram](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Skapa stapeldiagram")

8. I **Anpassa ritning** drar och släpper du värden enligt skärmbilden.

    ![Anpassa cirkeldiagram](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "anpassa stapeldiagram")

   * Ange **nycklar** till **källa**.
   * Ange **värden** till **< \id >**.
   * Ställ in **Sammansättning** på **COUNT** (Antal).
   * Ange **bildskärmstyp** till **cirkeldiagram**.

     Klicka på **Verkställ**.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med artikeln kan du avsluta klustret. Det gör du genom att välja **Kluster** i det vänstra fönstret i Azure Databricks-arbetsytan. Gå till klustret som du vill avsluta och rör markören över de tre punkterna under kolumnen **Åtgärder**. Välj sedan ikonen **Avsluta**.

![Stoppa ett Databricks-kluster](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Stoppa ett Databricks-kluster")

Om du inte manuellt avslutar klustret kommer det att stoppas automatiskt, förutsatt att du har markerat kryssrutan **Avsluta efter \_\_ minuters inaktivitet** när klustret skapades. I sådant fall stoppas klustret automatiskt om det har varit inaktivt under den angivna tiden.

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du skapade ett Spark-kluster i Azure Databricks och körde ett Spark-jobb med hjälp av data från Azure öppna datauppsättningar. Du kan också titta på [Spark-datakällor](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) för att lära dig att importera data från andra datakällor till Azure Databricks. Gå till nästa artikel om du vill lära dig hur du utför en ETL-åtgärd (extrahera, transformera och läsa in data) med Azure Databricks.

> [!div class="nextstepaction"]
>[Extrahera, transformera och läsa in data med Azure Databricks](databricks-extract-load-sql-data-warehouse.md)

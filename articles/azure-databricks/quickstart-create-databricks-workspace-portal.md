---
title: Köra ett Spark-jobb på Azure Databricks med Azure Portal
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
ms.openlocfilehash: f7f8c94504eef1023939ed41aa6124baef354e39
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "72791587"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Snabbstart: Köra ett Spark-jobb på Azure Databricks med Azure Portal

I den här snabb starten använder du Azure Portal för att skapa en Azure Databricks arbets yta med ett Apache Spark-kluster. Du kör ett jobb i klustret och använder anpassade diagram för att skapa rapporter i real tid från säkerhets data i Boston.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

> [!Note]
> Den här självstudien kan inte utföras med **Azures kostnads fri utvärderings prenumeration**.
> Om du har ett kostnads fritt konto går du till din profil och ändrar din prenumeration till **betala per**användning. Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Ta sedan [bort utgifts gränsen](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)och [begär en kvot ökning](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) för virtuella processorer i din region. När du skapar din Azure Databricks arbets yta kan du välja pris nivån **utvärdering (Premium-14-dagar gratis DBU)** för att ge arbets ytan åtkomst till kostnads fria Premium Azure Databricks DBU i 14 dagar.

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

I det här avsnittet skapar du en Azure Databricks-arbetsyta med Azure-portalen.

1. Välj **Skapa en resurs** > **Analys** > **Azure Databricks** i Azure Portal.

    ![Databricks på Azure Portal](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks på Azure Portal")

2. Under **Azure Databricks Service** (Azure Databricks-tjänsten) fyller du i värdena för att skapa en Databricks-arbetsyta.

    ![Skapa en Azure Databricks-arbetsyta](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Skapa en Azure Databricks-arbetsyta")

    Ange följande värden:
    
    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Namn på arbetsyta**     | Ange ett namn för Databricks-arbetsytan        |
    |**Prenumeration**     | I listrutan väljer du din Azure-prenumeration.        |
    |**Resursgrupp**     | Ange om du vill skapa en ny resursgrupp eller använda en befintlig. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i [översikten över Azure-resursgrupper](../azure-resource-manager/resource-group-overview.md). |
    |**Plats**     | Välj **Västra USA 2**. För andra tillgängliga regioner läser du informationen om [Azure-tjänsttillgänglighet per region](https://azure.microsoft.com/regions/services/).        |
    |**Prisnivå**     |  Välj mellan **standard**, **Premium**eller **utvärdering**. Mer information om de här nivåerna finns på [prissättningssidan för Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |
    |**Virtual Network**     |  Välj att distribuera en Azure Databricks arbets yta i din egen Virtual Network (VNet). Mer information finns i [distribuera Azure Databricks i Azure-Virtual Network (VNet-insprutning)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).        |

    Välj **Skapa**.

4. Det tar några minuter att skapa arbetsytan. När du skapar arbets ytan kan du Visa distributions status i **meddelanden**.

    ![Databricks distributions panel](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Databricks distributions panel")

## <a name="create-a-spark-cluster-in-databricks"></a>Skapa ett Spark-kluster i Databricks

> [!NOTE]
> Om du vill använda ett kostnadsfritt konto för att skapa Azure Databricks-klustret ska du innan du skapar klustret gå till din profil och ändra prenumerationen till **betala per användning**. Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

1. I Azure Portal går du till arbetsytan Databricks som du skapade och klickar sedan på **Starta arbetsyta**.

2. Du omdirigeras till Azure Databricks-portalen. Klicka på **nytt kluster**från portalen.

    ![Databricks på Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks på Azure")

3. På sidan **Nytt kluster** anger du värdena för att skapa ett kluster.

    ![Skapa Databricks Spark-kluster på Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Skapa Databricks Spark-kluster på Azure")

    Godkänn alla övriga standardvärden, förutom följande:

   * Ange ett namn för klustret.
   * I den här artikeln skapar du ett kluster med **5,3** Runtime.
   * Se till att markera kryssrutan **Avsluta efter \_\_ minuters inaktivitet**. Ange en varaktighet (i minuter) för att avsluta klustret om klustret inte används.
    
     Välj **Skapa kluster**. När klustret körs kan du ansluta anteckningsböcker till klustret och köra Spark-jobb.

Mer information om att skapa kluster finns i [Skapa ett Spark-kluster i Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="run-a-spark-sql-job"></a>Köra ett Spark SQL-jobb

Utför följande uppgifter för att skapa en antecknings bok i Databricks, konfigurera antecknings boken för att läsa data från en Azure Open-datauppsättning och kör sedan ett Spark SQL-jobb på data.

1. I den vänstra rutan väljer du **Azure Databricks**. Välj **ny antecknings bok**från **vanliga uppgifter**.

    ![Skapa antecknings bok i Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Skapa antecknings bok i Databricks")

2. I dialog rutan **skapa antecknings bok** anger du ett namn, väljer **python** som språk och väljer det Spark-kluster som du skapade tidigare.

    ![Skapa antecknings bok i Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Skapa antecknings bok i Databricks")

    Välj **Skapa**.

3. I det här steget skapar du en spark-DataFrame med Boston-säkerhetsdata från [Azure Open data uppsättningar](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks)och använder SQL för att fråga data.

   Följande kommando anger åtkomst information för Azure Storage. Klistra in den här PySpark-koden i den första cellen och Använd **Shift + Retur** för att köra koden.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   Med följande kommando kan Spark läsa från Blob Storage via fjärr anslutning. Klistra in den här PySpark-koden i nästa cell och Använd **Shift + Retur** för att köra koden.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   Följande kommando skapar en DataFrame. Klistra in den här PySpark-koden i nästa cell och Använd **Shift + Retur** för att köra koden.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Kör ett SQL-uttryck och returnera de 10 översta raderna med data från den temporära vyn som kallas **källa**. Klistra in den här PySpark-koden i nästa cell och Använd **Shift + Retur** för att köra koden.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. Du ser en tabellvy som i följande skärmbild (endast vissa kolumner visas):

    ![Exempeldata](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Exempel på JSON-data")

6. Nu skapar du en visuell representation av dessa data för att visa hur många säkerhets händelser som rapporteras med hjälp av medborgarna Anslut app-och stads Worker-appen i stället för andra källor. Längst ned i tabellens utdata **väljer du stapeldiagram** och klickar sedan på **rit alternativ**.

    ![Skapa liggande stapeldiagram](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Skapa liggande stapeldiagram")

8. I **Anpassa ritning** drar och släpper du värden enligt skärmbilden.

    ![Anpassa cirkel diagram](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Anpassa stapeldiagram")

   * Ange **nycklar** till **källa**.
   * Ange **värden** för att **< \id >** .
   * Ställ in **Sammansättning** på **COUNT** (Antal).
   * Ange **visnings typ** till **cirkel diagram**.

     Klicka på **Verkställ**.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med artikeln kan du avsluta klustret. Detta gör du genom att välja **Kluster** i det vänstra fönstret i Azure Databricks-arbetsytan. Gå till klustret som du vill avsluta och rör markören över de tre punkterna under kolumnen **Åtgärder**. Välj sedan ikonen **Avsluta**.

![Stoppa ett Databricks-kluster](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Stoppa ett Databricks-kluster")

Om du inte manuellt avslutar klustret kommer det att stoppas automatiskt, förutsatt att du har markerat kryssrutan **Avsluta efter \_\_ minuters inaktivitet** när klustret skapades. I sådant fall stoppas klustret automatiskt om det har varit inaktivt under den angivna tiden.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat ett Spark-kluster i Azure Databricks och körde ett Spark-jobb med hjälp av data från Azure Open-datauppsättningar. Du kan också titta på [Spark-datakällor](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) för att lära dig att importera data från andra datakällor till Azure Databricks. Gå till nästa artikel om du vill lära dig hur du utför en ETL-åtgärd (extrahera, transformera och läsa in data) med Azure Databricks.

> [!div class="nextstepaction"]
>[Extrahera, transformera och läsa in data med Azure Databricks](databricks-extract-load-sql-data-warehouse.md)

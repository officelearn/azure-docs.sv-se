---
title: Snabbstart – kör ett Spark-jobb på Azure Databricks med Azure-portalen
description: Den här snabbstarten visar hur du använder Azure-portalen för att skapa en Azure Databricks-arbetsyta, ett Apache Spark-kluster och köra ett Spark-jobb.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 03/23/2020
ms.custom: mvc
ms.openlocfilehash: d6af521238a034bc22612335119f08284b87eb4b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132713"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Snabbstart: Köra ett Spark-jobb på Azure Databricks med Azure Portal

I den här snabbstarten använder du Azure-portalen för att skapa en Azure Databricks-arbetsyta med ett Apache Spark-kluster. Du kör ett jobb i klustret och använder anpassade diagram för att producera realtidsrapporter från Bostons säkerhetsdata.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

> [!Note]
> Den här självstudien kan inte utföras med **Azure Free Trial Subscription**.
> Om du har ett gratis konto går du till din profil och ändrar din prenumeration **på användningsbaserad betalning.** Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Ta sedan [bort utgiftsgränsen](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)och [begär en kvotökning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) för virtuella processorer i din region. När du skapar din Azure Databricks-arbetsyta kan du välja prisnivån **Utvärderingsversion (Premium – 14 dagar gratis dbUs)** för att ge arbetsytan åtkomst till kostnadsfria Premium Azure Databricks DBUs i 14 dagar.

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

I det här avsnittet skapar du en Azure Databricks-arbetsyta med Azure-portalen.

1. I Azure-portalen väljer du **Skapa en resurs** > **Analytics** > **Azure Databricks**.

    ![Databricks på Azure-portalen](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks på Azure-portalen")

2. Under **Azure Databricks-tjänst** anger du värden för att skapa en Databricks-arbetsyta.

    ![Skapa en Azure Databricks-arbetsyta](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Skapa en Azure Databricks-arbetsyta")

    Ange följande värden:
    
    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Namn på arbetsyta**     | Ange ett namn för Databricks-arbetsytan        |
    |**Prenumeration**     | I listrutan väljer du din Azure-prenumeration.        |
    |**Resursgrupp**     | Ange om du vill skapa en ny resursgrupp eller använda en befintlig. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i [översikten över Azure-resursgrupper](../azure-resource-manager/management/overview.md). |
    |**Location**     | Välj **USA, västra 2**. För andra tillgängliga regioner läser du informationen om [Azure-tjänsttillgänglighet per region](https://azure.microsoft.com/regions/services/).        |
    |**Prisnivå**     |  Välj mellan **Standard,** **Premium**eller **Utvärderingsversion**. Mer information om de här nivåerna finns på [prissättningssidan för Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Välj **Granska + Skapa**och **skapa**sedan . Det tar några minuter att skapa arbetsytan. När arbetsytan skapas kan du visa distributionsstatus i **Meddelanden**. När den här processen är klar läggs ditt användarkonto automatiskt till som administratörsanvändare på arbetsytan.

    ![Databricks distributionspanel](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Databricks distributionspanel")

    När en arbetsytasdistribution misslyckas skapas arbetsytan fortfarande i ett misslyckat tillstånd. Ta bort den misslyckade arbetsytan och skapa en ny arbetsyta som löser distributionsfelen. När du tar bort den misslyckade arbetsytan tas även den hanterade resursgruppen och alla distribuerade resurser bort.

## <a name="create-a-spark-cluster-in-databricks"></a>Skapa ett Spark-kluster i Databricks

> [!NOTE]
> Om du vill använda ett kostnadsfritt konto för att skapa Azure Databricks-klustret ska du innan du skapar klustret gå till din profil och ändra prenumerationen till **betala per användning**. Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

1. I Azure Portal går du till arbetsytan Databricks som du skapade och klickar sedan på **Starta arbetsyta**.

2. Du omdirigeras till Azure Databricks-portalen. Klicka på **Nytt kluster**på portalen .

    ![Databricks på Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks på Azure")

3. På sidan **Nytt kluster** anger du värdena för att skapa ett kluster.

    ![Skapa Databricks Spark-kluster på Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Skapa Databricks Spark-kluster på Azure")

    Godkänn alla övriga standardvärden, förutom följande:

   * Ange ett namn för klustret.
   * Skapa ett kluster med **5.3-körning** i den här artikeln.
   * Markera kryssrutan **Avsluta efter \_ \_ minuter av inaktivitet.** Ange en varaktighet (i minuter) för att avsluta klustret om klustret inte används.
    
     Välj **Skapa kluster**. När klustret körs kan du ansluta anteckningsböcker till klustret och köra Spark-jobb.

Mer information om att skapa kluster finns i [Skapa ett Spark-kluster i Azure Databricks](/azure/databricks/clusters/create).

## <a name="run-a-spark-sql-job"></a>Köra ett Spark SQL-jobb

Utför följande uppgifter för att skapa en anteckningsbok i Databricks, konfigurera anteckningsboken för att läsa data från en Azure Open Dataset och kör sedan ett Spark SQL-jobb på data.

1. Välj **Azure Databricks**i den vänstra rutan . Välj **Ny** **anteckningsbok**i vanliga uppgifter .

    ![Skapa anteckningsbok i Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Skapa anteckningsbok i Databricks")

2. I dialogrutan **Skapa anteckningsbok** anger du ett namn, väljer **Python** som språk och väljer spark-klustret som du skapade tidigare.

    ![Skapa anteckningsbok i Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Skapa anteckningsbok i Databricks")

    Välj **Skapa**.

3. I det här steget skapar du en Spark DataFrame med Boston Safety Data från [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks)och använder SQL för att fråga data.

   Följande kommando anger azure storage access-informationen. Klistra in den här PySpark-koden i den första cellen och använd **Skift+Retur** för att köra koden.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   Med följande kommando kan Spark läsa från Blob-lagring på distans. Klistra in den här PySpark-koden i nästa cell och använd **Skift+Retur** för att köra koden.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   Följande kommando skapar en DataFrame. Klistra in den här PySpark-koden i nästa cell och använd **Skift+Retur** för att köra koden.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Kör ett SQL-uttryck returnera de 10 översta raderna med data från den tillfälliga vyn som kallas **källa**. Klistra in den här PySpark-koden i nästa cell och använd **Skift+Retur** för att köra koden.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. Du ser en tabellvy som i följande skärmbild (endast vissa kolumner visas):

    ![Exempeldata](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Exempel på JSON-data")

6. Du skapar nu en visuell representation av dessa data för att visa hur många säkerhetshändelser som rapporteras med hjälp av Citizens Connect App och City Worker App i stället för andra källor. Markera ikonen **Stapeldiagram** längst ned i tabellutmatningen och klicka sedan på **Ritytans alternativ**.

    ![Skapa stapeldiagram](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Skapa stapeldiagram")

8. I **Anpassa ritning** drar och släpper du värden enligt skärmbilden.

    ![Anpassa cirkeldiagram](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Anpassa stapeldiagram")

   * Ange **nycklar** till **källa**.
   * Ange **värden** till **<\id>**.
   * Ställ in **Sammansättning** på **COUNT** (Antal).
   * Ange **visningstyp** till **cirkeldiagram**.

     Klicka på **Använd**.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med artikeln kan du avsluta klustret. Detta gör du genom att välja **Kluster** i det vänstra fönstret i Azure Databricks-arbetsytan. Gå till klustret som du vill avsluta och rör markören över de tre punkterna under kolumnen **Åtgärder**. Välj sedan ikonen **Avsluta**.

![Stoppa ett Databricks-kluster](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Stoppa ett Databricks-kluster")

Om du inte avslutar klustret manuellt stoppas det automatiskt, förutsatt att du har markerat kryssrutan **Avsluta efter \_ \_ minuter av inaktivitet** när du skapade klustret. I sådant fall stoppas klustret automatiskt om det har varit inaktivt under den angivna tiden.

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du ett Spark-kluster i Azure Databricks och körde ett Spark-jobb med hjälp av data från Azure Open Datasets. Du kan också titta på [Spark-datakällor](/azure/databricks/data/data-sources/index) för att lära dig att importera data från andra datakällor till Azure Databricks. Gå till nästa artikel om du vill lära dig hur du utför en ETL-åtgärd (extrahera, transformera och läsa in data) med Azure Databricks.

> [!div class="nextstepaction"]
>[Extrahera, transformera och läsa in data med Azure Databricks](databricks-extract-load-sql-data-warehouse.md)

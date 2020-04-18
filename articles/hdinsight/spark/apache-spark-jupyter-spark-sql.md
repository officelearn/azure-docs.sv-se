---
title: 'Snabbstart: Skapa Apache Spark-kluster med mallen - Azure HDInsight'
description: Den här snabbstarten visar hur du använder Resource Manager-mallen för att skapa ett Apache Spark-kluster i Azure HDInsight och köra en Spark SQL-fråga.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: a92e1410b688dc3117cf28fee2ba9231641b65bb
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616870"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snabbstart: Skapa Apache Spark-kluster i Azure HDInsight med hjälp av Resource Manager-mallen

I den här snabbstarten använder du en Azure Resource Manager-mall för att skapa ett [Apache Spark-kluster](./apache-spark-overview.md) i Azure HDInsight. Du skapar sedan en Jupyter-anteckningsbok och använder den för att köra Spark SQL-frågor mot Apache Hive-tabeller. Azure HDInsight är en hanterad analystjänst med fullständigt spektrum med öppen källkod för företag. Apache Spark-ramverket för HDInsight möjliggör snabb dataanalys och klusterdatorer med hjälp av minnesbearbetning. Jupyter notebook kan du interagera med dina data, kombinera kod med markdown text, och göra enkla visualiseringar.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-an-apache-spark-cluster"></a>Skapa ett Apache Spark-kluster

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-spark-linux).

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json" range="1-143":::

Två Azure-resurser definieras i mallen:

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): skapa ett Azure Storage-konto.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): skapa ett HDInsight-kluster.

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj knappen **Distribuera till Azure** nedan om du vill logga in på Azure och öppna Resource Manager-mallen.

    [![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Ange eller välj följande värden:

    |Egenskap |Beskrivning |
    |---|---|
    |Prenumeration|Välj den Azure-prenumeration som används för klustret i listrutan.|
    |Resursgrupp|Välj din befintliga resursgrupp i listrutan eller välj **Skapa ny**.|
    |Location|Värdet fylls i automatiskt med den plats som används för resursgruppen.|
    |Klusternamn|Ange ett globalt unikt namn. För den här mallen använder du bara gemener och siffror.|
    |Användarnamn för klusterinloggning|Ange användarnamn, standard är **admin**.|
    |Lösenord för klusterinloggning|Ange ett lösenord. Lösenordet måste vara minst 10 tecken långt och innehålla minst en siffra, en versal och en gemen, ett icke-alfanumeriskt tecken (utom tecknen ' " ' ). |
    |Ssh-användarnamn|Ange användarnamn, standard är **sshuser**|
    |Ssh lösenord|Ange lösenordet.|

    ![Skapa Spark-kluster i HDInsight med Azure Resource Manager-mall](./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png "Skapa Spark-kluster i HDInsight med hjälp av en Azure Resource Manager-mall")

1. Läs igenom **villkoren**. Välj sedan **jag godkänner de villkor som anges ovan,** sedan **Köp**. Du får ett meddelande om att distributionen pågår. Det tar cirka 20 minuter att skapa ett kluster.

Om du stöter på ett problem med att skapa HDInsight-kluster kan det bero på att du inte har rätt behörighet att göra det. Mer information finns i [åtkomstkravkontrollen](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

När klustret har skapats får du ett **meddelande om distribution** med länken Gå till **resurs.** På sidan Resursgrupp visas en lista över ditt nya HDInsight-kluster och standardlagringsutrymmet som är associerat till klustret. Varje kluster har ett [Azure Storage-konto](../hdinsight-hadoop-use-blob-storage.md) eller ett [Azure Data Lake Storage-kontoberoende.](../hdinsight-hadoop-use-data-lake-store.md) Det kallas standardlagringskontot. HDInsight-klustret och dess standardlagringskonto måste samlokaliseras i samma Azure-region. Om du tar bort kluster tas inte lagringskontot bort.

## <a name="create-a-jupyter-notebook"></a>Skapa en Jupyter-anteckningsbok

[Jupyter Notebook](https://jupyter.org/) är en interaktiv anteckningsbok miljö som stöder olika programmeringsspråk. Med anteckningsboken kan du interagera med dina data, kombinera kod med markeringstext och utföra enkla visualiseringar.

1. Öppna [Azure-portalen](https://portal.azure.com).

2. Välj **HDInsight-kluster** och välj sedan det kluster som du skapade.

    ![Öppna HDInsight-kluster i Azure-portalen](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. Välj **Jupyter Notebook**i avsnittet **Klusterinstrumentpaneler** i portalen . Ange autentiseringsuppgifterna för klustret om du uppmanas att göra det.

   ![Öppna Jupyter Notebook för att köra interaktiv Spark SQL-fråga](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Öppna Jupyter Notebook för att köra interaktiv Spark SQL-fråga")

4. Välj **Ny** > **PySpark** om du vill skapa en anteckningsbok.

   ![Skapa en Jupyter-anteckningsbok för att köra interaktiv Spark SQL-fråga](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Skapa en Jupyter-anteckningsbok för att köra interaktiv Spark SQL-fråga")

   En ny anteckningsbok skapas och öppnas med namnet Untitled(Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Kör Apache Spark SQL-uttryck

SQL (Structured Query Language) är det vanligaste språket för frågor och transformering av data. Spark SQL fungerar som ett tillägg till Apache Spark för bearbetning av strukturerade data med den välbekanta SQL-syntaxen.

1. Verifiera att kerneln är klar. Kerneln är klar när du ser en tom cirkel bredvid kernelnamnet i den bärbara datorn. En fylld cirkel anger att kerneln är upptagen.

    ![Kernel-status](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Kernel-status")

    När du startar den bärbara datorn för första gången utför kerneln några uppgifter i bakgrunden. Vänta tills kerneln är klar.

1. Klistra in följande kod i en tom cell och tryck sedan på **SKIFT+RETUR** för att köra koden. Kommandot listar Hive-tabellerna i klustret:

    ```sql
    %%sql
    SHOW TABLES
    ```

    När du använder en Jupyter Notebook med hdinsight-klustret får du en förinställd `spark` session som du kan använda för att köra Hive-frågor med Spark SQL. `%%sql` anger att Jupyter Notebook ska använda den förinställda sessionen `spark` när Hive-frågan ska köras. Frågan hämtar de översta 10 raderna från en Hive-tabell (**hivesampletable**) som medföljer alla HDInsight-kluster som standard. Första gången du skickar frågan skapar Jupyter ett Spark-program för anteckningsboken. Det tar ungefär 30 sekunder att slutföra. När Spark-programmet är klart körs frågan om ungefär en sekund och ger resultaten. De utdata som returneras ser ut så här:

    ![Apache Hive fråga i HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Hive-fråga i HDInsight")

    Varje gång du kör en fråga i Jupyter visar fönsterrubriken i webbläsaren statusen **(Upptagen)** tillsammans med anteckningsbokens titel. Du ser även en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet.

1. Kör ytterligare en fråga för att visa data i `hivesampletable`.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Skärmen bör uppdateras så att frågeresultatet visas.

    ![Hive-frågeutdata i HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Hive-frågeutdata i HDInsight")

1. Välj **Stäng och stoppa** på anteckningsbokens **Arkiv**-meny. Om du stänger av anteckningsboken frigörs klusterresurserna, inklusive Spark-programmet.

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

Navigera till klustret på Azure-portalen och välj **Ta bort**.

![Azure-portalen tar bort ett HDInsight-kluster](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Ta bort HDInsight-kluster")

Du kan också välja resursgruppnamnet för att öppna resursgruppsidan. Välj sedan **Ta bort resursgrupp**. Genom att ta bort resursgruppen tar du bort både HDInsight-klustret och standardlagringskontot.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar ett Apache Spark-kluster i HDInsight och kör en grundläggande Spark SQL-fråga. Gå vidare till nästa självstudiekurs om du vill lära dig hur du använder ett HDInsight-kluster för att köra interaktiva frågor om exempeldata.

> [!div class="nextstepaction"]
> [Köra interaktiva frågor i Apache Spark](./apache-spark-load-data-run-query.md)

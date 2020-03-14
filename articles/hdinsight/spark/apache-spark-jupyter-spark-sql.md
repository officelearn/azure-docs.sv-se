---
title: 'Snabb start: skapa Apache Spark kluster med hjälp av mall – Azure HDInsight'
description: Den här snabb starten visar hur du använder Resource Manager-mall för att skapa ett Apache Spark kluster i Azure HDInsight och kör en Spark SQL-fråga.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/05/2020
ms.openlocfilehash: a4c207cdbe4bbd0fdef5e1da8da0f4b582702308
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241208"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snabb start: skapa Apache Spark kluster i Azure HDInsight med Resource Manager-mall

I den här snabb starten använder du en Azure Resource Manager-mall för att skapa ett Apache Spark kluster i Azure HDInsight. Sedan skapar du en Jupyter-anteckningsbok och använder den för att köra Spark SQL-frågor mot Apache Hive tabeller. Azure HDInsight är en hanterad analystjänst med fullständigt spektrum med öppen källkod för företag. Apache Spark Framework för HDInsight möjliggör snabb data analys och kluster data behandling med minnes intern bearbetning. Med Jupyter Notebook kan du interagera med dina data, kombinera kod med markdown text och göra enkla visualiseringar.

[Översikt: Apache Spark på Azure HDInsight](apache-spark-overview.md) | [Apache Spark](https://spark.apache.org/) | [Apache Hive](https://hive.apache.org/) | [Jupyter Notebook](https://jupyter.org/) | [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-apache-spark-cluster"></a>Skapa ett Apache Spark-kluster

Skapa ett Apache Spark kluster i HDInsight med en Azure Resource Manager-mall. Du hittar mallen på [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Information om JSON-syntaxen och JSON-egenskaperna för klustret finns i [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters).

Klustret använder Azure Storage Blobar som klusterlagring. Mer information om att använda Data Lake Storage Gen2 finns i [Snabbstart: Konfigurera kluster i HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

> [!IMPORTANT]  
> Fakturering för HDInsight-kluster sker proportionerligt per minut, oavsett om du använder dem eller inte. Se till att du tar bort dina kluster när du är klar med dem. Mer information finns i avsnittet [Rensa resurser](#clean-up-resources) i den här artikeln.

1. Välj följande länk för att öppna mallen i Azure Portal i en ny flik i webbläsaren:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank">Distribuera till Azure</a>

2. Ange följande värden:

    | Egenskap | Värde |
    |---|---|
    |Prenumeration|Välj den Azure-prenumeration som ska användas för att skapa klustret. |
    | Resursgrupp|Skapa en resursgrupp eller välj en befintlig. Resursgrupp används för att hantera Azure-resurser till dina projekt. Den nya resursgruppens namn som används för den här snabbstarten är **myspark20180403rg**.|
    | plats.|Välj en plats för resursgruppen. Mallen använder den här platsen för att skapa klustret och standard kluster lagringen. Den plats som används för den här snabbstarten är **USA, östra 2**.|
    | ClusterName|Ange ett namn på det kluster som du vill skapa. Det nya klusternamnet som används för den här snabbstarten är **myspark20180403**.|
    | Kluster inloggnings namn och lösen ord|Standard inloggnings namnet är admin. Välj ett lösen ord för kluster inloggningen. Inloggningsnamnet som används för den här snabbstarten är **admin**.|
    | Användar namn och lösen ord för SSH|Välj ett lösenord för SSH-användaren. SSH-användarnamnet som används för den här snabbstarten är **sshuser**.|

    ![Skapa Spark-kluster i HDInsight med Azure Resource Manager-mall](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Skapa Spark-kluster i HDInsight med hjälp av en Azure Resource Manager mall")

3. Välj **Jag godkänner villkoren som anges ovan** och välj sedan **Köp**. En ny panel visas med rubriken **Skicka malldistribution**. Det tar cirka 20 minuter att skapa klustret. Klustret måste skapas innan du kan fortsätta till nästa session.

Om du stöter på ett problem med att skapa HDInsight-kluster kan det bero på att du inte har rätt behörighet. Mer information finns i [åtkomstkravkontrollen](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="install-intellijeclipse-for-spark-applications"></a>Installera IntelliJ/Sol förmörkelse för Spark-program

Använd plugin-programmet Azure Toolkit for IntelliJ/Sol förmörkelse för att utveckla Spark-program som skrivits i [Scala](https://www.scala-lang.org/)och skicka dem sedan till ett Azure HDInsight-kluster direkt från IntelliJ/Sol förmörkelse-Integrated Development Environment (IDE). Mer information finns i [Använd IntelliJ för att skriva/skicka Spark-program](./apache-spark-intellij-tool-plugin.md) och [Använd Eclipse för att skriva/skicka Spark-program](./apache-spark-eclipse-tool-plugin.md).

## <a name="install-vscode-for-pysparkhive-applications"></a>Installera VSCode för PySpark/Hive-program

Lär dig hur du använder Azure HDInsight Tools för Visual Studio Code (VSCode) för att skapa och skicka Hive-batchjobb, interaktiva Hive-frågor, PySpark-batchjobb och interaktiva PySpark-skript. Azure HDInsight-verktyg kan installeras på de plattformar som stöds av VSCode. Dessa omfattar Windows, Linux och macOS. Mer information finns i [Använd VSCode för att skriva/skicka PySpark-program](../hdinsight-for-vscode.md).

## <a name="create-a-jupyter-notebook"></a>Skapa en Jupyter-anteckningsbok

[Jupyter Notebook](https://jupyter.org/) är en interaktiv anteckningsboksmiljö som stöder flera olika datorspråk. Med antecknings boken kan du interagera med dina data, kombinera kod med markdown-text och utföra enkla visualiseringar.

1. Öppna [Azure-portalen](https://portal.azure.com).

2. Välj **HDInsight-kluster** och välj sedan det kluster som du skapade.

    ![Öppna HDInsight-kluster i Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. I portalen, i avsnittet **kluster instrument paneler** , väljer du **Jupyter Notebook**. Ange autentiseringsuppgifterna för klustret om du uppmanas att göra det.

   ![Öppna Jupyter Notebook för att köra interaktiv Spark SQL-fråga](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Öppna Jupyter Notebook för att köra interaktiv Spark SQL-fråga")

4. Skapa en anteckningsbok genom att välja **Ny** > **PySpark**.

   ![Skapa en Jupyter Notebook för att köra interaktiv Spark SQL-fråga](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Skapa en Jupyter Notebook för att köra interaktiv Spark SQL-fråga")

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

    När du använder en Jupyter Notebook med ditt HDInsight-kluster får du en förinställd `spark`-session som du kan använda för att köra Hive-frågor med Spark SQL. `%%sql` anger att Jupyter Notebook ska använda den förinställda sessionen `spark` när Hive-frågan ska köras. Frågan hämtar de översta 10 raderna från en Hive-tabell (**hivesampletable**) som medföljer alla HDInsight-kluster som standard. Första gången du skickar frågan kommer Jupyter att skapa ett Spark-program för antecknings boken. Det tar ungefär 30 sekunder att slutföra. När Spark-programmet är klart körs frågan i ungefär en sekund och genererar resultatet. De utdata som returneras ser ut så här:

    ![Apache Hive fråga i HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Hive-fråga i HDInsight")

    Varje gång du kör en fråga i Jupyter visar fönsterrubriken i webbläsaren statusen **(Upptagen)** tillsammans med anteckningsbokens titel. Du ser även en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet.

1. Kör ytterligare en fråga för att visa data i `hivesampletable`.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Skärmen bör uppdateras så att frågeresultatet visas.

    ![Utdata från Hive-fråga i HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Utdata från Hive-fråga i HDInsight")

1. Välj **Stäng och stoppa** på anteckningsbokens **Arkiv**-meny. Om du stänger av Notebook frigörs kluster resurserna, inklusive Spark-programmet.

## <a name="clean-up-resources"></a>Rensa resurser

HDInsight sparar dina data och Jupyter-anteckningsböcker i Azure Storage eller Azure Data Lake Storage, så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger mer än avgifterna för lagring, är det ekonomiskt klokt att ta bort kluster när de inte används. Om du planerar att arbeta med självstudierna i [Nästa steg](#next-steps) direkt, kan du vilja behålla klustret.

Växla tillbaka till Azure Portal och välj **Ta bort**.

![Azure Portal ta bort ett HDInsight-kluster](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Ta bort HDInsight-kluster")

Du kan också välja resursgruppnamnet för att öppna resursgruppsidan. Välj sedan **Ta bort resursgrupp**. Genom att ta bort resurs gruppen tar du bort både HDInsight-klustret och standard lagrings kontot.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Apache Spark kluster i HDInsight och kör en grundläggande Spark SQL-fråga. Gå vidare till nästa självstudie och lär dig hur du använder ett HDInsight-kluster för att köra interaktiva frågor på exempel data.

> [!div class="nextstepaction"]
> [Köra interaktiva frågor i Apache Spark](./apache-spark-load-data-run-query.md)

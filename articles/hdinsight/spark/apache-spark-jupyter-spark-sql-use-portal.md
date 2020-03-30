---
title: 'Snabbstart: Skapa Spark-kluster i HDInsight med Azure-portal'
description: Den här snabbstarten visar hur du använder Azure-portalen för att skapa ett Apache Spark-kluster i Azure HDInsight och köra en Spark SQL-fråga.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2020
ms.openlocfilehash: 5f4d1b8ef742a8dcafa2b8e34a6209f85ae050d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77650681"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Snabbstart: Skapa Apache Spark-kluster i Azure HDInsight med Azure-portal

I den här snabbstarten använder du Azure-portalen för att skapa ett Apache Spark-kluster i Azure HDInsight. Du skapar sedan en Jupyter-anteckningsbok och använder den för att köra Spark SQL-frågor mot Apache Hive-tabeller. Azure HDInsight är en hanterad analystjänst med fullständigt spektrum med öppen källkod för företag. Apache Spark-ramverket för HDInsight möjliggör snabb dataanalys och klusterdatorer med hjälp av minnesbearbetning. Jupyter notebook kan du interagera med dina data, kombinera kod med markdown text, och göra enkla visualiseringar.

Detaljerade förklaringar av tillgängliga konfigurationer finns i [Konfigurera kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Mer information om hur portalen används för att skapa kluster finns [i Skapa kluster i portalen](../hdinsight-hadoop-create-linux-clusters-portal.md).

> [!IMPORTANT]  
> Fakturering för HDInsight-kluster sker proportionerligt per minut, oavsett om du använder dem eller inte. Se till att du tar bort dina kluster när du är klar med dem. Mer information finns i avsnittet [Rensa resurser](#clean-up-resources) i den här artikeln.

## <a name="prerequisites"></a>Krav

Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Skapa ett Apache Spark-kluster i HDInsight

Du använder Azure-portalen för att skapa ett HDInsight-kluster som använder Azure Storage Blobbar som klusterlagring. Mer information om att använda Data Lake Storage Gen2 finns i [Snabbstart: Konfigurera kluster i HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj + Skapa **en resurs**på den övre menyn .

    ![Azure portal skapa en resurs](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-resource.png "Skapa en resurs i Azure-portalen")

1. Välj **Analytics** > **Azure HDInsight** för att gå till **sidan Skapa HDInsight-kluster.**

1. Ange följande information på fliken **Grunderna:**

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Prenumeration  | Välj den Azure-prenumeration som används för klustret i listrutan. |
    |Resursgrupp | Välj din befintliga resursgrupp i listrutan eller välj **Skapa ny**.|
    |Klusternamn | Ange ett globalt unikt namn.|
    |Region   | Välj en region där klustret skapas i listrutan. |
    |Klustertyp| Välj Välj klustertyp om du vill öppna en lista. Välj **Spark**i listan .|
    |Klusterversion|Det här fältet fylls automatiskt i med standardversionen när klustertypen har valts.|
    |Användarnamn för klusterinloggning| Ange användarnamnet för kluster-inloggningen.  Standardnamnet är **admin**. Du använder det här kontot för att logga in på Jupyter-anteckningsboken senare i snabbstarten. |
    |Lösenord för klusterinloggning| Ange lösenordet för klusterinloggningen: |
    |Secure Shell (SSH)-användarnamn| Ange SSH-användarnamnet. SSH-användarnamnet som användes för den här snabbstarten är **sshuser**. Som standard delar här kontot samma lösenord som kontot *användarnamn för klusterinloggning*. |

    ![Skapa grundläggande konfigurationer för HDInsight-kluster](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Skapa Spark-kluster i HDInsight de grundläggande konfigurationerna")

    Välj **Nästa: Lagring >>** för att fortsätta till sidan **Lagring.**

1. Under **Lagring**, ange följande värden:

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Primär lagringstyp|Använd standardvärdet **Azure Storage**.|
    |Urvalsmetod|Använd standardvärdet **Välj från listan**.|
    |Primärt lagringskonto|Använd det automatiskt ifyllda värdet.|
    |Container|Använd det automatiskt ifyllda värdet.|

    ![Skapa grundläggande konfigurationer för HDInsight-kluster](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage.png "Skapa Spark-kluster i HDInsight de grundläggande konfigurationerna")

    Välj **Granska + skapa** för att fortsätta.

1. Under **Granska + skapa**väljer du **Skapa**. Det tar cirka 20 minuter att skapa klustret. Klustret måste skapas innan du kan fortsätta till nästa session.

Om du stöter på ett problem med att skapa HDInsight-kluster kan det bero på att du inte har rätt behörighet att göra det. Mer information finns i [åtkomstkravkontrollen](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Skapa en Jupyter-anteckningsbok

Jupyter Notebook är en interaktiv anteckningsboksmiljö som stöder flera olika datorspråk. Du kan använda anteckningsboken för att interagera med dina data, kombinera kod med markdown-text och utföra enkla visualiseringar.

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/jupyter`, var `CLUSTERNAME` är namnet på klustret. Ange autentiseringsuppgifterna för klustret om du uppmanas att göra det.

1. Välj **Ny** > **PySpark** om du vill skapa en anteckningsbok.

   ![Skapa en Jupyter-anteckningsbok för att köra interaktiv Spark SQL-fråga](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Skapa en Jupyter-anteckningsbok för att köra interaktiv Spark SQL-fråga")

   En ny anteckningsbok skapas och öppnas med namnet Untitled(Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Kör Apache Spark SQL-uttryck

SQL (Structured Query Language) är det vanligaste språket för frågor och definition av data. Spark SQL fungerar som ett tillägg till Apache Spark för bearbetning av strukturerade data med den välbekanta SQL-syntaxen.

1. Verifiera att kerneln är klar. Kerneln är klar när du ser en tom cirkel bredvid kernelnamnet i den bärbara datorn. En fylld cirkel anger att kerneln är upptagen.

    ![Apache Hive fråga i HDInsight](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Hive-fråga i HDInsight")

    När du startar den bärbara datorn för första gången utför kerneln några uppgifter i bakgrunden. Vänta tills kerneln är klar.

1. Klistra in följande kod i en tom cell och tryck sedan på **SKIFT+RETUR** för att köra koden. Kommandot listar Hive-tabellerna i klustret:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    När du använder en Jupyter Notebook med ditt HDInsight-kluster får du en förinställning `sqlContext` som du kan använda för att köra Hive-frågor med Spark SQL. `%%sql` anger att Jupyter Notebook ska använda den förinställda `sqlContext` när Hive-frågan ska köras. Frågan hämtar de översta 10 raderna från en Hive-tabell (**hivesampletable**) som medföljer alla HDInsight-kluster som standard. Det tar ungefär 30 sekunder att få resultatet. De utdata som returneras ser ut så här:

    ![Apache Hive fråga i HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query.png "Hive-fråga i HDInsight")

    Varje gång du kör en fråga i Jupyter visar fönsterrubriken i webbläsaren statusen **(Upptagen)** tillsammans med anteckningsbokens titel. Du ser även en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet.

1. Kör ytterligare en fråga för att visa data i `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Skärmen bör uppdateras så att frågeresultatet visas.

    ![Hive-frågeutdata i HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query-output.png "Hive-frågeutdata i HDInsight")

1. Välj **Stäng och stoppa** på anteckningsbokens **Arkiv**-meny. När du stänger anteckningsboken frigörs klusterresurserna.

## <a name="clean-up-resources"></a>Rensa resurser

HDInsight sparar dina data i Azure Storage eller Azure Data Lake Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används. Om du planerar att arbeta med självstudierna i [Nästa steg](#next-steps) direkt, kan du vilja behålla klustret.

Växla tillbaka till Azure Portal och välj **Ta bort**.

![Azure-portalen tar bort ett HDInsight-kluster](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-azure-portal-delete-cluster.png "Ta bort HDInsight-kluster")

Du kan också välja resursgruppnamnet för att öppna resursgruppsidan. Välj sedan **Ta bort resursgrupp**. Genom att ta bort resursgruppen tar du bort både HDInsight-klustret och standardlagringskontot.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar ett Apache Spark-kluster i HDInsight och kör en grundläggande Spark SQL-fråga. Gå vidare till nästa självstudiekurs om du vill lära dig hur du använder ett HDInsight-kluster för att köra interaktiva frågor om exempeldata.

> [!div class="nextstepaction"]
> [Köra interaktiva frågor i Apache Spark](./apache-spark-load-data-run-query.md)

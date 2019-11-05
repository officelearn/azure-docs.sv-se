---
title: Kernels för Jupyter Notebook på Spark-kluster i Azure HDInsight
description: Lär dig mer om PySpark-, PySpark3-och Spark-kernels för Jupyter Notebook som är tillgänglig med Spark-kluster på Azure HDInsight.
keywords: Jupyter Notebook på Spark, Jupyter Spark
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: 44089ea4b997e06cb7654fc6665a1a9a59ae2658
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494126"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Kernels för Jupyter Notebook på Apache Spark kluster i Azure HDInsight

HDInsight Spark-kluster tillhandahåller kärnor som du kan använda med Jupyter Notebook på [Apache Spark](https://spark.apache.org/) för att testa dina program. En kernel är ett program som kör och tolkar koden. De tre kernelerna är:

- **PySpark** – för program som skrivits i Python2.
- **PySpark3** – för program som skrivits i python3.
- **Spark** – för program som skrivits i Scala.

I den här artikeln får du lära dig hur du använder dessa kärnor och fördelarna med att använda dem.

## <a name="prerequisites"></a>Förutsättningar

Ett Apache Spark kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Skapa en Jupyter-anteckningsbok på Spark HDInsight

1. Välj ditt Spark-kluster från [Azure Portal](https://portal.azure.com/).  Se [lista och Visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters) för instruktioner. Vyn **Översikt** öppnas.

2. I vyn **Översikt** väljer du **Jupyter Notebook**i rutan **kluster instrument paneler** . Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

    ![Jupyter Notebook på Apache Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter Notebook på Spark")
  
   > [!NOTE]  
   > Du kan också komma åt Jupyter Notebook i Spark-kluster genom att öppna följande URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Välj **ny**och välj sedan antingen **Pyspark**, **PySpark3**eller **Spark** för att skapa en antecknings bok. Använd Spark-kärnan för Scala-program, PySpark-kernel för Python2-program och PySpark3-kernel för python3-program.

    ![Kernels för Jupyter Notebook på Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Kernels för Jupyter Notebook på Spark")

4. En antecknings bok öppnas med den kernel som du har valt.

## <a name="benefits-of-using-the-kernels"></a>Fördelar med att använda kärnorna

Här är några av fördelarna med att använda de nya kernelerna med Jupyter Notebook på Spark HDInsight-kluster.

- **Förinställda kontexter**. Med **PySpark**, **PySpark3**eller **Spark** -kärnan behöver du inte ange Spark-eller Hive-kontexterna explicit innan du börjar arbeta med dina program. Dessa är tillgängliga som standard. Dessa kontexter är:

  - **SC** – för Spark-kontext
  - **sqlContext** – för Hive-kontext

    Så du behöver inte köra instruktioner som följande för att ange kontexterna:

         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)

    I stället kan du använda de förinställda kontexterna i ditt program direkt.

- **Cell Magic**. PySpark-kärnan innehåller fördefinierade "MAGICS", som är särskilda kommandon som du kan anropa med `%%` (till exempel `%%MAGIC` `<args>`). Kommandot Magic måste vara det första ordet i en kod cell och tillåta flera rader med innehåll. Det magiska ordet ska vara det första ordet i cellen. Om du lägger till något före Magic, även kommentarer, uppstår ett fel.     Mer information om MAGICS finns [här](https://ipython.readthedocs.org/en/stable/interactive/magics.html).

    I följande tabell visas de olika Magic-alternativen som är tillgängliga via kernelerna.

   | Magic | Exempel | Beskrivning |
   | --- | --- | --- |
   | Hjälp |`%%help` |Genererar en tabell med alla tillgängliga MAGICS med exempel och beskrivning |
   | statusinformation |`%%info` |Visar sessionsinformation för den aktuella livy-slutpunkten |
   | Konfigurera |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Konfigurerar parametrar för att skapa en session. Force-flaggan (-f) är obligatorisk om en session redan har skapats, vilket säkerställer att sessionen släpps och återskapas. Titta på [LIVY post/sessions Request-brödtext](https://github.com/cloudera/livy#request-body) för en lista över giltiga parametrar. Parametrar måste skickas in som en JSON-sträng och måste vara på nästa rad efter Magic, som visas i kolumnen exempel. |
   | SQL |`%%sql -o <variable name>`<br> `SHOW TABLES` |Kör en Hive-fråga mot sqlContext. Om parametern `-o` skickas, sparas resultatet av frågan i den lokala python-kontexten%% som en [Pandas](https://pandas.pydata.org/) -dataframe. |
   | inställningar |`%%local`<br>`a=1` |All kod i efterföljande rader körs lokalt. Koden måste vara giltig Python2-kod även oberoende av vilken kernel du använder. Det innebär att även om du har valt **PySpark3** -eller **Spark** -kernel när du skapar antecknings boken, om du använder `%%local` Magic i en cell, måste cellen bara ha en giltig Python2-kod. |
   | loggar |`%%logs` |Matar ut loggarna för den aktuella livy-sessionen. |
   | delete |`%%delete -f -s <session number>` |Tar bort en speciell session av den aktuella livy-slutpunkten. Du kan inte ta bort den session som har initierats för själva kerneln. |
   | cleanup |`%%cleanup -f` |Tar bort alla sessioner för den aktuella livy-slutpunkten, inklusive den här Notebook-sessionen. Force-flaggan-f är obligatorisk. |

   > [!NOTE]  
   > Förutom de Magic som läggs till av PySpark-kärnan kan du också använda de [inbyggda ipython-Magic](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), inklusive `%%sh`. Du kan använda `%%sh` Magic för att köra skript och kodblock på klustrets huvudnoden.

- **Automatisk visualisering**. Pyspark-kärnan visualiserar automatiskt utdata från Hive-och SQL-frågor. Du kan välja mellan flera olika typer av visualiseringar, inklusive tabell, cirkel, linje, yta, stapel.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametrar som stöds med SQL-Magic%%

`%%sql` Magic stöder olika parametrar som du kan använda för att styra vilken typ av utdata du får när du kör frågor. I följande tabell visas utdata.

| Parameter | Exempel | Beskrivning |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Använd den här parametern om du vill behålla resultatet av frågan i den lokala python-kontexten%% som en [Pandas](https://pandas.pydata.org/) -dataframe. Namnet på dataframe-variabeln är det variabel namn som du anger. |
| -q |`-q` |Använd den här inställningen om du vill stänga av visualiseringar för cellen. Om du inte vill att autovisualisera innehållet i en cell och bara vill avbilda det som en dataframe använder du `-q -o <VARIABLE>`. Om du vill stänga av visualiseringar utan att samla in resultaten (till exempel för att köra en SQL-fråga, t. ex. en `CREATE TABLE`-instruktion), använder du `-q` utan att ange ett `-o` argument. |
| -m |`-m <METHOD>` |Där **metod** är antingen **ta** eller **Sampla** (standard är att **ta**). Om metoden är **ta**med, väljer kernel element från början av resultat data uppsättningen som anges av MAXROWS (beskrivs senare i den här tabellen). Om metoden är ett **exempel**, kan kernels slumpmässigt sampla element i data uppsättningen enligt `-r` parameter, som beskrivs härnäst i den här tabellen. |
| -r |`-r <FRACTION>` |Detta **bråktal** är ett flytt ALS nummer mellan 0,0 och 1,0. Om exempel metoden för SQL-frågan är `sample`, samplar kerneln slumpmässigt den angivna fraktionen av elementen i resultat uppsättningen åt dig. Om du till exempel kör en SQL-fråga med argumenten `-m sample -r 0.01`, så samplas 1% av resultat raderna slumpmässigt. |
| -n |`-n <MAXROWS>` |**MAXROWS** är ett heltals värde. Kärnan begränsar antalet utgående rader till **MAXROWS**. Om **MAXROWS** är ett negativt tal som **-1**, är antalet rader i resultat uppsättningen inte begränsat. |

**Exempel:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

Instruktionen ovan gör följande:

- Väljer alla poster från **hivesampletable**.
- Eftersom vi använder-q inaktive ras autovisualisering.
- Eftersom vi använder `-m sample -r 0.1 -n 500` slumpmässigt sampla 10% av raderna i hivesampletable och begränsar storleken på resultat uppsättningen till 500 rader.
- Slutligen, eftersom vi använde `-o query2` även spara utdata i en dataframe med namnet **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Att tänka på när du använder nya kärnor

Oavsett vilken kernel du använder, lämnar de antecknings böcker som körs förbrukar kluster resurserna.  Med dessa kärnor, eftersom kontexterna är förinställda, avslutar inte antecknings böckerna kontexten och därför fortsätter kluster resurserna att användas. En bra idé är att använda alternativet **Stäng och stoppa** från antecknings bokens **Arkiv** meny när du är klar med antecknings boken, som omsorg kontexten och sedan avslutar antecknings boken.

## <a name="where-are-the-notebooks-stored"></a>Var lagras antecknings böckerna?

Om ditt kluster använder Azure Storage som standard lagrings konto, sparas Jupyter-anteckningsböcker till lagrings kontot i mappen **/HdiNotebooks** .  Antecknings böcker, textfiler och mappar som du skapar inifrån Jupyter är tillgängliga från lagrings kontot.  Om du till exempel använder Jupyter för att skapa en mapp- **mapp och en mapp för** Notebook/min **Notebook. ipynb**, kan du komma åt den bärbara datorn på `/HdiNotebooks/myfolder/mynotebook.ipynb` i lagrings kontot.  Den omvända är också sann, det vill säga om du överför en antecknings bok direkt till ditt lagrings konto på `/HdiNotebooks/mynotebook1.ipynb`, visas antecknings boken även från Jupyter.  Antecknings böcker finns kvar i lagrings kontot även efter att klustret har tagits bort.

> [!NOTE]  
> HDInsight-kluster med Azure Data Lake Storage som standard lagring lagrar inte antecknings böcker i tillhör ande lagring.

Hur antecknings böcker sparas på lagrings kontot är kompatibelt med [Apache HADOOP HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Så om du använder SSH i klustret kan du använda fil hanterings kommandon som du ser i följande kodfragment:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Oavsett om klustret använder Azure Storage eller Azure Data Lake Storage som standard lagrings konto, sparas antecknings böckerna också på klustrets huvudnoden på `/var/lib/jupyter`.

## <a name="supported-browser"></a>Webbläsare som stöds

Jupyter-anteckningsböcker i Spark HDInsight-kluster stöds bara på Google Chrome.

## <a name="feedback"></a>Feedback

De nya kernelerna är i ett växande Stadium och kommer att bli mogna över tid. Detta kan också innebära att API: er kan ändras när dessa kärnor är mogna. Vi uppskattar all feedback som du har när du använder dessa nya kärnor. Detta är användbart när du utformar den slutliga versionen av dessa kärnor. Du kan lämna kommentarer/feedback under **feedback** -avsnittet längst ned i den här artikeln.

## <a name="seealso"></a>Se även

- [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

- [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
- [Apache Spark med Machine Learning: använda spark i HDInsight för analys av byggnads temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
- [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
- [Webbplats logg analys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program

- [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
- [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

- [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
- [Använd HDInsight tools-plugin för IntelliJ-idé för att felsöka Apache Spark program via fjärr anslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
- [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
- [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
- [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser

- [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
- [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

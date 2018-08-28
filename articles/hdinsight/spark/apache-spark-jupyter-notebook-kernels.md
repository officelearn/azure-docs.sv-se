---
title: Kärnor för Jupyter notebook i Spark-kluster i Azure HDInsight
description: Läs mer om PySpark och PySpark3 Spark kärnor för Jupyter-anteckningsbok som är tillgängliga med Azure HDInsight Spark-kluster.
keywords: jupyter notebook i spark, jupyter spark
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jasonh
ms.openlocfilehash: 589c02a78c78f415fe59d911f9f6339bfa3fb293
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051551"
---
# <a name="kernels-for-jupyter-notebook-on-spark-clusters-in-azure-hdinsight"></a>Kärnor för Jupyter notebook i Spark-kluster i Azure HDInsight 

HDInsight Spark-kluster tillhandahåller kernlar som du kan använda med Jupyter notebook i Spark för att testa ditt program. En kernel är ett program som körs och tolkar din kod. Det finns tre kernels:

- **PySpark** – för appar som skrivits i Python2
- **PySpark3** – för program som skrivits i Python3
- **Spark** – för program som skrivits i Scala

Lär dig hur du använder dessa kernlar och fördelarna med att använda dem i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Skapa en Jupyter-anteckningsbok på Spark HDInsight

1. Från den [Azure-portalen](https://portal.azure.com/), öppna ditt kluster.  Se [lista och visa kluster](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters) anvisningar. Klustret har öppnats i ett nytt portalblad.

2. Från den **snabblänkar** klickar du på **Klusterinstrumentpaneler** att öppna den **Klusterinstrumentpaneler** bladet.  Om du inte ser **snabblänkar**, klickar du på **översikt** menyn till vänster på bladet.

    ![Jupyter notebook i Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-jupyter-notebook-on-spark.png "Jupyter notebook i Spark") 

3. Klicka på **Jupyter-anteckningsbok**. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.
   
   > [!NOTE]
   > Du kan också nå Jupyter notebook i Spark-kluster genom att öppna följande URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

3. Klicka på **New**, och klicka sedan på antingen **Pyspark**, **PySpark3**, eller **Spark** att skapa en anteckningsbok. Använd Spark kernel för Scala-appar, PySpark-kerneln för Python2-program och PySpark3 kernel för Python3 program.
   
    ![Kärnor för Jupyter notebook i Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "kärnor för Jupyter notebook i Spark") 

4. En anteckningsbok öppnas med kernel som du har valt.

## <a name="benefits-of-using-the-kernels"></a>Fördelarna med att använda dessa kernlar

Här följer några fördelar med att använda de nya kernlar med Jupyter notebook i Spark HDInsight-kluster.

- **Förinställda kontexter**. Med **PySpark**, **PySpark3**, eller **Spark** kernlar, du behöver inte ange kontexter för Spark- eller Hive explicit innan du börjar arbeta med dina program. Dessa är tillgängliga som standard. Dessa kontexter är:
   
   * **SC** – för Spark-kontexten
   * **sqlContext** – för Hive-kontexten
   
   Så behöver du köra instruktioner som följande för att ange kontexter:
   
          sc = SparkContext('yarn-client')
          sqlContext = HiveContext(sc)
   
   Du kan i stället använda förinställda kontexter direkt i ditt program.

- **Cell användbara**. PySpark-kerneln innehåller vissa fördefinierade ”användbara”, vilket är särskilt kommandon som du kan anropa med `%%` (till exempel `%%MAGIC` <args>). Den magiska språkkommandot måste vara det första ordet i en kodcell och tillåter flera rader med innehåll. Magic ordet ska vara det första ordet i cellen. Att lägga till något innan magic, även kommentarer orsakar ett fel.     Mer information om användbara funktioner finns i [här](http://ipython.readthedocs.org/en/stable/interactive/magics.html).
   
    I följande tabell visas de olika användbara funktioner via dessa kernlar.

   | Magic | Exempel | Beskrivning |
   | --- | --- | --- |
   | Hjälp |`%%help` |Genererar en tabell med alla tillgängliga användbara med exempel och beskrivning |
   | info |`%%info` |Utdata sessionsinformation för den aktuella Livy-slutpunkten |
   | konfigurera |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Konfigurerar parametrar för att skapa en session. Flaggan force (-f) är obligatoriskt om en session redan har skapats, vilket garanterar att sessionen är släppas och återskapas. Titta på [Livy's POST /sessions Begärandetext](https://github.com/cloudera/livy#request-body) en lista över giltiga parametrar. Parametrar måste skickas som en JSON-sträng och måste vara på nästa rad efter magic, som visas i Exempelkolumnen. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Kör en Hive-fråga mot sqlContext. Om den `-o` parametern har gått ut, resultatet av frågan sparas i den %% lokal Python-kontext som en [Pandas](http://pandas.pydata.org/) dataframe. |
   | lokal |`%%local`<br>`a=1` |All kod i efterföljande rader körs lokalt. Koden måste vara giltig Python2 kod även oavsett kernel som du använder. Så även om du har valt **PySpark3** eller **Spark** kernlar när du skapar den bärbara datorn om du använder den `%%local` magic i en cell, cellen måste bara har giltig Python2-kod... |
   | loggar |`%%logs` |Utdata loggarna för den aktuella Livy-sessionen. |
   | delete |`%%delete -f -s <session number>` |Tar bort en viss session för den aktuella Livy-slutpunkten. Du kan inte ta bort sessionen som initieras för kernel själva. |
   | cleanup |`%%cleanup -f` |Tar bort alla sessioner för aktuella Livy slutpunkten, inklusive anteckningsbokens session. Framtvinga flaggan -f. är obligatoriskt. |

   > [!NOTE]
   > Förutom användbara funktioner har lagts till av PySpark-kerneln, du kan också använda den [inbyggda IPython användbara](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), inklusive `%%sh`. Du kan använda den `%%sh` magic att köra skript och kodblock på klustrets huvudnod.
   >
   >
2. **Automatisk visualisering**. Den **Pyspark** kernel automatiskt visualiserar utdata för Hive- och SQL-frågor. Du kan välja mellan flera olika typer av visualiseringar, inklusive tabell, cirkel, rad, området, -fältet.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametrar som stöds med den %% sql magic
Den `%%sql` magic har stöd för olika parametrar som du kan använda för att styra vilken typ av utdata som visas när du kör frågor. I följande tabell visas utdata.

| Parameter | Exempel | Beskrivning |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Använd den här parametern för att spara resultatet av frågan, i den %% lokal Python kontext som en [Pandas](http://pandas.pydata.org/) dataframe. Namnet på variabeln dataframe är variabelnamnet som du anger. |
| -q |`-q` |Används för att stänga av visualiseringar för cellen. Om du inte vill visualisera innehållet i en cell automatiskt och bara vill hämta in den som en dataram och sedan använda `-q -o <VARIABLE>`. Om du vill stänga av visualiseringar utan registrerar resultaten (till exempel för att köra en SQL-fråga som en `CREATE TABLE` instruktionen), Använd `-q` utan att ange en `-o` argumentet. |
| -m |`-m <METHOD>` |Där **metoden** är antingen **ta** eller **exempel** (standardvärdet är **ta**). Om metoden är **ta**, kernel hämtar element högst upp på resultatuppsättningen för data som anges av MAXROWS (beskrivs senare i den här tabellen). Om metoden är **exempel**, kernel-exempel slumpmässigt element i datauppsättningen enligt `-r` parametern, som beskrivs nedan i den här tabellen. |
| -r |`-r <FRACTION>` |Här **del** är ett flyttal mellan 0,0 och 1,0. Om exemplet metoden för SQL-frågan är `sample`, och sedan kernel exempel slumpmässigt för den angivna andelen av elementen i resultatuppsättningen för dig. Exempel: Om du kör en SQL-fråga med argument `-m sample -r 0.01`, och sedan 1% av resultatraderna slumpmässigt samplas. |
| -n |`-n <MAXROWS>` |**MAXROWS** är ett heltal. Kerneln begränsar antalet rader som utdata ska **MAXROWS**. Om **MAXROWS** är ett negativt tal som **-1**, antalet rader i resultatmängden är inte begränsad. |

**Exempel:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

Instruktionen ovan gör följande:

* Markera alla poster från **hivesampletable**.
* Eftersom vi använder - q, inaktiverar automatisk visualisering.
* Eftersom vi använder `-m sample -r 0.1 -n 500` det slumpmässigt exempel 10% av raderna i hivesampletable och begränsar storleken på resultatuppsättningen till 500 rader.
* Slutligen, eftersom vi använde `-o query2` sparas även utdata till en dataram som heter **fråga2**.

## <a name="considerations-while-using-the-new-kernels"></a>Att tänka på när du använder de nya kernlar

Oavsett vilket kernel som du använder, förbrukar lämnar de bärbara datorer som kör klusterresurserna.  Med dessa kernlar eftersom kontexter är förinställda bara avsluta de bärbara datorerna inte avsluta kontexten och kan därför klusterresurserna fortsätter att användas. Ett bra tips är att använda den **Stäng och stoppa** alternativet från den bärbara datorn **filen** menyn när du är klar med hjälp av anteckningsboken som stoppar kontexten och avslutas anteckningsboken.     

## <a name="show-me-some-examples"></a>Visa mig några exempel

När du öppnar en Jupyter-anteckningsbok kan se du två mappar som är tillgängliga på rotnivå.

* Den **PySpark** mapp har exempelanteckningsböcker som använder den nya **Python** kernel.
* Den **Scala** mapp har exempelanteckningsböcker som använder den nya **Spark** kernel.

Du kan öppna den **00 - [Läs mig första] Spark Magic Kernelfunktioner** anteckningsboken från den **PySpark** eller **Spark** mapp om du vill veta mer om de olika användbara funktioner. Du kan också använda de andra exemplet anteckningsböckerna som är tillgänglig under två mappar för att lära dig hur du utför olika scenarier med Jupyter-anteckningsböcker med HDInsight Spark-kluster.

## <a name="where-are-the-notebooks-stored"></a>Där lagras de bärbara datorerna?

Om klustret använder Azure Storage som standardkonto för lagring, Jupyter-anteckningsböcker sparas till storage-konto under den **/HdiNotebooks** mapp.  Bärbara datorer, textfiler och mappar som du skapar från inom Jupyter är tillgängliga från lagringskontot.  Exempel: Om du använder Jupyter för att skapa en mapp **MinMapp** och en anteckningsbok **myfolder/mynotebook.ipynb**, du kan komma åt den notebook på `/HdiNotebooks/myfolder/mynotebook.ipynb` i lagringskontot.  Det gäller även omvänt, det vill säga om du överför en bärbar dator direkt till ditt storage-konto på `/HdiNotebooks/mynotebook1.ipynb`, anteckningsboken är synlig från Jupyter samt.  Anteckningsböcker är kvar på lagringskontot även när klustret tas bort.

> [!NOTE]
> HDInsight-kluster med Azure Data Lake Store som standardlagring lagra inte anteckningsböcker i associerade lagringen.
>

Hur anteckningsböcker sparas till storage-kontot är kompatibelt med HDFS. Så, om du SSH till klustret som du kan använda filen kommandon för hantering som du ser i följande kodavsnitt:

    hdfs dfs -ls /HdiNotebooks                               # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                    # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Oavsett om klustret använder Azure Storage eller Azure Data Lake Store som standardkonto för lagring, sparas också de bärbara datorerna på klustrets huvudnod vid `/var/lib/jupyter`.

## <a name="supported-browser"></a>Webbläsare som stöds

Jupyter notebook i Spark HDInsight-kluster stöds bara på Google Chrome.

## <a name="feedback"></a>Feedback
Ny dessa kernlar finns i utvecklas scenen och kommer mogna över tid. Detta kan också innebära att API: er kan ändras när dessa kernlar mogna. Skicka oss gärna feedback som du har när du använder dessa kernlar som är nya. Detta är användbart i att forma den slutliga utgåvan av dessa kernlar. Du kan lämna kommentarer eller feedback under den **kommentarer** avsnittet längst ned i den här artikeln.

## <a name="seealso"></a>Se även
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att felsöka Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

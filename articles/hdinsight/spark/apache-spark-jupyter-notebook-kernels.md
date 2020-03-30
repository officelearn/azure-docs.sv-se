---
title: Kärnor för jupyter-anteckningsbok på Spark-kluster i Azure HDInsight
description: Lär dig mer om pyspark-, PySpark3- och Spark-kärnorna för jupyter-anteckningsboken som är tillgänglig med Spark-kluster på Azure HDInsight.
keywords: jupyter anteckningsbok på gnista, jupyter gnista
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/20/2020
ms.openlocfilehash: a04b8fee31ffa5280bc8ad0fca35495bb87e0e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064473"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Kärnor för Jupyter-anteckningsbok på Apache Spark-kluster i Azure HDInsight

HDInsight Spark-kluster tillhandahåller kärnor som du kan använda med den jupyterdator som du kan använda på [Apache Spark](./apache-spark-overview.md) för att testa dina program. En kärna är ett program som kör och tolkar din kod. De tre kärnorna är:

- **PySpark** - för applikationer skrivna i Python2.
- **PySpark3** - för program skrivna i Python3.
- **Spark** - för applikationer skrivna i Scala.

I den här artikeln får du lära dig hur du använder dessa kärnor och fördelarna med att använda dem.

## <a name="prerequisites"></a>Krav

Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Skapa en Jupyter-anteckningsbok på Spark HDInsight

1. Välj ditt Spark-kluster i [Azure-portalen.](https://portal.azure.com/)  Se [Lista och visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters) för instruktionerna. **Översiktsvyn** öppnas.

2. Välj **Jupyter-anteckningsbok**i rutan **Klusterinstrumentpaneler** i **översiktsvyn** . Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

    ![Jupyter anteckningsbok på Apache Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter anteckningsbok på Spark")
  
   > [!NOTE]  
   > Du kan också nå Jupyter-anteckningsboken i Spark-klustret genom att öppna följande webbadress i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Välj **Ny**och välj sedan **Antingen Pyspark,** **PySpark3**eller **Spark** för att skapa en anteckningsbok. Använd Spark-kärnan för Scala-program, PySpark-kärnan för Python2-program och PySpark3-kärnan för Python3-program.

    ![Kärnor för Jupyter notebook på Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Kärnor för Jupyter notebook på Spark")

4. En anteckningsbok öppnas med den kärna du har valt.

## <a name="benefits-of-using-the-kernels"></a>Fördelar med att använda kärnorna

Här är några fördelar med att använda de nya kärnorna med Jupyter notebook på Spark HDInsight-kluster.

- **Förinställda kontexter**. Med **PySpark,** **PySpark3**eller **Spark-kärnorna** behöver du inte uttryckligen ange Spark- eller Hive-kontexterna innan du börjar arbeta med dina program. Dessa är tillgängliga som standard. Dessa sammanhang är:

  - **sc** - för Spark-kontext
  - **sqlContext** - för Hive-kontext

    Så du **behöver inte** köra uttalanden som följande för att ställa in sammanhang:

         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)

    I stället kan du direkt använda de förinställda kontexterna i programmet.

- **Cell magi**. PySpark-kärnan ger några fördefinierade "magi", som är speciella kommandon `%%` som du `%%MAGIC` `<args>`kan ringa med (till exempel). Det magiska kommandot måste vara det första ordet i en kodcell och tillåta flera innehållsrader. Det magiska ordet borde vara det första ordet i cellen. Lägga till något innan magi, även kommentarer, orsakar ett fel.     För mer information om magi, se [här](https://ipython.readthedocs.org/en/stable/interactive/magics.html).

    I följande tabell visas de olika magier som finns tillgängliga via kärnorna.

   | Magi | Exempel | Beskrivning |
   | --- | --- | --- |
   | Hjälp |`%%help` |Genererar en tabell över alla tillgängliga magier med exempel och beskrivning |
   | information |`%%info` |Utdata sessionsinformation för den aktuella Livy-slutpunkten |
   | konfigurera |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Konfigurerar parametrarna för att skapa en session. Kraftflaggan (-f) är obligatorisk om en session redan har skapats, vilket säkerställer att sessionen tas bort och återskapas. Titta på [Livy's POST / sessioner Request Body](https://github.com/cloudera/livy#request-body) för en lista över giltiga parametrar. Parametrar måste skickas in som en JSON-sträng och måste vara på nästa rad efter magin, som visas i exempelkolumnen. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Kör en Hive-fråga mot sqlContext. Om `-o` parametern skickas sparas resultatet av frågan i kontexten %%lokal Python som en [Pandas-dataram.](https://pandas.pydata.org/) |
   | lokalt |`%%local`<br>`a=1` |All kod i efterföljande rader körs lokalt. Koden måste vara giltig Python2-kod även oavsett vilken kärna du använder. Så även om du valde **PySpark3-** eller **Spark-kärnor** `%%local` när du skapade anteckningsboken, om du använder magin i en cell, får cellen bara ha giltig Python2-kod. |
   | loggar |`%%logs` |Matar ut loggarna för den aktuella Livy-sessionen. |
   | delete |`%%delete -f -s <session number>` |Tar bort en viss session för den aktuella Livy-slutpunkten. Du kan inte ta bort sessionen som initieras för själva kärnan. |
   | cleanup |`%%cleanup -f` |Tar bort alla sessioner för den aktuella Livy-slutpunkten, inklusive den här anteckningsbokens session. Kraftflaggan -f är obligatorisk. |

   > [!NOTE]  
   > Förutom den magi som lagts till av PySpark-kärnan kan du också använda den `%%sh` [inbyggda IPython-magin](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), inklusive . Du kan `%%sh` använda magin för att köra skript och kodblock på klusterheadnoden.

- **Automatisk visualisering**. Pyspark-kärnan visualiserar automatiskt utdata från Hive- och SQL-frågor. Du kan välja mellan flera olika typer av visualiseringar, inklusive Tabell, Cirkel, Linje, Område, Stapel.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametrar som stöds med %%sql magic

Magin `%%sql` stöder olika parametrar som du kan använda för att styra vilken typ av utdata du får när du kör frågor. I följande tabell visas utdata.

| Parameter | Exempel | Beskrivning |
| --- | --- | --- |
| o |`-o <VARIABLE NAME>` |Använd den här parametern om du vill spara resultatet av [Pandas](https://pandas.pydata.org/) frågan, i kontexten %%lokal Python, som en Pandas-dataram. Namnet på dataramsvariabeln är det variabelnamn som du anger. |
| -q |`-q` |Använd detta för att inaktivera visualiseringar för cellen. Om du inte vill initiera innehållet i en cell automatiskt och bara vill samla in `-q -o <VARIABLE>`den som en dataram använder du . Om du vill inaktivera visualiseringar utan att samla in resultaten (till exempel `CREATE TABLE` för `-q` att köra `-o` en SQL-fråga, till exempel en sats), använd utan att ange ett argument. |
| -m. |`-m <METHOD>` |Där **METOD** antingen **tas** eller **exempel** (standard är **ta).** Om metoden **används**väljer kärnan element från toppen av resultatdatauppsättningen som anges av MAXROWS (beskrivs senare i den här tabellen). Om metoden är **exempel**tar kärnan slumpmässigt prover på `-r` element i datauppsättningen enligt parametern, som beskrivs bredvid i den här tabellen. |
| -r |`-r <FRACTION>` |Här är **FRACTION** ett flyttalsnummer mellan 0,0 och 1,0. Om exempelmetoden för SQL-frågan är, `sample`tar kärnan slumpmässigt prover den angivna delen av elementen i resultatuppsättningen åt dig. Om du till exempel kör en `-m sample -r 0.01`SQL-fråga med argumenten har 1 % av resultatraderna ett slumpmässigt urval. |
| -n |`-n <MAXROWS>` |**MAXROWS** är ett heltalsvärde. Kärnan begränsar antalet utdatarader till **MAXROWS**. Om **MAXROWS** är ett negativt tal som **-1**begränsas inte antalet rader i resultatuppsättningen. |

**Exempel:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

Uttalandet ovan gör följande:

- Markerar alla poster från **hivesampletable**.
- Eftersom vi använder -q inaktiveras automatiskanpassning.
- Eftersom vi `-m sample -r 0.1 -n 500` använder det slumpmässigt prover 10% av raderna i hivesampletable och begränsar storleken på resultatet inställd på 500 rader.
- Slutligen, eftersom `-o query2` vi använde det också sparar utdata i en dataram som kallas **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Överväganden när du använder de nya kärnorna

Oavsett vilken kärna du använder förbrukar klusterresurserna om du lämnar de bärbara datorerna som körs.  Med dessa kärnor, eftersom kontexterna är förinställda, dödar helt enkelt inte anteckningsböckerna kontexten och därför fortsätter klusterresurserna att användas. En bra idé är att använda alternativet **Stäng och stoppa** från anteckningsbokens **Arkiv-meny** när du är klar med anteckningsboken, som dödar kontexten och sedan avslutar anteckningsboken.

## <a name="where-are-the-notebooks-stored"></a>Var lagras anteckningsböckerna?

Om klustret använder Azure Storage som standardlagringskonto sparas Jupyter-anteckningsböcker i lagringskontot under mappen **/HdiNotebooks.**  Anteckningsböcker, textfiler och mappar som du skapar inifrån Jupyter är tillgängliga från lagringskontot.  Till exempel, om du använder Jupyter för att skapa en mapp **myfolder** och en anteckningsbok **myfolder / mynotebook.ipynb**, kan du komma åt den bärbara datorn på `/HdiNotebooks/myfolder/mynotebook.ipynb` inom lagringskontot.  Det omvända är också sant, det vill säga om `/HdiNotebooks/mynotebook1.ipynb`du laddar upp en anteckningsbok direkt till ditt lagringskonto på , är anteckningsboken synlig från Jupyter också.  Anteckningsböcker finns kvar i lagringskontot även efter att klustret har tagits bort.

> [!NOTE]  
> HDInsight-kluster med Azure Data Lake Storage som standardlagring lagrar inte anteckningsböcker i associerad lagring.

Det sätt bärbara filer sparas på lagringskontot är kompatibelt med [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Så om du SSH i klustret kan du använda filhanteringskommandon som visas i följande utdrag:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it's visible from Jupyter

Oavsett om klustret använder Azure Storage eller Azure Data Lake Storage som standardlagringskonto `/var/lib/jupyter`sparas även anteckningsböckerna på klusterhuvudnoden på .

## <a name="supported-browser"></a>Webbläsare som stöds

Jupyter-anteckningsböcker i Spark HDInsight-kluster stöds endast på Google Chrome.

## <a name="feedback"></a>Feedback

De nya kärnorna är i föränderliga skede och kommer att mogna med tiden. Detta kan också innebära att API:er kan ändras när dessa kärnor mognar. Vi skulle uppskatta all feedback som du har när du använder dessa nya kärnor. Detta är användbart för att forma den slutliga versionen av dessa kärnor. Du kan lämna dina kommentarer/feedback under avsnittet **Feedback** längst ned i den här artikeln.

## <a name="see-also"></a>Se även

- [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

- [Apache Spark med BI: Utför interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
- [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
- [Apache Spark med maskininlärning: Använd Spark i HDInsight för att förutsäga resultat för livsmedelsinspektion](apache-spark-machine-learning-mllib-ipython.md)
- [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program

- [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
- [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

- [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
- [Använd HDInsight Tools Plugin för IntelliJ IDEA för att felsöka Apache Spark-program på distans](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
- [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
- [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
- [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser

- [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
- [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

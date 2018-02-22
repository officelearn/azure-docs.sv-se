---
title: "Kärnor för Jupyter notebook i Spark-kluster i Azure HDInsight | Microsoft Docs"
description: "Läs mer om PySpark och PySpark3 Spark kärnor för Jupyter-anteckningsbok med Spark-kluster i Azure HDInsight."
keywords: "jupyter-anteckningsbok på spark jupyter spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0719e503-ee6d-41ac-b37e-3d77db8b121b
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: 2be4477528c9109151c4737eabc16741cc020ce8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="kernels-for-jupyter-notebook-on-spark-clusters-in-azure-hdinsight"></a>Kärnor för Jupyter notebook i Spark-kluster i Azure HDInsight 

HDInsight Spark-kluster tillhandahåller kernlar som du kan använda med Jupyter notebook i Spark för att testa ditt program. En kernel är ett program som körs och tolkar din kod. Det finns tre kärnor:

- **PySpark** - för appar som skrivits i Python2
- **PySpark3** - för appar som skrivits i Python3
- **Spark** - för program som skrivits i Scala

Lär dig hur du använder dessa kärnor och fördelarna med att använda dem i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Skapa en Jupyter-anteckningsbok på Spark HDInsight

1. Från den [Azure-portalen](https://portal.azure.com/), öppna ditt kluster.  Se [listan och visa](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters) för instruktionerna. Klustret har öppnats i ett nytt portalblad.

2. Från den **snabblänkar** klickar du på **kluster instrumentpaneler** att öppna den **kluster instrumentpaneler** bladet.  Om du inte ser **snabblänkar**, klickar du på **översikt** i den vänstra menyn på bladet.

    ![Jupyter-anteckningsbok på Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-jupyter-notebook-on-spark.png "Jupyter notebook i Spark") 

3. Klicka på **Jupyter-anteckningsbok**. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.
   
   > [!NOTE]
   > Du kan också nå Jupyter notebook i Spark-kluster genom att öppna följande URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

3. Klicka på **ny**, och klicka sedan på antingen **Pyspark**, **PySpark3**, eller **Spark** att skapa en bärbar dator. Använda Spark kernel för Scala program, PySpark-kerneln för Python2 program och PySpark3 kernel för Python3 program.
   
    ![Kärnor för Jupyter notebook i Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "kärnor för Jupyter notebook i Spark") 

4. En bärbar dator öppnas med kärnan som du har valt.

## <a name="benefits-of-using-the-kernels"></a>Fördelarna med att använda kärnor

Här är några fördelar med att använda de nya kärnor med Jupyter notebook i Spark HDInsight-kluster.

- **Förinställningen kontexter**. Med **PySpark**, **PySpark3**, eller **Spark** kärnor, du behöver inte ange kontexter för Spark och Hive explicit innan du börjar arbeta med dina program. Dessa är tillgängliga som standard. Dessa kontexter är:
   
   * **SC** - Spark-kontexten
   * **sqlContext** - Hive-kontexten
   
   Så behöver du köra instruktioner som följande för att ange kontexter:
   
          sc = SparkContext('yarn-client')
          sqlContext = HiveContext(sc)
   
   Du kan i stället använda förinställda kontexter direkt i ditt program.

- **Cell användbara**. PySpark-kerneln innehåller vissa fördefinierade ”användbara”, som är särskilda kommandon som kan anropas med `%%` (till exempel `%%MAGIC` <args>). Kommandot magiskt måste vara det första ordet i en cell i koden och tillåter flera rader med innehåll. Magiskt ordet ska vara det första ordet i cellen. Lägger till något innan magic, även kommentarer orsakar ett fel.     Mer information om användbara finns [här](http://ipython.readthedocs.org/en/stable/interactive/magics.html).
   
    I följande tabell visas de olika tillgängliga via de kernlar som är användbara.

   | Magiskt tal | Exempel | Beskrivning |
   | --- | --- | --- |
   | hjälp |`%%help` |Genererar en tabell med alla tillgängliga användbara med exempel och beskrivning |
   | info |`%%info` |Utdata sessionsinformation för den aktuella Livius slutpunkten |
   | konfigurera |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Konfigurerar parametrar för att skapa en session. Flaggan force (-f) om en session redan har skapats, vilket garanterar att sessionen släppas och återskapas. Titta på [Liviuss POST /sessions brödtext i begäran](https://github.com/cloudera/livy#request-body) en lista över giltiga parametrar. Parametrar måste överföras i som en JSON-sträng och måste vara på nästa rad efter magic, som visas i Exempelkolumnen. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Kör en Hive-fråga mot sqlContext. Om den `-o` -parameter har skickats, resultatet av frågan sparas i den %% lokala Python kontext som en [Pandas](http://pandas.pydata.org/) dataframe. |
   | lokal |`%%local`<br>`a=1` |Alla koden i efterföljande rader körs lokalt. Koden måste vara giltig Python2 kod även oavsett kernel som du använder. Så även om du har valt **PySpark3** eller **Spark** kärnor när du skapar den bärbara datorn om du använder den `%%local` magiskt i en cell, cellen får bara ha giltig Python2 kod... |
   | loggar |`%%logs` |Utdata loggar för den aktuella Livius-sessionen. |
   | radera |`%%delete -f -s <session number>` |Tar bort en viss session på den aktuella Livius slutpunkten. Du kan inte ta bort sessionen som initieras för kernel sig själv. |
   | cleanup |`%%cleanup -f` |Tar bort alla sessioner för aktuella Livius slutpunkten, inklusive anteckningsbokens session. Tvingad flaggan -f. är obligatoriskt. |

   > [!NOTE]
   > Förutom de användbara som lagts till av PySpark-kerneln, du kan också använda den [inbyggda IPython användbara](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), inklusive `%%sh`. Du kan använda den `%%sh` Magiskt tal för att köra skript och kodblock på klustret headnode.
   >
   >
2. **Automatisk visualiseringen**. Den **Pyspark** kernel automatiskt visualizes utdata från Hive och SQL-frågor. Du kan välja mellan flera olika typer av visualiseringar inklusive tabell, cirkeldiagram, rad, område, fältet.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametrar som stöds med den %% Magiskt tal för sql
Den `%%sql` magic stöder olika parametrar som du kan använda för att styra vilken typ av utdata som visas när du kör frågor. I följande tabell visas utdata.

| Parameter | Exempel | Beskrivning |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Använd den här parametern för att spara resultatet av frågan i den %% lokala Python kontext som en [Pandas](http://pandas.pydata.org/) dataframe. Namnet på variabeln dataframe är variabelnamnet som du anger. |
| -q |`-q` |Används för att inaktivera visualiseringar i cellen. Om du inte vill visualisera automatiskt innehållet i en cell och bara vill samla in det som en dataframe och sedan använda `-q -o <VARIABLE>`. Om du vill stänga av visualiseringar utan att samla in resultatet (till exempel för att köra en SQL-fråga som en `CREATE TABLE` instruktionen), Använd `-q` utan att ange en `-o` argumentet. |
| -m |`-m <METHOD>` |Där **metoden** är antingen **ta** eller **exempel** (standard är **ta**). Om metoden är **ta**, kernel hämtar element från början av det resultat som anges av MAXROWS (beskrivs senare i den här tabellen). Om metoden är **exempel**, kernel slumpmässigt exempel element i datauppsättningen enligt `-r` parametern beskrivs nedan i den här tabellen. |
| -r |`-r <FRACTION>` |Här **bråk** är ett flyttal mellan 0,0 och 1,0. Om exemplet för SQL-frågan är `sample`, och sedan på den angivna andelen av elementen i resultatuppsättningen du exempel slumpmässigt för kernel. Om du kör en SQL-fråga med argument exempelvis `-m sample -r 0.01`, och sedan 1% av resultatet rader samplas slumpmässigt. |
| -n |`-n <MAXROWS>` |**MAXROWS** är ett heltal. Kernel begränsar antalet rader som utdata ska **MAXROWS**. Om **MAXROWS** är ett negativt tal som **-1**, antalet rader i resultatmängden är inte begränsad. |

**Exempel:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

Instruktionen ovan gör följande:

* Markera alla poster från **hivesampletable**.
* Eftersom vi använder - q, inaktiverar automatisk visualiseringen.
* Eftersom vi använder `-m sample -r 0.1 -n 500` den slumpmässigt exempel 10% av raderna i hivesampletable och begränsar storleken på resultatuppsättningen till 500 rader.
* Slutligen, eftersom vi använde `-o query2` sparas även utdata till en dataframe kallas **fråga2**.

## <a name="considerations-while-using-the-new-kernels"></a>Att tänka på när du använder de nya kärnor

Oavsett vilken kernel som du använder, förbrukar lämnar de bärbara datorer kör klusterresurserna.  Med dessa kärnor eftersom kontexter är förinställda bara avsluta de bärbara datorerna inte avsluta kontexten och därför klusterresurserna fortsätter att användas. Är en bra idé att använda den **Stäng och stoppa** alternativet från den bärbara datorn **filen** menyn när du är klar med den bärbara datorn stängs kontexten och avslutas anteckningsboken.     

## <a name="show-me-some-examples"></a>Visa exempel

När du öppnar en Jupyter-anteckningsbok kan se du två mappar på rotnivå.

* Den **PySpark** mappen innehåller exempel bärbara datorer som använder den nya **Python** kernel.
* Den **Scala** mappen innehåller exempel bärbara datorer som använder den nya **Spark** kernel.

Kan du öppna den **00 - [läsa mig första] Spark Magic Kernel funktioner** bärbar dator från den **PySpark** eller **Spark** mapp att lära dig om olika användbara. Du kan också använda de andra exemplet anteckningsböckerna som är tillgängliga under två mappar för att lära dig att uppnå olika scenarier med Jupyter-anteckningsböcker med HDInsight Spark-kluster.

## <a name="where-are-the-notebooks-stored"></a>Var lagras de bärbara datorerna?

Jupyter-anteckningsböcker sparas till storage-konto som är associerade med klustret under den **/HdiNotebooks** mapp.  Bärbara datorer, textfiler och mappar som du skapar från i Jupyter är tillgängliga från lagringskontot.  Till exempel om du skapar en mapp med Jupyter **MinMapp** och en bärbar dator **myfolder/mynotebook.ipynb**, du kan komma åt den bärbara på `/HdiNotebooks/myfolder/mynotebook.ipynb` inom lagringskonto.  Den gäller även omvänt, det vill säga om du överför en bärbar dator direkt till ditt lagringskonto på `/HdiNotebooks/mynotebook1.ipynb`, anteckningsboken är synlig från Jupyter samt.  Bärbara datorer finns kvar i lagringskontot även när klustret har tagits bort.

Hur anteckningsböcker sparas till lagringskontot är kompatibelt med HDFS. Så, om du SSH till klustret som du kan använda filen management kommandon som visas i följande utdrag:

    hdfs dfs -ls /HdiNotebooks                               # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                    # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


Om det finns problem med att komma åt lagringskontot för klustret, de bärbara datorerna sparas även på headnode `/var/lib/jupyter`.

## <a name="supported-browser"></a>Webbläsare som stöds

Jupyter-anteckningsböcker på HDInsight Spark-kluster stöds bara på Google Chrome.

## <a name="feedback"></a>Feedback
Nya kärnor i utvecklingen av steg och kommer mogna över tid. Detta kan också innebära att API: er kan ändra som dessa kärnor mogna. Vi gärna feedback som du har när du använder dessa nya kärnor. Detta är användbart i shaping slutversionen av dessa kärnor. Du kan lämna kommentarer/feedback under den **kommentarer** avsnittet längst ned i den här artikeln.

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

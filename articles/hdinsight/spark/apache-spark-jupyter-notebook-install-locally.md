---
title: Installera Jupyter lokalt och Anslut till Spark i Azure HDInsight
description: Lär dig mer om att installera Jupyter-anteckningsbok lokalt på datorn och ansluter den till ett Apache Spark-kluster.
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: hrasheed
ms.openlocfilehash: 489685485af4e3c8868f7e0281d2f81464a166f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066182"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Installera Jupyter notebook på datorn och ansluta till Apache Spark i HDInsight

I den här artikeln lär du dig hur du installerar Jupyter-anteckningsbok med anpassade PySpark (för Python) och Apache Spark (för Scala) kernlar med Spark magic och ansluter den bärbara datorn till ett HDInsight-kluster. Det kan finnas flera anledningar till att installera Jupyter på den lokala datorn och det kan finnas några utmaningar samt. Mer information om detta finns i avsnittet [Varför bör jag installera Jupyter på datorn](#why-should-i-install-jupyter-on-my-computer) i slutet av den här artikeln.

Det finns fyra viktiga steg som ingår i installera Jupyter och ansluta till Apache Spark i HDInsight.

* Konfigurera Spark-kluster.
* Installera Jupyter-anteckningsboken.
* Installera dessa kernlar PySpark och Spark med Spark-magic.
* Konfigurera Spark magic för att komma åt Spark-kluster i HDInsight.

Mer information om anpassade kernlar och den Spark magin som är tillgängliga för Jupyter-anteckningsböcker med HDInsight-kluster finns i [Kernlar som är tillgängliga för Jupyter-anteckningsböcker med Apache Spark Linux-kluster i HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

De förutsättningar som anges här är inte för att installera Jupyter. Det här är för Jupyter-anteckningsboken för att ansluta till ett HDInsight-kluster när anteckningsboken har installerats.

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Installera Jupyter notebook på datorn

Du måste installera Python innan du kan installera Jupyter-anteckningsböcker. Den [Anaconda distribution](https://www.anaconda.com/download/) installerar både, Python och Jupyter-anteckningsboken.

Ladda ned den [Anaconda installer](https://www.anaconda.com/download/) för din plattform och kör installationen. När du kör installationsguiden, kontrollera att du väljer alternativet att lägga till Anaconda i PATH-variabeln.  Se även [installera Jupyter med Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Installera Spark magic

1. Ange något av kommandona nedan för att installera Spark magic. Se även [sparkmagic dokumentation](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Klusterversion | Installationskommando |
    |---|---|
    |v3.6 och v3.5 |`pip install sparkmagic==0.12.7`|
    |v3.4|`pip install sparkmagic==0.2.3`|

1. Se till att `ipywidgets` har installerats korrekt genom att köra följande kommando:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Installera PySpark och Spark-kärnor

1. Bestäm var du `sparkmagic` har installerats genom att ange följande kommando:

    ```cmd
    pip show sparkmagic
    ```

    Ändra din arbetskatalog till den plats som anges med kommandot ovan.

1. Ange en eller flera av kommandona nedan för att installera den önskade kernel(s) från din nya arbetskatalog:

    |Kernel | Kommando |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Valfri. Ange kommandot nedan för att aktivera servertillägget:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Konfigurera Spark magic att ansluta till HDInsight Spark-kluster

I det här avsnittet ska konfigurera du Spark-magic som du har installerat tidigare för att ansluta till ett Apache Spark-kluster.

1. Starta Python-gränssnittet med följande kommando:

    ```cmd
    python
    ```

2. Jupyter Konfigurationsinformationen lagras vanligtvis i arbetskatalogen för användare. Ange följande kommando för att identifiera arbetskatalogen och skapa en mapp med namnet **.sparkmagic**.  Ska vara den fullständiga sökvägen för utdata.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. I mappen `.sparkmagic`, skapa en fil med namnet **config.json** och Lägg till följande JSON-kodfragmentet i den.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Vill du göra följande ändringar i filen:

    |Värdet för mallen | Nytt värde |
    |---|---|
    |{USERNAME}|Klusterinloggning, standardvärdet är `admin`.|
    |{CLUSTERDNSNAME}|Klusternamn|
    |{BASE64ENCODEDPASSWORD}|En base64-kodad lösenordet för ditt faktiska lösenord.  Du kan generera en base64-lösenord vid [ https://www.url-encode-decode.com/base64-encode-decode/ ](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Håll om du använder `sparkmagic 0.12.7` (kluster v3.5 och v3.6).  Om du använder `sparkmagic 0.2.3` (kluster v3.4), Ersätt med `"should_heartbeat": true`.|

    Du kan se ett fullständigt exempel filen vid [exempel config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Det sänds pulsslag för att säkerställa att sessioner inte sprids. När en dator försätts i viloläge eller är avstängd, sänds pulsslag, vilket resulterar i sessionen som rensas. För kluster v3.4 om du vill inaktivera den här funktionen kan du ange Livy-config `livy.server.interactive.heartbeat.timeout` till `0` från Ambari UI. För kluster v3.5, om du inte anger 3,5 konfigurationen ovan tas sessionen inte bort.

5. Starta Jupyter. Använd följande kommando i Kommandotolken.

    ```cmd
    jupyter notebook
    ```

6. Kontrollera att du kan använda den Spark magin som är tillgängliga med dessa kernlar. Utför följande steg.

    a. Skapa en ny anteckningsbok. I det högra hörnet väljer **New**. Du bör se standardkernel **Python 2** eller **Python 3** och kernlar som du har installerat. De faktiska värdena kan variera beroende på vilka installationsalternativ.  Välj **PySpark**.

    ![Kernlar i Jupyter-anteckningsbok](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Kernlar i Jupyter-anteckningsbok")

    > [!IMPORTANT]  
    > När du har valt **New** granska ditt gränssnitt för eventuella fel.  Om du ser felet `TypeError: __init__() got an unexpected keyword argument 'io_loop'` kan uppstå ett känt problem med vissa versioner av storm.  I så, fall stoppa kernel och sedan nedgraderar storm installationen med följande kommando: `pip install tornado==4.5.3`.

    b. Kör följande kodavsnitt.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Om du har hämta utdata, testas din anslutning till HDInsight-kluster.

    Om du vill uppdatera konfigurationen av anteckningsboken för att ansluta till ett annat kluster måste du uppdatera config.json med den nya uppsättningen värden, som visas i steg3 ovan.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Varför bör jag installera Jupyter på datorn?

Det kan finnas flera skäl till varför du kanske vill installera Jupyter på datorn och ansluter den till ett Apache Spark-kluster i HDInsight.

* Även om Jupyter notebooks finns redan på Spark-kluster i Azure HDInsight, installera Jupyter på datorn ger dig möjlighet att skapa dina anteckningsböcker lokalt, testa programmet mot ett aktivt kluster och sedan ladda upp den anteckningsböcker till klustret. Om du vill ladda upp anteckningsböckerna till klustret, kan du ladda upp dem med Jupyter-anteckningsboken som körs eller klustret eller spara dem i mappen /HdiNotebooks i lagringskontot som associerats med klustret. Mer information om hur anteckningsböcker lagras i klustret finns i [där lagras Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Med de bärbara datorerna som är tillgänglig lokalt, kan du ansluta till olika Spark-kluster utifrån behov.
* Du kan använda GitHub för att implementera ett källkontrollsystem och har versionskontroll för de bärbara datorerna. Du kan också ha en samarbetsmiljö där flera användare kan arbeta med samma anteckningsbok.
* Du kan arbeta med bärbara datorer lokalt utan även att ett kluster. Du behöver bara ett kluster att testa dina anteckningsböcker mot, inte för att manuellt hantera dina anteckningsböcker eller en utvecklingsmiljö.
* Det kan vara enklare att konfigurera din egen lokala utvecklingsmiljö än att konfigurera Jupyter-installationen på klustret.  Du kan dra nytta av all programvara som du har installerat lokalt utan att konfigurera ett eller flera fjärranslutna kluster.

> [!WARNING]  
> Med Jupyter installerat på din lokala dator, kan flera användare köra samma anteckningsbok i samma Spark-klustret på samma gång. I detta fall skapas flera Livy-sessioner. Om du får problem och vill felsöka det blir en komplicerad uppgift att spåra vilken Livy-session som hör till vilken användare.  

## <a name="next-steps"></a>Nästa steg

* [Översikt: Apache Spark på Azure HDInsight](apache-spark-overview.md)
* [Apache Spark med BI: Utföra interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
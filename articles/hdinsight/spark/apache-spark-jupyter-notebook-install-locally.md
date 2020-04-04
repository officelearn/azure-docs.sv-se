---
title: Installera Jupyter lokalt och anslut till Spark i Azure HDInsight
description: Lär dig hur du installerar Jupyters bärbara dator lokalt på datorn och ansluter den till ett Apache Spark-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/02/2020
ms.openlocfilehash: 1d044ddaea0a2c7a1d489523cc9aa4515df0728a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632666"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Installera Jupyter notebook på datorn och anslut till Apache Spark på HDInsight

I den här artikeln får du lära dig hur du installerar Jupyter notebook med anpassade PySpark (för Python) och Apache Spark (för Scala) kärnor med Spark magi. Du ansluter sedan anteckningsboken till ett HDInsight-kluster.

Det finns fyra viktiga steg inblandade i att installera Jupyter och ansluta till Apache Spark på HDInsight.

* Konfigurera Spark-kluster.
* Installera Jupyter notebook.
* Installera PySpark- och Spark-kärnorna med Spark-magin.
* Konfigurera Spark magi för att komma åt Spark-kluster på HDInsight.

Mer information om anpassade kärnor och Spark-magi finns i [Kärnor som är tillgängliga för Jupyter-anteckningsböcker med Apache Spark Linux-kluster på HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Krav

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md). Den lokala anteckningsboken ansluter till HDInsight-klustret.

* Kunskaper om Jupyter Notebooks med Spark på HDInsight.

## <a name="install-jupyter-notebook-on-your-computer"></a>Installera Jupyter notebook på datorn

Installera Python innan du installerar Jupyter-anteckningsböcker. [Anaconda-distributionen](https://www.anaconda.com/download/) installerar både Python och Jupyter Notebook.

Ladda ner [Anaconda-installationsprogrammet](https://www.anaconda.com/download/) för din plattform och kör installationen. När du kör installationsguiden kontrollerar du att du väljer alternativet att lägga till Anaconda i sökvägens variabel.  Se även [installera Jupyter med Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Installera Spark magi

1. Ange ett av kommandona nedan för att installera Spark magic. Se även [sparkmagic dokumentation](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Klusterversion | Kommandot Installera |
    |---|---|
    |v3.6 och v3.5 |`pip install sparkmagic==0.13.1`|
    |v3.4|`pip install sparkmagic==0.2.3`|

1. Kontrollera `ipywidgets` att den är korrekt installerad genom att köra följande kommando:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Installera PySpark- och Spark-kärnor

1. Identifiera `sparkmagic` var som installeras genom att ange följande kommando:

    ```cmd
    pip show sparkmagic
    ```

    Ändra sedan arbetskatalogen till den **plats** som identifieras med kommandot ovan.

1. Från din nya arbetskatalog anger du ett eller flera av kommandona nedan för att installera önskad kärna:From your new working directory, enter one or more of the commands below to install the wanted kernel(s):

    |Kernel | Kommando |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |Gnist|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |Pyspark (på en)|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |Pyspark3 (på andra sätt)|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Valfri. Ange kommandot nedan för att aktivera servertillägget:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Konfigurera Spark magic för att ansluta till HDInsight Spark-kluster

I det här avsnittet konfigurerar du Spark-magin som du installerade tidigare för att ansluta till ett Apache Spark-kluster.

1. Starta Python-skalet med följande kommando:

    ```cmd
    python
    ```

2. Jupyter-konfigurationsinformationen lagras vanligtvis i användarnas arbetskatalog. Ange följande kommando för att identifiera arbetskatalogen och skapa en mapp som heter ** \.sparkmagic**.  Den fullständiga sökvägen matas ut.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Skapa en `.sparkmagic`fil som heter **config.json** i mappen och lägg till följande JSON-utdrag i den.  

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

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Gör följande ändringar i filen:

    |Mallvärde | Nytt värde |
    |---|---|
    |{ANVÄNDARNAMN}|Klusterinloggning, `admin`standard är .|
    |{CLUSTERDNSNAME}|Klusternamn|
    |{BASE64ENCODEDPASSWORD}|Ett base64-kodat lösenord för ditt faktiska lösenord.  Du kan generera ett base64-lösenord på [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Behåll `sparkmagic 0.12.7` om du använder (kluster v3.5 och v3.6).  Om `sparkmagic 0.2.3` du använder (kluster v3.4), ersätt med `"should_heartbeat": true`.|

    Du kan se en fullständig exempelfil på [exempel config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Pulsslag skickas för att säkerställa att sessioner inte läcker ut. När en dator försätts i viloläge eller stängs av skickas inte pulsslag, vilket resulterar i att sessionen rensas. För kluster v3.4, om du vill inaktivera detta beteende, kan `livy.server.interactive.heartbeat.timeout` `0` du ställa in Livy config till från Ambari UI. För kluster v3.5, om du inte ställer in 3,5-konfigurationen ovan, kommer sessionen inte att tas bort.

5. Starta Jupyter. Använd följande kommando från kommandotolken.

    ```cmd
    jupyter notebook
    ```

6. Kontrollera att du kan använda Spark-magin som är tillgänglig med kärnorna. Utför följande steg.

    a. Skapa en ny anteckningsbok. Välj **Nytt**i det högra hörnet . Du bör se standardkärnan **Python 2** eller **Python 3** och de kärnor du installerade. De faktiska värdena kan variera beroende på dina installationsval.  Välj **PySpark**.

    ![Tillgängliga kärnor i Jupyters bärbara dator](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Kärnor i Jupyter anteckningsbok")

    > [!IMPORTANT]  
    > När du har valt **Nytt** granska skalet för eventuella fel.  Om du ser `TypeError: __init__() got an unexpected keyword argument 'io_loop'` felet kan du ha ett känt problem med vissa versioner av Tornado.  Om så är fallet, stoppa kärnan och sedan `pip install tornado==4.5.3`nedgradera din Tornado installation med följande kommando: .

    b. Kör följande kodavsnitt.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Om du kan hämta utdata testas anslutningen till HDInsight-klustret.

    Om du vill uppdatera konfigurationen för bärbara datorer för att ansluta till ett annat kluster uppdaterar du config.json med den nya uppsättningen värden, som visas i steg 3 ovan.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Varför ska jag installera Jupyter på min dator?

Anledningar till att installera Jupyter på datorn och sedan ansluta den till en Apache Spark kluster på HDInsight:

* Här kan du skapa dina anteckningsböcker lokalt, testa programmet mot ett kluster som körs och sedan överföra anteckningsböckerna till klustret. Om du vill överföra anteckningsböckerna till klustret kan du antingen ladda upp dem med `/HdiNotebooks` den Jupyter-anteckningsbok som körs eller i klustret, eller spara dem i mappen i lagringskontot som är associerat med klustret. Mer information om hur anteckningsböcker lagras i klustret finns i [Var lagras Jupyter-anteckningsböcker?](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)
* Med de bärbara datorerna som är tillgängliga lokalt kan du ansluta till olika Spark-kluster baserat på ditt programkrav.
* Du kan använda GitHub för att implementera ett källkontrollsystem och ha versionskontroll för anteckningsböckerna. Du kan också ha en samarbetsmiljö där flera användare kan arbeta med samma anteckningsbok.
* Du kan arbeta med anteckningsböcker lokalt utan att ens ha ett kluster upp. Du behöver bara ett kluster för att testa dina anteckningsböcker mot, inte manuellt hantera dina anteckningsböcker eller en utvecklingsmiljö.
* Det kan vara enklare att konfigurera din egen lokala utvecklingsmiljö än att konfigurera Jupyter-installationen i klustret.  Du kan dra nytta av all programvara som du har installerat lokalt utan att konfigurera ett eller flera fjärrkluster.

> [!WARNING]  
> Med Jupyter installerat på din lokala dator kan flera användare köra samma anteckningsbok på samma Spark-kluster samtidigt. I en sådan situation skapas flera Livy-sessioner. Om du stöter på ett problem och vill felsöka det, blir det en komplex uppgift att spåra vilken Livy-session som tillhör vilken användare.  

## <a name="next-steps"></a>Nästa steg

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)
* [Apache Spark med BI: Analysera Apache Spark-data med Power BI i HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)

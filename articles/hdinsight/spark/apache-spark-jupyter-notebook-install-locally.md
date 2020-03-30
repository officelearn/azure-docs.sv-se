---
title: Installera Jupyter lokalt och anslut till Spark i Azure HDInsight
description: Lär dig hur du installerar Jupyters bärbara dator lokalt på datorn och ansluter den till ett Apache Spark-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 225ee7028b9610a4974f9bee05da667d78d3355e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903739"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Installera Jupyter notebook på datorn och anslut till Apache Spark på HDInsight

I den här artikeln lär du dig att installera Jupyter notebook, med de anpassade PySpark -kärnorna PySpark (för Python) och Apache Spark (för Scala) med Spark-magi och ansluter den bärbara datorn till ett HDInsight-kluster. Det kan finnas ett antal skäl att installera Jupyter på din lokala dator, och det kan finnas vissa utmaningar också. För mer information om detta, se avsnittet [Varför ska jag installera Jupyter på min dator](#why-should-i-install-jupyter-on-my-computer) i slutet av den här artikeln.

Det finns fyra viktiga steg inblandade i att installera Jupyter och ansluta till Apache Spark på HDInsight.

* Konfigurera Spark-kluster.
* Installera Jupyter notebook.
* Installera PySpark- och Spark-kärnorna med Spark-magin.
* Konfigurera Spark magi för att komma åt Spark-kluster på HDInsight.

Mer information om de anpassade kärnorna och Spark-magin för Jupyter-anteckningsböcker med HDInsight-kluster finns i [Kärnor som är tillgängliga för Jupyter-anteckningsböcker med Apache Spark Linux-kluster på HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Krav

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md). Detta är en förutsättning för att ansluta Jupyter-anteckningsboken till ett HDInsight-kluster när den bärbara datorn har installerats.

* Kunskaper om Jupyter Notebooks med Spark på HDInsight.

## <a name="install-jupyter-notebook-on-your-computer"></a>Installera Jupyter notebook på datorn

Du måste installera Python innan du kan installera Jupyter-anteckningsböcker. [Anaconda-distributionen](https://www.anaconda.com/download/) installerar både Python och Jupyter Notebook.

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

    Ändra sedan arbetskatalogen till den plats som identifieras med kommandot ovan.

1. Från den nya arbetskatalogen anger du ett eller flera av kommandona nedan för att installera önskad kärna:From your new working directory, enter one or more of the commands below to install the desired kernel(s):

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

2. Jupyter-konfigurationsinformationen lagras vanligtvis i användarnas arbetskatalog. Ange följande kommando för att identifiera arbetskatalogen och skapa en mapp med namnet **.sparkmagic**.  Den fullständiga sökvägen matas ut.

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

Det kan finnas ett antal anledningar till varför du kanske vill installera Jupyter på din dator och sedan ansluta den till en Apache Spark kluster på HDInsight.

* Även om Jupyter-anteckningsböcker redan är tillgängliga i Spark-klustret i Azure HDInsight, ger du möjlighet att skapa dina anteckningsböcker lokalt genom att installera Jupyter-datorer, testa programmet mot ett kluster som körs och sedan ladda upp bärbara datorer till klustret. Om du vill överföra anteckningsböckerna till klustret kan du antingen ladda upp dem med den Jupyter-anteckningsbok som körs eller i klustret, eller spara dem i mappen /HdiNotebooks i lagringskontot som är associerat med klustret. Mer information om hur anteckningsböcker lagras i klustret finns i [Var lagras Jupyter-anteckningsböcker?](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)
* Med de bärbara datorerna som är tillgängliga lokalt kan du ansluta till olika Spark-kluster baserat på ditt programkrav.
* Du kan använda GitHub för att implementera ett källkontrollsystem och ha versionskontroll för anteckningsböckerna. Du kan också ha en samarbetsmiljö där flera användare kan arbeta med samma anteckningsbok.
* Du kan arbeta med anteckningsböcker lokalt utan att ens ha ett kluster upp. Du behöver bara ett kluster för att testa dina anteckningsböcker mot, inte manuellt hantera dina anteckningsböcker eller en utvecklingsmiljö.
* Det kan vara enklare att konfigurera din egen lokala utvecklingsmiljö än att konfigurera Jupyter-installationen i klustret.  Du kan dra nytta av all programvara som du har installerat lokalt utan att konfigurera ett eller flera fjärrkluster.

> [!WARNING]  
> Med Jupyter installerat på din lokala dator kan flera användare köra samma anteckningsbok på samma Spark-kluster samtidigt. I en sådan situation skapas flera Livy-sessioner. Om du stöter på ett problem och vill felsöka det, blir det en komplex uppgift att spåra vilken Livy-session som tillhör vilken användare.  

## <a name="next-steps"></a>Nästa steg

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)
* [Apache Spark med BI: Utför interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)

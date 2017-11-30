---
title: Installera Jupyter lokalt och ansluta till ett Azure HDInsight Spark-kluster | Microsoft Docs
description: "Lär dig mer om att installera Jupyter-anteckningsbok lokalt på datorn och ansluta till ett Apache Spark-kluster i Azure HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48593bdf-4122-4f2e-a8ec-fdc009e47c16
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 1508faf7a05461de65b7a4c2f68e2ef9bbd7e19d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Installera Jupyter-anteckningsbok på datorn och ansluta till Apache Spark i HDInsight

I den här artikeln lär du dig att installera Jupyter-anteckningsbok med anpassade PySpark (för Python) och Spark (för Scala) kärnor med Spark magic och ansluta den bärbara datorn till ett HDInsight-kluster. Det kan finnas flera skäl till att installera Jupyter på den lokala datorn och det kan finnas några utmaningar samt. Mer information om detta finns i avsnittet [Varför bör jag installera Jupyter på datorn](#why-should-i-install-jupyter-on-my-computer) i slutet av den här artikeln.

Det finns tre viktiga steg ingår i installera Jupyter och Spark-magic på datorn.

* Installera Jupyter-anteckningsbok
* Installera PySpark och Spark kärnor med Spark-magic
* Konfigurera Spark Magiskt tal för att komma åt Spark-kluster i HDInsight

Mer information om anpassade kärnor och Spark Magiskt tal för Jupyter-anteckningsböcker med HDInsight-kluster finns [kernlar som är tillgängliga för Jupyter-anteckningsböcker med Apache Spark Linux-kluster i HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Krav
De förutsättningar som anges här är inte för att installera Jupyter. Det här är för att ansluta Jupyter-anteckningsbok till ett HDInsight-kluster när den bärbara datorn har installerats.

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Installera Jupyter-anteckningsbok på datorn

Du måste installera Python innan du kan installera Jupyter-anteckningsböcker. Både Python och Jupyter är tillgängliga som en del av den [Anaconda distribution](https://www.continuum.io/downloads). När du installerar Anaconda, installerar du en distribution av Python. När Anaconda har installerats kan du lägga till Jupyter-installation genom att köra lämpliga kommandon.

1. Hämta den [Anaconda installer](https://www.continuum.io/downloads) för din plattform och kör installationsprogrammet. När du kör guiden, kontrollera att du väljer alternativet att lägga till Anaconda i PATH-variabeln.
2. Kör följande kommando för att installera Jupyter.

        conda install jupyter

    Läs mer om hur du installerar Jupyter [installera Jupyter med Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Installera kärnor och Spark Magiskt tal

Instruktioner om hur du installerar Spark magic PySpark och Spark kärnor, Följ installationsanvisningarna i den [sparkmagic dokumentationen](https://github.com/jupyter-incubator/sparkmagic#installation) på GitHub. Det första steget i Spark magiskt dokumentationen uppmanar dig att installera Spark Magiskt tal. Ersätt det första steget i länken med följande kommandon, beroende på vilken version av du ansluter till HDInsight-klustret. När, följer du stegen i magiskt Spark-dokumentationen. Om du vill installera olika kärnor, måste du utföra steg3 i avsnittet Spark magiskt installationen instruktioner.

* Installera sparkmagic 0.2.3 för kluster v3.4 genom att köra`pip install sparkmagic==0.2.3`

* För kluster v3.5 och v3.6, installera sparkmagic 0.11.2 genom att köra`pip install sparkmagic==0.11.2`

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Konfigurera Spark Magiskt tal för att ansluta till HDInsight Spark-kluster

I det här avsnittet kan du konfigurera Spark Magiskt tal som du tidigare installerat för att ansluta till ett Apache Spark-kluster som du har redan skapat i Azure HDInsight.

1. Jupyter Konfigurationsinformationen lagras vanligtvis i arbetskatalogen för användare. Skriv följande kommandon för att hitta arbetskatalogen på alla operativsystem/plattform.

    Starta Python-gränssnittet. Skriv följande i ett kommandofönster:

        python

    Ange följande kommando för att ta reda på arbetskatalogen på Python-gränssnittet.

        import os
        print(os.path.expanduser('~'))

2. Navigera till arbetskatalogen och skapa en mapp med namnet **.sparkmagic** om den inte redan finns.
3. Skapa en fil med namnet i mappen **config.json** och Lägg till följande JSON-fragment inuti den.

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
          }
        }

4. Ersätt **{USERNAME}**, **{CLUSTERDNSNAME}**, och **{BASE64ENCODEDPASSWORD}** med lämpliga värden. Du kan använda ett antal verktyg i din favorit programmeringsspråket eller online för att generera ett base64-kodat lösenord för det faktiska lösenordet.

5. Konfigurera rätt inställningar för pulsslag i `config.json`. Du bör lägga till de här inställningarna på samma nivå som den `kernel_python_credentials` och `kernel_scala_credentials` kodavsnitt din lagts till tidigare. Ett exempel på hur och var du vill lägga till inställningar för pulsslag finns det [exempel config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

    * För `sparkmagic 0.2.3` (kluster v3.4) inkluderar:

            "should_heartbeat": true,
            "heartbeat_refresh_seconds": 5,
            "heartbeat_retry_seconds": 1

    * För `sparkmagic 0.11.2` (kluster v3.5 och v3.6) inkluderar:

            "heartbeat_refresh_seconds": 5,
            "livy_server_heartbeat_timeout_seconds": 60,
            "heartbeat_retry_seconds": 1

    >[!TIP]
    >Pulsslag skickas för att säkerställa att sessioner inte sprids. När en dator försätts i viloläge eller stängas av, sänds pulsslag, vilket resulterar i sessionen som rensas. För kluster v3.4 om du vill inaktivera den här funktionen kan du ange Livius config `livy.server.interactive.heartbeat.timeout` till `0` från Ambari UI. För kluster v3.5 om du inte anger ovanstående 3.5 konfigurationen tas sessionen inte bort.

6. Starta Jupyter. Använd följande kommando i Kommandotolken.

        jupyter notebook

7. Kontrollera att du kan ansluta till klustret med Jupyter-anteckningsboken och som du kan använda Spark Magiskt tal tillgängliga med kärnor. Utför följande steg.

    a. Skapa en ny anteckningsbok. I det högra hörnet, klickar du på **ny**. Du bör se standardkernel **Python2** och två nya kernlar som du installerar **PySpark** och **Spark**. Klicka på **PySpark**.

    ![Kernlar som är i Jupyter-anteckningsbok](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "kärnor i Jupyter-anteckningsbok")

    b. Kör följande kodavsnitt.

        %%sql
        SELECT * FROM hivesampletable LIMIT 5

    Om du kan hämta utdata, testas anslutningen till HDInsight-klustret.

    >[!TIP]
    >Om du vill uppdatera konfigurationen av bärbara datorer att ansluta till ett annat kluster måste du uppdatera config.json med den nya uppsättningen av värden som visas i steg3 ovan.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Varför ska jag installera Jupyter på min dator?
Det kan finnas flera skäl till varför du kanske vill installera Jupyter på datorn och ansluter sedan till ett Spark-kluster i HDInsight.

* Även om Jupyter-anteckningsböcker finns redan på Spark-kluster i Azure HDInsight, installera Jupyter på datorn ger dig möjlighet att skapa dina anteckningsböcker lokalt, testa programmet mot ett kluster som körs och sedan ladda upp den bärbara datorer i klustret. För att ladda upp de bärbara datorerna i klustret kan antingen ladda upp dem med Jupyter-anteckningsbok som körs eller klustret eller spara dem i mappen /HdiNotebooks i storage-konto som är associerade med klustret. Mer information om hur bärbara datorer lagras på klustret finns [där lagras Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Med de bärbara datorerna som är tillgängliga lokalt, kan du ansluta till olika Spark-kluster som är baserat på din programkrav.
* Du kan använda GitHub för att implementera ett system för källa och har versionskontroll för de bärbara datorerna. Du kan också ha en gemensam miljö där flera användare kan arbeta med samma anteckningsbok.
* Du kan arbeta med anteckningsböcker lokalt utan att behöva ett kluster även. Du behöver bara ett kluster för att testa dina anteckningsböcker mot, inte för att hantera dina anteckningsböcker eller en utvecklingsmiljö manuellt.
* Det kan vara enklare att konfigurera din egen lokala utvecklingsmiljö än att konfigurera Jupyter-installationen på klustret.  Du kan dra nytta av alla program som du har installerat lokalt utan att konfigurera ett eller flera fjärranslutna kluster.

> [!WARNING]
> Med Jupyter installerat på den lokala datorn, kan flera användare köra samma anteckningsbok på samma Spark-kluster på samma gång. I detta fall skapas flera Livius sessioner. Om du stöter på ett problem och vill felsöka det blir uppgiften att spåra vilka Livius session hör till som användare.
>
>

## <a name="seealso"></a>Se även
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Använda Spark i HDInsight för att bygga program för strömning i realtid](apache-spark-eventhub-streaming.md)
* [Webbplatslogganalys med Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att felsöka Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

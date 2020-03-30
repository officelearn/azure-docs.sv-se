---
title: Lär dig att använda en Apache Hadoop sandlåda, emulator - Azure HDInsight
description: 'Om du vill börja lära dig mer om hur du använder Apache Hadoop-ekosystemet kan du konfigurera en Hadoop-sandlåda från Hortonworks på en virtuell Azure-dator. '
keywords: hadoop emulator, hadoop sandlåda
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 47ee66393e3e1678576b12a70b767f35cb3bc635
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73044775"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Kom igång med en Apache Hadoop sandlåda, en emulator på en virtuell maskin

Lär dig hur du installerar Apache Hadoop sandlåda från Hortonworks på en virtuell maskin för att lära dig om Hadoop-ekosystemet. Sandlådan ger en lokal utvecklingsmiljö för att lära dig mer om Hadoop, Hadoop Distributed File System (HDFS) och jobbinlämning. När du är bekant med Hadoop kan du börja använda Hadoop på Azure genom att skapa ett HDInsight-kluster. Mer information om hur du kommer igång finns i [Kom igång med Hadoop på HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Krav

* [Oracle VirtualBox](https://www.virtualbox.org/). Ladda ner och installera det [härifrån](https://www.virtualbox.org/wiki/Downloads).

## <a name="download-and-install-the-virtual-machine"></a>Ladda ned och installera den virtuella datorn

1. Bläddra till [Cloudera-nedladdningarna](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

1. Klicka på **VIRTUALBOX** under **Välj installationstyp** för att hämta den senaste Hortonworks Sandbox på en virtuell dator. Logga in eller fyll i formuläret för produktintresse.

1. Klicka på knappen **HDP SANDBOX (SENASTE)** för att börja ladda ner.

Instruktioner om hur du konfigurerar sandlådan finns i [Guiden för distribution och installation av begränsat läge](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Om du vill hämta en äldre SANDLÅDA för HDP-versioner läser du länkarna under **Äldre versioner**.

## <a name="start-the-virtual-machine"></a>Starta den virtuella datorn

1. Öppna VirtualBox för Oracle VM.
1. Klicka på Importera **apparat**på **Arkiv-menyn** och ange sedan bild av Hortonworks Sandbox.
1. Markera Hortonworks Sandbox, klicka på **Start**och sedan **Normal Start**. När den virtuella datorn är klar med startprocessen visas inloggningsinstruktioner.

    ![virtualbox manager normal start](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. Öppna en webbläsare och navigera till webbadressen som visas (vanligtvis `http://127.0.0.1:8888`).

## <a name="set-sandbox-passwords"></a>Ange lösenord för sandbox

1. Välj **Visa avancerade alternativ**från steget Komma **igång** på sidan Hortonworks Sandbox . Använd informationen på den här sidan för att logga in i sandlådan med SSH. Använd det angivna namnet och lösenordet.

   > [!NOTE]
   > Om du inte har en SSH-klient installerad kan du använda den webbaserade SSH som tillhandahålls av den virtuella datorn på **http://localhost:4200/**.

    Första gången du ansluter med SSH uppmanas du att ändra lösenordet för rotkontot. Ange ett nytt lösenord som du använder när du loggar in med SSH.

2. När du har loggat in anger du följande kommando:

        ambari-admin-password-reset

    Ange ett lösenord för Ambari-administratörskontot när du uppmanas att göra det. Detta används när du öppnar Ambari Web UI.

## <a name="use-hive-commands"></a>Använda Hive-kommandon

1. Från en SSH-anslutning till sandlådan använder du följande kommando för att starta Hive-skalet:

        hive
2. När skalet har startat använder du följande för att visa tabellerna som medföljer sandlådan:

        show tables;
3. Använd följande för att hämta 10 rader från `sample_07` tabellen:

        select * from sample_07 limit 10;

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du använder Visual Studio med Hortonworks Sandbox](../hdinsight-hadoop-emulator-visual-studio.md)

* [Att lära sig linorna i Hortonworks Sandbox](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Hadoop handledning - Komma igång med HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

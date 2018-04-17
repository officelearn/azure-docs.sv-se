---
title: Använd Azure Toolkit för IntelliJ med Hortonworks Sandbox | Microsoft Docs
description: Lär dig hur du använder HDInsight-verktyg i Azure Toolkit för IntelliJ med Hortonworks Sandbox.
keywords: hadoop-verktygen hive-fråga, intellij, hortonworks sandbox, azure toolkit för intellij
services: HDInsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: b587cc9b-a41a-49ac-998f-b54d6c0bdfe0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: jgao
ms.openlocfilehash: c72fb17a53b9d373bbd141f76f01e6b029d1e8f7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Använda HDInsight Tools för IntelliJ med Hortonworks Sandbox

Lär dig hur du använder HDInsight-verktyg för IntelliJ att utveckla program för Apache Scala och testa programmen på [Hortonworks Sandbox](http://hortonworks.com/products/sandbox/) körs på datorn. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) är en integrerad utvecklingsmiljö (IDE) för Java för utveckling av programvara. När du utvecklar och testar dina program på Hortonworks Sandbox du flytta program till [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudiekursen behöver du följande:

- Hortonworks Data Platform (HDP) 2.4 på Hortonworks Sandbox körs på den lokala datorn. Om du vill konfigurera HDP finns [komma igång med Hadoop-ekosystemet med en Hadoop sandbox på en virtuell dator](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > HDInsight-verktyg för IntelliJ har testats med HDP 2.4. För att få HDP 2.4, expandera **Hortonworks Sandbox Arkiv** på den [Hortonworks Sandbox hämtar plats](http://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) version 1,8 eller senare](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Azure Toolkit för IntelliJ kräver JDK.

- [IntelliJ IDEA community edition](https://www.jetbrains.com/idea/download) med den [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) plugin-program och [Azure Toolkit för IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) plugin-programmet. HDInsight-verktyg för IntelliJ är tillgänglig som en del av Azure Toolkit för IntelliJ. 

Installera plugin-program:

  1. Öppna IntelliJ IDEA.
  2. På den **Välkommen** väljer **konfigurera**, och välj sedan **plugin-program**.
  3. I det nedre vänstra hörnet väljer **installera JetBrains plugin**.
  4. Använda sökfunktionen för att söka efter **Scala**, och välj sedan **installera**.
  5. Slutför installationen genom att markera **starta om IntelliJ IDEA**.
  6. Upprepa steg 4 och 5 för att installera **Azure Toolkit för IntelliJ**. Mer information finns i [installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-a-spark-scala-application"></a>Skapa ett Spark Scala-program

I det här avsnittet Skapa en Scala exempelprojektet med IntelliJ IDEA. I nästa avsnitt länka du IntelliJ IDEA till Hortonworks Sandbox (emulator) innan du skickar projektet.

1. Öppna IntelliJ IDEA på datorn. I den **nytt projekt** dialogrutan Fyll dessa steg:

   1. Välj **HDInsight** > **Spark i HDInsight (Scala)**.
   2. I den **Build verktyget** listan, Välj något av följande, baserat på ditt scenario:

    * **Maven**: för Scala skapa projekt guiden-stöd.
    * **Segregerade Barlasttankar**: för att hantera beroenden och skapa för Scala-projektet.

   ![Dialogrutan Nytt projekt](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Välj **Nästa**.
3. I nästa **nytt projekt** dialogrutan rutan, gör du följande:

    1. I den **projektnamn** ange ett projektnamn.
    2. I den **projektet plats** ange en projektplats.
    3. Bredvid den **projekt SDK** listrutan, Välj **ny**väljer **JDK**, och sedan ange mappen för Java JDK 1,7 eller senare. Välj **Java 1.8** för 2.x Spark-klustret. Välj **Java 1.7** för 1.x Spark-klustret. Standardsökvägen är C:\Program Files\Java\jdk1.8.x_xxx.
    4. I den **Spark version** listrutan Scala projektguiden integrerar rätt version för Spark SDK och Scala SDK. Om den Spark-kluster av versionen är äldre än 2.0 väljer **Väck 1.x**. Annars väljer **Spark2.x**. Det här exemplet använder Spark 1.6.2 (Scala 2.10.5). Kontrollera att du använder databasen markeras **Scala 2.10.x**. Använd inte databasen markeras Scala 2.11.x.
    
    ![Skapa IntelliJ Scala projektegenskaperna](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Välj **Slutför**.
5. Om den **projekt** vyn inte är redan öppen genom att trycka på **Alt + 1** att öppna den.
6. I **Projektutforskaren**, expandera projektet och välj sedan **src**.
7. Högerklicka på **src**, peka på **ny**, och välj sedan **Scala klassen**.
8. I den **namn** ange ett namn. I den **typ** väljer **objektet**. Välj **OK**.

    ![Dialogrutan Skapa ny Scala-klass](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. Klistra in följande kod i filen .scala:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. På den **skapa** väljer du **bygga projektet**. Se till att kompileringen slutförs.


## <a name="link-to-the-hortonworks-sandbox"></a>Länk till sandlådan Hortonworks

Innan du kan länka till en Hortonworks Sandbox (emulator), måste du ha ett befintligt IntelliJ program.

Länka till en emulator:

1. Öppna projektet i IntelliJ.
2. På den **visa** väljer du **verktyg Windows**, och välj sedan **Azure Explorer**.
3. Expandera **Azure**, högerklicka på **HDInsight**, och välj sedan **länka en Emulator**.
4. I den **länken en ny emulatorn** dialogrutan Ange lösenordet som du har angett för rotkontot i sandlådan Hortonworks. Ange värden som liknar de som används i följande skärmbild. Välj **OK**. 

   ![Länka en ny emulatorn dialogruta](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Om du vill konfigurera emulatorn, Välj **Ja**.

När emulatorn har anslutits visas emulatorn (Hortonworks Sandbox) på HDInsight-nod.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Skicka Spark Scala-programmet till Hortonworks Sandbox

När du har länkat IntelliJ IDEA till emulatorn, kan du skicka projektet.

Att skicka ett projekt till en emulator:

1. I **Projektutforskaren**, högerklicka på projektet och välj sedan **skicka Spark-program till HDInsight**.
2. Utför följande steg:

    1. I den **Spark-kluster (endast Linux)** listrutan väljer du din lokala Hortonworks Sandbox.
    2. I den **Main klassnamn** väljer eller anger klassnamnet för huvudsakliga. Den här kursen är namn **GroupByTest**.

3. Välj **skicka**. Jobbet skicka loggarna visas i fönstret Spark skicka verktyget.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder HDInsight-verktyg i Azure Toolkit för IntelliJ till att skapa Spark-program för ett HDInsight Spark Linux-kluster](../spark/apache-spark-intellij-tool-plugin.md).

- Se en video om HDInsight-verktyg för IntelliJ [införa HDInsight-verktyg för IntelliJ för Spark-utveckling](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Lär dig hur du [felsöka Spark-program på ett HDInsight-kluster med Azure Toolkit för IntelliJ via SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Lär dig hur du [använder HDInsight-verktyg i Azure Toolkit för IntelliJ till att felsöka Spark-program via fjärranslutning på ett HDInsight Spark Linux-kluster](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Lär dig hur du [använda HDInsight Tools i Azure Toolkit för Eclipse att skapa Spark-program](../spark/apache-spark-eclipse-tool-plugin.md).

- Se en video om HDInsight-verktyg för Eclipse [använda HDInsight Tools för Eclipse att skapa Spark-program](https://mix.office.com/watch/1rau2mopb6fha).

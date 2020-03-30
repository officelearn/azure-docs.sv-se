---
title: Använd Azure Toolkit för IntelliJ med Hortonworks Sandbox
description: Lär dig hur du använder HDInsight Tools i Azure Toolkit för IntelliJ med Hortonworks Sandbox.
keywords: hadoop verktyg, hive fråga, intellij, hortonworks sandlåda, azure verktygslåda för intellij
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/16/2018
ms.openlocfilehash: 65a15a8506b88e95e14af8c87bcbe33087301519
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647840"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Använd HDInsight Tools för IntelliJ med Hortonworks Sandbox

Lär dig hur du använder HDInsight Tools för IntelliJ för att utveckla Apache Scala-program och sedan testa programmen på [Hortonworks Sandbox](https://hortonworks.com/products/sandbox/) som körs på datorn. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) är en Java-integrerad utvecklingsmiljö (IDE) för utveckling av datorprogram. När du har utvecklat och testat dina program i Hortonworks Sandbox kan du flytta programmen till [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Krav

Innan du börjar den här artikeln måste du ha följande:

- Hortonworks Data Platform (HDP) 2.4 på Hortonworks Sandbox som körs på din lokala dator. Om du vill konfigurera HDP finns [i Kom igång i Apache Hadoop-ekosystemet med en Hadoop-sandlåda på en virtuell dator](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > HDInsight Tools för IntelliJ har endast testats med HDP 2.4. För att få HDP 2.4, expandera **Hortonworks Sandbox Archive** på [Hortonworks Sandbox nedladdningar webbplats](https://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) version 1.8 eller senare](https://aka.ms/azure-jdks). Azure Toolkit för IntelliJ kräver JDK.

- [IntelliJ IDEA community edition](https://www.jetbrains.com/idea/download) med [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) plug-in och [Azure Toolkit för IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) plug-in. HDInsight Tools för IntelliJ är tillgängligt som en del av Azure Toolkit för IntelliJ. 

Så här installerar du plugin-programmen:

  1. Öppna IntelliJ IDEA.
  2. På **välkomstsidan** väljer du **Konfigurera**och väljer sedan **Plugins**.
  3. I det nedre vänstra hörnet väljer du **Installera JetBrains plugin**.
  4. Använd sökfunktionen för att söka efter **Scala**och välj sedan **Installera**.
  5. Om du vill slutföra installationen väljer du **Starta om IntelliJ IDEA**.
  6. Upprepa steg 4 och 5 för att installera **Azure Toolkit för IntelliJ**. Mer information finns i [Installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Skapa ett Apache Spark Scala-program

I det här avsnittet skapar du ett exempel på Scala-projekt med intelliJ IDEA. I nästa avsnitt länkar du IntelliJ IDEA till Hortonworks Sandbox (emulator) innan du skickar in projektet.

1. Öppna IntelliJ IDEA på datorn. Gör så här i dialogrutan **Nytt projekt:**

   1. Välj **HDInsight** > **Spark på HDInsight (Scala)**.
   2. Välj något av följande i listan **Byggverktyg:**

      * **Maven**: För Scala projektskapande guiden stöd.
      * **SBT**: För hantering av beroenden och byggande för Scala-projektet.

   ![Intellij skapa nytt scala-projekt](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Välj **Nästa**.
3. Gör så här i nästa dialogrutan **Nytt projekt:**

   1. Ange ett projektnamn i rutan **Projektnamn.**
   2. Ange en projektplats i rutan **Projektplats.**
   3. Bredvid listrutan **Projekt SDK** väljer du **Ny**, väljer **JDK**och anger sedan mappen för Java JDK version 1.7 eller senare. Välj **Java 1.8** för Spark 2.x-klustret. Välj **Java 1.7** för Spark 1.x-klustret. Standardplatsen är C:\Program\Java\jdk1.8.x_xxx.
   4. I listrutan **Spark-version** integrerar guiden Skapa Scala-projekt rätt version för Spark SDK och Scala SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2.x**. I det här exemplet används Spark 1.6.2 (Scala 2.10.5). Kontrollera att du använder databasen som är märkt **Scala 2.10.x**. Använd inte databasen som är märkt Scala 2.11.x.
    
      ![Skapa projektegenskaper för IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Välj **Slutför**.
5. Om **projektvyn** inte redan är öppen trycker du på **Alt+1** för att öppna den.
6. Expandera projektet i **Project Explorer**och välj sedan **src**.
7. Högerklicka på **src**, peka på **Nytt**och välj sedan **Scala-klass**.
8. Ange ett namn i rutan **Namn**. Välj **Objekt**i rutan **Typ** . Välj **OK**.

    ![Dialogrutan Skapa ny scala-klass](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. Klistra in följande kod i SCALA-filen:

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

10. Välj **Bygg projekt**på **Build-menyn** . Kontrollera att kompileringen har slutförts.


## <a name="link-to-the-hortonworks-sandbox"></a>Länk till Hortonworks Sandbox

Innan du kan länka till en Hortonworks Sandbox (emulator) måste du ha ett befintligt IntelliJ-program.

Så här länkar du till en emulator:

1. Öppna projektet i IntelliJ.
2. Välj **Verktyg Windows**på **Visa-menyn** och välj sedan **Azure Explorer**.
3. Expandera **Azure**, högerklicka på **HDInsight**och välj sedan **Länka en emulator**.
4. I dialogrutan **Länka en ny emulator** anger du det lösenord som du har angett för rotkontot för Hortonworks-sandlådan. Ange sedan värden som liknar dem som används i följande skärmbild. Välj **OK**. 

   ![Dialogrutan Länka en ny emulator](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Om du vill konfigurera emulatorn väljer du **Ja**.

När emulatorn är ansluten visas emulatorn (Hortonworks Sandbox) på HDInsight-noden.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Skicka Spark Scala-programmet till Hortonworks Sandbox

När du har länkat IntelliJ IDEA till emulatorn kan du skicka in ditt projekt.

Så här skickar du ett projekt till en emulator:

1. Högerklicka på projektet i **Project Explorer**och välj sedan **Skicka Spark-programmet till HDInsight**.
2. Utför följande steg:

    1. I listrutan **Spark-kluster (endast Linux)** väljer du din lokala Hortonworks Sandbox.
    2. Markera eller ange huvudklassnamnet i rutan **Huvudklassnamn.** För den här artikeln är namnet **GroupByTest**.

3. Välj **Skicka**. Jobbinlämningsloggarna visas i fönstret Förinläsning av spark.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder HDInsight Tools i Azure Toolkit för IntelliJ för att skapa Apache Spark-program för ett HDInsight Spark Linux-kluster](../spark/apache-spark-intellij-tool-plugin.md).

- En video om HDInsight Tools för IntelliJ finns i [Introducera HDInsight Tools för IntelliJ för Utveckling av Apache Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Lär dig hur du [fjärrsöker Apache Spark-program i ett HDInsight-kluster med Azure Toolkit för IntelliJ via SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Lär dig hur du [använder HDInsight Tools i Azure Toolkit för IntelliJ för att fjärrsöka Apache Spark-program i ett HDInsight Spark Linux-kluster](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Lär dig hur du [använder HDInsight-verktyg i Azure Toolkit for Eclipse för att skapa Apache Spark-program](../spark/apache-spark-eclipse-tool-plugin.md).


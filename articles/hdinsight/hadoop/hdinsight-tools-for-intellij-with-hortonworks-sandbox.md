---
title: Använda Azure Toolkit for IntelliJ med begränsat läge för Hortonworks
description: Lär dig hur du använder HDInsight-verktyg i Azure Toolkit for IntelliJ med sandbox-Hortonworks.
keywords: Hadoop-verktyg, Hive-fråga, IntelliJ, Hortonworks sandbox, Azure Toolkit för IntelliJ
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/16/2018
ms.openlocfilehash: 65a15a8506b88e95e14af8c87bcbe33087301519
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75647840"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Använda HDInsight-verktyg för IntelliJ med begränsat läge för Hortonworks

Lär dig hur du använder HDInsight Tools för IntelliJ för att utveckla Apache Scala-program och sedan testa programmen på [Hortonworks sandbox](https://hortonworks.com/products/sandbox/) som körs på datorn. 

[INTELLIJ idén](https://www.jetbrains.com/idea/) är en Java Integrated Development Environment (IDE) för att utveckla dator program vara. När du utvecklar och testar dina program på Hortonworks sandbox kan du flytta programmen till [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Krav

Innan du börjar den här artikeln måste du ha följande objekt:

- Hortonworks Data Platform (HDP) 2,4 på Hortonworks sandbox körs på den lokala datorn. Information om hur du konfigurerar HDP finns i [komma igång i det Apache Hadoop eko systemet med ett Hadoop-sandbox på en virtuell dator](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > HDInsight Tools för IntelliJ har endast testats med HDP 2,4. Om du vill hämta HDP 2,4 expanderar du **Hortonworks sandbox-arkivet** på [webbplatsen Hortonworks sandbox downloads](https://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) version 1,8 eller senare](https://aka.ms/azure-jdks). Azure Toolkit for IntelliJ kräver JDK.

- [INTELLIJ idé Community Edition](https://www.jetbrains.com/idea/download) med plugin-programmet [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) och [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) -plugin-programmet. HDInsight Tools för IntelliJ är tillgängligt som en del av Azure Toolkit for IntelliJ. 

Så här installerar du plugin-programmen:

  1. Öppna IntelliJ IDEA.
  2. På sidan **Välkommen** väljer du **Konfigurera**och väljer sedan **plugin**-program.
  3. I det nedre vänstra hörnet väljer du **Installera JetBrains-plugin-programmet**.
  4. Använd Sök funktionen för att söka efter **Scala**och välj sedan **Installera**.
  5. Slutför installationen genom att välja **Starta INTELLIJ idé**.
  6. Upprepa steg 4 och 5 för att installera **Azure Toolkit for IntelliJ**. Mer information finns i [installera Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Skapa ett Apache Spark Scala-program

I det här avsnittet skapar du ett exempel på Scala-projekt med IntelliJ-idén. I nästa avsnitt länkar du IntelliJ idé till Hortonworks Sandbox (emulator) innan du skickar projektet.

1. Öppna IntelliJ-idén på din dator. I dialog rutan **nytt projekt** slutför du följande steg:

   1. Välj **HDInsight** > **Spark på HDInsight (Scala)**.
   2. I listan **build-verktyg** väljer du något av följande, baserat på ditt scenario:

      * **Maven**: stöd för guiden skapa projekt i Scala.
      * **SBT**: för att hantera beroenden och skapa för Scala-projektet.

   ![IntelliJ skapa nytt Scala-projekt](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Välj **Nästa**.
3. Utför följande steg i dialog rutan nästa **nya projekt** :

   1. Ange ett projekt namn i rutan **projekt namn** .
   2. I rutan **projekt plats** anger du en projekt plats.
   3. Bredvid List rutan **Project SDK** väljer du **ny**, Välj **JDK**och ange sedan mappen för Java JDK version 1,7 eller senare. Välj **Java 1,8** för Spark 2. x-klustret. Välj **Java 1,7** för Spark 1. x-klustret. Standard platsen är C:\Program Files\Java\jdk1.8. x_xxx.
   4. I list rutan **Spark-version** integrerar guiden skapa Scala-projekt rätt version för Spark SDK och Scala SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2.x**. I det här exemplet används Spark-1.6.2 (Scala 2.10.5). Se till att du använder databasen som marker ATS som **Scala 2.10. x**. Använd inte databasen som marker ATS som Scala 2.11. x.
    
      ![Skapa IntelliJ Scala-projekt egenskaper](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Välj **Slutför**.
5. Om **projektvyn** inte redan är öppen trycker du på **Alt + 1** för att öppna den.
6. Expandera projektet i **projekt Utforskaren**och välj **src**.
7. Högerklicka på **src**, peka på **ny**och välj sedan Scala- **klass**.
8. Ange ett namn i rutan **Namn**. I rutan **typ** väljer du **objekt**. Välj **OK**.

    ![Dialog rutan skapa ny Scala-klass](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. I Scala-filen klistrar du in följande kod:

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

10. På menyn **build** väljer du **bygg projekt**. Se till att kompileringen har slutförts.


## <a name="link-to-the-hortonworks-sandbox"></a>Länk till sandbox-Hortonworks

Innan du kan länka till en Hortonworks Sandbox (emulator) måste du ha ett befintligt IntelliJ-program.

Så här länkar du till en emulator:

1. Öppna projektet i IntelliJ.
2. I menyn **Visa** väljer du **verktyg Windows**och sedan **Azure Explorer**.
3. Expandera **Azure**, högerklicka på **HDInsight**och välj **Länka en emulator**.
4. I dialog rutan **Länka en ny emulator** anger du det lösen ord som du har angett för rot kontot för Hortonworks-sandbox. Ange sedan värden som liknar dem som används i följande skärm bild. Välj **OK**. 

   ![Dialog rutan länka en ny emulator](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Välj **Ja**om du vill konfigurera emulatorn.

När emulatorn har anslutits visas emulatorn (Hortonworks sandbox) på HDInsight-noden.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Skicka Spark Scala-programmet till sand boxen Hortonworks

När du har länkat IntelliJ-idén till emulatorn kan du skicka projektet.

Så här skickar du ett projekt till en emulator:

1. I **Project Explorer**högerklickar du på projektet och väljer **Skicka Spark-program till HDInsight**.
2. Utför följande steg:

    1. Välj din lokala Hortonworks-sandbox i list rutan **Spark-kluster (endast Linux)** .
    2. I rutan **namn på huvud klass** väljer eller anger du huvud klassens namn. I den här artikeln är namnet **GroupByTest**.

3. Välj **Skicka**. Jobb sändnings loggarna visas i fönstret Spark-överförings verktyg.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder HDInsight-verktyg i Azure Toolkit for IntelliJ för att skapa Apache Spark-program för ett HDInsight Spark Linux-kluster](../spark/apache-spark-intellij-tool-plugin.md).

- En video om HDInsight-verktyg för IntelliJ finns i [Introduktion till HDInsight-verktyg för IntelliJ för Apache Spark utveckling](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Lär dig hur du [fjärrfelsökar Apache Spark program i ett HDInsight-kluster med Azure Toolkit for IntelliJ via SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Lär dig hur du [använder HDInsight-verktyg i Azure Toolkit for IntelliJ för att felsöka Apache Spark program via fjärr anslutning på ett HDInsight Spark Linux-kluster](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Lär dig hur du [använder HDInsight-verktyg i Azure Toolkit for Eclipse för att skapa Apache Spark-program](../spark/apache-spark-eclipse-tool-plugin.md).


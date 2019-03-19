---
title: Använd Azure Toolkit för IntelliJ med begränsat Hortonworks-läge
description: Lär dig hur du använder HDInsight-verktygen i Azure Toolkit för IntelliJ med begränsat Hortonworks-läge.
keywords: hadoop-verktyg, hive-fråga, intellij, begränsat hortonworks-läge, azure toolkit för intellij
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 98f22f531ca15bf88cd7d0a9add2851651e2eec7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118331"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Använda HDInsight Tools för IntelliJ med begränsat Hortonworks-läge

Lär dig hur du använder HDInsight Tools för IntelliJ för att utveckla Apache Scala-appar och testa programmen på [Hortonworks Sandbox](https://hortonworks.com/products/sandbox/) körs på datorn. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) är en integrerad utvecklingsmiljö (IDE) för Java för att utveckla programvara. När du utvecklar och testar dina program på begränsat Hortonworks-läge, du kan flytta program till [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudiekursen behöver du följande:

- Hortonworks Data Platform (HDP) 2.4 på begränsat Hortonworks-läge som körs på den lokala datorn. Om du vill konfigurera HDP Se [Kom igång med Apache Hadoop-ekosystemet med ett begränsat Hadoop-läge på en virtuell dator](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > HDInsight Tools för IntelliJ har testats endast med HDP 2.4. För att få HDP 2.4 kan expandera **Hortonworks Sandbox Arkiv** på den [begränsat Hortonworks-läge hämtar plats](https://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) version 1.8 eller senare](https://aka.ms/azure-jdks). Azure Toolkit för IntelliJ kräver JDK.

- [IntelliJ IDEA community edition](https://www.jetbrains.com/idea/download) med den [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) plugin-programmet och [Azure Toolkit för IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) plugin-programmet. HDInsight Tools för IntelliJ är tillgänglig som en del av Azure Toolkit för IntelliJ. 

Installera plugin-program:

  1. Öppna IntelliJ IDEA.
  2. På den **Välkommen** väljer **konfigurera**, och välj sedan **plugin-program**.
  3. I det nedre vänstra hörnet väljer **installera JetBrains plugin-programmet**.
  4. Använda sökfunktionen för att söka efter **Scala**, och välj sedan **installera**.
  5. För att slutföra installationen, Välj **starta om IntelliJ IDEA**.
  6. Upprepa steg 4 och 5 för att installera **Azure Toolkit för IntelliJ**. Mer information finns i [installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Skapa ett Apache Spark Scala-program

I det här avsnittet skapar du en Scala exempelprojektet med hjälp av IntelliJ IDEA. I nästa avsnitt länkar du IntelliJ IDEA till Hortonworks Sandbox (emulator) innan du skickar in projektet.

1. Öppna IntelliJ IDEA på datorn. I den **nytt projekt** dialogrutan Fyll de här stegen:

   1. Välj **HDInsight** > **Spark i HDInsight (Scala)**.
   2. I den **byggverktyg** listan, Välj något av följande, baserat på ditt scenario:

      * **Maven**: För Scala skapa projekt guiden support.
      * **SBT**: För att hantera beroenden och utveckling för Scala-projektet.

   ![Dialogrutan Nytt projekt](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Välj **Nästa**.
3. I nästa **nytt projekt** dialogrutan rutan, gör du följande:

   1. I den **projektnamn** , ange ett projektnamn.
   2. I den **projektet plats** anger en projektplats.
   3. Bredvid den **projekt SDK** listrutan, väljer **New**väljer **JDK**, och sedan anger mappen för Java JDK version 1.7 eller senare. Välj **Java 1.8** för 2.x Spark-klustret. Välj **Java 1.7** för 1.x Spark-klustret. Standardplatsen är c:\Program\Microsoft Files\Java\jdk1.8.x_xxx.
   4. I den **Spark-version** listrutan, Scala projektguiden är rätt version för Spark-SDK och Scala SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2.x**. Det här exemplet använder Spark 1.6.2 (Scala 2.10.5). Kontrollera att du använder databasen som markerats **Scala 2.10.x**. Använd inte den lagringsplats som markerats Scala 2.11.x.
    
      ![Skapa IntelliJ Scala Projektegenskaper](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Välj **Slutför**.
5. Om den **projekt** vyn inte är öppen, tryck på **Alt + 1** att öppna den.
6. I **Projektutforskaren**, expandera projektet och välj sedan **src**.
7. Högerklicka på **src**, peka på **New**, och välj sedan **Scala klass**.
8. I den **namn** anger du ett namn. I den **typ** väljer **objektet**. Välj **OK**.

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

10. På den **skapa** menyn och välj **Build projekt**. Se till att kompileringen har slutförts.


## <a name="link-to-the-hortonworks-sandbox"></a>Länk till begränsat Hortonworks-läge

Innan du kan länka till ett begränsat Hortonworks-läge (emulator), måste du ha ett befintligt IntelliJ-program.

Länka till en emulator:

1. Öppna projektet i IntelliJ.
2. På den **visa** menyn och välj **verktyg Windows**, och välj sedan **Azure Explorer**.
3. Expandera **Azure**, högerklicka på **HDInsight**, och välj sedan **länka en Emulator**.
4. I den **Link A nya emulatorn** dialogrutan anger du det lösenord som du har angett för rotkontot av Hortonworks sandbox-miljön. Ange sedan värden som liknar de som används i följande skärmbild. Välj **OK**. 

   ![Länka en dialogruta för nya emulatorn](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. För att konfigurera emulatorn, Välj **Ja**.

När emulatorn anslutits visas emulatorn (Hortonworks Sandbox) på HDInsight-nod.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Skicka Spark Scala-programmet till begränsat Hortonworks-läge

När du har länkat IntelliJ IDEA till emulatorn kan du skicka in ditt projekt.

Att skicka ett projekt till en emulator:

1. I **Projektutforskaren**, högerklicka på projektet och välj sedan **skicka Spark-program till HDInsight**.
2. Utför följande steg:

    1. I den **Spark-kluster (endast Linux)** listrutan väljer du din lokala begränsat Hortonworks-läge.
    2. I den **Main klassnamn** markerar eller ange namnet main-klass. Den här självstudien namnet är **GroupByTest**.

3. Välj **Skicka**. Jobbet skicka loggarna visas i fönstret Spark bidrag verktyget.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder HDInsight-verktygen i Azure Toolkit för IntelliJ för att skapa Apache Spark-program för ett HDInsight Spark Linux-kluster](../spark/apache-spark-intellij-tool-plugin.md).

- Se en video om HDInsight Tools för IntelliJ [introducera HDInsight Tools för IntelliJ för Apache Spark-utveckling](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Lär dig hur du [Fjärrfelsöka Apache Spark-program på ett HDInsight-kluster med Azure Toolkit för IntelliJ genom SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Lär dig hur du [använda HDInsight-verktygen i Azure Toolkit för IntelliJ till att felsöka Apache Spark-program via fjärranslutning på ett HDInsight Spark Linux-kluster](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Lär dig hur du [Använd HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Apache Spark-program](../spark/apache-spark-eclipse-tool-plugin.md).

- Se en video om HDInsight Tools för Eclipse [använda HDInsight Tools för Eclipse för att skapa Spark-program](https://mix.office.com/watch/1rau2mopb6fha).

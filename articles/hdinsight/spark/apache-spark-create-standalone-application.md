---
title: "Skapa Scala app att köras på Spark - kluster i Azure HDInsight | Microsoft Docs"
description: "Skapa ett Spark-program som skrivits i Scala med Apache Maven som build-system och en befintlig Maven archetype för Scala som tillhandahålls av IntelliJ IDEA."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: nitinme
ms.openlocfilehash: bf1b127c4a8e05517d7bd877b463a04bcc0990a0
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="create-a-scala-maven-application-to-run-on-apache-spark-cluster-on-hdinsight"></a>Skapa en Scala Maven-program körs i Apache Spark-kluster i HDInsight

Lär dig hur du skapar ett Spark-program som skrivits i Scala med IntelliJ IDEA Maven. Artikeln använder Apache Maven som build-system och börjar med en befintlig Maven archetype för Scala som tillhandahålls av IntelliJ IDEA.  Skapa en koppling i Scala i IntelliJ IDEA omfattar följande steg:

* Använd Maven som build-system.
* Uppdatera projektet Object Model (POM)-filen för att lösa Spark modulen beroenden.
* Skriva ditt program i Scala.
* Generera en jar-fil som kan skickas till HDInsight Spark-kluster.
* Kör programmet på Spark-kluster med Livy.

> [!NOTE]
> HDInsight tillhandahåller även en IntelliJ IDEA plugin-verktyget för att underlätta processen med att skapa och skicka ett HDInsight Spark-kluster på Linux-program. Mer information finns i [använda HDInsight Tools-Plugin för IntelliJ IDEA till att skapa och skicka Spark-program](apache-spark-intellij-tool-plugin.md).
> 
> 

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development kit. Du kan installera den från [här](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* En Java IDE. Den här artikeln använder IntelliJ IDEA 15.0.1. Du kan installera den från [här](https://www.jetbrains.com/idea/download/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installera Scala-plugin för IntelliJ IDEA
Om installationen för IntelliJ IDEA tillfrågades inte inte för att aktivera Scala-plugin-programmet, starta IntelliJ IDEA och gå igenom följande steg för att installera plugin-programmet:

1. Starta IntelliJ IDEA och klicka på välkomstskärmen **konfigurera** och klicka sedan på **plugin-program**.
   
    ![Aktivera scala plugin-program](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
2. Klicka på nästa skärm **installera JetBrains plugin** från det nedre vänstra hörnet. I den **Bläddra JetBrains plugin-program** dialogrutan som öppnas, söka efter Scala och klicka sedan på **installera**.
   
    ![Installera scala plugin-programmet](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
3. När plugin-programmet installeras, klickar du på den **starta om IntelliJ IDEA knappen** att starta om IDE.

## <a name="create-a-standalone-scala-project"></a>Skapa ett fristående Scala-projekt
1. Öppna IntelliJ IDEA och skapa ett nytt projekt. Följande alternativ i dialogrutan Nytt projekt och klicka sedan på **nästa**.
   
    ![Skapa Maven-projekt](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Välj **Maven** som projekttypen.
   * Ange en **projektet SDK**. Klicka på nytt och navigera till installationskatalogen Java vanligtvis `C:\Program Files\Java\jdk1.8.0_66`.
   * Välj den **skapa från archetype** alternativet.
   * Välj i listan över archetypes **org.scala tools.archetypes:scala-archetype-enkel**. Detta skapar rätt katalogstrukturen och hämta nödvändig beroenden för att skriva Scala program.
2. Ange relevanta värden för **GroupId**, **artefakt-ID**, och **Version**. Klicka på **Nästa**.
3. I nästa dialogrutan där du anger Maven arbetskatalog och andra inställningar, acceptera standardinställningarna och klickar på **nästa**.
4. I den sista dialogrutan, ange ett namn och en plats och klicka sedan på **Slutför**.
5. Ta bort den **MySpec.Scala** filen på **src\test\scala\com\microsoft\spark\example**. Du behöver inte detta för programmet.
6. Om det behövs, Byt namn på standard käll- och test. Gå till i den vänstra rutan i IntelliJ IDEA **src\main\scala\com.microsoft.spark.example**. Högerklicka på **App.scala**, klickar du på **Refactor**, klicka på Byt namn på filen i dialogrutan, ange det nya namnet för programmet och klicka sedan på **Refactor**.
   
    ![Byta namn på filer](./media/apache-spark-create-standalone-application/rename-scala-files.png)  
7. I efterföljande steg uppdateras pom.xml om du vill definiera beroenden för Spark Scala-programmet. Dessa beroenden hämtas och lösas automatiskt, måste du konfigurera Maven därefter.
   
    ![Konfigurera Maven vid automatisk hämtning](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. Från den **filen** -menyn klickar du på **inställningar**.
   2. I den **inställningar** dialogrutan går du till **bygga, körning och distribution** > **Build Tools** > **Maven**  >  **Importera**.
   3. Välj alternativet för att **importera Maven-projekt automatiskt**.
   4. Klicka på **tillämpa**, och klicka sedan på **OK**.
8. Uppdatera källfilen Scala om du vill inkludera programkoden. Öppna och Ersätt den befintliga exempelkoden med följande kod och spara ändringarna. Den här koden läser data från HVAC.csv (finns i alla HDInsight Spark-kluster), hämtar de rader som bara innehåller en siffra i sjätte kolumnen och skriver utdata till **/HVACOut** under standardbehållare för lagring för det kluster.
   
        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
9. Uppdatera pom.xml.
   
   1. Inom `<project>\<properties>` Lägg till följande:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. Inom `<project>\<dependencies>` Lägg till följande:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Spara ändringar i pom.xml.
10. Skapa .jar-fil. IntelliJ IDEA gör det möjligt för skapa JAR som ett resultat av ett projekt. Utför följande steg.
    
    1. Från den **filen** -menyn klickar du på **projektstruktur**.
    2. I den **projektstruktur** dialogrutan klickar du på **artefakter** och sedan klicka på plustecknet. Klicka på dialogrutan popup- **JAR**, och klicka sedan på **från moduler med beroenden**.
       
        ![Skapa JAR](./media/apache-spark-create-standalone-application/create-jar-1.png)
    3. I den **skapa JAR från moduler** dialogrutan klickar du på ellipsknappen (![tre punkter](./media/apache-spark-create-standalone-application/ellipsis.png) ) mot den **Main klassen**.
    4. I den **Välj Main klassen** dialogrutan, Välj den klass som visas som standard och klicka sedan **OK**.
       
        ![Skapa JAR](./media/apache-spark-create-standalone-application/create-jar-2.png)
    5. I den **skapa JAR från moduler** dialogrutan rutan, kontrollera att alternativet att **extrahera till målet JAR** är markerad och klicka sedan på **OK**. Detta skapar en enda JAR med alla beroenden.
       
        ![Skapa JAR](./media/apache-spark-create-standalone-application/create-jar-3.png)
    6. Fliken layout utdata visar alla burkar som ingår som en del av Maven-projekt. Du kan välja och ta bort de där programmet Scala har ingen direkt beroende. För program som skapar vi här, kan du ta bort alla utom det senaste (**SparkSimpleApp kompilera utdata**). Välj burkar att ta bort och klicka sedan på den **ta bort** ikon.
       
        ![Skapa JAR](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Kontrollera att **bygger på Kontrollera** är markerad, vilket säkerställer att jar skapas varje gång projektet skapats eller uppdaterats. Klicka på **gäller** och sedan **OK**.
    7. På menyraden klickar du på **skapa**, och klicka sedan på **Se projektet**. Du kan också klicka på **skapa artefakter** att skapa jar. Utdata jar skapas under **\out\artifacts**.
       
        ![Skapa JAR](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Köra programmet på Spark-kluster
Om du vill köra programmet på klustret, måste du göra följande:

* **Kopiera jar för programmet till Azure storage-blob** associeras med klustret. Du kan använda [ **AzCopy**](../../storage/common/storage-use-azcopy.md), ett kommandoradsverktyg, gör. Det finns en mängd andra klienter samt som du kan använda för att överföra data. Du kan hitta mer om dem i [överföra data för Hadoop-jobb i HDInsight](../hdinsight-upload-data.md).
* **Använda Livius för att skicka ett program-jobb via fjärranslutning** till Spark-kluster. Spark-kluster i HDInsight innehåller Livius som exponerar REST-slutpunkter för att skicka Spark jobb via fjärranslutning. Mer information finns i [skicka Spark jobb via fjärranslutning med Livy med Spark-kluster i HDInsight](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Nästa steg

Du lärt dig hur du skapar ett Spark scala-program i den här artikeln. Gå vidare till nästa artikeln information om hur du kör det här programmet på ett HDInsight Spark-kluster med Livy.

> [!div class="nextstepaction"]
>[Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)


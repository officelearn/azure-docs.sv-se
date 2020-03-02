---
title: 'Självstudie: Scala maven-app för Spark & IntelliJ – Azure HDInsight'
description: Självstudie – Skapa ett Spark-program skrivet i Scala med Apache maven som build-system och en befintlig maven archetype för Scala som tillhandahålls av IntelliJ-idén.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/28/2020
ms.openlocfilehash: aa23b61967b27fefba863255721f4a0709ec02d5
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2020
ms.locfileid: "78204586"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Självstudie: Skapa ett Scala Maven-program för Apache Spark i HDInsight med hjälp av IntelliJ

I den här självstudien lär du dig att skapa ett [Apache Spark](./apache-spark-overview.md)-program som skrivits i [Scala](https://www.scala-lang.org/) med hjälp av [Apache Maven](https://maven.apache.org/) med IntelliJ IDEA. Artikeln använder Apache Maven som build-system och startar med en befintlig Maven-arketyp för Scala som tillhandahålls av IntelliJ IDEA.  Att skapa ett Scala-program i IntelliJ IDEA innefattar följande steg:

* Använd Maven som build-system.
* Uppdatera POM-filen (Project Object Model) för att hantera Spark-modulens beroenden.
* Skriv ditt program i Scala.
* Generera en jar-fil som kan skickas till HDInsight Spark-kluster.
* Kör programmet på ett Spark-kluster med Livy.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Installera plugin-programmet Scala för IntelliJ IDEA
> * Använda IntelliJ till att utveckla ett Scala Maven-program
> * Skapa ett fristående Scala-projekt

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Oracle Java Development Kit](https://www.azul.com/downloads/azure-only/zulu/).  Den här kursen använder Java version 8.0.202.

* En Java IDE. I den här artikeln används [INTELLIJ idé community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Se [Installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installera plugin-programmet Scala för IntelliJ IDEA

Installera Scala-plugin-programmet via följande steg:

1. Öppna IntelliJ IDEA.

2. På välkomstskärmen går du till **Konfigurera** > **Plugin-program** för att öppna fönstret **Plugin-program**.

    ![IntelliJ idé aktivera Scala-plugin-programmet](./media/apache-spark-create-standalone-application/enable-scala-plugin1.png)

3. Välj **Installera** för det Scala-plugin-program som visas i det nya fönstret.  

    ![IntelliJ idé installera Scala-plugin](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. Du måste starta om IDE när plugin-programmet har installerats.

## <a name="use-intellij-to-create-application"></a>Använda IntelliJ till att skapa program

1. Starta IntelliJ IDEA och välj **Skapa nytt projekt** för att öppna fönstret **Nytt projekt**.

2. Välj **Apache Spark/HDInsight** i det vänstra fönstret.

3. Välj **Spark-projekt (Scala)** i huvudfönstret.

4. I list rutan **build-verktyg** väljer du något av följande värden:
      * **Maven** för guidestöd när du skapar Scala-projekt.
      * **SBT** för att hantera beroenden när du skapar Scala-projektet.

   ![IntelliJ dialog rutan nytt projekt](./media/apache-spark-create-standalone-application/intellij-project-apache-spark.png)

5. Välj **Nästa**.

6. I fönstret **Nytt projekt** anger du följande information:  

  	|  Egenskap   | Beskrivning   |  
  	| ----- | ----- |  
  	|Projektnamn| Ange ett namn.|  
  	|Projektplats| Ange önskad plats för att spara projektet.|
  	|Projekt-SDK| Det här är tomt första gången du använder IDEA.  Välj **Nytt...**  och navigera till din JDK.|
  	|Spark-version|Skapandeguiden integrerar rätt version för Spark SDK och Scala SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2.x**. I det här exemplet används **Spark 2.3.0 (Scala 2.11.8)** .|

    ![IntelliJ idé att välja Spark SDK](./media/apache-spark-create-standalone-application/hdi-scala-new-project.png)

7. Välj **Slutför**.

## <a name="create-a-standalone-scala-project"></a>Skapa ett fristående Scala-projekt

1. Starta IntelliJ IDEA och välj **Skapa nytt projekt** för att öppna fönstret **Nytt projekt**.

2. Välj **Maven** i den vänstra rutan.

3. Ange en **Projekt-SDK**. Om det är tomt väljer du **Nytt...** och går till installationskatalogen för Java.

4. Markera kryssrutan **Create from archetype** (Skapa från arketyp).  

5. I listan med arketyper väljer du **org.scala tools.archetypes:scala-archetype-simple**. Den här arketypen skapar rätt katalogstruktur och laddar ned de beroenden som krävs för att skriva Scala-program.

    ![IntelliJ idé skapa Maven-projekt](./media/apache-spark-create-standalone-application/intellij-project-create-maven.png)

6. Välj **Nästa**.

7. Expandera **artefakt koordinater**. Ange relevanta **värden för** **ArtifactId**. **Namn**och **plats** fylls i automatiskt. I den här självstudien används följande värden:

    - **GroupId:** com.microsoft.spark.example
    - **ArtifactId:** SparkSimpleApp

    ![IntelliJ idé skapa Maven-projekt](./media/apache-spark-create-standalone-application/intellij-artifact-coordinates.png)

8. Välj **Nästa**.

9. Kontrollera inställningarna och välj sedan **Nästa**.

10. Kontrollera projektets namn och plats och välj sedan **Slutför**.  Projektet kan ta några minuter att importeras.

11. När projektet har importerats går du i det vänstra fönstret till **SparkSimpleApp** > **src** > **test** > **scala** > **com** > **microsoft** > **spark** > **example**.  Högerklicka på **specifikation**och välj sedan **ta bort...** . Du behöver inte den här filen för programmet.  Välj **OK** i dialogrutan.
  
12. I efterföljande steg uppdaterar du **pom.xml** för att definiera beroenden för Spark Scala-programmet. För att dessa beroenden ska kunna laddas ner och hanteras automatiskt, måste du konfigurera Maven därefter.

13. Från **Arkiv-menyn** väljer du **Inställningar** för att öppna fönstret **Inställningar**.

14. I fönstret **Inställningar** går du till **Build, Execution, Deployment (Skapa, köra och distribuera)**  > **Build Tools** > **Maven** > **Import**.

15. Markera kryssrutan **Import Maven projects automatically** (Importera Maven-projekt automatiskt).

16. Tryck på **Tillämpa** och välj sedan **OK**.  Sedan kommer du tillbaka till projekt fönstret.

    ![Konfigurera Maven för automatisk nedladdning](./media/apache-spark-create-standalone-application/configure-maven-download.png)

17. I den vänstra rutan går du till **src** > **main** > **scala** > **com.microsoft.spark.example** och dubbelklickar på **App** för att öppna App.scala.

18. Ersätt den befintliga exempelkoden med följande kod och spara ändringarna. Den här koden läser data från HVAC.csv (finns i alla HDInsight Spark-kluster), hämtar de rader som bara innehåller en siffra i sjätte kolumnen och skriver utdatan till **/HVACOut** under standardlagringens container för klustret.

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
19. I den vänstra rutan dubbelklickar du på **pom.xml**.  

20. I `<project>\<properties>` lägger du till följande segment:

          <scala.version>2.11.8</scala.version>
          <scala.compat.version>2.11.8</scala.compat.version>
          <scala.binary.version>2.11</scala.binary.version>

21. I `<project>\<dependencies>` lägger du till följande segment:

           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>2.3.0</version>
           </dependency>

    Spara ändringarna i pom.xml.

22. Skapa .jar-filen. Med IntelliJ IDEA går det att skapa JAR-filen som en artefakt av ett projekt. Utför följande steg.

    1. På **Arkiv-menyn** väljer du **Projektstruktur...** .

    2. I fönstret **Projektstruktur** går du till **Artefakter** > **plustecknet +**  > **JAR** > **From modules with dependencies...** (Från moduler med beroenden...).

        ![IntelliJ idé projekt struktur Lägg till jar](./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png)

    3. I fönstret **Skapa JAR från moduler** väljer du mappikonen i textrutan **Main Class** (Main-klass).

    4. I fönstret **Select Main Class** (Välj Main-klass) väljer du den klass som visas som standard och sedan **OK**.

        ![IntelliJ idé projekt struktur Välj klass](./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png)

    5. I fönstret **Create JAR from Modules** (Skapa JAR-fil från moduler) kontrollerar du att alternativet **Extract to the target JAR** (Extrahera till mål-JAR) är markerat. Välj sedan **OK**.  Den här inställningen skapar en enda JAR-fil med alla beroenden.

        ![IntelliJ idé projekt struktur jar från modul](./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png)

    6. Fliken **Output Layout** (Utdatalayout) visar alla jar-filer som ingår i Maven-projektet. Du kan markera och ta bort sådana som Scala-programmet inte har något direkt beroende till. När du skapar ett program kan du ta bort alla utom det sista (**SparkSimpleApp compile output**). Välj de jar-filer som ska tas bort och välj sedan minustecknet **-** .

        ![Ta bort utdata för IntelliJ idé projekt struktur](./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png)

        Kontrollera att kryssrutan **Include in project build** (Inkludera i projektversionen) är markerad, vilket säkerställer att jar-filen skapas varje gång projektet skapas eller uppdateras. Välj **Applicera** och sedan **OK**.

    7. Skapa jar-filen genom att gå till **Skapa** > **Build Artifacts (Skapa artefakter)**  > **Skapa**. Projektet kompileras inom cirka 30 sekunder.  Utdatans jar-fil skapas under **\out\artifacts**.

        ![IntelliJ idé projekt artefakt utdata](./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Köra programmet på Apache Spark-klustret

Om du vill köra programmet på klustret, kan du använda följande metoder:

* **Kopiera programmet jar till den Azure Storage BLOB** som är associerad med klustret. Du kan använda kommandoradsverktyget [**AzCopy**](../../storage/common/storage-use-azcopy.md) till att göra detta. Det finns även andra klienter som du kan använda för att ladda upp data. Det finns mer information om dem i [Ladda upp data för Apache Hadoop-jobb i HDInsight](../hdinsight-upload-data.md).

* **Använd Apache Livy till att skicka ett programjobb via fjärranslutning** till Spark-klustret. Spark-kluster i HDInsight innehåller Livy som gör REST-slutpunkter tillgängliga, så att man kan skicka Spark-jobb via en fjärranslutning. Mer information finns i [Skicka Apache Spark-jobb via fjärranslutning med hjälp av Apache Livy med Spark-kluster i HDInsight](apache-spark-livy-rest-interface.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet, tar du bort det kluster som du skapade med följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. I rutan **Sök** längst upp skriver du **HDInsight**.

1. Välj **HDInsight-kluster** under **Tjänster**.

1. I listan med HDInsight-kluster som visas väljer du **...** bredvid det kluster som du skapade för den här självstudien.

1. Välj **Ta bort**. Välj **Ja**.

![HDInsight Azure Portal ta bort kluster](./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png "Ta bort HDInsight-kluster")

## <a name="next-step"></a>Nästa steg

I den här artikeln har du lärt dig att skapa ett Apache Spark Scala-program. Gå vidare till nästa artikel om du vill lära dig att köra programmet på ett HDInsight Spark-kluster med Livy.

> [!div class="nextstepaction"]
>[Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](./apache-spark-livy-rest-interface.md)

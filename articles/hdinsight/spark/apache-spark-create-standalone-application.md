---
title: 'Självstudie: Skapa ett Scala Maven-program för Spark i Azure HDInsight med hjälp av IntelliJ'
description: Skapa ett Spark-program som skrivits i Scala med Apache Maven som build-system och en befintlig Maven-arketyp för Scala som tillhandahålls av IntelliJ IDEA.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/07/2018
ms.openlocfilehash: d83c04946b67dd25bae306c2fa41a0864287bfc8
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52499312"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Självstudie: Skapa ett Scala Maven-program för Apache Spark i HDInsight med hjälp av IntelliJ

I den här självstudien lär du dig att skapa ett [Apache Spark](https://spark.apache.org/)-program som skrivits i [Scala](https://www.scala-lang.org/) med hjälp av [Apache Maven](https://maven.apache.org/) med IntelliJ IDEA. Artikeln använder Apache Maven som build-system och startar med en befintlig Maven-arketyp för Scala som tillhandahålls av IntelliJ IDEA.  Att skapa ett Scala-program i IntelliJ IDEA innefattar följande steg:

* Använd Maven som build-system.
* Uppdatera POM-filen (Project Object Model) för att hantera Spark-modulens beroenden.
* Skriv ditt program i Scala.
* Generera en jar-fil som kan skickas till HDInsight Spark-kluster.
* Kör programmet på ett Spark-kluster med Livy.

> [!NOTE]
> HDInsight tillhandahåller även ett plugin-verktyg i IntelliJ IDEA som underlättar processen med att skapa och skicka program till ett HDInsight Spark-kluster i Linux. Mer information finns i [Använda HDInsight-verktygsplugin för IntelliJ IDEA till att skapa och skicka Apache Spark-program](apache-spark-intellij-tool-plugin.md).
> 

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Använda IntelliJ till att utveckla ett Scala Maven-program

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.


## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit. Du kan installera det [härifrån](https://aka.ms/azure-jdks).
* En Java IDE. Den här artikeln använder IntelliJ IDEA 18.1.1. Du kan installera det [härifrån](https://www.jetbrains.com/idea/download/).

## <a name="use-intellij-to-create-application"></a>Använda IntelliJ till att skapa program

1. Starta IntelliJ IDEA och skapa sedan ett projekt. Gör följande i dialogrutan **Nytt projekt**: 

   a. Välj **HDInsight** > **Spark i HDInsight (Scala)**.

   b. I listan med **byggverktyg** väljer du något av följande enligt dina behov:

      * **Maven**, för guidestöd när du skapar Scala-projekt
      * **SBT**, för att hantera beroenden när du skapar Scala-projekt

   ![Dialogrutan Nytt projekt](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

1. Välj **Nästa**.

1. Guiden för att skapa Scala-projekt identifierar automatiskt om du har installerat Scalas plugin-program. Välj **Installera**.

   ![Kontroll av Scalas plugin-program](./media/apache-spark-create-standalone-application/Scala-Plugin-check-Reminder.PNG) 

1. Om du vill ladda ned Scalas plugin-program väljer du **OK**. Följ anvisningarna för att starta om IntelliJ. 

   ![Dialogrutan för att installera Scalas plugin-program](./media/apache-spark-create-standalone-application/Choose-Scala-Plugin.PNG)

1. Gör följande i fönstret **Nytt projekt**:  

    ![Välj Spark SDK](./media/apache-spark-create-standalone-application/hdi-new-project.png)

   a. Ange ett projektnamn och en plats.

   b. I listrutan **Projekt-SDK** väljer du **Java 1.8** för Spark 2.x-klustret, eller **Java 1.7** för Spark 1.x-klustret.

   c. I listrutan **Spark-version** integrerar guiden för att skapa Scala-projekt rätt version för Spark SDK och Scala SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2.x**. I det här exemplet används **Spark 2.0.2 (Scala 2.11.8)**.

1. Välj **Slutför**.

## <a name="install-scala-plugin-for-intellij-idea"></a>Installera plugin-programmet Scala för IntelliJ IDEA
Använd följande steg för att installera plugin-programmet Scala:

1. Öppna IntelliJ IDEA.
1. På välkomstskärmen väljer du **Konfigurera** och sedan **Plugin-program**.
   
    ![Aktivera plugin-programmet Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
1. Välj **Install JetBrains plugin** (Installera plugin-programmet JetBrains) i det nedre vänstra hörnet. 
1. I dialogrutan **Browse JetBrains Plugins** (Bläddra efter plugin-programmet JetBrains) söker du efter **Scala** och väljer sedan **Installera**.
   
    ![Installera plugin-programmet Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
1. Du måste starta om IDE när plugin-programmet har installerats.

## <a name="create-a-standalone-scala-project"></a>Skapa ett fristående Scala-projekt
1. Öppna IntelliJ IDEA.
1. I menyn **Arkiv** väljer du **Nytt > Projekt** för att skapa ett nytt projekt.
1. I dialogrutan Nytt projekt gör du följande val:
   
    ![Skapa Maven-projekt](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Välj **Maven** som projekttyp.
   * Ange en **Projekt-SDK**. Välj **Nytt** och gå till installationskatalogen för Java, vanligtvis `C:\Program Files\Java\jdk1.8.0_66`.
   * Välj alternativet **Create from archetype** (Skapa från arketyp).
   * I listan med arketyper väljer du **org.scala tools.archetypes:scala-archetype-simple**. Den här arketypen skapar rätt katalogstruktur och laddar ned de beroenden som krävs för att skriva Scala-program.
1. Välj **Nästa**.
1. Ange relevanta värden för **GroupId**, **ArtifactId** och **Version**. I den här självstudien används följande värden:

    - GroupId: com.microsoft.spark.example
    - ArtifactId: SparkSimpleApp
1. Välj **Nästa**.
1. Kontrollera inställningarna och välj sedan **Nästa**.
1. Kontrollera projektets namn och plats och välj sedan **Slutför**.
1. I den vänstra rutan väljer du **src > test > scala > com > microsoft > spark > example**. Högerklicka på **MySpec** och välj sedan **Ta bort**. Du behöver inte den här filen för programmet.
  
1. I efterföljande steg uppdaterar du pom.xml för att definiera beroenden för Spark Scala-programmet. För att dessa beroenden ska kunna laddas ner och hanteras automatiskt, måste du konfigurera Maven därefter.
   
    ![Konfigurera Maven för automatisk nedladdning](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. I menyn **Arkiv** väljer du **Inställningar**.
   1. I dialogrutan **Inställningar** går du till **Build, Execution, Deployment** (Skapa, köra och distribuera) > **Skapa verktyg** > **Maven** > **Importera**.
   1. Välj alternativet **Import Maven projects automatically** (Importera Maven-projekt automatiskt).
   1. Tryck på **Tillämpa** och välj sedan **OK**.
1. I den vänstra rutan väljer du **src > main > scala > com.microsoft.spark.example**. Dubbelklicka sedan på **App** för att öppna App.scala.

1. Ersätt den befintliga exempelkoden med följande kod och spara ändringarna. Den här koden läser data från HVAC.csv (finns i alla HDInsight Spark-kluster), hämtar de rader som bara innehåller en siffra i sjätte kolumnen och skriver utdatan till **/HVACOut** under standardlagringens container för klustret.

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
1. I den vänstra rutan dubbelklickar du på **pom.xml**.
   
   1. I `<project>\<properties>` lägger du till följande segment:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   1. I `<project>\<dependencies>` lägger du till följande segment:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Spara ändringarna i pom.xml.
1. Skapa .jar-filen. Med IntelliJ IDEA går det att skapa JAR-filen som en artefakt av ett projekt. Utför följande steg.
    
    1. I menyn **Arkiv** väljer du **Projektstruktur**.
    1. I dialogrutan **Projektstruktur** väljer du **Artefakter** och sedan plustecknet. I popup-dialogrutan väljer du **JAR** och sedan **From modules with dependencies** (Från moduler med beroenden).
       
        ![Skapa JAR-fil](./media/apache-spark-create-standalone-application/create-jar-1.png)
    1. I dialogrutan **Create JAR from Modules** (Skapa JAR-fil från moduler) väljer du ellipsen (![tre punkter](./media/apache-spark-create-standalone-application/ellipsis.png)) mot **Huvudklass**.
    1. I dialogrutan **Select Main Class** (Välj huvudklass) väljer du den klass som visas som standard och sedan **OK**.
       
        ![Skapa JAR-fil](./media/apache-spark-create-standalone-application/create-jar-2.png)
    1. I dialogrutan **Create JAR from Modules** (Skapa JAR-fil från moduler) kontrollerar du att alternativet **Extract to the target JAR** (Extrahera till mål-JAR) är markerat. Välj sedan **OK**.  Den här inställningen skapar en enda JAR-fil med alla beroenden.
       
        ![Skapa JAR-fil](./media/apache-spark-create-standalone-application/create-jar-3.png)
    1. Layoutfliken med utdata visar alla jar-filer som ingår i Maven-projektet. Du kan markera och ta bort sådana som Scala-programmet inte har något direkt beroende till. För programmet som du skapar här kan du ta bort alla utom den sista (**SparkSimpleApp compile output**). Välj de jar-filer som ska tas bort och välj sedan ikonen **Ta bort**.
       
        ![Skapa JAR-fil](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Kontrollera att rutan **Include in project build** (Inkludera i projektversionen) är markerad, vilket säkerställer att jar-filen skapas varje gång projektet skapas eller uppdateras. välj **Tillämpa** och sedan **OK**.
    1. I menyn **Skapa** väljer du **Build Artifacts** (Skapa artefakter) för att skapa jar-filen. Utdatans jar-fil skapas under **\out\artifacts**.
       
        ![Skapa JAR-fil](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Köra programmet på Apache Spark-klustret
Om du vill köra programmet på klustret, kan du använda följande metoder:

* **Kopiera programmets jar-fil till den Azure Storage Blob** som är associerad med klustret. Du kan använda kommandoradsverktyget [**AzCopy**](../../storage/common/storage-use-azcopy.md) till att göra detta. Det finns även andra klienter som du kan använda för att ladda upp data. Det finns mer information om dem i [Ladda upp data för Apache Hadoop-jobb i HDInsight](../hdinsight-upload-data.md).
* **Använd Apache Livy till att skicka ett programjobb via fjärranslutning** till Spark-klustret. Spark-kluster i HDInsight innehåller Livy som gör REST-slutpunkter tillgängliga, så att man kan skicka Spark-jobb via en fjärranslutning. Mer information finns i [Skicka Apache Spark-jobb via fjärranslutning med hjälp av Apache Livy med Spark-kluster i HDInsight](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Nästa steg

I den här artikeln har du lärt dig att skapa ett Apache Spark Scala-program. Gå vidare till nästa artikel om du vill lära dig att köra programmet på ett HDInsight Spark-kluster med Livy.

> [!div class="nextstepaction"]
>[Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](./apache-spark-livy-rest-interface.md)


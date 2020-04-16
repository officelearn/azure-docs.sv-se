---
title: Självstudiekurs - Azure Toolkit för IntelliJ (Spark-program)
description: Självstudiekurs - Använd Azure Toolkit för IntelliJ för att utveckla Spark-program, som är skrivna i Scala, och skicka dem till en Apache Spark-pool (förhandsversion).
services: synapse-analytics
author: v-jiche
ms.author: v-jiche
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 310dfec2bbd1bf6cb69f6293becead30487d6cbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422657"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-spark-pools-preview"></a>Självstudiekurs: Använd Azure Toolkit för IntelliJ för att skapa Apache Spark-program för Spark-pooler (förhandsversion)

Den här självstudien visar hur du använder plugin-programmet Azure Toolkit för IntelliJ för att utveckla Apache Spark-program, som är skrivna i [Scala,](https://www.scala-lang.org/)och sedan skicka dem till en Spark-pool (förhandsversion) direkt från IntelliJ-integrerad utvecklingsmiljö (IDE). Du kan använda plugin-programmet på några olika sätt:

- Utveckla och skicka in ett Scala Spark-program på en Spark-pool.
- Få tillgång till dina Spark-poolresurser.
- Utveckla och kör ett Scala Spark-program lokalt.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> - Använda plugin-programmet Azure Toolkit för IntelliJ
> - Utveckla Apache Spark-program
> - Skicka in ansökan till Spark-pooler

## <a name="prerequisites"></a>Krav

- [IntelliJ IDEA gemenskapen Version](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Azure toolkit plugin 3.27.0-2019.2 – Installera från [IntelliJ Plugin-databas](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [JDK (version 1.8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Scala Plugin - Installera från [IntelliJ Plugin repository](/hdinsight/spark/apache-spark-intellij-tool-plugin#install-scala-plugin-for-intellij-idea.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Denna förutsättning är endast för Windows-användare.

  När du kör det lokala Spark Scala-programmet på en Windows-dator kan du få ett undantag, vilket förklaras i [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Undantaget inträffar eftersom WinUtils.exe saknas i Windows.
  LÃ¶s problemet genom att [Ã¥tinstÃ¤lla winutils-filen](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) till en plats som **C:\WinUtils\bin**. Lägg sedan till miljövariabeln **HADOOP_HOME**och ange variabelns värde på **C:\WinUtils**.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Skapa ett Spark Scala-program för en Spark-pool

1. Starta IntelliJ IDEA och välj **Skapa nytt projekt** för att öppna fönstret **Nytt projekt**.
2. Välj **Apache Spark/HDInsight** i den vänstra rutan.
3. Välj **Spark Project med Exempel (Scala)** i huvudfönstret.
4. Välj en av följande typer i listrutan **Byggverktyg:**

   - **Maven** för guidestöd när du skapar Scala-projekt.
   - **SBT** för att hantera beroenden när du skapar Scala-projektet.

    ![Dialogrutan IntelliJ IDEA Nytt projekt](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Välj **Nästa**.
6. I fönstret **Nytt projekt** anger du följande information:

    | Egenskap | Beskrivning |
    | ----- | ----- |
    |Projektnamn| Ange ett namn. I den här självstudien används `myApp`.|
    |Projektplats| Ange önskad plats för att spara projektet.|
    |Projekt-SDK| Det kan vara tomt på din första användning av IDEA. Välj **Nytt... ** och navigera till din JDK.|
    |Spark-version|Skapandeguiden integrerar rätt version för Spark SDK och Scala SDK. Synapse stöder endast **Spark 2.4.0**.|

    ![Välja Apache Spark SDK](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Välj **Slutför**. Det kan ta några minuter innan projektet blir tillgängligt.
8. Spark-projektet skapar automatiskt en artefakt åt dig. Så här visar du artefakten:

   a. Från menyraden navigerar du till > **Arkivprojektstruktur...**. **File**

   b. Välj **Artefakter**i fönstret **Projektstruktur** .

   c. Välj **Avbryt** när du har visat artefakten.

    ![Artefaktinformation i dialogrutan](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Hitta **LogQuery** från **myApp** > **src** > **huvud** > **scala**> **prov**> **LogQuery**. Den här självstudien använder **LogQuery** för att köras.

   ![Kommandon för att skapa en Scala-klass från Project](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Anslut till dina Spark-pooler

Logga in på Azure-prenumerationen för att ansluta till dina Spark-pooler.

### <a name="sign-in-to-your-azure-subscription"></a>Logga in på din Azure-prenumeration

1. På menyraden navigerar du till **Visa** > **verktyg i Utforskaren** > i**Utforskaren**.

   ![IntelliJ IDEA visar Azure Explorer](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. Högerklicka på Azure-noden **Azure** i Azure Explorer och välj sedan **Logga in**.

   ![IntelliJ IDEA explorer högerklickar på azure](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. I dialogrutan **Azure-inloggning** väljer du **Enhetsinloggning**och väljer sedan **Logga in**.

    ![IntelliJ IDEA azure-inloggning](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. Klicka på **Kopiera&öppna**i dialogrutan Azure Device **Login** .

   ![IntelliJ IDEA azure enhetsinloggning](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. Klistra in koden i webbläsargränssnittet och klicka sedan på **Nästa**.

   ![Microsoft går in i koddialogrutan för HDI](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Ange dina Azure-autentiseringsuppgifter och stäng sedan webbläsaren.

   ![Microsoft går in i e-postdialogruta för HDI](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. När du är inloggad visas alla Azure-prenumerationer som är associerade med autentiseringsuppgifterna i dialogrutan **Välj prenumerationer.** Välj din prenumeration och klicka sedan på **Välj**.

    ![Dialogrutan Select Subscriptions (Välj prenumerationer)](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. Expandera **Apache Spark på Synapse från** Azure **Explorer**för att visa de arbetsytor som finns i dina prenumerationer.

    ![Huvudvy för IntelliJ IDEA Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Om du vill visa Spark-poolerna kan du expandera en arbetsyta ytterligare.

    ![Lagringskonton för Azure Explorer](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Fjärrkör ett Spark Scala-program på en Spark-pool

När du har skapat ett Scala-program kan du fjärrköra det.

1. Öppna fönstret **Kör/felsöka konfigurationer** genom att klicka på ikonen.

    ![Kommandot Skicka Spark-program till HDInsight](./media/intellij-tool-synapse/open-configuration-window.png)

2. Klicka på **+** i dialogfönstret **Kör/felsöka konfigurationer** och välj sedan **Apache Spark på Synapse**.

    ![Kommandot Skicka Spark-program till HDInsight](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. Ange följande värden i fönstret **Kör/debug-konfigurationer** och välj sedan **OK:**

    |Egenskap |Värde |
    |----|----|
    |Spark pooler|Välj de Spark-pooler som du vill köra programmet på.|
    |Välj en artefakt som ska skickas|Lämna standardinställningen.|
    |Huvudklassnamn|Standardvärdet är huvudklassen från den valda filen. Du kan ändra klassen genom att välja ellips(**...**) och välja en annan klass.|
    |Jobbkonfigurationer|Du kan ändra standardnyckeln och standardvärdena. Mer information finns i [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Kommandoradsargument|Du kan ange argument avgränsade med blanksteg för huvudklassen om det behövs.|
    |Refererade burkar och refererade filer|Du kan ange sökvägarna för de refererade burkarna och filerna om sådana finns. Du kan också bläddra bland filer i Azures virtuella filsystem, som för närvarande bara stöder ADLS Gen 2-kluster. Mer information: [Apache Spark-konfiguration](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) och [så här laddar du upp resurser till klustret](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).|
    |Lagring av jobbuppladdning|Expandera för att visa ytterligare alternativ.|
    |Lagringstyp|Välj **Använd Azure Blob för att ladda upp** från listrutan.|
    |Lagringskonto|Ange ditt lagringskonto.|
    |Lagringsnyckel|Ange din lagringsnyckel.|
    |Behållare för lagring|Välj lagringsbehållaren i listrutan när **lagringskontot** och **lagringsnyckeln** har angetts.|

    ![Dialogrutan Spark-inlämning](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Klicka på **Ikonen SparkJobRun** om du vill skicka projektet till den valda Spark-poolen. På fliken **Fjärr spark-jobb i kluster** visas jobbkörningsstatusen längst ned. Du kan stoppa programmet genom att klicka på den röda knappen.

    ![Fönstret Apache Spark Inlämning](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![Dialogrutan Spark-inlämning](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Lokala Körning/Felsökning Avsökning av Apache Spark-program

Du kan följa instruktionerna nedan för att ställa in din lokala körning och lokala felsökning för ditt Apache Spark-jobb.

### <a name="scenario-1-do-local-run"></a>Scenario 1: Kör lokal körning

1. Öppna dialogrutan **Kör/felsöka konfigurationer,** välj**+** plustecknet ( ). Välj sedan alternativet **Apache Spark på Synapse.** Ange information för **Namn**, **Huvudklassnamn** att spara.

    ![Intellij Kör felsökningskonfigurationer lokal körning](./media/intellij-tool-synapse/local-run-synapse.png)

    - Miljövariabler och WinUtils.exe-plats är endast för Windows-användare.
    - Miljövariabler: Systemmiljövariabeln kan identifieras automatiskt om du har ställt in den tidigare och du behöver inte lägga till den manuellt.
    - [WinUtils.exe Plats:](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)Du kan ange WinUtils plats genom att klicka på mappikonen till höger.

2. Klicka sedan på den lokala uppspelningsknappen.

    ![Intellij Kör felsökningskonfigurationer lokal körning](./media/intellij-tool-synapse/local-run-synapse01.png)

3. När den lokala körningen är klar, om skriptet innehåller utdata, kan du kontrollera utdatafilen från > **__datastandarden__**. **data**

    ![Resultat för lokalt körning i Intellij-projekt](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Scenario 2: Felsöker lokal felsökning

1. Öppna **LogQuery-skriptet,** ange brytpunkter.
2. Klicka på **Ikonen Lokal felsökning** om du vill göra lokal felsökning.

    ![Resultat för lokalt körning i Intellij-projekt](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Komma åt och hantera Synapse Workspace

Du kan utföra olika åtgärder i Azure Explorer i Azure Toolkit för IntelliJ. På menyraden navigerar du till **Visa** > **verktyg i Utforskaren** > i**Utforskaren**.

### <a name="launch-workspace"></a>Starta arbetsyta

1. Från Azure Explorer navigerar du till **Apache Spark på Synapse**och expanderar den sedan.

    ![Huvudvy för IntelliJ IDEA Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Högerklicka på en arbetsyta och välj sedan **Starta arbetsyta**, webbplats öppnas.

    ![Information om spark-jobbvyprogram](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Information om spark-jobbvyprogram](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Spark-konsol

Du kan köra Spark Local Console(Scala) eller köra Spark Livy Interactive Session Console(Scala).

### <a name="spark-local-console-scala"></a>Spark lokal konsol (Scala)

Se till att du har uppfyllt WINUTILS. EXE-förutsättning.

1. På menyraden navigerar du till **Kör** > **redigeringskonfigurationer...**.
2. Från fönstret **Kör/debug-konfigurationer,** i den vänstra rutan, navigera till **Apache Spark på Synapse** > **[Spark på Synapse] myApp**.
3. Välj fliken **Lokalt kör** i huvudfönstret.
4. Ange följande värden och välj sedan **OK:**

    |Egenskap |Värde |
    |----|----|
    |Miljövariabler|Kontrollera att värdet för HADOOP_HOME är korrekt.|
    |PLATS FÖR WINUTILS.exe|Kontrollera att banan är korrekt.|

    ![Konfiguration av lokal konsoluppsättning](./media/intellij-tool-synapse/local-console-synapse01.png)

5. Från Project, navigera till **myApp** > **src** > **huvud** > **scala** > **myApp**.
6. På menyraden navigerar du till **Verktyg** > **Spark-konsolen** > **Kör Spark Lokal konsol(Scala).**
7. Sedan kan två dialogrutor visas för att fråga dig om du vill åtgärda beroenden automatiskt. Om så är fallet väljer du **Åtgärda automatiskt**.

    ![Dialogrutan IntelliJ IDEA Spark Auto Fix1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![Dialogrutan IntelliJ IDEA Spark Auto Fix2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. Konsolen ska se ut ungefär som bilden nedan. I konsolfönstret `sc.appName`skriver du och trycker sedan på ctrl+Retur. Resultatet visas. Du kan stoppa den lokala konsolen genom att klicka på den röda knappen.

    ![IntelliJ IDEA lokalt konsolresultat](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Spark Livy interaktiv sessionskonsol (Scala)

Det stöds bara på IntelliJ 2018.2 och 2018.3.

1. På menyraden navigerar du till **Kör** > **redigeringskonfigurationer...**.

2. Från fönstret **Kör/debug-konfigurationer,** i den vänstra rutan, navigera till **Apache Spark på synapse** > **[Spark på synapse] myApp**.

3. Välj fliken **Fjärrkörning i kluster i** huvudfönstret.

4. Ange följande värden och välj sedan **OK:**

    |Egenskap |Värde |
    |----|----|
    |Spark pooler|Välj de Spark-pooler som du vill köra programmet på.|
    ||

    ![Konfiguration av interaktiv konsoluppsättning](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. Från Project, navigera till **myApp** > **src** > **huvud** > **scala** > **myApp**.

6. På menyraden navigerar du till **Verktyg** > **Spark-konsolen** > **Kör Spark Livy Interactive Session Console(Scala)**.
7. Konsolen ska se ut ungefär som bilden nedan. I konsolfönstret `sc.appName`skriver du och trycker sedan på ctrl+Retur. Resultatet visas. Du kan stoppa den lokala konsolen genom att klicka på den röda knappen.

    ![Intellij IDEA Interaktiv konsol resultat](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Skicka markering till Spark-konsolen

Det är bekvämt för dig att förutse skriptresultatet genom att skicka lite kod till den lokala konsolen eller Livy Interactive Session Console (Scala). Du kan markera en del kod i Scala-filen och sedan högerklicka på **Skicka markering till spark-konsol**. Den valda koden skickas till konsolen och göras. Resultatet visas efter koden i konsolen. Konsolen kontrollerar felen om den finns.

   ![Skicka markering till Spark-konsolen](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Nästa steg

- [Azure Synapse Analytics](../overview-what-is.md)
- [Skapa en ny Apache Spark-pool för en Azure Synapse Analytics-arbetsyta](../../synapse-analytics/quickstart-create-apache-spark-pool.md)

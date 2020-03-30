---
title: Datasjöverktyg för Visual Studio & Hortonworks - Azure HDInsight
description: Lär dig hur du använder Azure Data Lake-verktygen för Visual Studio med Hortonworks sandlåda som körs i en lokal virtuell dator. Med dessa verktyg kan du skapa och köra Hive- och Pig-jobb i sandlådan och visa jobbutdata och historik.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: e128aaf6e1726b7a1341fefc6df3cdafd3beb880
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73500155"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Använda Azure Data Lake-verktygen för Visual Studio med Hortonworks Sandbox

Azure Data Lake innehåller verktyg för att arbeta med generiska Apache Hadoop-kluster. Det här dokumentet innehåller de steg som krävs för att använda datasjöverktygen med Hortonworks Sandbox som körs på en lokal virtuell dator.

Med hjälp av Hortonworks Sandbox kan du arbeta med Hadoop lokalt på din utvecklingsmiljö. När du har utvecklat en lösning och vill distribuera den i stor skala kan du sedan flytta till ett HDInsight-kluster.

## <a name="prerequisites"></a>Krav

* Hortonworks Sandbox, som körs i en virtuell maskin på din utvecklingsmiljö. Det här dokumentet skrevs och testades med sandlådan som körs i Oracle VirtualBox. Information om hur du konfigurerar sandlådan finns i [sandlådan Kom igång med Hortonworks sandlåda.](hadoop/apache-hadoop-emulator-get-started.md) Dokument.

* Visual Studio.

* [Azure SDK för .NET](https://azure.microsoft.com/downloads/) 2.7.1 eller senare.

* [Azure Data Lake-verktygen för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Konfigurera lösenord för sandlådan

Se till att Hortonworks Sandbox är igång. Följ sedan stegen i dokumentet [Kom igång i Hortonworks Sandbox-dokumentet.](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) I de här stegen konfigureras lösenordet för SSH-kontot `root` och Apache Ambari-kontot. `admin` Dessa lösenord används när du ansluter till sandlådan från Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Anslut verktygen till sandlådan

1. Öppna Visual Studio, välj **Visa**och välj sedan **Serverutforskaren**.

2. Högerklicka på **HDInsight-posten** från **Server Explorer**och välj sedan Anslut **till HDInsight Emulator**.

    ![Server Explorer, med Anslut till HDInsight Emulator markerad](./media/hdinsight-hadoop-emulator-visual-studio/connect-hdinsight-emulator.png)

3. Ange det lösenord som du har konfigurerat för Ambari i dialogrutan **Anslut till HDInsight Emulator.**

    ![Skärmbild av dialogrutan, med textrutan ambari-lösenord markerad](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Välj **Nästa** för att fortsätta.

4. Använd fältet **Lösenord** för att ange det `root` lösenord som du har konfigurerat för kontot. Lämna de andra fälten till standardvärdet.

    ![Skärmbild av dialogrutan, med textrutan för rotlösenord markerad](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password1.png)

    Välj **Nästa** för att fortsätta.

5. Vänta tills valideringen av tjänsterna är klar. I vissa fall kan valideringen misslyckas och du uppmanas att uppdatera konfigurationen. Om valideringen misslyckas väljer du **Uppdatera**och väntar på att konfigurationen och verifieringen för tjänsten ska slutföras.

    ![Skärmbild av dialogrutan, med knappen Uppdatera markerad](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update-window.png)

    > [!NOTE]  
    > Uppdateringsprocessen använder Ambari för att ändra Hortonworks Sandbox-konfigurationen till vad som förväntas av Data Lake-verktygen för Visual Studio.

6. När valideringen är klar väljer du **Slutför** för att slutföra konfigurationen.
    ![Skärmbild av dialogrutan med knappen Slutför markerad](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect-dialog.png)

     >[!NOTE]  
     > Beroende på utvecklingsmiljöns hastighet och mängden minne som allokerats till den virtuella datorn kan det ta flera minuter att konfigurera och validera tjänsterna.

När du har följt dessa steg har du nu en **lokal HDInsight-klusterpost** i Server Explorer under avsnittet **HDInsight.**

## <a name="write-an-apache-hive-query"></a>Skriva en Apache Hive-fråga

Hive tillhandahåller ett SQL-liknande frågespråk (HiveQL) för att arbeta med strukturerade data. Följ följande steg för att lära dig hur du kör frågor på begäran mot det lokala klustret.

1. Högerklicka på posten för det lokala kluster som du lade till tidigare i **Server Explorer**och välj sedan Skriv **en Hive-fråga**.

    ![Skärmbild av Server Explorer, med Skriva en Hive-fråga markerad](./media/hdinsight-hadoop-emulator-visual-studio/write-apache-hive-query.png)

    Ett nytt frågefönster visas. Här kan du snabbt skriva och skicka en fråga till det lokala klustret.

2. Ange följande kommando i det nya frågefönstret:

        select count(*) from sample_08;

    Om du vill köra frågan väljer du **Skicka** högst upp i fönstret. Lämna de andra värdena **(batch-** och servernamn) till standardvärdena.

    ![Skärmbild av frågefönstret, med knappen Skicka markerad](./media/hdinsight-hadoop-emulator-visual-studio/query-window-submit-hive.png)

    Du kan också använda den nedrullningsbara menyn bredvid **Skicka** för att välja **Avancerat**. Med avancerade alternativ kan du ange ytterligare alternativ när du skickar in jobbet.

    ![Skärmbild av registreringsfönstret Skicka skript](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-hive.png)

3. När du har skickat frågan visas jobbstatusen. Jobbstatusen visar information om jobbet när det bearbetas av Hadoop. **Jobbtillstånd** ger jobbets status. Tillståndet uppdateras regelbundet eller så kan du använda uppdateringsikonen för att uppdatera tillståndet manuellt.

    ![Skärmbild av dialogrutan Jobbvy med jobbtillstånd markerat](./media/hdinsight-hadoop-emulator-visual-studio/job-view-dialog-box1.png)

    När **jobbtillståndet** har ändrats till **Färdig**visas en riktad acyklisk graf (DAG). I det här diagrammet beskrivs körningssökvägen som fastställdes av Tez vid bearbetning av Hive-frågan. Tez är standardkörningsmotorn för Hive i det lokala klustret.

    > [!NOTE]  
    > Apache Tez är också standard när du använder Linux-baserade HDInsight-kluster. Det är inte standard på Windows-baserade HDInsight. Om du vill använda den `set hive.execution.engine = tez;` där måste du lägga till raden i början av Hive-frågan.

    Använd länken **Jobbutdata** för att visa utdata. I det här fallet är det 823, antalet rader i sample_08 tabellen. Du kan visa diagnostikinformation om jobbet med hjälp av **jobbloggen** och **hämta YARN-logglänkarna.**

4. Du kan också köra Hive-jobb interaktivt genom att ändra fältet **Batch** till **Interaktivt**. Välj sedan **Kör**.

    ![Skärmbild av knapparna Interaktiv och Kör markerad](./media/hdinsight-hadoop-emulator-visual-studio/hdi-interactive-query.png)

    En interaktiv fråga strömmar utdataloggen som genereras under bearbetningen till **fönstret HiveServer2-utdata.**

    > [!NOTE]  
    > Informationen är densamma som är tillgänglig från länken **Jobblogg** när ett jobb har slutförts.

    ![Skärmbild av HiveServer2-utdata](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output-box.png)

## <a name="create-a-hive-project"></a>Skapa ett Hive-projekt

Du kan också skapa ett projekt som innehåller flera Hive-skript. Använd ett projekt när du har relaterade skript eller vill lagra skript i ett versionskontrollsystem.

1. Välj **Arkiv**, **Ny**i Visual Studio och sedan **Projekt**.

2. Expandera **Mallar**i listan över projekt, expandera **Azure Data Lake**och välj sedan **HIVE (HDInsight).** Välj **Hive-exempel**i listan över mallar . Ange ett namn och en plats och välj sedan **OK**.

    ![Nytt projektfönster med Azure Data Lake, Hive Sample och OK](./media/hdinsight-hadoop-emulator-visual-studio/new-apache-hive-project.png)

**Hive Sample-projektet** innehåller två skript, **WebLogAnalysis.hql** och **SensorDataAnalysis.hql**. Du kan skicka in dessa skript med samma **skicka-knapp** högst upp i fönstret.

## <a name="create-an-apache-pig-project"></a>Skapa ett Apache Pig-projekt

Medan Hive tillhandahåller ett SQL-liknande språk för att arbeta med strukturerade data, fungerar Pig genom att utföra omvandlingar på data. Pig ger ett språk (Pig Latin) som låter dig utveckla en pipeline av omvandlingar. Så här använder du Pig med det lokala klustret:

1. Öppna Visual Studio och välj **Arkiv**, **Ny**och sedan **Project**. Expandera **Mallar**i listan över projekt, expandera **Azure Data Lake**och välj sedan Pig **(HDInsight).** Välj **Grisprogram i**listan över mallar . Ange ett namn, en plats och välj sedan **OK**.

    ![Skärmbild av fönstret Nytt projekt, med Azure Data Lake, Pig, Pig Application och OK markerat](./media/hdinsight-hadoop-emulator-visual-studio/new-apche-pig-project.png)

2. Ange följande text som innehållet i filen **script.pig** som skapades med det här projektet.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Pig använder ett annat språk än Hive, men hur du kör jobben är konsekvent mellan båda **språken,** via skicka-knappen. Om du markerar listrutan bredvid **Skicka** visas en avancerad skicka-dialogruta för Pig.

    ![Skärmbild av dialogrutan Skicka skript](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-pig1.png)

3. Jobbstatus och utdata visas också, samma som en Hive-fråga.

    ![Skärmbild av ett slutfört Grisjobb](./media/hdinsight-hadoop-emulator-visual-studio/completed-apache-pig.png)

## <a name="view-jobs"></a>Visa jobb

Med datasjöverktyg kan du också enkelt visa information om jobb som har körts på Hadoop. Följ följande steg för att se de jobb som har körts på det lokala klustret.

1. Högerklicka på det lokala klustret i **Server Explorer**och välj sedan **Visa jobb**. En lista över jobb som har skickats till klustret visas.

    ![Skärmbild av Server Explorer, med Visa jobb markerade](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-view-jobs.png)

2. Välj en i listan med jobb för att visa jobbinformationen.

    ![Skärmbild av jobbwebbläsare, med ett av jobben markerat](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-job-details.png)

    Informationen som visas liknar den du ser när du har kört en Hive- eller Pig-fråga, inklusive länkar för att visa utdata- och logginformation.

3. Du kan också ändra och skicka jobbet igen härifrån.

## <a name="view-hive-databases"></a>Visa Hive-databaser

1. Expandera den lokala **klusterposten HDInsight** i **Server Explorer**och expandera sedan **Hive-databaser**. **Standard-** och **xademodatabaserna** i det lokala klustret visas. När du expanderar en databas visas tabellerna i databasen.

    ![Skärmbild av Server Explorer, med databaser expanderade](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases-view.png)

2. När du expanderar en tabell visas kolumnerna för den tabellen. Om du snabbt vill visa data högerklickar du på en tabell och väljer **Visa de översta 100 raderna**.

    ![Serverutforskaren, med tabellen expanderad och Visa de 100 raderna markerade](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-top-100-rows.png)

### <a name="database-and-table-properties"></a>Databas- och tabellegenskaper

Du kan visa egenskaperna för en databas eller tabell. Om du väljer **Egenskaper** visas information om det markerade objektet i egenskapsfönstret. Se till exempel informationen som visas i följande skärmbild:

![Skärmbild av fönstret Egenskaper](./media/hdinsight-hadoop-emulator-visual-studio/hdi-properties-window.png)

### <a name="create-a-table"></a>Skapa en tabell

Om du vill skapa en tabell högerklickar du på en databas och väljer sedan **Skapa tabell**.

![Skärmbild av Server Explorer, med Skapa tabell markerat](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-create-table.png)

Du kan sedan skapa tabellen med hjälp av ett formulär. Längst ner på följande skärmdump kan du se den råa HiveQL som används för att skapa tabellen.

![Skärmbild av formuläret som används för att skapa en tabell](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form-box.png)

## <a name="next-steps"></a>Nästa steg

* [Att lära sig linorna i Hortonworks Sandbox](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Apache Hadoop handledning - Komma igång med HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

---
title: Data Lake tools för Visual Studio med begränsat Hortonworks-läge – Azure HDInsight
description: Lär dig hur du använder Azure Data Lake-verktygen för Visual Studio med Hortonworks sandbox-miljön som körs i en lokal virtuell dator. Med dessa verktyg kan du skapa och köra Hive och Pig-jobb i begränsat läge, och visa jobbutdata och historik.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: hrasheed
ms.openlocfilehash: e6a3fab0e6b3b092ddb55043882c4d284268abfc
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51006863"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Använda Azure Data Lake-verktyg för Visual Studio med begränsat Hortonworks-läge

Azure Data Lake innehåller verktyg för att arbeta med allmänt Hadoop-kluster. Det här dokumentet innehåller de steg som behövs för att använda Data Lake-verktyg med begränsat Hortonworks-läge som körs i en lokal virtuell dator.

Med begränsat Hortonworks-läge kan du arbeta med Hadoop lokalt i din utvecklingsmiljö. När du har utvecklat en lösning och vill distribuera i stor skala, kan du sedan flytta till ett HDInsight-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Hortonworks sandbox-miljön, som körs i en virtuell dator i din utvecklingsmiljö. Det här dokumentet har skrivits och testats med sandbox-miljön som körs i Oracle VirtualBox. Information om hur du konfigurerar sandbox-miljön finns i den [Kom igång med begränsat Hortonworks-läge.](hadoop/apache-hadoop-emulator-get-started.md) dokumentet.

* Visual Studio 2013, Visual Studio 2015 eller Visual Studio 2017 (alla versioner).

* Den [Azure SDK för .NET](https://azure.microsoft.com/downloads/) 2.7.1 eller senare.

* Den [Azure Data Lake tools för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Konfigurera lösenord för sandbox-miljön

Se till att Hortonworks Sandbox körs. Följ stegen i den [Kom igång med Hortonworks Sandbox](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) dokumentet. De här stegen konfigurera lösenordet för en SSH `root` konto och Ambari `admin` konto. Dessa lösenord används när du ansluter till sandbox från Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Ansluta verktygen till sandbox-miljön

1. Öppna Visual Studio, Välj **visa**, och välj sedan **Server Explorer**.

2. Från **Server Explorer**, högerklicka på den **HDInsight** posten och välj sedan **Anslut till HDInsight Emulator**.

    ![Skärmbild av Server Explorer med Anslut till HDInsight Emulator markerat](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Från den **Anslut till HDInsight Emulator** dialogrutan anger du det lösenord som du har konfigurerat för Ambari.

    ![Skärmbild av dialogrutan med lösenordet i textrutan markerat](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Välj **Nästa** för att fortsätta.

4. Använd den **lösenord** fält för att ange lösenordet som du konfigurerade för den `root` konto. Lämna övriga fält på standardvärdet.

    ![Skärmbild av dialogrutan med lösenordet i textrutan markerat](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Välj **Nästa** för att fortsätta.

5. Vänta på verifiering av tjänster ska slutföras. I vissa fall verifieringen misslyckas och uppmanar dig att uppdatera konfigurationen. Om valideringen misslyckas, Välj **uppdatering**, och vänta tills konfigurationen och verifiering för tjänsten att slutföra.

    ![Skärmbild av dialogrutan med knappen Uppdatera markerade](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > Uppdateringsprocessen använder Ambari för att ändra konfigurationen av Hortonworks Sandbox vad som förväntas av Data Lake-verktyg för Visual Studio.

6. När verifieringen är klar kan du välja **Slutför** att slutföra konfigurationen.
    ![Skärmbild av dialogrutan med Slutför markerat](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]
     > Det kan ta flera minuter att konfigurera och verifiera tjänsterna beroende på din utvecklingsmiljö och mängden minne som allokeras till den virtuella datorn hastighet.

När du har följt de här stegen, nu har du en **lokalt kluster för HDInsight** post i Server Explorer under den **HDInsight** avsnittet.

## <a name="write-a-hive-query"></a>Skriv en Hive-fråga

Hive innehåller ett SQL-liknande frågespråk (HiveQL) för att arbeta med strukturerade data. Använd följande steg för att lära dig hur du kör på begäran-frågor mot det lokala klustret.

1. I **Server Explorer**, högerklicka på posten för det lokala klustret som du lagt till tidigare och välj sedan **Skriv en Hive-fråga**.

    ![Skärmbild av Server Explorer med Skriv en Hive-fråga som markerat](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Ett nytt frågefönster visas. Här kan du snabbt skriva och skicka en fråga till det lokala klustret.

2. I nya frågefönstret anger du följande kommando:

        select count(*) from sample_08;

    Om du vill köra frågan **skicka** överst i fönstret. Lämna de andra värdena (**Batch** och servernamnet) på standardvärden.

    ![Skärmbild av frågefönstret med knappen Skicka markerat](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Du kan också använda den nedrullningsbara menyn bredvid **skicka** att välja **Avancerat**. Avancerade alternativ kan du ange ytterligare alternativ när du har skickat jobbet.

    ![Skärmbild av skicka skript-dialogrutan](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. När du har skickat frågan visas jobbets status. Jobbstatusen innehåller information om jobbet som bearbetas av Hadoop. **Jobbets status** innehåller statusen för jobbet. Tillståndet uppdateras regelbundet, eller du kan använda på Uppdatera-ikonen för att uppdatera tillståndet manuellt.

    ![Skärmbild av Jobbvy dialogrutan med jobbstatus markerat](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Efter den **jobbstatus** ändras till **slutfört**, visas en riktad Acyklisk graf (DAG). Det här diagrammet beskriver körningssökvägen som fastställts av Tez vid bearbetning av Hive-frågan. Tez är standard Körningsmotor för Hive i det lokala klustret.

    > [!NOTE]
    > Tez är standardalternativet när du använder Linux-baserade HDInsight-kluster. Det är inte standard på Windows-baserade HDInsight. För att använda den där, måste du lägga till raden `set hive.execution.engine = tez;` i början av Hive-frågan.

    Använd den **Jobbutdata** länken för att visa utdata. I det här fallet är det 823, antalet rader i tabellen sample_08. Du kan visa diagnostikinformation om jobbet med hjälp av den **Jobblogg** och **ladda ned YARN-logg** länkar.

4. Du kan också köra Hive-jobb interaktivt genom att ändra den **Batch** fältet **interaktiv**. Välj sedan **kör**.

    ![Skärmbild av interaktiva och kör knappar markerat](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    En interaktiv fråga strömmar i utdataloggen som genereras under bearbetningen till den **HiveServer2 utdata** fönster.

    > [!NOTE]
    > Det är samma som är tillgänglig från den **Jobblogg** länka när ett jobb har slutförts.

    ![Skärmbild av loggen](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Skapa en Hive-projekt

Du kan också skapa ett projekt som innehåller flera Hive-skript. Använda ett projekt när du har relaterade skript eller vill lagra skript i ett system.

1. I Visual Studio väljer **filen**, **New**, och sedan **projekt**.

2. I listan med projekt expanderar **mallar**, expandera **Azure Data Lake**, och välj sedan **HIVE (HDInsight)**. Välj i listan över mallar **Hive exempel**. Ange ett namn och plats och välj sedan **OK**.

    ![Skärmbild av nytt projekt fönster, med Azure Data Lake, HIVE, Hive-exempel och OK markerat](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

Den **Hive exempel** projektet innehåller två skript **WebLogAnalysis.hql** och **SensorDataAnalysis.hql**. Du kan skicka dessa skript med hjälp av samma **skicka** längst upp i fönstret.

## <a name="create-a-pig-project"></a>Skapa ett Pig-projekt

Medan Hive innehåller ett SQL-liknande språk för att arbeta med strukturerade data, fungerar Pig genom att utföra omvandlingar på data. Pig innehåller ett språk (Pig Latin) som gör det möjligt att utveckla en pipeline med transformationer. Följ dessa steg om du vill använda Pig med det lokala klustret:

1. Öppna Visual Studio och välj **filen**, **New**, och sedan **projekt**. I listan med projekt expanderar **mallar**, expandera **Azure Data Lake**, och välj sedan **Pig (HDInsight)**. Välj i listan över mallar **Pig program**. Ange ett namn, plats, och välj sedan **OK**.

    ![Skärmbild av nytt projekt fönster, med Azure Data Lake, Pig, Pig-programmet och OK markerat](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Ange följande text som innehållet i den **script.pig** filen som skapades med det här projektet.

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

    Medan Pig använder ett annat språk än Hive, hur du kör jobben är konsekvent mellan båda språken via den **skicka** knappen. Att välja listrutan bredvid **skicka** visas en dialogruta med avancerade skicka för Pig.

    ![Skärmbild av skicka skript-dialogrutan](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. Jobbstatus och utdata visas också, samma som en Hive-fråga.

    ![Skärmbild av ett slutfört Pig-jobb](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Visa jobb

Data Lake-verktyg kan du enkelt se information om jobb som körts på Hadoop. Använd följande steg för att se de jobb som körts på det lokala klustret.

1. Från **Server Explorer**, högerklicka på det lokala klustret och välj sedan **visa jobb**. En lista över jobb som har skickats till klustret visas.

    ![Skärmbild av Server Explorer med Visa jobb markerat](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Välj något att visa Jobbinformationen för jobben i listan.

    ![Skärmbild av Jobbwebbläsare, ett av jobben markerat](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    Informationen som visas liknar det som visas när du har kört en Hive- eller Pig fråga, inklusive länkar för att visa utdata och logga information.

3. Du kan också ändra och skicka jobbet härifrån.

## <a name="view-hive-databases"></a>Visa Hive-databaser

1. I **Server Explorer**, expandera den **lokalt kluster för HDInsight** post, och expandera sedan **Hive-databaser**. Den **standard** och **xademo** databaser i det lokala klustret visas. Expandera en databas visar tabeller i databasen.

    ![Skärmbild av Server Explorer med databaser expanderas](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Expandera en tabell innehåller kolumner för tabellen. Högerklicka på en tabell för att snabbt visa data, och välj **visa de 100 översta raderna**.

    ![Skärmbild av Server Explorer med tabellen expanderas och visa de 100 översta raderna valt](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Egenskaper för databas och tabell

Du kan visa egenskaperna för en databas eller tabell. Att välja **egenskaper** visar information för det valda objektet i egenskapsfönstret. Se exempelvis informationen som visas i följande skärmbild:

![Skärmbild av egenskapsfönstret](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Skapa en tabell

Högerklicka på en databas för att skapa en tabell, och välj sedan **Create Table**.

![Skärmbild av Server Explorer med Create Table markerat](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Du kan sedan skapa tabellen med hjälp av ett formulär. Du kan se den råa HiveQL som används för att skapa tabellen längst ned på följande skärmbild.

![Skärmbild av formuläret som används för att skapa en tabell](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Nästa steg

* [Lära dig grunderna för Hortonworks sandbox-miljön](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop-självstudiekurs – komma igång med HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

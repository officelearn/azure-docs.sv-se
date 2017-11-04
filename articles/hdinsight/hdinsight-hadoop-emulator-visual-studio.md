---
title: "Data Lake-verktyg för Visual Studio med Hortonworks Sandbox - Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du använder Azure Data Lake-verktyg för Visual Studio med Hortonworks sandbox körs på en lokal virtuell dator. Med dessa verktyg kan du skapa och köra Hive och Pig-jobb på begränsat, och visa jobbutdata och historik."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e3434c45-95d1-4b96-ad4c-fb59870e2ff0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/11/2017
ms.author: larryfr
ms.openlocfilehash: 53db03029ffca724638871b22a12dfa7f160fc64
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Använda Azure Data Lake-verktyg för Visual Studio med Hortonworks Sandbox

Azure Data Lake innehåller verktyg för att arbeta med allmänt Hadoop-kluster. Det här dokumentet innehåller de steg som behövs för att använda Data Lake-verktyg med Hortonworks Sandbox körs i en lokal virtuell dator.

Med Hortonworks Sandbox kan du arbeta med Hadoop lokalt på din utvecklingsmiljö. När du har utvecklat en lösning och vill distribuera i skala, kan du sedan flytta till ett HDInsight-kluster.

## <a name="prerequisites"></a>Krav

* Hortonworks Sandbox körs i en virtuell dator på din utvecklingsmiljö. Dokumentet har skrivits och testas med sandbox körs i Oracle VirtualBox. Information om hur du konfigurerar sandbox finns det [Kom igång med Hortonworks sandbox.](hadoop/apache-hadoop-emulator-get-started.md) dokumentet.

* Visual Studio 2013, Visual Studio 2015 eller Visual Studio 2017 (någon utgåva).

* Den [Azure SDK för .NET](https://azure.microsoft.com/downloads/) 2.7.1 eller senare.

* Den [Azure Data Lake-verktyg för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Konfigurera lösenord för sandbox

Kontrollera att sandlådan Hortonworks körs. Följ stegen i den [komma igång i sandlådan Hortonworks](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) dokumentet. De här stegen konfigurera lösenordet för SSH `root` konto och Ambari `admin` konto. Dessa lösenord används när du ansluter till sandbox från Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Ansluta verktygen till sandbox

1. Öppna Visual Studio, markera **visa**, och välj sedan **Server Explorer**.

2. Från **Server Explorer**, högerklicka på den **HDInsight** posten och välj sedan **Anslut till HDInsight-Emulator**.

    ![Skärmbild av Server Explorer, med Anslut till HDInsight-Emulator markerat](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Från den **Anslut till HDInsight-Emulator** dialogrutan Ange lösenordet som du har konfigurerat för Ambari.

    ![Skärmbild av dialogrutan med lösenordet i textrutan markerat](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Välj **nästa** att fortsätta.

4. Använd den **lösenord** fältet för att ange lösenordet som du har konfigurerat för den `root` konto. Lämna de andra fälten standardvärdet.

    ![Skärmbild av dialogrutan med lösenordet i textrutan markerat](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Välj **nästa** att fortsätta.

5. Vänta på verifiering av tjänster ska slutföras. I vissa fall verifieringen misslyckas och uppmanar dig att uppdatera konfigurationen. Om valideringen misslyckas, Välj **uppdatering**, och väntar på att konfigurations- och verifiering för tjänsten för att avsluta.

    ![Skärmbild av dialogrutan med knappen Uppdatera markerat](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > Uppdateringsprocessen använder Ambari för att ändra konfigurationen för Hortonworks begränsat till vad som förväntas av Data Lake-verktyg för Visual Studio.

6. När verifieringen är klar väljer du **Slutför** att slutföra konfigurationen.
    ![Skärmbild av dialogrutan med Slutför markerat](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]
     > Beroende på din utvecklingsmiljö och mängden minne som allokerats till den virtuella datorn, kan det ta flera minuter att konfigurera och verifiera tjänsterna.

När du har följt de här stegen, nu har du en **lokala HDInsight-kluster** transaktion i Server Explorer under den **HDInsight** avsnitt.

## <a name="write-a-hive-query"></a>Skriv en Hive-fråga

Strukturen innehåller ett SQL-liknande frågespråk (HiveQL) för att arbeta med strukturerade data. Använd följande steg för att lära dig hur du kör på begäran frågor mot det lokala klustret.

1. I **Server Explorer**, högerklicka på posten för det lokala klustret som du lagt till tidigare och välj sedan **skriva en Hive-fråga**.

    ![Skärmbild av Server Explorer med Skriv en Hive-fråga som markerats](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Ett nytt frågefönster visas. Här kan du snabbt skriva och skicka en fråga till det lokala klustret.

2. I frågefönstret nytt anger du följande kommando:

        select count(*) from sample_08;

    Om du vill köra frågan **skicka** längst upp i fönstret. Lämna de andra värdena (**Batch** och namn på servern) sina standardvärden.

    ![Skärmbild av frågefönstret med knappen Skicka markerat](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Du kan också använda den nedrullningsbara menyn bredvid **skicka** att välja **Avancerat**. Avancerade alternativ kan du ange ytterligare alternativ när du har skickat jobbet.

    ![Dialogrutan Skicka skript skärmbild](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. När du skickar frågan visas jobbstatus. Jobbet har statusen visar information om jobbet som bearbetas av Hadoop. **Jobbets status** ger status för jobbet. Tillståndet uppdateras regelbundet och du kan använda den här ikonen för att uppdatera tillståndet manuellt.

    ![Skärmbild av jobbet dialogrutan vy med jobbstatus markerat](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Efter den **jobbstatus** ändras till **avslutad**, dirigeras acykliska diagram (DAG) visas. Det här diagrammet beskrivs körning av sökväg som fastställts av Tez vid bearbetning av Hive-fråga. Tez är standard Körningsmotor för Hive i det lokala klustret.

    > [!NOTE]
    > Tez är standardalternativet när du använder Linux-baserade HDInsight-kluster. Det är inte standard på Windows-baserade HDInsight. Du använder den, måste du lägga till raden `set hive.execution.engine = tez;` till början av Hive-fråga.

    Använd den **Jobbutdata** länken om du vill visa resultatet. I det här fallet är det 823, antalet rader i tabellen sample_08. Du kan visa diagnostikinformation om jobbet genom att använda den **Jobblogg** och **hämta YARN-logg** länkar.

4. Du kan också köra Hive-jobb interaktivt genom att ändra den **Batch** till **interaktiv**. Välj sedan **kör**.

    ![Skärmbild av interaktiva och kör knappar markerat](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    En interaktiv fråga strömmar utdataloggen genereras under bearbetningen till den **HiveServer2 utdata** fönster.

    > [!NOTE]
    > Informationen är samma som är tillgänglig från den **Jobblogg** länka när ett jobb har slutförts.

    ![Skärmbild av logg för utdata](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Skapa ett Hive-projekt

Du kan också skapa ett projekt som innehåller flera Hive-skript. Använd ett projekt när du har relaterade skript eller vill lagra skript i ett system för version.

1. I Visual Studio väljer **filen**, **ny**, och sedan **projekt**.

2. I listan över projekt, expandera **mallar**, expandera **Azure Data Lake**, och välj sedan **HIVE (HDInsight)**. Välj i listan över mallar **Hive exempel**. Ange ett namn och plats och välj sedan **OK**.

    ![Skärmbild av nytt projekt fönster, med Azure Data Lake, HIVE, Hive exempel och OK markerat](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

Den **Hive exempel** projektet innehåller två skript **WebLogAnalysis.hql** och **SensorDataAnalysis.hql**. Du kan skicka dessa skript med hjälp av samma **skicka** längst upp i fönstret.

## <a name="create-a-pig-project"></a>Skapa ett Pig-projekt

Medan Hive innehåller en SQL-liknande språk för att arbeta med strukturerade data, fungerar Pig genom att utföra omvandlingar på data. Pig innehåller ett språk (Pig Latin) som hjälper dig att utveckla en pipeline omformningar. Följ dessa steg om du vill använda Pig med det lokala klustret:

1. Öppna Visual Studio och välj **filen**, **ny**, och sedan **projekt**. I listan över projekt, expandera **mallar**, expandera **Azure Data Lake**, och välj sedan **Pig (HDInsight)**. Välj i listan över mallar **Pig programmet**. Ange ett namn, plats, och välj sedan **OK**.

    ![Skärmbild av nytt projekt fönster, med Azure Data Lake, Pig, Pig program och OK markerat](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

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

    Pig använder ett annat språk än Hive, hur du kör jobben är konsekventa mellan både språk via den **skicka** knappen. Att välja i listrutan bredvid **skicka** visar en dialogruta med avancerade skicka för Pig.

    ![Dialogrutan Skicka skript skärmbild](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. Jobbets status och utdata visas också, samma som en Hive-fråga.

    ![Skärmbild av ett avslutat Pig-projekt](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Visa jobb

Data Lake-verktyg kan du enkelt se information om jobb som har körts på Hadoop. Använd följande steg om du vill visa de jobb som har körts på det lokala klustret.

1. Från **Server Explorer**, högerklicka på det lokala klustret och välj sedan **visa jobb**. En lista över jobb som har skickats till klustret visas.

    ![Skärmbild av Server Explorer, med Visa jobb markerat](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Välj något att visa Jobbinformationen för jobben i listan.

    ![Skärmbild av jobbet webbläsare med ett av jobben markerat](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    Informationen liknar det som visas när du har kört en Hive- eller Pig fråga, inklusive länkar om du vill visa utdata och logga information.

3. Du kan också ändra och skicka jobbet från den här.

## <a name="view-hive-databases"></a>Visa Hive-databaser

1. I **Server Explorer**, expandera den **lokala HDInsight-kluster** post, och expandera sedan **Hive-databaser**. Den **standard** och **xademo** databaser i det lokala klustret visas. Expandera en databas visar tabeller i databasen.

    ![Skärmbild av Server Explorer, med databaser expanderas](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Expandera en tabell visar kolumnerna för tabellen. Högerklicka på en tabell för att snabbt visa data, och välj **visa de 100 översta raderna**.

    ![Skärmbild av Server Explorer, med tabellen expanderas och visa de 100 översta raderna markerat](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Egenskaper för databas och tabell

Du kan visa egenskaperna för en databas eller tabell. Att välja **egenskaper** visar information för det valda objektet i egenskapsfönstret. Till exempel se informationen som visas i följande skärmbild:

![Skärmbild av egenskapsfönstret](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Skapa en tabell

Högerklicka på en databas för att skapa en tabell, och välj sedan **Create Table**.

![Skärmbild av Server Explorer, med Create Table markerat](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Du kan sedan skapa tabellen med hjälp av ett formulär. Du kan se rådata HiveQL som används för att skapa tabellen längst ned i följande skärmbild.

![Skärmbild av formuläret som används för att skapa en tabell](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Nästa steg

* [Learning linor av sandlådan Hortonworks](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop-vägledning - komma igång med HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

---
title: Data Lake verktyg för Visual Studio med Hortonworks sandbox – Azure HDInsight
description: Lär dig hur du använder Azure Data Lake verktyg för Visual Studio med sand boxen Hortonworks som körs i en lokal virtuell dator. Med dessa verktyg kan du skapa och köra Hive-och gris-jobb i sand boxen och visa utdata och historik för jobb.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 1edab776ec93f057ebf7e37ac887747f86a27db9
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098780"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Använd Azure Data Lake verktyg för Visual Studio med sandbox-Hortonworks

Azure Data Lake innehåller verktyg för att arbeta med allmänna Apache Hadoop-kluster. Det här dokumentet innehåller de steg som krävs för att använda Data Lake-verktyg med sandbox-Hortonworks som körs på en lokal virtuell dator.

Med Hortonworks sandbox kan du arbeta med Hadoop lokalt i utvecklings miljön. När du har utvecklat en lösning och vill distribuera den i stor skala kan du sedan flytta till ett HDInsight-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Hortonworks sandbox, som körs i en virtuell dator i utvecklings miljön. Det här dokumentet har skrivits och testats med sand boxen som körs i Oracle VirtualBox. Information om hur du konfigurerar sandbox finns i avsnittet [Kom igång med Hortonworks sandbox.](hadoop/apache-hadoop-emulator-get-started.md) handling.

* Visual Studio.

* [Azure SDK för .net](https://azure.microsoft.com/downloads/) 2.7.1 eller senare.

* [Azure Data Lake verktyg för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Konfigurera lösen ord för sandbox

Kontrol lera att sandbox-Hortonworks körs. Följ sedan stegen i dokumentet [Kom igång i Hortonworks sandbox](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) . De här stegen konfigurerar lösen ordet för SSH `root` -kontot och Apache Ambari `admin` -kontot. Dessa lösen ord används när du ansluter till sand boxen från Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Ansluta verktygen till sand boxen

1. Öppna Visual Studio, Välj **Visa**och välj sedan **Server Explorer**.

2. Högerklicka på posten **HDInsight** från **Server Explorer**och välj sedan **Anslut till HDInsight-emulatorn**.

    ![Server Explorer, med anslutning till HDInsight-Emulator markerat](./media/hdinsight-hadoop-emulator-visual-studio/connect-hdinsight-emulator.png)

3. I dialog rutan **Anslut till HDInsight-Emulator** anger du det lösen ord som du konfigurerade för Ambari.

    ![Skärm bild av dialog rutan med text rutan Ambari-lösenord markerad](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Välj **Nästa** för att fortsätta.

4. Använd fältet **lösen ord** för att ange det lösen ord som du `root` har konfigurerat för kontot. Lämna övriga fält till standardvärdet.

    ![Skärm bild av dialog rutan med text rutan rot lösen ord markerad](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password1.png)

    Välj **Nästa** för att fortsätta.

5. Vänta tills tjänsterna har verifierats. I vissa fall kan verifieringen Miss lyckas och du ombeds att uppdatera konfigurationen. Om valideringen Miss lyckas väljer du **Uppdatera**och väntar på att konfigurationen och verifieringen av tjänsten ska slutföras.

    ![Skärm bild av dialog rutan med knappen Uppdatera markerad](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update-window.png)

    > [!NOTE]  
    > Vid uppdaterings processen används Ambari för att ändra Hortonworks sandbox-konfigurationen till vad som förväntas av Data Lake verktyg för Visual Studio.

6. När verifieringen är klar väljer du **Slutför** för att slutföra konfigurationen.
    ![Skärm bild av dialog rutan med knappen Slutför markerad](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect-dialog.png)

     >[!NOTE]  
     > Beroende på hastigheten i utvecklings miljön och mängden minne som allokeras till den virtuella datorn kan det ta flera minuter att konfigurera och validera tjänsterna.

När du har gjort de här stegen har du nu en **lokal HDInsight-kluster** post i Server Explorer under avsnittet **HDInsight** .

## <a name="write-an-apache-hive-query"></a>Skriv en Apache Hive fråga

Hive innehåller ett SQL-liknande frågespråk (HiveQL) för att arbeta med strukturerade data. Använd följande steg för att lära dig hur du kör frågor på begäran mot det lokala klustret.

1. I **Server Explorer**högerklickar du på posten för det lokala klustret som du har lagt till tidigare och väljer sedan **Skriv en Hive-fråga**.

    ![Skärm bild av Server Explorer, med Skriv en Hive-fråga markerad](./media/hdinsight-hadoop-emulator-visual-studio/write-apache-hive-query.png)

    Ett nytt frågefönster visas. Här kan du snabbt skriva och skicka en fråga till det lokala klustret.

2. I fönstret ny fråga anger du följande kommando:

        select count(*) from sample_08;

    Om du vill köra frågan väljer du **Skicka** överst i fönstret. Lämna de andra värdena (**batch** -och Server namn) till standardvärdena.

    ![Skärm bild av frågefönstret med knappen Skicka markerad](./media/hdinsight-hadoop-emulator-visual-studio/query-window-submit-hive.png)

    Du kan också använda den nedrullningsbara menyn bredvid **Skicka** för att välja **Avancerat**. Med avancerade alternativ kan du ange ytterligare alternativ när du skickar jobbet.

    ![Skärm bild av dialog rutan Skicka skript i registrerings data filen](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-hive.png)

3. När du har skickat frågan visas jobb statusen. Jobb status visar information om jobbet när det bearbetas av Hadoop. **Jobb status** ger jobbets status. Statusen uppdateras regelbundet, eller så kan du använda uppdaterings ikonen för att uppdatera statusen manuellt.

    ![Skärm bild av dialog rutan jobb visning med jobb status markerat](./media/hdinsight-hadoop-emulator-visual-studio/job-view-dialog-box1.png)

    När **jobbets tillstånd** ändras till **avslutad**visas ett dirigerat acykliska diagram (dag). Det här diagrammet beskriver den körnings Sök väg som avgjordes av Tez vid bearbetning av Hive-frågan. Tez är standard körnings motorn för Hive i det lokala klustret.

    > [!NOTE]  
    > Apache Tez är också standard när du använder Linux-baserade HDInsight-kluster. Det är inte standard på Windows-baserade HDInsight. Om du vill använda den där måste du lägga till `set hive.execution.engine = tez;` raden i början av din Hive-fråga.

    Använd länken **jobbets utdata** om du vill visa utdata. I det här fallet är det 823, antalet rader i sample_08-tabellen. Du kan visa diagnostikinformation om jobbet med hjälp av **jobb loggen** och **Hämta garn logg** länkar.

4. Du kan också köra Hive-jobb interaktivt genom att ändra **batch** - **fältet till interaktivt**. Välj sedan **Kör**.

    ![Skärm bild av interaktiva och kör knappar markerade](./media/hdinsight-hadoop-emulator-visual-studio/hdi-interactive-query.png)

    En interaktiv fråga strömmar den utgående loggen som genereras under bearbetningen till **HiveServer2-utdatafönstret** .

    > [!NOTE]  
    > Informationen är samma som är tillgänglig från **jobb logg** länken när ett jobb har avslut ATS.

    ![Skärm bild av HiveServer2-utdata](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output-box.png)

## <a name="create-a-hive-project"></a>Skapa ett Hive-projekt

Du kan också skapa ett projekt som innehåller flera Hive-skript. Använd ett projekt när du har relaterade skript eller vill lagra skript i ett versions kontroll system.

1. I Visual Studio väljer du **Arkiv**, **nytt**och sedan **projekt**.

2. I listan med projekt expanderar du **mallar**, expanderar **Azure Data Lake**och väljer sedan **HIVE (HDInsight)** . Välj **Hive-exempel**i listan med mallar. Ange ett namn och en plats och välj sedan **OK**.

    ![Nytt projekt fönster med Azure Data Lake, Hive-exempel och OK](./media/hdinsight-hadoop-emulator-visual-studio/new-apache-hive-project.png)

**Hive-exempelprojektet** innehåller två skript, **WebLogAnalysis. HQL** och **SensorDataAnalysis. HQL**. Du kan skicka dessa skript genom att använda samma **Skicka** -knapp överst i fönstret.

## <a name="create-an-apache-pig-project"></a>Skapa ett Apache gris-projekt

Medan Hive innehåller ett SQL-liknande språk för att arbeta med strukturerade data, fungerar gris genom att utföra omvandlingar på data. I gris finns ett språk (gris Latin) som gör att du kan utveckla en pipeline för transformationer. Följ dessa steg om du vill använda gris med det lokala klustret:

1. Öppna Visual Studio och välj **Arkiv**, **nytt**och sedan **projekt**. I listan med projekt expanderar du **mallar**, expanderar **Azure Data Lake**och väljer sedan **gris (HDInsight)** . Välj **programmet gris**i listan med mallar. Ange ett namn, en plats och välj sedan **OK**.

    ![Skärm bild av nytt projekt fönster med Azure Data Lake, gris, gris program och OK markerat](./media/hdinsight-hadoop-emulator-visual-studio/new-apche-pig-project.png)

2. Ange följande text som innehållet i filen **script. gris** som skapades med det här projektet.

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

    Medan gris använder ett annat språk än Hive, är det konsekvent att köra jobben mellan båda språken genom att **Skicka** -knappen. Om du markerar List rutan bredvid **Skicka** visas en dialog ruta för att skicka en avancerad sändning för gris.

    ![Skärm bild av dialog rutan Skicka skript, gris](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-pig1.png)

3. Jobbets status och utdata visas också, samma som en Hive-fråga.

    ![Skärm bild av ett slutfört jobb i gris](./media/hdinsight-hadoop-emulator-visual-studio/completed-apache-pig.png)

## <a name="view-jobs"></a>Visa jobb

Med Data Lake verktyg kan du också enkelt visa information om jobb som har körts på Hadoop. Använd följande steg för att se de jobb som har körts i det lokala klustret.

1. Från **Server Explorer**högerklickar du på det lokala klustret och väljer sedan **Visa jobb**. En lista över jobb som har skickats till klustret visas.

    ![Skärm bild av Server Explorer med Visa jobb markerat](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-view-jobs.png)

2. I listan med jobb väljer du en för att visa jobb informationen.

    ![Skärm bild av jobb webbläsare, med ett av jobben markerat](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-job-details.png)

    Den information som visas liknar det du ser när du har kört en Hive-eller gris-fråga, inklusive länkar för att visa utdata och logg information.

3. Du kan också ändra och skicka jobbet igen härifrån.

## <a name="view-hive-databases"></a>Visa Hive-databaser

1. I **Server Explorer**, expandera den **lokala HDInsight-kluster** posten och expandera sedan **Hive-databaser**. **Standard** -och **xademo** -databaserna på det lokala klustret visas. Om du expanderar en databas visas tabellerna i databasen.

    ![Skärm bild av Server Explorer med expanderade databaser](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases-view.png)

2. Om du expanderar en tabell visas kolumnerna för tabellen. Om du snabbt vill visa data högerklickar du på en tabell och väljer **Visa de översta 100 raderna**.

    ![Server Explorer, med tabell expanderad och Visa de 100 markerade raderna](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-top-100-rows.png)

### <a name="database-and-table-properties"></a>Egenskaper för databas och tabell

Du kan visa egenskaperna för en databas eller tabell. Om du väljer **Egenskaper** visas information om det markerade objektet i fönstret Egenskaper. Se till exempel informationen som visas på följande skärm bild:

![Skärm bild av Fönstret Egenskaper](./media/hdinsight-hadoop-emulator-visual-studio/hdi-properties-window.png)

### <a name="create-a-table"></a>Skapa en tabell

Om du vill skapa en tabell högerklickar du på en databas och väljer sedan **Skapa tabell**.

![Skärm bild av Server Explorer med skapa tabell markerad](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-create-table.png)

Du kan sedan skapa tabellen med ett formulär. Längst ned på följande skärm bild kan du se den RAW-HiveQL som används för att skapa tabellen.

![Skärm bild av formuläret som används för att skapa en tabell](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form-box.png)

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om Hortonworks sand Box](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Apache Hadoop själv studie kurs – komma igång med HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

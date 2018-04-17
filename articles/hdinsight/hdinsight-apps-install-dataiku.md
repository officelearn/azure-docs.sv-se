---
title: Installera publicerade program - Dataiku DDS - Azure HDInsight | Microsoft Docs
description: Installera och använda Hadoop-programmet Dataiku DDS från tredje part.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: facee9187528fa0c6a74f71ee73636dc23ca35ba
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---dataiku-dds"></a>Installera programmet - Dataiku DDS

Den här artikeln beskriver hur du installerar och kör den [Dataiku DDS](https://www.dataiku.com/) publicerade Hadoop-program på Azure HDInsight. En översikt över HDInsight-programplattformen och en lista över tillgängliga oberoende leverantör (ISV) publicerade program finns i [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md). Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

## <a name="about-dataiku-dss"></a>Om Dataiku DSS

Dataiku [Data vetenskap Studio (DSS)](https://www.dataiku.com/dss/features/connectivity/), är en gemensam datavetenskap plattform som gör att data forskare att bygga och leverera Analyslösningar. Erbjudande DSS som ett HDInsight kan program du använda datavetenskap att bygga stordatalösningar och köra dem på enterprise-klass och skala.

Du kan använda DSS för att implementera en komplett analytiska lösning från och med datapåfyllning, förberedelse och bearbetning. En DSS-lösning kan även inkludera utbildning och använder machine learning-modeller, visualisering och sedan operationalizing.

Du kan installera DSS på HDInsight med Hadoop eller Spark-kluster. Du kan installera DSS på befintliga kluster som körs eller när du skapar nya kluster. DSS stöder också använda Azure Blob storage som en koppling för läsning av data.

Du kan använda DSS för att skapa projekt som sedan kan generera MapReduce eller Spark jobb. Dessa jobb körs som regelbundet MapReduce eller Spark jobb i HDInsight, så du kan skala kluster på begäran.

## <a name="prerequisites"></a>Förutsättningar

Om du vill installera den här appen på en ny HDInsight-kluster eller ett befintligt kluster, måste du ha följande konfiguration:

* Klustret tier(s): Standard, Premium
* Kluster-typer: Hadoop, Spark
* Kluster-versioner: 3.4, 3.5

## <a name="install-the-dataiku-dss-published-application"></a>Installera Dataiku DSS publicerat program

Stegvisa instruktioner om hur du installerar det här och andra tillgängliga ISV-program läsa [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md).

## <a name="launch-dataiku-dss"></a>Starta Dataiku DSS

1. Efter installationen kan du starta DSS från ditt kluster i Azure-portalen genom att gå till den **inställningar** fönstret klicka **program** under den **allmänna** kategori. Den installerade appar rutan visas de installerade programmen.

    ![Installerade Dataiku DSS app](./media/hdinsight-apps-install-dataiku/app.png)

2. När du väljer DSS på HDInsight finns en länk till webbsidan och sökväg för SSH-slutpunkt. Välj länken för WEBBSIDAN.

3. Först startas visas med ett formulär för att skapa ett nytt konto Dataiku gratis eller för att logga in på ett befintligt konto. Du har också möjlighet att starta en kostnadsfri utvärderingsversion 2 veckor av [Enterprise Edition](https://www.dataiku.com/dss/editions/). Nu har du möjlighet att fortsätter med att ange en licensnyckel för Enterprise Edition eller använda Community Edition.

4. När du har slutfört din valda licens alternativet visas för en inloggningsformulär. Ange standardautentiseringsuppgifterna visas innan inloggningsformuläret.

Följande steg ger en enkel demonstration.

1. [Hämta exempel order CSV](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv).

2. DSS-instrumentpanelen, Välj den **+** (nytt projekt) länk på den vänstra menyn för att skapa ett nytt projekt.

    ![Nytt projektlänk](./media/hdinsight-apps-install-dataiku/new-project.png)

3. I det nya projektformuläret, skriver du en **namn**. Den **projekt nyckeln** fylls automatiskt med ett föreslaget värde. Ange i det här fallet ”order”. Klicka på **skapa**.

    ![Nytt projekt formulär](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. Välj **+ importera din första DATAUPPSÄTTNINGEN** i den nya sidan för projektet.

    ![Filuppladdning](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. Välj **överför filerna** under den **filer** dataset-listan. Visas dialogrutan överför. Klicka på Lägg till en fil, Välj den `haiku_shirt_sales.csv` filen som du hämtade och validera.

6. Filen har överförts till DSS. Kontrollera om DSS identifieras CSV-format korrekt genom att klicka på knappen förhandsgranskning.

    ![Filuppladdning](./media/hdinsight-apps-install-dataiku/preview.png)

7. Importen är nästan perfekt. CSV-filen använder fliken avgränsare. Du kan se data som finns i tabellformat, med kolumner som kallas funktioner och rader som representerar observationer. Ett fel är att uppenbarligen filen innehöll en tom rad mellan huvudet och data. Åtgärda felet genom att ange `1` i den **hoppa över nästa rader** fältet.

    ![Spara](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. Namnge den nya datamängden. Ange **haiku_shirt_sales** i fältet på skärmen väljer den **skapa** knappen.

9. Du ser en tabellvy för dina data där du kan börja utforska den. För varje kolumn, bör du se att Dataiku vetenskap Studio har identifierat en datatyp i _blå_ – i det här fallet, Text, tal eller datum (oparsad). En mätare visar förhållandet mellan kolumnen för vilken värdena inte verkar passa typ (i rött) eller saknar (tom). I det här exemplet datamängden har avdelningen både tomma värden och ogiltiga data.

    ![Tabellvy](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>Datamanipulering

Verkliga data är nästan alltid rörigt och sällan den paketeras snyggt. Rensa data vanligtvis kräver en kedja av skript och associerade affärslogik. Dataiku DSS tillhandahåller en dedikerad **Lab** verktyg som gör denna uppgift mer användarvänlig.

1. Klicka på **Lab** i det övre högra hörnet.

    ![Knappen labb](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. Testlabb-fönstret öppnas. Labbet är där du upprepade gånger arbetar med dina dataset för att få ytterligare till den. Den här kursen visar Visual analys aspekt. Välj den **ny** nedan visuell analys. Du uppmanas att ange ett namn för din analys. Lämna standardnamnet för tillfället och klicka sedan på **skapa**.

    ![Skapa labb](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. Välj den **snabb kolumner stats** -knappen i det övre högra hörnet på sidan.

    ![Snabb kolumner statistik](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. Du ser statistik för datatyper och värden som visas i tidslinjen-baserade diagram under den **kolumner snabb överblick** fönstret.

    ![Snabb överblick av kolumner](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

Du kan nu utforska DSS med exempeldata. Du kan rensa och arbeta med data och skapa nya visualiseringar.

Djupgående självstudier läsa [Läs Dataiku DSS](https://www.dataiku.com/learn/).

## <a name="next-steps"></a>Nästa steg

* [Dataiku DSS dokumentationen](https://doc.dataiku.com/dss/latest/).
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserat HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): Lär dig hur du använder skriptåtgärd till att installera fler program.
* [Använd tom edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Lär dig hur du använder en tom kantnod för åtkomst till HDInsight-kluster och för att testa och värd för HDInsight-program.

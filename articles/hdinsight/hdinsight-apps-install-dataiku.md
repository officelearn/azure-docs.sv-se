---
title: Installera publicerad program – Dataiku DDS - Azure HDInsight
description: Installera och använda Apache Hadoop-program Dataiku DDS från tredje part.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: feb94f450621e999da01f77f0b5550258377d6c9
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51038271"
---
# <a name="install-published-application---dataiku-dds"></a>Installera publicerad program – Dataiku DDS

Den här artikeln beskrivs hur du installerar och kör den [Dataiku DDS](https://www.dataiku.com/) publicerade Apache Hadoop-program på Azure HDInsight. En översikt över HDInsight-programplattform och en lista över tillgängliga oberoende programvaruleverantör (ISV) publicerade program finns i [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md). Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

## <a name="about-dataiku-dss"></a>Om Dataiku DSS

Dataiku [Data Science Studio (DSS)](https://www.dataiku.com/dss/features/connectivity/), är en datavetenskapsplattform som gör det möjligt för datautvecklarteam att skapa och leverera analytiska lösningar. Program kan erbjuder DSS som ett HDInsight du använda datavetenskap att skapa lösningar för Stordata och köra dem i företagsklass och skala.

Du kan använda DSS för att implementera en komplett lösning för analytiska, från och med datainmatning, förberedelse och bearbetning. En DSS-lösning kan även innehålla utbildning och tillämpa machine learning-modeller, visualisering och sedan operationalisera.

Du kan installera DSS på HDInsight med Hadoop- eller Spark-kluster. Du kan installera DSS på befintliga kluster som körs eller när du skapar nya kluster. DSS har också stöd för Azure Blob storage som en anslutning för läsning av data.

Du kan använda DSS för att skapa projekt, som sedan kan generera MapReduce eller Spark-jobb. Dessa jobb körs som vanlig MapReduce eller Spark-jobb i HDInsight, så du kan skala kluster på begäran.

## <a name="prerequisites"></a>Förutsättningar

Om du vill installera den här appen på ett nytt HDInsight-kluster eller ett befintligt kluster, måste du ha följande konfiguration:

* Kluster tier(s): Standard, Premium
* Klustertyper: Hadoop, Spark
* Kluster-versioner: 3.4, 3.5

## <a name="install-the-dataiku-dss-published-application"></a>Installera Dataiku DSS publicerad program

Stegvisa instruktioner om hur du installerar det här och andra tillgängliga ISV-program finns [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md).

## <a name="launch-dataiku-dss"></a>Starta Dataiku DSS

1. Efter installationen kan du starta DSS från ditt kluster i Azure-portalen genom att gå till den **inställningar** och sedan klicka på **program** under den **Allmänt** kategori. Fönstret installerade appar visar en lista över de installerade programmen.

    ![Installerade Dataiku DSS-app](./media/hdinsight-apps-install-dataiku/app.png)

2. När du väljer DSS på HDInsight kan se du en länk till sidan och sökvägen för SSH-slutpunkten. Välj länken för WEBBSIDAN.

3. Första gången programmet startas visas ett formulär för att skapa ett nytt Dataiku-konto utan kostnad eller att logga in på ett befintligt konto. Du har också möjlighet att starta en kostnadsfri utvärderingsversion för 2 veckor av [Enterprise Edition](https://www.dataiku.com/dss/editions/). Från och med nu har du möjlighet att du fortsätter med att ange en licensnyckel för Enterprise Edition eller använda Community-utgåvan av.

4. När du har slutfört ditt valda Licensgrupperna alternativ, visas ett inloggningsformulär. Ange standardautentiseringsuppgifterna som visas innan inloggningsformuläret.

I följande steg finns en enkel demonstration.

1. [Ladda ned exemplet order CSV](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv).

2. DSS-instrumentpanelen väljer du den **+** (nytt projekt) länk på den vänstra menyn för att skapa ett nytt projekt.

    ![Länkar för nya projekt](./media/hdinsight-apps-install-dataiku/new-project.png)

3. Skriv i det nya projektformuläret, en **namn**. Den **Projektnyckel** fylls automatiskt med ett föreslaget värde. I det här fallet ange ”Orders”. Klicka på **skapa**.

    ![Nytt projekt-formulär](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. Välj **+ importera ditt första DATAUPPSÄTTNINGEN** i den nya sidan för projektet.

    ![Filuppladdning](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. Välj **ladda upp dina filer** under den **filer** Datauppsättningslista. Visas dialogrutan för filöverföring. Klicka på Lägg till en fil, Välj den `haiku_shirt_sales.csv` filen som du hämtade och validera.

6. Filen har överförts till DSS. Kontrollera om DSS identifieras CSV-format korrekt genom att klicka på knappen.

    ![Filuppladdning](./media/hdinsight-apps-install-dataiku/preview.png)

7. Importen är nästan perfekt. CSV-filen använder fliken avgränsare. Du kan se data är i ett tabellformat med kolumnrubrikerna funktioner och rader som representerar observationer. Ett fel är att uppenbarligen fil som innehåller en tom rad mellan huvud- och data. Du kan åtgärda det här felet genom att ange `1` i den **hoppa över nästa rader** fält.

    ![Spara](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. Namnge den nya datauppsättningen. Ange **haiku_shirt_sales** i fältet på skärmen och markera den **skapa** knappen.

9. Du ser en tabellvy över dina data där du kan börja utforska den. För varje kolumn, bör du se att Dataiku Science Studio har identifierat en datatyp i _blå_ – i det här fallet, Text, tal eller datum (neanalyzovanou). En mätare visar förhållandet mellan kolumnen som värdena inte verkar matchar inte (i rött) eller saknar (tomt). I den här exempel-datauppsättningen har avdelningen både tomma värden och ogiltiga data.

    ![Tabellvy](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>Datamanipulering

Verkliga data är nästan alltid röriga och data som sällan är det snyggt paketerat. Rensa data som vanligtvis kräver en kedja av skript och associerade affärslogik. Dataiku DSS ger en dedikerad **Lab** verktyg som gör denna uppgift mer användarvänlig.

1. Klicka på **Lab** i det övre högra hörnet.

    ![Labb-knappen](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. Fönstret Lab öppnas. Labbet är där du iterativt arbetar med din datauppsättning för att få ytterligare i den. Den här kursen visar aspekt för visuella analyser. Välj den **New** knappen under visuella analyser. Du uppmanas att ange ett namn för din analys. Lämna standardnamnet för tillfället och klicka sedan på **skapa**.

    ![Skapa labb](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. Välj den **snabb kolumner stats** knappen i det övre högra hörnet på sidan.

    ![Snabb kolumner stats](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. Du kan se statistik för datatyper och värden som visas i tidslinjen-baserade diagram under den **kolumner snabb överblick** fönstret.

    ![Snabb kolumnvy](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

Nu kan du utforska DSS med exempeldata. Du kan rensa och arbeta med data och skapa nya visualiseringar.

Fler djupgående självstudier läsa [Läs Dataiku DSS](https://www.dataiku.com/learn/).

## <a name="next-steps"></a>Nästa steg

* [Dataiku DSS dokumentation](https://doc.dataiku.com/dss/latest/).
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md): Lär dig hur du använder skriptåtgärd till att installera fler program.
* [Använda tomma kantnoder i HDInsight](hdinsight-apps-use-edge-node.md): Lär dig hur du använder en tom edge-nod för att komma åt HDInsight-kluster och för testning och som är värd för HDInsight-program.

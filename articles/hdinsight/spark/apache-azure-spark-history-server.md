---
title: Använda de utökade funktionerna i Apache Spark History Server för att felsöka appar - Azure HDInsight
description: Använd de utökade funktionerna i Apache Spark History Server för att felsöka och diagnostisera Spark-program - Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 5cf1986711479f7330b0cd477744d9f4e2ac6459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548942"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Använda de utökade funktionerna i Apache Spark History Server för att felsöka och diagnostisera Spark-program

Den här artikeln visar hur du använder de utökade funktionerna i Apache Spark History Server för att felsöka och diagnostisera slutförda eller köra Spark-program. Tillägget innehåller fliken **Data,** fliken **Diagram** och fliken **Diagnos.** På fliken **Data** kan du kontrollera in- och utdata för Spark-jobbet. På fliken **Diagram** kan du kontrollera dataflödet och spela upp jobbdiagrammet igen. På fliken **Diagnos** kan du referera till funktionerna **Data skeva,** **tidssnedställning**och **körverkningsanvändningsanalys.**

## <a name="get-access-to-the-spark-history-server"></a>Få tillgång till Spark History Server

Spark History Server är webbgränssnittet för färdiga och köra Spark-program. Du kan öppna den antingen från Azure-portalen eller från en URL.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Öppna användargränssnittet för Spark History Server från Azure-portalen

1. Öppna Spark-klustret från [Azure-portalen.](https://portal.azure.com/) Mer information finns i [Lista och visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Välj **Spark-historikserver**från **klusterinstrumentpaneler**. Ange administratörsautentiseringsuppgifterna för Spark-klustret när du uppmanas att göra det.

    ![Starta Spark History Server från Azure-portalen.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark-historikserver")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Öppna webbgränssnittet Spark History Server efter URL

Öppna Spark History Server genom `https://CLUSTERNAME.azurehdinsight.net/sparkhistory`att bläddra till , där **CLUSTERNAME** är namnet på Spark-klustret.

Webbgränssnittet Spark History Server kan se ut ungefär som den här bilden:

![Sidan Spark History Server.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Använda fliken Data i Spark History Server

Välj jobb-ID och välj sedan **Data** på verktygsmenyn för att se datavyn.

+ Granska **indata,** **utdata**och **tabelloperationer** genom att välja de enskilda flikarna.

    ![Dataflikar på sidan Data för Spark-program.](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Kopiera alla rader genom att markera knappen **Kopiera.**

    ![Kopiera data på sidan Spark-program.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Spara alla data som en . CSV-filen genom att välja **csv-knappen.**

    ![Spara data som en . CSV-fil från sidan Data for Spark Application.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Sök efter data genom att ange nyckelord i fältet **Sök.** Sökresultaten visas omedelbart.

    ![Sök data på sidan Data for Spark Application.](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Markera kolumnrubriken för att sortera tabellen. Välj plustecknet om du vill expandera en rad för att visa mer information. Markera minustecknet om du vill komprimera en rad.

    ![Datatabellen på sidan Data för Spark-program.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Ladda ner en enda fil genom att välja knappen **Partiell nedladdning** till höger. Den valda filen hämtas lokalt. Om filen inte längre finns öppnas en ny flik för att visa felmeddelandena.

    ![Datanedladdningsraden på sidan Data for Spark Application.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Kopiera en fullständig sökväg eller en relativ sökväg genom att välja alternativet **Kopiera fullständig sökväg** eller **Kopiera relativ sökväg,** som expanderas från hämtningsmenyn. För Azure Data Lake Storage-filer väljer du **Öppna i Azure Storage Explorer** för att starta Azure Storage Explorer och hitta mappen efter inloggning.

    ![Kopiera alternativen för fullständig sökväg och kopiera relativ sökväg på sidan Data för Spark-program.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Om det finns för många rader att visa på en enda sida markerar du sidnumren längst ned i tabellen för att navigera.

    ![Sidnummer på sidan Data för spark-program.](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Om du vill ha mer information håller du muspekaren över eller väljer frågetecknet bredvid **Data för Spark-program för** att visa verktygstipset.

    ![Få mer information från sidan Data for Spark Application.](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  Om du vill skicka feedback om problem väljer du **Ge oss feedback**.

    ![Ge feedback från sidan Data for Spark Application.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Använda fliken Diagram i Spark History Server

+ Välj jobb-ID och välj sedan **Diagram** på verktygsmenyn för att se jobbdiagrammet. Som standard visar diagrammet alla jobb. Filtrera resultatet med hjälp av listrutan **Jobb-ID.**

    ![Listrutan Jobb-ID på sidan Spark-program & jobbdiagram.](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ **Förloppet** är markerat som standard. Kontrollera dataflödet genom att välja **Läs** eller **skrivet** på listrutan **Visa.**

    ![Kontrollera dataflödet på sidan Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ Bakgrundsfärgen för varje uppgift motsvarar en värmekarta.

   ![Värmekartan på sidan Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Color |Beskrivning |
    |---|---|
    |Grön|Jobbet har slutförts.|
    |Orange|Aktiviteten misslyckades, men det påverkar inte det slutliga resultatet av jobbet. Dessa aktiviteter har dubblett- eller återförsöksinstanser som kan lyckas senare.|
    |Blå|Aktiviteten körs.|
    |Vit|Aktiviteten väntar på att köras eller så har scenen hoppat över.|
    |Röd|Aktiviteten misslyckades.|

     ![Köra en uppgift på sidan Spark-program & jobbdiagram.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     De överhoppade stadierna visas i vitt.
    ![En överhoppad uppgift på sidan Spark-program & jobbdiagram.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![En misslyckad aktivitet på sidan Spark-program & jobbdiagram.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > Uppspelning är tillgänglig för slutförda jobb. Välj knappen **Uppspelning** för att spela upp jobbet. Stoppa jobbet när som helst genom att välja stoppknappen. När ett jobb spelas upp visar varje aktivitet sin status efter färg. Uppspelning stöds inte för ofullständiga jobb.

+ Bläddra för att zooma in eller ut i jobbdiagrammet eller välj **Zoom för att få** plats så att den passar till skärmen.

    ![Välj Zooma för att få plats på sidan Spark-program & jobbdiagram.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ När aktiviteter misslyckas hovrar du över diagramnoden för att se verktygstipset och markerar sedan scenen för att öppna den på en ny sida.

    ![Visa verktygstipset på sidan Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ På sidan Spark Application & Job Graph visas verktygstips och små ikoner i faserna om uppgifterna uppfyller följande villkor:
  + Data skeva: Datalässtorlek > genomsnittlig dataläsa storlek för alla uppgifter i detta skede * 2 *och* datalässtorlek > 10 MB.
  + Tidsnedskjutning: Körningstid > genomsnittlig körningstid för alla aktiviteter i detta skede * 2 *och* körningstid > 2 minuter.

    ![Den skeva uppgiftsikonen på sidan Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ Jobbdiagramnoden visar följande information om varje steg:
  + ID
  + Namn eller beskrivning
  + Totalt aktivitetsnummer
  + Data läsa: summan av indatastorlek och blanda lässtorlek
  + Dataskrivning: summan av utdatastorlek och blandningsskrivningsstorlek
  + Körningstid: tiden mellan starttiden för det första försöket och slutförandetiden för det senaste försöket
  + Antal rader: summan av indataposter, utdataposter, blanda läsposter och blanda skrivposter
  + Förlopp

    > [!NOTE]  
    > Som standard visar jobbdiagramnoden information från det senaste försöket i varje steg (förutom körningstid för fas). Men under uppspelningen visar jobbdiagramnoden information om varje försök.

    > [!NOTE]  
    > För dataläsa och data skrivstorlekar, använder vi 1MB = 1000 KB = 1000 * 1000 byte.

+ Skicka feedback om problem genom att välja **Ge oss feedback**.

    ![Feedbackalternativet på sidan Spark-program & jobbdiagram.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Använda fliken Diagnos i Spark History Server

Välj jobb-ID och välj sedan Diagnos på **verktygsmenyn** för att se jobbdiagnosvyn. Fliken **Diagnos** innehåller **datasnedställning,** **tidsskepning**och **analys av körningsanvändning**.

+ Granska **datasnedställning,** **tidssventering**och analys av **körningsanvändning** genom att välja flikarna respektive.

    ![Fliken Data skeva på fliken Diagnos.](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Data skeva

Välj fliken **Data skeva.** Motsvarande skeva aktiviteter visas baserat på de angivna parametrarna.

#### <a name="specify-parameters"></a>Ange parametrar

I avsnittet **Ange parametrar** visas parametrarna som används för att identifiera datasnedställning. Standardregeln är: Läsdata för aktivitetsdata är större än tre gånger av den genomsnittliga aktivitetsdata som läses och aktivitetsdata som läses är mer än 10 MB. Om du vill definiera en egen regel för skeva aktiviteter kan du välja parametrar. Avsnitten **Skev scen** och **skeva diagram** uppdateras i enlighet med detta.

#### <a name="skewed-stage"></a>Skev scen

Avsnittet **Skevt steg** visar faser som har skeva aktiviteter som uppfyller de angivna villkoren. Om det finns mer än en skev aktivitet i en fas visas bara den mest skeva aktiviteten i avsnittet **Skeva** steg (det vill än den största informationen för datasnedställning).

![Större vy av fliken Data skeva på fliken Diagnos.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>Skeva diagram

När du markerar en rad i tabellen **Skeva scen** visas mer information om aktivitetsdistribution baserat på dataläsning och körningstid i tabellen **Skeva** fas. De skeva aktiviteterna markeras med rött och de normala aktiviteterna markeras i blått. För prestandaövervägande visar diagrammet upp till 100 exempeluppgifter. Uppgiftsinformationen visas på den nedre högra panelen.

![Skevdiagrammet för steg 10 i spark-användargränssnittet.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Tidsnedställning

På fliken **Tidsavskjutning** visas skeva aktiviteter baserat på körningstiden för aktiviteten.

#### <a name="specify-parameters"></a>Ange parametrar

I avsnittet **Ange parametrar** visas parametrarna som används för att identifiera tidssnedställning. Standardregeln är: Körningstiden för aktivitet är större än tre gånger genomsnittlig körningstid och körningstiden för aktiviteten är större än 30 sekunder. Du kan ändra parametrarna baserat på dina behov. I diagrammet **Skevt steg** och **skeva** visas motsvarande steg och uppgifter, precis som på fliken **Data skeva.**

När du väljer **Tidssnedställning**visas det filtrerade resultatet i avsnittet **Skevt steg,** enligt de parametrar som anges i avsnittet **Ange parametrar.** När du markerar ett objekt i avsnittet **Skev scen,** utformas motsvarande diagram i det tredje avsnittet och aktivitetsinformationen visas på den nedre högra panelen.

![Fliken Tidssnedställning på fliken Diagnos.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>Diagram för analys av köroranvändning

**Körordningsanvändningsdiagrammet** visar jobbets faktiska körektorallokering och körstatus.  

När du väljer **Analys av köruttrar användning**utarbetas fyra olika kurvor om executoranvändning: **Allokerade köror,** **Körutnratorer,** **inaktiva köror**och **Max executor-instanser**. Varje **executor som läggs till** eller **executor borttagen** händelse kommer att öka eller minska de allokerade utförarna. Du kan kontrollera **händelsetidslinjen** på fliken **Jobb** för fler jämförelser.

![Fliken Analys av köroranvändning på fliken Diagnos.](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

Välj färgikonen om du vill markera eller avmarkera motsvarande innehåll i alla utkast.

 ![Markera diagrammet på fliken Analys av köranvändning.](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="how-do-i-revert-to-the-community-version"></a>Hur återgår jag till communityversionen?

Gör följande om du vill återgå till communityversionen.

1. Öppna klustret i Ambari.
1. Navigera till **Spark2** > **Configs**.
1. Välj **Anpassade spark2-standardvärden**.
1. Välj **Lägg till egenskap ...**.
1. Lägg till **spark.ui.enhancement.enabled=false**och spara den sedan.
1. Egenskapen är falsk **nu.**
1. Välj **Spara** om du vill spara konfigurationen.

    ![Stäng av en funktion i Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Välj **Spark2** på den vänstra panelen. Välj sedan **Spark2 History Server**på fliken **Sammanfattning** .

    ![Sammanfattningsvyn i Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Om du vill starta om Spark History Server väljer du knappen **Startad** till höger om **Spark2 History Server**och väljer sedan **Starta om** på den nedrullningsbara menyn.

    ![Starta om Spark History Server i Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. Uppdatera webbgränssnittet för Spark History Server. Det kommer att återgå till community-versionen.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Hur laddar jag upp en Spark History Server-händelse för att rapportera det som ett problem?

Om du stöter på ett fel i Spark History Server gör du följande steg för att rapportera händelsen.

1. Hämta händelsen genom att välja **Hämta** i användargränssnittet för Spark History Server.

    ![Hämta händelsen i användargränssnittet för Spark History Server.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Välj **Ge oss feedback** från sidan Spark Application & Job **Graph.**

    ![Ge feedback på sidan Spark-program & jobbdiagram](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Ange titeln och en beskrivning av felet. Dra sedan ZIP-filen till redigeringsfältet och välj **Skicka nytt ärende**.

    ![Ladda upp och skicka in ett nytt nummer.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Hur uppgraderar jag en JAR-fil i ett snabbkorrigeringsscenario?

Om du vill uppgradera med en snabbkorrigering använder du `spark-enhancement.jar*`följande skript som uppgraderar .

**upgrade_spark_enhancement.sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

#### <a name="usage"></a>Användning

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Exempel

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Använda bash-filen från Azure-portalen

1. Starta [Azure-portalen](https://ms.portal.azure.com)och välj sedan ditt kluster.
2. Slutför en [skriptåtgärd](../hdinsight-hadoop-customize-cluster-linux.md) med följande parametrar.

    |Egenskap |Värde |
    |---|---|
    |Skripttyp|- Anpassad|
    |Namn|UppgraderaJar|
    |Bash skript URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Nodtyper|Chef, Arbetare|
    |Parametrar|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Skriptåtgärd för Skicka skript i Azure-portalen](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Kända problem

+ För närvarande fungerar Spark History Server endast för Spark 2.3 och 2.4.

+ In- och utdata som använder RDD visas inte på fliken **Data.**

## <a name="next-steps"></a>Nästa steg

+ [Hantera resurser för ett Apache Spark-kluster på HDInsight](apache-spark-resource-manager.md)
+ [Konfigurera Apache Spark-inställningar](apache-spark-settings.md)

## <a name="feedback"></a>Feedback

Om du har någon feedback eller stöter på några problem[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)när du använder det här verktyget, skicka ett mail till ( ).

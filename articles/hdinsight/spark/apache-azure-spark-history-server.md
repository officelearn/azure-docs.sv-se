---
title: Utökad Spark-historik Server för att felsöka appar – Azure HDInsight
description: Använd utökad Spark-historik Server för att felsöka och diagnostisera Spark-program – Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 7e9ab0e41086a4c9478f95c5a56754640feeab4e
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561838"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Använd utökad Apache Spark historik Server för att felsöka och diagnostisera Apache Spark program

Den här artikeln innehåller vägledning om hur du använder utökade Apache Spark historik Server för att felsöka och diagnostisera slutförda och köra Spark-program. Tillägget inkluderar fliken data och fliken och fliken diagnostik. På fliken **data** kan användare kontrol lera indata och utdata för Spark-jobbet. På fliken **diagram** kan användare kontrol lera data flödet och spela upp jobb diagrammet. På fliken **diagnos** kan användaren referera till **data skevning**, **tids skevning**och **användnings analys av utförar**.

## <a name="get-access-to-apache-spark-history-server"></a>Få åtkomst till Apache Spark historik Server

Apache Spark historik Server är webb gränssnittet för slutförd och körning av Spark-program.

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Öppna webb gränssnittet för Apache Spark historik Server från Azure Portal

1. Öppna Spark-klustret från [Azure Portal](https://portal.azure.com/). Mer information finns i [lista och Visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Välj **Spark historik Server**från **kluster instrument paneler**. När du uppmanas till det anger du administratörs behörighet för Spark-klustret.

    ![Portal, starta Spark historik Server](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark-historik Server")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Öppna webb gränssnittet för Spark historik server med URL

Öppna Spark historik servern genom att bläddra till `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` där kluster namn är namnet på ditt Spark-kluster.

Spark historik serverns webb gränssnitt kan se ut ungefär så här:

![HDInsight Spark historik Server](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="data-tab-in-spark-history-server"></a>Fliken data i Spark historik Server

Välj jobb-ID och välj sedan **data** på verktygs menyn för att hämta datavyn.

+ Granska åtgärder för **indata**, **utdata**och **tabeller** genom att välja flikarna separat.

    ![Data för Spark-Programflikar](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Kopiera alla rader genom att välja knappen **Kopiera**.

    ![Data för Spark-programkopia](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Spara alla data som CSV-fil genom att välja knappen **CSV**.

    ![Data för Spark-program spara](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Sök genom att ange nyckelord i fälts **ökningen**visas Sök resultatet omedelbart.

    ![Data för Spark-Programsökning](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Välj kolumn rubriken för att sortera tabellen, Välj plus tecknet för att expandera en rad för att visa mer information eller Välj minus tecknet för att komprimera en rad.

    ![Data för Spark program tabell](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Hämta en enskild fil genom att välja en knapp som är delvis att ladda **ned** till höger, kommer den valda filen att laddas ned till lokal, om filen inte finns längre, så öppnas en ny flik där fel meddelandena visas.

    ![Data för hämtnings raden för Spark-program](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Kopiera fullständig sökväg eller relativ sökväg genom att välja den **fullständiga sökvägen**kopiera den **relativa sökvägen** som expanderas från nedladdnings menyn. För Azure Data Lake Storage-filer startar du Azure Storage Explorer **i Azure Storage Explorer** och letar upp i mappen när du loggar in.

    ![Data för Spark-programmets kopierings Sök väg](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Välj talet under tabellen för att navigera sidor när för många rader ska visas på en sida.

    ![Sidan data för Spark-program](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Hovra över frågetecknet bredvid data för att Visa knapp beskrivningen, eller Välj frågetecknet om du vill ha mer information.

    ![Data för Spark-program, mer information](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Skicka feedback med problem genom att klicka på **ge oss feedback**.

    ![Spark-diagram ge oss feedback igen](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Fliken diagram på Apache Spark historik Server

Välj jobb-ID och klicka sedan på **Graph** på menyn verktyg för att hämta jobb diagram visningen.

+ Granska översikten av jobbet med det genererade jobb diagrammet.

+ Som standard visas alla jobb och det kan filtreras efter **jobb-ID**.

    ![Jobb-ID för Spark-program och jobb diagram](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ Som standard väljs **förlopp** , användaren kan kontrol lera data flödet genom att välja **Läs/skriv** i list rutan med **visning**.

    ![Visning av Spark-program och jobb diagram](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Graf-noden visas i färg som visar termisk karta.

    ![Termisk karta för Spark-program och jobb diagram](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Spela upp jobbet genom att välja **uppspelnings** knappen och sluta när som helst genom att klicka på stopp knappen. Uppgiften visas i färg för att visa olika status vid uppspelning:

    |Färg |Beskrivning |
    |---|---|
    |Grön|Jobbet har slutförts.|
    |Orange|Instanser av aktiviteter som misslyckats men inte påverkar det slutliga resultatet av jobbet. De här uppgifterna hade dubbla eller nya försök för instanser som kan lyckas senare.|
    |Blå|Uppgiften körs.|
    |Vit|Uppgiften väntar på att köras eller så har fasen hoppats över.|
    |Röd|Uppgiften misslyckades.|

    ![Färg exempel för Spark-program och jobb diagram, köra](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

    Det överhoppade steget visas i vitt.
    färg exempel för ![Spark-program och jobb diagram, hoppa över](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Färg exemplet för Spark-program och jobb diagram misslyckades](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

    > [!NOTE]  
    > Uppspelning för varje jobb tillåts. Uppspelning stöds inte för oavslutat jobb.

+ Musen rullar för att zooma in/ut i jobb diagrammet, eller klicka på **Zooma för att passa** för att få plats på skärmen.

    ![Spark-program och jobb diagram anpassas till](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Hovra över diagramvyn för att se knapp beskrivningen när det finns misslyckade uppgifter och klicka på scenen för att öppna sidan Stage.

    ![Verktygs tips för Spark-program och jobb diagram](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ På fliken jobb diagram visas en knapp beskrivning och en liten ikon som visas om de har aktiviteter som uppfyller följande villkor:
  + Data skevning: data Läs storlek > genomsnittlig data läsnings storlek för alla aktiviteter i det här steget * 2 och dataens Läs storlek > 10 MB.
  + Tids skevning: körnings tid > genomsnittlig körnings tid för alla aktiviteter i det här steget * 2 och körnings tiden > 2 minuter.

    ![Ikon för skevning av Spark-program och jobb diagram](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ I noden jobb diagram visas följande information om varje steg:
  + Identitet.
  + Namn eller beskrivning.
  + Totalt aktivitets nummer.
  + Lästa data: summan av storleken på indata och den blandade Läs storleken.
  + Data skrivning: summan av storleken på utdata och blanda Skriv storleken.
  + Körnings tid: tiden mellan start tiden för det första försöket och slut för ande tiden för det senaste försöket.
  + Radantal: summan av inmatnings poster, utgående poster, blandar Läs poster och blanda Skriv poster.
  + Pågår.

    > [!NOTE]  
    > Som standard visar noden jobb diagram information från det senaste försöket för varje steg (förutom körnings tid för fas), men under uppspelnings diagram noden visas information om varje försök.

    > [!NOTE]  
    > För data storlek för läsning och skrivning använder vi 1 MB = 1000 KB = 1000 * 1000 byte.

+ Skicka feedback med problem genom att välja **ge oss feedback**.

    ![Feedback om Spark-program och jobb diagram](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Fliken diagnos i Apache Spark historik Server

Välj jobb-ID och välj sedan **diagnos** på verktygs menyn för att hämta vyn jobb diagnos. Fliken diagnos innehåller **data skevning**, **tids skevning**och **användnings analys av utförar**.

+ Granska **data skevningen**, **tids skevningen**och **utförar användnings analys** genom att välja flikarna.

    ![SparkUI för att förvränga data igen](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Data skevning

Välj fliken **Dataskevning** , motsvarande skevade uppgifter visas baserat på de angivna parametrarna.

+ **Ange parametrar** – det första avsnittet visar parametrarna som används för att identifiera data skevning. Den inbyggda regeln är: aktiviteternas lästa data är större än tre gånger i den genomsnittliga aktivitets informationen som läses och aktivitets data som läses är större än 10 MB. Om du vill definiera en egen regel för skevade uppgifter kan du välja parametrar, den **skevade fasen**och **skeva TKN** -avsnittet kommer att uppdateras i enlighet med detta.

+ **Skevad fas** – det andra avsnittet visar steg, som har skevade uppgifter som uppfyller de villkor som anges ovan. Om det finns fler än en skevad aktivitet i ett stadium, visar den skevade scen tabellen bara den mest sneda aktiviteten (t. ex. största data för data skevning).

    ![sparkui för att förvränga data](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Skeva diagram** – när en rad i tabellen skeva steg är markerad visar skev diagrammet mer information om aktivitets distributioner baserat på data Läs-och körnings tid. De sneda aktiviteterna markeras i rött och normala aktiviteter markeras i blått. För prestanda hänsyn visar diagrammet bara upp till 100 exempel aktiviteter. Uppgifts informationen visas i den högra panelen.

    ![sparkui skeva diagram för steg 10](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Tids skevning

På fliken **tids skevning** visas skevade uppgifter utifrån aktivitetens körnings tid.

+ **Ange parametrar** – det första avsnittet visar parametrarna som används för att identifiera tids skevning. Standard kriteriet för att identifiera tids skevning är: aktivitets körnings tiden är större än tre gånger i genomsnittlig körnings tid och uppgifts körnings tid är längre än 30 sekunder. Du kan ändra parametrarna utifrån dina behov. Det **sneda steget** och **skeva diagrammet** visar motsvarande steg och aktivitets information precis som fliken **dataskevning** ovan.

+ Välj **tids skevning**och sedan visas det filtrerade resultatet i avsnittet **skevad fas** enligt parametrarna som anges i avsnittet **Ange parametrar**. Markera ett objekt i avsnittet **skevat Stadium** . därefter skapas motsvarande diagram i section3 och uppgifts informationen visas i den högra panelen.

    ![sparkui diagnos tid skeva avsnitt](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Användnings analys av utförar

Användnings diagrammet för utförar visualiserar Spark-jobbets faktiska utförar-allokering och körnings status.  

+ Välj **utförar-användnings analys**, och sedan fyra typer av kurvor om utförar-användning är utkast, inklusive **allokerade körningar**, **körnings**körningar, **inaktiva körningar**och **högst utförar-instanser**. För allokerade körningar ökar eller minskar de allokerade körningarna genom att varje "utförar tillagd" eller "utförar borttagen"-händelse ökar eller minskar de allokerade körningarna. du kan kontrol lera "händelsens tids linje" på fliken "jobb" för mer

    ![fliken sparkui-diagnostik](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ Välj färg ikonen för att markera eller avmarkera motsvarande innehåll i alla utkast.

    ![sparkui-diagnostik Välj diagram](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>FAQ

### <a name="1-revert-to-community-version"></a>1. återgå till community-versionen

Gör så här om du vill återgå till community-versionen:

1. Öppna kluster i Ambari.
1. Navigera till **Spark2** > **configs** > **anpassade Spark2-standardvärden**.
1. Välj **Lägg till egenskap...** , Lägg till **Spark. UI. förbättring. enabled = false**, Spara.
1. Egenskapen anges till **false** nu.
1. Välj **Spara** för att spara konfigurationen.

    ![Apache Ambari-funktionen stängs av](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Välj **Spark2** i den vänstra panelen, under fliken **Sammanfattning** väljer du **Spark2 historik Server**.

    ![Apache Ambari Spark2 Summary-vy](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Starta om historik Server genom att välja **starta om** **Spark2 historik Server**.

    ![Spark2-historik för Apache Ambari-](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  
1. Uppdatera Spark historik serverns webb gränssnitt. det kommer att återställas till community-versionen.

### <a name="2-upload-history-server-event"></a>2. händelse vid uppladdning av historik Server

Om du kör till historik Server fel följer du stegen för att tillhandahålla händelsen:

1. Ladda ned händelse genom att välja **Hämta** i historik serverns webb gränssnitt.

    ![Hämtning av Spark2 historik Server](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Välj **ge oss feedback** från fliken Data/graf.

    ![Spark-diagram ge oss feedback](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Ange rubriken och beskrivningen av felet, dra zip-filen till redigerings fältet och klicka sedan på **Skicka nytt ärende**.

    ![exempel på Apache Spark-Filproblem](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. uppgradera jar-filen för snabb korrigerings scenario

Om du vill uppgradera med Hotfix använder du skriptet nedan som kommer att uppgradera Spark-Enhancement. jar *.

**upgrade_spark_enhancement. sh**:

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

**Användning**:

`upgrade_spark_enhancement.sh https://${jar_path}`

**Exempel**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

**Så här använder du bash-filen från Azure Portal**

1. Starta [Azure Portal](https://ms.portal.azure.com)och välj ditt kluster.
2. Slutför en [skript åtgärd](../hdinsight-hadoop-customize-cluster-linux.md) med följande parametrar:

    |Egenskap |Värde |
    |---|---|
    |Skript typ|– Anpassad|
    |Namn|UpgradeJar|
    |Bash-skript-URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Node-typ (er)|Head, Worker|
    |Parametrar|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Azure Portal skicka skript åtgärd](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Kända problem

+ För närvarande fungerar det bara för Spark 2,3 och 2,4-kluster.

+ In-/utdata-data med RDD visas inte i fliken data.

## <a name="next-steps"></a>Nästa steg

+ [Hantera resurser för ett Apache Spark kluster i HDInsight](apache-spark-resource-manager.md)
+ [Konfigurera Apache Spark inställningar](apache-spark-settings.md)

## <a name="contact-us"></a>Kontakta oss

Om du har feedback eller om du kommer över eventuella problem när du använder det här verktyget kan du skicka ett e-postmeddelande till ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).

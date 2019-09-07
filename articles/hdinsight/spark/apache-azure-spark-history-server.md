---
title: Utökad Spark-historik Server för att felsöka Spark-program – Azure HDInsight
description: Använd utökad Spark-historik Server för att felsöka och diagnostisera Spark-program – Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 9ba03f6b1e658e08c3d07d7ccb5e2a99e96fe69c
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736459"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Använd utökad Apache Spark historik Server för att felsöka och diagnostisera Apache Spark program

Den här artikeln innehåller vägledning om hur du använder utökade Apache Spark historik Server för att felsöka och diagnostisera slutförda och köra Spark-program. Tillägget inkluderar fliken data och fliken och fliken diagnostik. På fliken **data** kan användare kontrol lera indata och utdata för Spark-jobbet. På fliken **diagram** kan användare kontrol lera data flödet och spela upp jobb diagrammet. På fliken **diagnos** kan användaren referera till **data skevning**, **tids skevning** och **användnings analys av utförar**.

## <a name="get-access-to-apache-spark-history-server"></a>Få åtkomst till Apache Spark historik Server

Apache Spark historik Server är webb gränssnittet för slutförd och körning av Spark-program. 

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Öppna webb gränssnittet för Apache Spark historik Server från Azure Portal

1. Öppna Spark-klustret från [Azure Portal](https://portal.azure.com/). Mer information finns i [lista och Visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Från **snabb länkar**klickar du på **kluster instrument panel**och sedan på **Spark historik Server**. När du uppmanas till det anger du administratörs behörighet för Spark-klustret. 

    ![Spark-historik Server](./media/apache-azure-spark-history-server/launch-history-server.png "Spark-historik Server")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Öppna webb gränssnittet för Spark historik server med URL
Öppna Spark-historik servern genom att bläddra till följande URL, ersätta `<ClusterName>` med Spark-klustrets namn för kunden.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

Spark historik serverns webb gränssnitt ser ut så här:

![HDInsight Spark historik Server](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="data-tab-in-spark-history-server"></a>Fliken data i Spark historik Server
Välj jobb-ID och klicka sedan på **data** på verktygs menyn för att hämta datavyn.

+ Kontrol lera **indata**-, **utdata**-och **tabell åtgärder** genom att välja flikarna separat.

    ![Dataflikar](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ Kopiera alla rader genom att klicka på knappen **Kopiera**.

    ![Datakopiering](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ Spara alla data som CSV-fil genom att klicka på knappen **CSV**.

    ![Spara data](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ Sök genom att ange nyckelord i fälts **ökningen**visas Sök resultatet omedelbart.

    ![Datas ökning](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ Klicka på kolumn rubriken för att sortera tabellen, klicka på plus tecknet för att expandera en rad för att visa mer information eller klicka på minus tecknet för att komprimera en rad.

    ![Data tabell](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ Hämta en enskild fil genom att klicka på knappen del av fil **hämtningen** som placeras till höger. sedan laddas den valda filen ned till lokalt, om filen inte finns längre, öppnas en ny flik där fel meddelandena visas.

    ![Data hämtnings rad](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Kopiera fullständig sökväg eller relativ sökväg genom att välja den **fullständiga sökvägen**kopiera den **relativa sökvägen** som expanderas från nedladdnings menyn. För Azure Data Lake Storage-filer startar du Azure Storage Explorer **i Azure Storage Explorer** och letar upp i mappen när du loggar in.

    ![Sökväg för data kopiering](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Klicka på siffran under tabellen för att navigera sidor när för många rader ska visas på en sida. 

    ![Data sida](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ Hovra över frågetecknet bredvid data för att Visa knapp beskrivningen eller klicka på frågetecknet för att få mer information.

    ![Data information](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Skicka feedback med problem genom att klicka på **ge oss feedback**.

    ![Graph-feedback](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="graph-tab-in-apache-spark-history-server"></a>Fliken diagram på Apache Spark historik Server
Välj jobb-ID och klicka sedan på **Graph** på menyn verktyg för att hämta jobb diagram visningen.

+ Kontrol lera översikten av jobbet med det genererade jobb diagrammet. 

+ Som standard visas alla jobb och det kan filtreras efter **jobb-ID**.

    ![Graf-jobb-ID](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ Som standard väljs **förlopp** , användaren kan kontrol lera data flödet genom att välja **Läs/skriv** i list rutan med **visning**.

    ![diagram visning](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Graf-noden visas i färg som visar termisk karta.

    ![Graf-termisk karta](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Spela upp jobbet genom att klicka på **uppspelnings** knappen och sluta när som helst genom att klicka på stopp knappen. Uppgiften visas i färg för att visa olika status vid uppspelning:

  + Grön för lyckad: Jobbet har slutförts.
  + Orange för nya försök: Instanser av aktiviteter som misslyckats men inte påverkar det slutliga resultatet av jobbet. De här uppgifterna hade dubbla eller nya försök för instanser som kan lyckas senare.
  + Blå för att köra: Uppgiften körs.
  + Vit för att vänta eller hoppa över: Uppgiften väntar på att köras eller så har fasen hoppats över.
  + Rött för misslyckades: Uppgiften misslyckades.

    ![diagram färgs exempel, köra](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    Det överhoppade steget visas i vitt.
    ![diagram färg exempel, hoppa över](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![diagram färgs exempel, misslyckades](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]  
    > Uppspelning för varje jobb tillåts. Uppspelning stöds inte för oavslutat jobb.


+ Musen rullar för att zooma in/ut i jobb diagrammet, eller klicka på **Zooma för att passa** för att få plats på skärmen.
 
    ![diagram zoomning för att passa](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Hovra över diagramvyn för att se knapp beskrivningen när det finns misslyckade uppgifter och klicka på scenen för att öppna sidan Stage.

    ![diagram knapp Beskrivning](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ På fliken jobb diagram visas en knapp beskrivning och en liten ikon som visas om de har aktiviteter som uppfyller följande villkor:
  + Data skevning: data Läs storlek > genomsnittlig data läsnings storlek för alla aktiviteter i det här steget * 2 och dataens Läs storlek > 10 MB.
  + Tids skevning: körnings tid > genomsnittlig körnings tid för alla aktiviteter i det här steget * 2 och körnings tiden > 2 minuter.

    ![ikon för diagram skevning](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ I noden jobb diagram visas följande information om varje steg:
  + IDENTITET.
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

+ Skicka feedback med problem genom att klicka på **ge oss feedback**.

    ![Graph-feedback](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Fliken diagnos i Apache Spark historik Server
Välj jobb-ID och klicka sedan på **diagnos** på verktygs menyn för att hämta vyn jobb diagnos. Fliken diagnos innehåller **data skevning**, **tids skevning**och **användnings analys av utförar**.
    
+ Kontrol lera **data skevningen**, **tids skevningen**och **utförar användnings analys** genom att välja flikarna.

    ![Diagnostic-flikar](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Data skevning
Klicka på fliken **data skevning** så visas motsvarande skevade uppgifter baserat på de angivna parametrarna. 

+ **Ange parametrar** – det första avsnittet visar de parametrar som används för att identifiera data skevning. Den inbyggda regeln är: Lästa aktivitets data är större än tre gånger i Genomsnittligt aktivitets data och att lästa aktivitets data är större än 10 MB. Om du vill definiera en egen regel för skevade uppgifter kan du välja parametrar, den **skevade fasen**och **skeva TKN** -avsnittet kommer att uppdateras i enlighet med detta.

+ **Skevad fas** – det andra avsnittet visar steg som har skevade uppgifter som uppfyller de villkor som anges ovan. Om det finns fler än en skevad aktivitet i ett stadium visar den skevade scen tabellen bara den mest sneda aktiviteten (t. ex. de största data för data skevning).

    ![Data skev section2](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Skeva diagram** – när en rad i tabellen skeva steg är markerad visar skev diagrammet mer information om aktivitets distributioner baserat på data Läs-och körnings tid. De sneda aktiviteterna markeras i rött och normala aktiviteter markeras i blått. För prestanda hänsyn visar diagrammet bara upp till 100 exempel aktiviteter. Uppgifts informationen visas i den högra panelen.

    ![Data skev section3](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Tids skevning
På fliken **tids skevning** visas skevade uppgifter utifrån aktivitetens körnings tid. 

+ **Ange parametrar** – det första avsnittet visar de parametrar som används för att identifiera tids skevning. Standard kriteriet för att identifiera tids skevning är: aktivitetens körnings tid är större än 3 gånger den genomsnittliga körnings tiden och körnings tiden för aktiviteten är mer än 30 sekunder. Du kan ändra parametrarna utifrån dina behov. Det **sneda steget** och **skeva diagrammet** visar motsvarande steg och aktivitets information precis som fliken **dataskevning** ovan.

+ Klicka på **tids skevning**så visas det filtrerade resultatet i avsnittet **skevad fas** enligt parametrarna som anges i avsnittet **Ange parametrar**. Klicka på ett objekt i avsnittet **skevat Stadium** . därefter skapas motsvarande diagram i section3 och uppgifts informationen visas i den högra nedre panelen.

    ![Tids skev section2](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Användnings analys av utförar
Användnings diagrammet för utförar visualiserar Spark-jobbets faktiska utförar-allokering och körnings status.  

+ Klicka på **utförar användnings analys**, och sedan fyra typer av kurvor om utförar-användning är utkast, inklusive **allokerade körningar**, **körnings**körningar,**inaktiva körningar**och **högst utförar-instanser**. För allokerade körningar ökar eller minskar de allokerade körningarna genom att varje "utförar tillagd" eller "utförar borttagen"-händelse ökar eller minskar de allokerade körningarna. du kan kontrol lera "händelsens tids linje" på fliken "jobb" för mer

    ![Fliken körningar](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ Klicka på färg ikonen för att markera eller avmarkera motsvarande innehåll i alla utkast.

    ![Välj diagram](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="1-revert-to-community-version"></a>1. Återgå till community-version

Gör så här om du vill återgå till community-versionen:

1. Öppna kluster i Ambari. Klicka på **Spark2** i den vänstra panelen.
2. Klicka på fliken **konfigurationer** .
3. Expandera gruppen **anpassade spark2-defaults**.
4. Klicka på **Lägg till egenskap**, Lägg till **Spark. UI. förbättring. enabled = false**, Spara.
5. Egenskapen anges till **false** nu.
6. Klicka på **Spara** för att spara konfigurationen.

    ![funktionen stängs av](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. Klicka på **Spark2** i den vänstra panelen, under fliken **Sammanfattning** , klicka på **Spark2 historik Server**.

    ![Starta om server1](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. Starta om historik Server genom att klicka på **starta om** från **Spark2 historik Server**.

    ![Starta om server2](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Uppdatera Spark historik serverns webb gränssnitt. det kommer att återställas till community-versionen.

### <a name="2-upload-history-server-event"></a>2. Händelse för uppladdning av historik Server

Om du kör till historik Server fel följer du stegen för att tillhandahålla händelsen:
1. Ladda ned händelse genom att klicka på **Hämta** i historik serverns webb gränssnitt.

    ![Ladda ned händelse](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Klicka på **ge oss feedback** från fliken Data/graf.

    ![Graph-feedback](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Ange rubriken och beskrivningen av felet, dra zip-filen till redigerings fältet och klicka sedan på **Skicka nytt ärende**.

    ![fil problem](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Uppgradera jar-fil för snabb korrigerings scenario

Om du vill uppgradera med Hotfix använder du skriptet nedan som kommer att uppgradera Spark-Enhancement. jar *.

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

**Användning**: 

`upgrade_spark_enhancement.sh https://${jar_path}`

**Exempel**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar` 

**Så här använder du bash-filen från Azure Portal**

1. Starta [Azure Portal](https://ms.portal.azure.com)och välj ditt kluster.
2. Klicka på **skript åtgärder**och **skicka sedan ny**. Slutför formuläret **Skicka skript åtgärd** och klicka sedan på knappen **skapa** .
    
    + **Skript typ**: Välj **anpassad**.
    + **Namn**: Ange ett skript namn.
    + **Bash-skript-URI**: Ladda upp bash-filen till det privata klustret och kopiera sedan URL: en här. Du kan också använda den angivna URI: n.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

   + Kontrol lera **huvud** och **arbetare**.
   + **Parametrar**: ange parametrarna följer bash-användningen.

     ![Ladda upp snabb korrigering för logg eller uppgradering](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issues"></a>Kända problem

1.  För närvarande fungerar det bara för Spark 2,3 och 2,4-kluster.

2.  In-/utdata-data med RDD visas inte i fliken data.

## <a name="next-steps"></a>Nästa steg

* [Hantera resurser för ett Apache Spark kluster i HDInsight](apache-spark-resource-manager.md)
* [Konfigurera Apache Spark inställningar](apache-spark-settings.md)


## <a name="contact-us"></a>Kontakta oss

Om du har feedback eller om du stöter på andra problem när du använder det här verktyget kan du skicka ett e[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)-postmeddelande på ().

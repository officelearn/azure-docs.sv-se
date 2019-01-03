---
title: Använd utökat Spark-Historikserver att felsöka och diagnostisera Spark - program i Azure HDInsight
description: Använd utökat Spark-Historikserver att felsöka och diagnostisera Spark - program i Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: a896c949e1f05a5d9ee179fa475150ad8da34283
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792789"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Använd utökat Apache Spark-Historikserver att felsöka och diagnostisera Apache Spark-program

Den här artikeln innehåller vägledning om hur du använder utökade Apache Spark-Historikserver för att felsöka och diagnostisera slutförda och körs Spark-program. Tillägget innehåller data-fliken och graph flikarna och diagnos. På den **Data** fliken användare kan kontrollera inkommande och utgående data av Spark-jobbet. På den **Graph** fliken användare kan kontrollera data flödar och spela upp jobbdiagram. På den **diagnos** fliken som användaren kan referera till **Data förskjuta**, **Tidsförskjutningsintervallet** och **Executor användningsanalys**.

## <a name="get-access-to-apache-spark-history-server"></a>Få åtkomst till Apache Spark-Historikserver

Apache Spark-Historikserver är webbgränssnittet för slutförda och körs Spark-program. 

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Öppna Webbgränssnittet för Apache Spark historik Server från Azure-portalen

1. Från den [Azure-portalen](https://portal.azure.com/), öppna Spark-klustret. Mer information finns i [lista och visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Från **snabblänkar**, klickar du på **Klusterinstrumentpanel**, och klicka sedan på **Spark-Historikserver**. När du uppmanas, anger du autentiseringsuppgifter som administratör för Spark-klustret. 

    ![Spark-Historikserver](./media/apache-azure-spark-history-server/launch-history-server.png "Spark-Historikserver")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Öppna Spark historik Server webbgränssnittet efter URL
Öppna Spark-Historikserver genom att bläddra till följande URL ersätter <ClusterName> med Apache Spark-klusternamn av kunden.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

Spark-Historikserver web UI som ser ut som:

![HDInsight Spark-Historikserver](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="data-tab-in-spark-history-server"></a>Data-fliken i Spark-Historikserver
Välj jobb-ID och klicka sedan **Data** på Verktyg-menyn för att hämta datavyn.

+ Kontrollera den **indata**, **utdata**, och **tabellåtgärder** genom att välja flikarna separat.

    ![Dataflikar](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ Kopiera alla rader genom att klicka på knappen **kopiera**.

    ![Datakopiering](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ Spara alla data som CSV-fil genom att klicka på knappen **csv**.

    ![Data spara](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ Sök efter att ange nyckelord i fältet **Search**, sökresultatet visas omedelbart.

    ![Sök efter data](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ Klicka på kolumnrubriken om du vill sortera tabellen, klicka på plustecknet för att expandera en rad för att visa mer information eller klicka på minustecknet att komprimera en rad.

    ![Datatabell](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ Hämta enstaka fil genom att klicka på knappen **partiella hämta** som placeras till höger, och sedan på den valda filen laddas ned till lokal, om filen inte finns längre, öppnas en ny flik för att visa felmeddelanden.

    ![Ladda ned datarad](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Kopiera fullständig sökväg eller relativ sökväg genom att välja den **Kopiera fullständig sökväg**, **kopiera relativa sökväg** som utökas från hämta-menyn. För azure data lake storage filer **öppna i Azure Storage Explorer** startar Azure Storage Explorer och leta upp till mappen vid inloggning.

    ![Kopiera sökvägen](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Klicka på numret under tabellen att navigera sidor när för många rader för att visa i en sida. 

    ![Datasidan](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ Hovra över frågetecknet bredvid Data att visa knappbeskrivningen eller klicka på frågetecknet om du vill ha mer information.

    ![Data mer info](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Skicka feedback med problem genom att klicka på **ge oss feedback**.

    ![Graph-feedback](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="graph-tab-in-apache-spark-history-server"></a>Graph-fliken i Apache Spark-Historikserver
Välj jobb-ID och klicka sedan **Graph** på Verktyg-menyn för att få jobbet diagramvy.

+ Kontrollera översikt över ditt jobb genom att den genererade jobbdiagram. 

+ Som standard visas alla jobb och den kan filtreras efter **jobb-ID**.

    ![Graph jobb-ID](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ Som standard **förlopp** är markerat kan användaren Kontrollera dataflödet genom att välja **Läs/skriftliga** i listrutan för **visa**.

    ![diagrammet](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Graph noden visas i färg som visar den termiska kartan.

    ![Graph termisk karta](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Spela upp jobbet genom att klicka på den **uppspelning** knappen och stoppa när som helst genom att klicka på Stoppa. Aktiviteten visas i färg att visa status för olika när uppspelning:

    + Grön för lyckades: Jobbet har slutförts.
    + Orange för igen: Instanser av uppgifter som misslyckades men påverkar inte det slutgiltiga resultatet av jobbet. Dessa uppgifter hade duplicera eller försök instanser som kan lyckas senare.
    + Blå för att köra: Aktiviteten körs.
    + Vit för att vänta eller hoppades över: Uppgiften väntar på att köra eller scenen har hoppades över.
    + Det gick inte att rött för: Uppgiften misslyckades.

    ![Graph färgprov som körs](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    Överhoppade scenen visningen i vitt.
    ![Graph färgprov, hoppa över](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Graph färgprov misslyckades](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]  
    > Spela upp för varje jobb är tillåtet. Spela upp stöds inte för ej slutförda jobb.


+ Mus rullar för att zooma in och ut jobbdiagram, eller klicka på **zoomning så att de passar** så att de blir Anpassa till skärm.
 
    ![Graph zoomning så att de passar](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Hovra över graph noden finns i knappbeskrivningen när det finns misslyckade uppgifter och klicka på scenen för att öppna sidan steg.

    ![Graph knappbeskrivning](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Jobbet graph fliken faser har knappbeskrivning och lilla ikonen som visas om de uppgifter som uppfyller de nedan villkor:
    + Datasnedställning: data lässtorlek > genomsnittlig lässtorlek för alla aktiviteter i det här skedet * 2 och data läses storlek > 10 MB.
    + Tidssnedställning: körningstid > Genomsnittlig körningstid för alla aktiviteter i det här skedet * 2 och körningstid > 2 minuter.

    ![skeva Diagramikon](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ Jobbet graph-nod visas följande information i varje fas:
    + -ID.
    + Namn eller beskrivning.
    + Totalt antal uppgift tal.
    + Data läses: summan av Indatastorleken och shuffle lässtorlek.
    + Dataskrivning: summan av storlek och shuffle skriva storlek.
    + Körningstid: tiden mellan starttiden för det första försöket och tidsåtgången för det senaste försöket.
    + Radantal: summan av poster som indata, utdata poster, blanda läsa poster och blanda skriva poster.
    + Förlopp.

    > [!NOTE]  
    > Information från senaste försöket i varje fas (förutom scenen körningstid) visas i noden jobb graph som standard, men under uppspelning graph noden visas information för varje försök.

    > [!NOTE]  
    > För datastorleken för läsning och skrivning som vi använder 1 MB = 1 000 KB = 1 000 * 1 000 byte.

+ Skicka feedback med problem genom att klicka på **ge oss feedback**.

    ![Graph-feedback](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Fliken diagnostik i Apache Spark-Historikserver
Välj jobb-ID och klicka sedan **diagnos** på Verktyg-menyn för att få jobbet diagnos vy. Fliken diagnostik innehåller **Data förskjuta**, **Tidsförskjutningsintervallet**, och **Executor användningsanalys**.
    
+ Kontrollera den **Data förskjuta**, **Tidsförskjutningsintervallet**, och **Executor användningsanalys** genom att välja flikarna respektive.

    ![Du kan undersöka flikar](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Datasnedställning
Klicka på **Data förskjuta** fliken motsvarande skeva uppgifter visas baserat på de angivna parametrarna. 

+ **Ange parametrar** – det första avsnittet visas de parametrar som används för att identifiera Data skeva. Inbyggda regeln är: Uppgiften lästa är större än 3 gånger för den genomsnittliga uppgiftsdata läses och aktivitetsdata läsa är mer än 10MB. Om du vill definiera dina egna regel för skeva uppgifter kan du välja din parametrar, den **förvrängd scenen**, och **förskjuta Char** avsnittet kommer att uppdateras i enlighet med detta.

+ **Förvrängd scenen** -det andra avsnittet visar steg som har förvrängd uppgifter som uppfyller de kriterier som anges ovan. Om det finns fler än en skeva uppgift i ett steg, visar skeva mellanlagringstabellen endast mest skeva uppgift (t.ex. största data för data förskjuta).

    ![Data förskjuta sektion 2](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Ge skeva diagrammet** – när en rad i tabellen skeva steg väljs de skeva diagrammet visar flera distributioner aktivitetsinformation baserat på data som lästs in och körningstid. Skeva uppgifter markeras i rött och de normala uppgifterna markeras i blått. Diagrammet visar bara upp till 100 exempelaktiviteter prestanda överväger. Aktivitetsinformationen visas i höger panel.

    ![Data förskjuta avsnitt3](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Tid skeva
Den **Tidsförskjutningsintervallet** fliken visas skeva aktiviteter baserat på körningstid för uppgiften. 

+ **Ange parametrar** – det första avsnittet visas de parametrar som används för att identifiera Tidsförskjutningsintervallet. Standardkriterier för att identifiera tidssnedställning är: körningstid för aktiviteten är större än 3 gånger av Genomsnittlig körningstid och körningstid för aktiviteten är högre än 30 sekunder. Du kan ändra parametrarna efter dina behov. Den **förvrängd scenen** och **förskjuta diagrammet** visa motsvarande steg och uppgifter information precis som den **Data förskjuta** flik ovan.

+ Klicka på **Tidsförskjutningsintervallet**, filtrerat resultat visas i **förvrängd scenen** avsnittet enligt de parametrar som anges i avsnitt **ange parametrar**. Klicka på ett objekt i **förvrängd scenen** avsnittet motsvarande diagrammet upprättats i avsnitt3 och aktivitetsinformationen visas i höger panel.

    ![Tid skeva sektion 2](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Executor användningsanalys
Executor Se Användningsdiagram visualiserar Spark faktiska executor allokering och körs jobbstatusen.  

+ Klicka på **Executor användningsanalys**, och sedan fyra typer kurvor om executor användning sammanställs, inklusive **allokerade Executors**, **kör Executors**, **inaktiva Executors**, och **Max Executor instanser**. Om allokerade executors varje ”Executor har lagts till” eller ”Executor borttagen” händelse kommer öka eller minska allokerat executors kan du kontrollera ”händelse tidslinje” på ”jobbfliken” mer jämförelse.

    ![Executors fliken](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ Klicka på färgikonen för att markera eller avmarkera motsvarande innehållet i alla utkast.

    ![Välj diagrammet](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="1-revert-to-community-version"></a>1. Återgå till community-versionen

Om du vill återgå till community-versionen, gör du följande:

1. Öppna klustret i Ambari. Klicka på **Spark2** i vänster panel.
2. Klicka på **Peeringkonfigurationer** fliken.
3. Expandera gruppen **anpassade spark2-standardvärden**.
4. Klicka på **Lägg till egenskap**, lägga till **spark.ui.enhancement.enabled=false**, spara.
5. Egenskapen anger till **FALSKT** nu.
6. Klicka på **spara** att spara konfigurationen.

    ![funktionen stängs av](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. Klicka på **Spark2** i vänster panel, under **sammanfattning** fliken **Spark2 Historikserver**.

    ![Starta om server1](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. Starta om historik-servern genom att klicka på **starta om** av **Spark2 Historikserver**.

    ![Starta om server2](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Uppdatera Spark historik server-Webbgränssnittet, kommer att återställas till community-versionen.

### <a name="2-upload-history-server-event"></a>2. Ladda upp historik serverhändelse

Om du stöter på historik-serverfel, följer du stegen för att tillhandahålla händelsen:
1. Hämta händelse genom att klicka på **hämta** i historikserver webb-UI.

    ![Hämta händelse](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Klicka på **ge oss feedback** data/graph-fliken.

    ![Graph-feedback](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Ange rubrik och beskrivning av felet, dra zip-filen till redigeringsfält och sedan på **skicka nytt ärende**.

    ![problemet med filen](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Uppgradera jar-filen för snabbkorrigeringen scenario

Om du vill uppgradera med snabbkorrigering använda skriptet nedan som kommer att uppgraderas spark-enhancement.jar*.

**upgrade_spark_enhancement.SH**:

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

**Att använda bash filen från Azure-portalen**

1. Starta [Azure-portalen](https://ms.portal.azure.com), och välj ditt kluster.
2. Klicka på **skriptåtgärder**, sedan **Skicka ny**. Slutför den **skicka skriptåtgärd** formuläret och klicka sedan på **skapa** knappen.
    
    + **Skripttyp**: Välj **anpassad**.
    + **Namn på**: Ange ett skriptnamn.
    + **Bash-skript-URI: N**: Överför bash filen till privata klustret och sedan kopiera URL: en här. Du kan också använda den URI som angavs.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

    + Kontrollera **Head** och **Worker**.
    + **Parametrar**: Ange parametrarna-Följ bash-användning.

    ![Överför loggen eller uppgradera snabbkorrigering](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issues"></a>Kända problem

1.  För närvarande fungerar det bara för 2.3 Spark-kluster.

2.  Indata/utdata med RDD visas inte i data-fliken.

## <a name="next-steps"></a>Nästa steg

* [Hantera resurser för ett Apache Spark-kluster i HDInsight](apache-spark-resource-manager.md)
* [Konfigurera inställningar för Apache Spark](apache-spark-settings.md)


## <a name="contact-us"></a>Kontakta oss

Om du har feedback, eller om du stöter på andra problem när du använder det här verktyget, skicka ett e-postmeddelande på ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).

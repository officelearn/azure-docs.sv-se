---
title: Använd den utökade Spark-historikservern för att felsöka appar - Apache Spark i Azure Synapse
description: Använd den utökade Spark-historikservern för att felsöka och diagnostisera Spark-program i Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4f03033942517f4778192e0b12f84610df8fd469
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429218"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Använd utökad Apache Spark-historikserver för att felsöka och diagnostisera Apache Spark-program

Den här artikeln innehåller vägledning om hur du använder den utökade Apache Spark-historikservern för att felsöka och diagnostisera slutförda och köra Spark-program.

Tillägget innehåller en dataflik, diagramfliken och **Data** fliken Diagnos. På fliken **Diagram** visas dataflödet och uppspelningen av jobbdiagrammet. På fliken **Diagnos** visas **datasnedställning,** **tidssnedställning**och **analys av körningsanvändning**.

## <a name="access-the-apache-spark-history-server"></a>Få tillgång till Apache Spark-historikservern

Apache Spark-historikservern är webbanvändargränssnittet för färdiga och köraNdet-program. Du kan öppna webbgränssnittet för Apache Spark-historikservern från Azure Synapse Analytics.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Öppna webbgränssnittet Spark History Server från Apache spark-programnod

1. Öppna [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Klicka på **Övervaka**och välj sedan **Apache Spark-program**.

    ![Klicka på bildskärmen och välj sedan spark-program.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Markera ett program och öppna sedan **Loggfrågan** genom att klicka på den.

    ![Öppna loggfrågefönstret.](./media/apache-spark-history-server/open-application-window.png)

4. Välj **Spark-historikserver**, då visas webbgränssnittet för Spark History Server.

    ![Öppna sparkhistorikservern.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>Öppna webbgränssnittet Spark History Server från datanoden

1. Välj **Spark-historikserver** från utdatacellen För jobbkörning från din Azure Synapse Studio-anteckningsbok eller från statuspanelen längst ned i anteckningsboksdokumentet. Välj **Sessionsinformation**.

   ![Starta Spark-historikserver](./media/apache-spark-history-server/launch-history-server2.png "Starta Spark-historikserver")

2. Välj **Spark-historikserver** på panelen Bild ut.

   ![Starta Spark-historikserver](./media/apache-spark-history-server/launch-history-server.png "Starta Spark-historikserver")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Utforska fliken Data på Spark-historikservern

Välj jobb-ID för det jobb som du vill visa. Välj sedan **Data** på verktygsmenyn för att hämta datavyn. I det här avsnittet visas hur du utför olika uppgifter på fliken Data.

* Kontrollera **indata,** **utdata**och **tabelloperationer** genom att välja flikarna separat.

    ![Data för flikar i Spark-program](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Kopiera alla rader genom att välja **Kopiera**.

    ![Data för spark-programkopia](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Spara alla data som CSV-fil genom att välja **csv**.

    ![Data för Spark-program spara](./media/apache-spark-history-server/apache-spark-data-save.png)

* Sök genom att ange nyckelord i **fältSökning**. Sökresultaten visas omedelbart.

    ![Data för Spark-programsökning](./media/apache-spark-history-server/apache-spark-data-search.png)

* Markera kolumnrubriken för att sortera tabellen, markera plustecknet för att expandera en rad för att visa mer information eller markera minustecknet för att komprimera en rad.

    ![Data för spark-programtabell](./media/apache-spark-history-server/apache-spark-data-table.png)

* Ladda ner en enda fil genom att välja **Partiell nedladdning**. Den valda filen hämtas till lokal. Om filen inte längre finns visas en ny flik med ett felmeddelande.

    ![Data för hämtningsrad för Spark-program](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Om du vill kopiera en fullständig sökväg eller relativ sökväg markerar du alternativen **Kopiera fullständig sökväg** eller **Kopiera relativ sökväg** som expanderar på den nedrullningsbara menyn. För Azure Data Lake Storage-filer startar **Open in Azure Storage Explorer** Azure Storage Explorer och mapparna hittas när du är inloggad.

    ![Data för sökväg för miniatyrprogramkopiering](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Markera sidnummer under tabellen för att navigera på sidor när det finns för många rader att visa på en sida.

    ![Data för spark-programsida](./media/apache-spark-history-server/apache-spark-data-page.png)

* Hovra på frågetecknet bredvid **Data** för att visa verktygstipset eller välj frågetecknet för att få mer information.

    ![Data för Spark-programmet mer info](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Skicka feedback med problem genom att välja **Ge oss feedback**.

    ![Spark graf ger oss feedback igen](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Fliken Diagram i Apache Spark-historikservern

Välj jobb-ID för det jobb som du vill visa. Välj sedan **Diagram** på verktygsmenyn för att hämta jobbdiagramvyn.

### <a name="overview"></a>Översikt

Du kan se en översikt över ditt jobb i det genererade jobbdiagrammet. Som standard visar diagrammet alla jobb. Du kan filtrera den här vyn **efter jobb-ID**.

![Spark-jobb- och jobbdiagramjobb-ID](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Skärm

Som standard är **förloppsvisningen** markerad. Du kan kontrollera dataflödet genom att välja **Läs** eller **Skrivt** i listrutan **Visa.**

![Spark-program- och jobbdiagramvisning](./media/apache-spark-history-server/sparkui-graph-display.png)

Grafnoden visar färgerna som visas i värmekartförklaringen.

![Spark ansökan och jobb diagram heatmap](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Uppspelning

Om du vill spela upp jobbet väljer du **Uppspelning**. Du kan välja **Stoppa** när som helst för att stoppa. Aktivitetsfärgerna visar olika statusar när du spelar upp:

|Color|Betydelse|
|-|-|
|Grön|Lyckades: Jobbet har slutförts.|
|Orange|Retried: Instanser av aktiviteter som misslyckades men inte påverkar det slutliga resultatet av jobbet. Dessa aktiviteter hade dubblett- eller återförsöksförekomster som kan lyckas senare.|
|Blå|Kör: Aktiviteten körs.|
|Vit|Väntar eller hoppas över: Aktiviteten väntar på att köras eller scenen har hoppat över.|
|Röd|Misslyckades: Aktiviteten misslyckades.|

Följande bild visar gröna, orangea och blå statusfärger.

![Spark-program och jobbdiagramfärgprov, som körs](./media/apache-spark-history-server/sparkui-graph-color-running.png)

Följande bild visar gröna och vita statusfärger.

![Spark-program och jobbdiagramfärgprov, hoppa över](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

Följande bild visar röda och gröna statusfärger.

![Spark-program och jobbdiagramfärgprov, misslyckades](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> Uppspelning för varje jobb är tillåten. För ofullständiga jobb stöds inte uppspelning.

### <a name="zoom"></a>Zoom

Använd musrullningen för att zooma in och ut i jobbdiagrammet, eller välj **Zoom för att få** plats så att den passar till skärmen.

![Spark ansökan och jobb diagram zoom för att passa](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Knappbeskrivningar

Hovra på diagramnoden för att se verktygstipset när det finns misslyckade aktiviteter och välj en fas för att öppna scensidan.

![Spark-program och jobbdiagramverktygstips](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

På fliken Jobbdiagram visas ett verktygstips och en liten ikon om de har uppgifter som uppfyller följande villkor:

|Villkor|Beskrivning|
|-|-|
|Data skeva|datalässtorlek > genomsnittlig dataläsa storlek på alla uppgifter i detta skede * 2 och datalässtorlek > 10 MB|
|Tidssnedställning|körningstiden > genomsnittlig körningstid för alla aktiviteter i det här steget * 2 och körningstid > 2 minuter|
   
![Ikon för Spark-program och jobbdiagram](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Beskrivning av diagramnod

Jobbdiagramnoden visar följande information för varje steg:

  * Id.
  * Namn eller beskrivning.
  * Totalt aktivitetsnummer.
  * Data läsa: summan av indatastorlek och blanda lässtorlek.
  * Dataskrivning: summan av utdatastorlek och blanda skriver storlek.
  * Körningstid: tiden mellan starttiden för det första försöket och slutförandetiden för det senaste försöket.
  * Antal rader: summan av indataposter, utdataposter, blanda läsposter och blanda skrivposter.
  * Framsteg.

    > [!NOTE]  
    > Som standard visar jobbdiagramnoden information från det sista försöket i varje steg (förutom körningstid för fas). Under uppspelningen visar dock grafnoden information om varje försök.
    >  
    > Datastorleken för läsning och skrivning är 1MB = 1000 KB = 1000 * 1000 Bytes.

### <a name="provide-feedback"></a>Ge feedback

Skicka feedback med problem genom att välja **Ge oss feedback**.

![Spark-feedback för program och jobbdiagram](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Utforska fliken Diagnos i Apache Spark-historikservern

Om du vill komma åt fliken Diagnos väljer du ett jobb-ID. Välj sedan Diagnos på **verktygsmenyn** för att få jobbdiagnosvyn. Fliken Diagnos innehåller **Data skeva,** **tidssnedställning**och **analys av körningsanvändning**.

Kontrollera **datasnedställning,** **tidssvventering**och analys av **körningsanvändning** genom att välja flikarna respektive.

![Fliken SparkUI-diagnosdata skeva igen](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Data skeva

När du väljer fliken **Data skeva** visas motsvarande skeva aktiviteter baserat på de angivna parametrarna.

* **Ange parametrar** - Det första avsnittet visar parametrarna, som används för att identifiera data skeva. Standardregeln är: Läs av aktivitetsdata är större än tre gånger av den genomsnittliga aktivitetsdata som läses upp och aktivitetsdata som läses är mer än 10 MB. Om du vill definiera en egen regel för skeva aktiviteter kan du välja parametrar, **skeva scen-** och **skeva teckenavsnitt** uppdateras i enlighet med detta.

* **Skev fas** - Det andra avsnittet visar faser som har skeva uppgifter som uppfyller de villkor som anges ovan. Om det finns mer än en skev aktivitet i en fas visar den skeva scentabellen bara den mest skeva aktiviteten (till exempel den största informationen för datasnedställning).

    ![fliken sparkui-diagnosnedställning](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Skeva diagram** – När en rad i skevningsstegtabellen är markerad visar skevningsdiagrammet mer information om aktivitetsdistribution baserat på dataläsning och körningstid. De skeva aktiviteterna markeras i rött och de normala aktiviteterna markeras i blått. Diagrammet visar upp till 100 exempeluppgifter och uppgiftsinformationen visas på den nedre panelen längst ned.

    ![sparkui skevdiagram för steg 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Tidsnedställning

På fliken **Tidsavskjutning** visas skeva aktiviteter baserat på körningstiden för aktiviteten.

* **Ange parametrar** - Det första avsnittet visar parametrarna, som används för att identifiera tidssnedställning. Standardvillkoren för att identifiera tidssnedställning är: körningstiden för aktiviteten är större än tre gånger genomsnittlig körningstid och körningstiden för aktiviteten är större än 30 sekunder. Du kan ändra parametrarna baserat på dina behov. I **skeva scen-** och **skevningsdiagram visas** motsvarande steg och uppgiftersinformation precis som fliken **Data skeva** ovan.

* Välj **Tidssnedställning**och sedan visas det filtrerade resultatet i avsnittet **Skevt steg** enligt de parametrar som anges i avsnittet **Ange parametrar**. Markera ett objekt i avsnittet **Skev scen,** sedan utformas motsvarande diagram i avsnitt3 och aktivitetsinformationen visas på den högra panelen längst ned.

    ![sparkui diagnos tid skeva avsnitt](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Analys av körningsanvändning

Körningsanvändningsdiagrammet visualiserar Spark-jobbutdelningsverktygets allokering och körstatus.  

1. Välj **Analys av körningsanvändning**, sedan skrivs fyra typerkurvor om körningsanvändning, inklusive **allokerade körorer,** **körutnratorer,** **inaktiva köror**och **max körinstansar**. När det gäller allokerade utförare ökar eller minskar varje "Executor added" eller "Executor removed"-händelse de allokerade utförarna. Du kan kontrollera "Evenemangstidslinje" på fliken Jobb för mer jämförelse.

   ![fliken sparkui-diagnosut executors](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Välj färgikonen om du vill markera eller avmarkera motsvarande innehåll i alla utkast.

    ![sparkui diagnoser välj diagram](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Kända problem

Indata/utdata med hjälp av Fröna distribuerade datauppsättningar (RDDs) visas inte på fliken Data.

## <a name="next-steps"></a>Nästa steg

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET för Apache Spark-dokumentation](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)


---
title: Synapse Studio-anteckningsböcker
description: I den här artikeln får du lära dig att skapa och utveckla bärbara datorer i Azure Synapse Studio (för hands version) för att förbereda data och visualiseringar.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: ea765ae5ff93625cc6a0ed36776a8925e5fce836
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311140"
---
# <a name="create-develop-and-maintain-synapse-studio-preview-notebooks-in-azure-synapse-analytics"></a>Skapa, utveckla och underhålla antecknings böcker för Synapse Studio (för hands version) i Azure Synapse Analytics

En Synapse Studio-anteckningsbok (för hands version) är ett webb gränssnitt där du kan skapa filer som innehåller Live-kod, visualiseringar och text. Bärbara datorer är en bra plats för att verifiera idéer och använda snabba experiment för att få insikter om dina data. Antecknings böcker används också ofta i förberedelse av data, data visualisering, maskin inlärning och andra stora data scenarier.

Med en Azure Synapse Studio-anteckningsbok kan du:

* Kom igång med noll setup-arbete.
* Skydda dina data med inbyggda funktioner för företags säkerhet.
* Analysera data över RAW-format (CSV, txt, JSON osv.), bearbetade fil format (Parquet, delta Lake, ORC osv.) och SQL-datafiler i tabell mot Spark och SQL.
* Var produktiv med förbättrade redigerings funktioner och inbyggd data visualisering.

Den här artikeln beskriver hur du använder antecknings böcker i Azure Synapse Studio.

## <a name="create-a-notebook"></a>Skapa en notebook-fil

Det finns två sätt att skapa en antecknings bok. Du kan skapa en ny antecknings bok eller importera en befintlig antecknings bok till en Azure Synapse-arbetsyta från **Object Explorer**. Bärbara Azure Synapse Studio-datorer kan känna igen standard Jupyter Notebook IPYNB-filer.

![Skapa import-anteckningsbok](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Utveckla notebook-filer

Antecknings böcker består av celler, som är enskilda kodblock eller text som kan köras oberoende av varandra eller som en grupp.

### <a name="add-a-cell"></a>Lägg till en cell

Det finns flera sätt att lägga till en ny cell i din bärbara dator.

1. Expandera knappen uppe till vänster **+ cell** och välj **Lägg till kod cell** eller **Lägg till text cell**.

    ![Lägg till cell-med-cell-knapp](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Hovra över utrymmet mellan två celler och välj **Lägg till kod** eller **Lägg till text**.

    ![Lägg till cell-mellan-blank steg](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Använd [kortkommandon under kommando läge](#shortcut-keys-under-command-mode). Tryck på **a** för att infoga en cell ovanför den aktuella cellen. Tryck på **B** för att infoga en cell under den aktuella cellen.

### <a name="set-a-primary-language"></a>Ange ett primärt språk

Bärbara Azure Synapse Studio-datorer stöder fyra Apache Spark språk:

* pySpark (python)
* Spark (Scala)
* SparkSQL
* .NET för Apache Spark (C#)

Du kan ställa in det primära språket för nya tillagda celler i list rutan i det övre kommando fältet.

   ![standard – Synapse – språk](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Använd flera språk

Du kan använda flera språk i en bärbar dator genom att ange rätt språk-Magic-kommando i början av en cell. I följande tabell visas Magic-kommandon för att växla mellan cell språk.

|Magiskt kommando |Språk | Beskrivning |  
|---|------|-----|
|%% pyspark| Python | Kör en **python** -fråga mot Spark-kontext.  |
|%% Spark| Scala | Kör en **Scala** -fråga mot Spark-kontext.  |  
|%% SQL| SparkSQL | Kör en **SparkSQL** -fråga mot Spark-kontext.  |
|%% csharp | .NET för Spark C # | Kör en **.net for Spark C#** -fråga mot Spark-kontext. |

Följande bild är ett exempel på hur du kan skriva en PySpark-fråga med kommandot **%% PySpark** Magic eller en SparkSQL-fråga med **%% SQL** Magic-kommandot i en **Spark-anteckningsbok (Scala)** . Observera att det primära språket för antecknings boken är inställt på pySpark.

   ![Synapse Spark Magic-kommandon](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Använd temporära tabeller för att referera till data över olika språk

Du kan inte referera till data eller variabler direkt på olika språk i en Synapse Studio-anteckningsbok. I Spark kan en tillfällig tabell refereras mellan olika språk. Här är ett exempel på hur du läser en `Scala` DataFrame i `PySpark` och `SparkSQL` använder en spark-temporär tabell som en lösning.

1. I cell 1 läser du en DataFrame från en SQL-pool med hjälp av Scala och skapar en temporär tabell.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics("mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. Fråga data i cell 2 med hjälp av Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Använd data i PySpark i cell 3.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE-stil, IntelliSense

Bärbara Azure Synapse Studio-datorer är integrerade med Monaco-redigeraren för att kunna använda IntelliSense i IDE-format i cell redigeraren. Syntaxkontroll, fel markör och automatisk kod komplettering hjälper dig att skriva kod och identifiera problem snabbare.

IntelliSense-funktionerna finns på olika förfallo nivåer för olika språk. Använd följande tabell för att se vad som stöds.

|Språk| Syntaxkontroll | Fel markör för syntaxfel  | Komplettering av syntax kod | Slut för ande av variabel kod| Slut för ande av system funktions kod| Slut för ande av användar funktions kod| Smarta indrag | Kod vikning|
|--|--|--|--|--|--|--|--|--|
|PySpark (python)|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Spark (Scala)|Ja|Ja|Ja|Ja|-|-|-|Ja|
|SparkSQL|Ja|Ja|-|-|-|-|-|-|
|.NET för Spark (C#)|Ja|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formatera en text cell med knappar i verktygsfältet

Du kan använda format knapparna i verktygsfältet text celler för att utföra vanliga markdown-åtgärder. Den innehåller fet text, kursivering av text, infoga kodfragment, infoga osorterad lista, infoga sorterad lista och infoga bild från URL.

  ![Synapse text cells verktygsfält](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Ångra cell åtgärder
Klicka på knappen **Ångra** eller tryck på **CTRL + Z** för att återkalla den senaste cell åtgärden. Nu kan du ångra upp till de senaste 20 historiska cell åtgärderna. 

   ![Synapse ångra celler](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Flytta en cell

Välj ellipserna (...) för att få åtkomst till menyn ytterligare cell åtgärder längst till höger. Välj sedan **Flytta cell uppåt** eller **Flytta cell nedåt** för att flytta den aktuella cellen. 

Du kan också använda [kortkommandon under kommando läge](#shortcut-keys-under-command-mode). Tryck på **CTRL + ALT + ↑** för att flytta upp den aktuella cellen. Tryck på **CTRL + ALT + ↓** för att flytta den aktuella cellen nedåt.

   ![flytta – a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Ta bort en cell

Om du vill ta bort en cell väljer du ellipserna (...) för att komma åt menyn ytterligare cell åtgärder längst till höger och väljer sedan **ta bort cell**. 

Du kan också använda [kortkommandon under kommando läge](#shortcut-keys-under-command-mode). Tryck på **d, d** för att ta bort den aktuella cellen.
  
   ![ta bort a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Komprimera en cell Indatatyp
Välj pilknappen längst ned i den aktuella cellen för att dölja den. Om du vill expandera den väljer du pilknappen när cellen är komprimerad.

   ![Komprimera-cell-Indatatyp](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Komprimera cell utdata

Klicka på knappen **Komprimera utdata** längst upp till vänster i den aktuella cellens utdata för att dölja den. För att expandera den, väljer du **Visa cellens utdata** medan cellens utdata är komprimerad.

   ![Komprimera-cell-utdata](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Köra notebook-filer

Du kan köra kod cellerna i din bärbara dator separat eller alla på en gång. Status och förlopp för varje cell representeras i antecknings boken.

### <a name="run-a-cell"></a>Köra en cell

Det finns flera sätt att köra koden i en cell.

1. Hovra över den cell som du vill köra och välj knappen **Kör cell** eller tryck på **CTRL + RETUR**.

   ![Kör-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Om du vill komma åt menyn ytterligare cell åtgärder längst till höger väljer du ellipserna ( **...** ). Välj sedan **Kör cell**.

   ![Kör-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Använd [kortkommandon under kommando läge](#shortcut-keys-under-command-mode). Tryck på **SKIFT + RETUR** för att köra den aktuella cellen och markera cellen nedan. Tryck på **Alt + Retur** för att köra den aktuella cellen och infoga en ny cell nedan.


### <a name="run-all-cells"></a>Kör alla celler
Välj knappen **Kör alla** för att köra alla celler i den aktuella antecknings boken i följd.

   ![Kör alla – celler](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Kör alla celler ovanför eller under

Om du vill komma åt menyn ytterligare cell åtgärder längst till höger väljer du ellipserna ( **...** ). Välj sedan **Kör celler ovan** för att köra alla celler ovanför den aktuella i sekvensen. Välj **Kör celler nedan** om du vill köra alla celler under den aktuella sekvensen.

   ![Run-cellernas över-eller-under](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>Avbryt alla celler som körs
Välj knappen **Avbryt alla** om du vill avbryta de celler eller celler som väntar i kön. 
   ![Avbryt – alla celler](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

### <a name="cell-status-indicator"></a>Cell status indikator

En steg-för-steg-cells körnings status visas under cellen för att hjälpa dig att se den aktuella förloppet. När cell körningen är klar visas en körnings översikt med den totala varaktigheten och slut tiden, där det finns en framtida referens.

![cell-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Status indikator för Spark

Azure Synapse Studio-anteckningsbok är enbart Spark-baserad. Kod celler körs på poolen utan server Apache Spark. En status indikator för Spark-jobbet visas med en förlopps indikator i real tid som hjälper dig att förstå jobb körnings statusen.
Antalet aktiviteter per jobb eller steg hjälper dig att identifiera parallell nivån för ditt Spark-jobb. Du kan också gå djupare till Spark-ANVÄNDARGRÄNSSNITTET för ett bestämt jobb (eller steg) genom att välja länken på jobbets (eller scenens) namn.


![Spark-Progress-Indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Konfiguration av Spark-session

Du kan ange tids längd, antal och storlek på körningar för att ge den aktuella Spark-sessionen i **Konfigurera session**. Starta om Spark-sessionen för att konfigurations ändringarna ska börja gälla. Alla cachelagrade Notebook-variabler rensas.

[![session-hantering](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png#lightbox)

En spark-sessionshantering är nu tillgänglig på konfigurations panelen för Spark-sessionen. Du kan välja en spark-pool direkt från konfigurations panelen för sessionen och se hur många noder som används och hur många återstående körningar som är tillgängliga. Den här informationen kan hjälpa dig att ange lämplig sessionsgräns i stället för att ändra den och tillbaka.

![session – rekommendera](./media/apache-spark-development-using-notebooks/synapse-spark-session-recommender.png)


## <a name="bring-data-to-a-notebook"></a>Hämta data till en bärbar dator

Du kan läsa in data från Azure Blob Storage, Azure Data Lake Store gen 2 och SQL-poolen som visas i kod exemplen nedan.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Läs en CSV-fil från Azure Data Lake Store Gen2 som en spark-DataFrame

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Läs en CSV-fil från Azure Blob Storage som en spark-DataFrame

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>Läs data från det primära lagrings kontot

Du kan komma åt data i det primära lagrings kontot direkt. Du behöver inte ange de hemliga nycklarna. I Datautforskaren högerklickar du på en fil och väljer **ny antecknings** bok för att se en ny antecknings bok med autogenerated dataextractor.

![data till cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Visualisera data i en bärbar dator

### <a name="produce-rendered-table-view"></a>Producera åter givnings tabell visning

En vy med tabell resultat tillhandahålls med möjligheten att skapa ett stapeldiagram, linje diagram, cirkel diagram, punkt diagram och ytdiagram. Du kan visualisera dina data utan att behöva skriva kod. Diagrammen kan anpassas i **diagram alternativen**. 

Utdata från **%% SQL** Magic-kommandon visas som standard i vyn renderad tabell. Du kan anropa <code>display(df)</code> Spark-DataFrames, Pandas DataFrames, list eller elastiska distribuerade data uppsättningar (RDD) för att skapa den åter givnings tabellen.

   [![inbyggt – diagram](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png#lightbox)

### <a name="visualize-built-in-charts-from-large-scale-dataset"></a>Visualisera inbyggda diagram från storskalig data uppsättning 

Som standard <code>display(df)</code> tar funktionen bara de första 1000 raderna i data för att återge diagram. Kontrol lera **agg regeringen för alla resultat** och välj **Verkställ** om du vill använda diagrammets generering från hela data uppsättningen. Ett Spark-jobb utlöses när diagram inställningen ändras, det tar en stund att slutföra beräkningen och återge diagrammet. 
    [![Builtin – diagram – agg regering – alla](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png#lightbox)



### <a name="visualize-data-statistic-information"></a>Visualisera information om data statistik
Du kan använda <code>display(df, summary = True)</code> för att kontrol lera statistik sammanfattningen för en specifik Spark-DataFrame som innehåller kolumn namn, kolumn typ, unika värden och saknade värden för varje kolumn. Du kan också välja en speciell kolumn för att se dess minimala värde, maximalt värde, medelvärde och standard avvikelse.
    [![Builtin – diagram – Sammanfattning ](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png#lightbox)

### <a name="render-html-or-interactive-libraries"></a>Återge HTML eller interaktiva bibliotek

Du kan återge HTML-kod, inklusive Java Script, CSS, D3 eller interaktiva bibliotek, t. ex. **bokeh** , med hjälp av **displayHTML ()**.

Följande bild är ett exempel på hur du ritar glyfer över en karta med **bokeh**.

   ![bokeh – exempel](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Kör följande exempel kod för att rita bilden ovan.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)

```

## <a name="save-notebooks"></a>Spara antecknings böcker

Du kan spara en enskild bärbar dator eller alla antecknings böcker i din arbets yta.

1. Om du vill spara ändringar som du har gjort i en enskild antecknings bok väljer du knappen **publicera** i kommando fältet för antecknings boken.

   ![publicera – Notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Om du vill spara alla antecknings böcker i arbets ytan väljer du knappen **publicera alla** i arbets ytans kommando fält. 

   ![publicera – alla](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

I egenskaperna för antecknings boken kan du konfigurera om du vill ta med cellens utdata när du sparar.

   ![Notebook – egenskaper](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Magic-kommandon
Du kan använda välkända Jupyter Magic-kommandon i Azure Synapse Studio-anteckningsböcker. Granska följande lista som de aktuella tillgängliga Magic-kommandona. Berätta för oss [dina användnings fall på GitHub](https://github.com/MicrosoftDocs/azure-docs/issues/new) så att vi kan fortsätta att bygga upp fler Magic-kommandon för att uppfylla dina behov.

Tillgängliga rad Magic: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% tid](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Tillgängliga cell Magic: [%% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeIt](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% csharp](#use-multiple-languages)


## <a name="integrate-a-notebook"></a>Integrera en bärbar dator

### <a name="add-a-notebook-to-a-pipeline"></a>Lägga till en antecknings bok i en pipeline

Välj knappen **Lägg till i** det övre högra hörnet för att lägga till en antecknings bok i en befintlig pipeline eller skapa en ny pipeline.

![Lägg till antecknings bok i pipeline](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Ange en parameter cell

Om du vill Parameterisera din bärbara dator väljer du ellipserna (...) för att få åtkomst till menyn ytterligare cell åtgärder längst till höger. Välj sedan **Växla parameter cell** för att ange cellen som parameter cellen.

![Växla-parameter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

Azure Data Factory letar efter parameter cellen och behandlar den här cellen som standard för de parametrar som skickas vid körningen. Körnings motorn lägger till en ny cell under parameter cellen med indataparametrar för att skriva över standardvärdena. När en parameter cell inte anges infogas den inmatade cellen längst upp i antecknings boken.

### <a name="assign-parameters-values-from-a-pipeline"></a>Tilldela parameter värden från en pipeline

När du har skapat en antecknings bok med parametrar kan du köra den från en pipeline med Azure Synapse Notebook-aktiviteten. När du har lagt till aktiviteten till din pipeline-arbetsyta kan du ange parametrar-värden under avsnittet **grundläggande parametrar** på fliken **Inställningar** . 

![Tilldela en parameter](./media/apache-spark-development-using-notebooks/assign-parameter.png)

När du tilldelar parameter värden kan du använda [uttrycks språket](../../data-factory/control-flow-expression-language-functions.md) eller [systemvariablerna](../../data-factory/control-flow-system-variables.md)för pipelinen.



## <a name="shortcut-keys"></a>Kortkommandon

I likhet med Jupyter-anteckningsböcker har Azure Synapse Studio-anteckningsbokar ett spärrat användar gränssnitt. Tangent bordet skiljer sig åt beroende på vilket läge som Notebook-cellen finns i. Bärbara datorer i Synapse Studio har stöd för följande två lägen för en specifik kod cell: kommando läge och redigerings läge.

1. En cell är i kommando läge när ingen text markör visas som anger att du skriver. När en cell är i kommando läge kan du redigera antecknings boken som helhet men inte skriva in enskilda celler. Ange kommando läge genom att trycka `ESC` eller använda musen för att välja utanför cellens redigerings området.

   ![kommando läge](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. Redigerings läget anges av en text markör där du ombeds ange redigerings ytan. När en cell är i redigerings läge kan du skriva i cellen. Ange redigerings läge genom att trycka `Enter` eller använda musen för att välja en cells redigerings yta.
   
   ![redigerings-läge](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Kortkommandon under kommando läge

Med följande kortkommandon kan du enkelt navigera och köra kod i Azure Synapse Notebooks.

| Action |Genvägar till Synapse Studio-anteckningsbok  |
|--|--|
|Kör den aktuella cellen och välj nedan | SKIFT + RETUR |
|Kör den aktuella cellen och infoga den nedan | ALT + RETUR |
|Markera cell ovanför| Upp |
|Markera cell under| Ned |
|Infoga cell ovanför| A |
|Infoga cell under| B |
|Utöka markerade celler ovanför| Skift + upp |
|Utöka markerade celler nedan| Skift + ned|
|Flytta upp cell| Ctrl + Alt + ↑ |
|Flytta cell nedåt| Ctrl + Alt + ↓ |
|Ta bort markerade celler| D, D |
|Växla till redigerings läge| Ange |

### <a name="shortcut-keys-under-edit-mode"></a>Kortkommandon under redigerings läge

Med följande kortkommandon kan du enkelt navigera och köra kod i Azure Synapse Notebooks i redigerings läge.

| Action |Genvägar till Synapse Studio-anteckningsbok  |
|--|--|
|Flytta markören uppåt | Upp |
|Flytta markören nedåt|Ned|
|Ångra|Ctrl + Z|
|Gör om|Ctrl + Y|
|Kommentera/ta bort kommentar|CTRL +/|
|Ta bort ord före|CTRL + BACKSTEG|
|Ta bort ord efter|CTRL + Delete|
|Gå till cell start|Ctrl + Home|
|Gå till cell slut |CTRL + END|
|Gå ett ord till vänster|CTRL + vänster|
|Gå ett ord till höger|CTRL + höger|
|Välj alla|CTRL + A|
|Uppgiften| Ctrl +]|
|Dra ut|Ctrl + [|
|Växla till kommando läge| Esc |

## <a name="next-steps"></a>Nästa steg
- [Kolla Synapse-exempel Notebooks](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Snabb start: skapa en Apache Spark pool (förhands granskning) i Azure Synapse Analytics med hjälp av webb verktyg](../quickstart-apache-spark-notebook.md)
- [Vad är Apache Spark i Azure Synapse Analytics](apache-spark-overview.md)
- [Använda .NET för Apache Spark med Azure Synapse Analytics](spark-dotnet.md)
- [Dokumentation om .NET för Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)

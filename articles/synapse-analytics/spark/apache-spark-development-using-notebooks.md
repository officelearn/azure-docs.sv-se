---
title: Skapa, utveckla och underhålla Azure Synapse Studio -datorer (förhandsversion)
description: I den här artikeln får du lära dig hur du skapar och utvecklar Azure Synapse Studio (förhandsversion) anteckningsböcker för att göra dataförberedelser och visualisering.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430232"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Skapa, utveckla och underhålla Azure Synapse Studio -datorer (förhandsversion)

En Azure Synapse Studio -anteckningsbok (förhandsversion) är ett webbgränssnitt där du kan skapa filer som innehåller live-kod, visualiseringar och berättande text. Anteckningsböcker är ett bra ställe att validera idéer och använda snabba experiment för att få insikter från dina data. Anteckningsböcker används också i stor utsträckning i dataförberedelser, datavisualisering, maskininlärning och andra Big Data-scenarier.

Med en Azure Synapse Studio-anteckningsbok kan du:

* Kom igång med noll installationsansträngning.
* Skydda data med inbyggda säkerhetsfunktioner för företag.
* Analysera data över råformat (CSV, txt, JSON, etc.), bearbetade filformat (parkett, Delta Lake, ORC, etc.) och SQL-tabelldatafiler mot Spark och SQL.
* Var produktiv med förbättrade redigeringsfunktioner och inbyggd datavisualisering.

I den här artikeln beskrivs hur du använder anteckningsböcker i Azure Synapse Studio.

## <a name="create-a-notebook"></a>Skapa en notebook-fil

Det finns två sätt att skapa en anteckningsbok. Du kan skapa en ny anteckningsbok eller importera en befintlig anteckningsbok till en Azure Synapse-arbetsyta från **Objektutforskaren**. Azure Synapse Studio-anteckningsböcker kan känna igen vanliga IPYNB-filer för bärbara Filer i Jupyter.

![synaps-create-import-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Utveckla bärbara datorer

Anteckningsböcker består av celler, som är enskilda kodblock eller textblock som kan kördes oberoende av eller som en grupp.

### <a name="add-a-cell"></a>Lägga till en cell

Det finns flera sätt att lägga till en ny cell i anteckningsboken.

1. Expandera den övre vänstra **+ cellknappen** och välj **Lägg till kodcell** eller **Lägg till textcell**.

    ![add-cell-with-cell-knapp](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Hovra över avståndet mellan två celler och välj **Lägg till kod** eller Lägg till **text**.

    ![lägga till cell mellan mellan blanksteg](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Använd [Kortkommandon under kommandoläge](#shortcut-keys-under-command-mode). Tryck på **A** för att infoga en cell ovanför den aktuella cellen. Tryck på **B** för att infoga en cell under den aktuella cellen.

### <a name="set-a-primary-language"></a>Ange ett primärt språk

Azure Synapse Studio-anteckningsböcker har stöd för fyra sparkspråk:

* pyspark (pyspark)
* gnista (Scala)
* sparkSQL
* Spark.NET (C#)

Du kan ange det primära språket för nya tillagda celler från listrutan i det övre kommandofältet.

   ![standard-synaps-språk](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Använda flera språk

Du kan använda flera språk i en anteckningsbok genom att ange rätt språkmagikommando i början av en cell. I följande tabell visas de magiska kommandona för att växla cellspråk.

|Magiskt kommando |Språk | Beskrivning |  
|---|------|-----|
|%%pyspark| Python | Kör en **Python-fråga** mot Spark-kontext.  |
|%%spark| Scala | Kör en **Scala-fråga** mot Spark-kontext.  |  
|%%sql| SparkSQL (Kvm) | Kör en **SparkSQL-fråga** mot Spark-kontext.  |
|%%csharp | Spark.NET C # | Kör en **Spark.NET C#-fråga** mot Spark-kontext. |

Följande bild är ett exempel på hur du kan skriva en PySpark-fråga med kommandot **%%pyspark** magic command eller en SparkSQL-fråga med kommandot **%%sql** magic i en **Spark(Scala)-anteckningsbok.** Observera att det primära språket för anteckningsboken är inställt på Scala.

   ![synaps-spark-magi](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Använda temporära tabeller för att referera till data mellan språk

Du kan inte referera till data eller variabler direkt på olika språk i en anteckningsbok för Synapse Studio. I Spark kan en tillfällig tabell refereras över språk. Här är ett exempel på `Scala` hur `PySpark` du `SparkSQL` läser en DataFrame i och använder en Spark temp-tabell som en lösning.

1. I cell 1 läser du en DataFrame från SQL-poolkoppling med Scala och skapar en tillfällig tabell.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. I cell 2 frågar du data med Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Använd data i PySpark i cell 3.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE-stil IntelliSense

Azure Synapse Studio-anteckningsböcker är integrerade med Monaco-redigeraren för att föra IDE-stil IntelliSense till cellredigeraren. Syntaxmarkering, felskapare och automatiska kodkompletter hjälper dig att skriva kod och identifiera problem snabbare.

IntelliSense-funktionerna ligger på olika mognadsnivåer för olika språk. Använd tabellen nedan för att se vad som stöds.

|Språk| Syntaxmarkering | Syntaxfelsmarkör  | Slutförande av syntaxkod | Slutförande av variabel kod| Slutförande av systemfunktionskod| Slutförande av användarfunktionskod| Smarta indrag | Vikning av kod|
|--|--|--|--|--|--|--|--|--|
|Pyspark (Pyspark )|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Gnista (Scala)|Ja|Ja|Ja|Ja|-|-|-|Ja|
|SparkSQL (Kvm)|Ja|Ja|-|-|-|-|-|-|
|Spark.NET (C#)|Ja|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formatera textcell med knappar i verktygsfältet

Du kan använda formatknapparna i verktygsfältet textceller för att utföra vanliga markeringsåtgärder. Den innehåller fetstil text, kursiv text, infoga kodavsnitt, infoga oordnad lista, infoga ordnad lista och infoga bild från URL.

  ![synaps-text-cell-verktygsfält](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Ångra cellåtgärder
Klicka på **ångra-knappen** eller tryck på **Ctrl+Z** för att återkalla den senaste cellåtgärden. Nu kan du ångra upp till de senaste 20 historiska cellåtgärderna. 

   ![synaps-ångra-celler](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Flytta en cell

Välj ellipserna (...) för att komma åt menyn för ytterligare cellåtgärder längst till höger. Välj sedan **Flytta cell uppåt** eller Flytta **cellen nedåt** om du vill flytta den aktuella cellen. 

Du kan också använda [kortkommandon under kommandoläge](#shortcut-keys-under-command-mode). Tryck på **Ctrl+Alt+↑** för att flytta upp den aktuella cellen. Tryck på **Ctrl+Alt+↓** om du vill flytta den aktuella cellen nedåt.

   ![flytta en cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Ta bort en cell

Om du vill ta bort en cell markerar du ellipserna (...) för att komma åt menyn för ytterligare cellåtgärder längst till höger och väljer sedan **Ta bort cell**. 

Du kan också använda [kortkommandon under kommandoläge](#shortcut-keys-under-command-mode). Tryck på **D,D** för att ta bort den aktuella cellen.
  
   ![ta bort en cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Komprimera en cellinmatning
Klicka på pilknappen längst ned i den aktuella cellen för att dölja den. Om du vill expandera den klickar du på pilknappen medan cellen är komprimerad.

   ![komprimera-cell-ingång](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Dölja en cellutdata

Klicka på **knappen dölj utdata** längst upp till vänster i den aktuella cellutmatningen för att dölja den. Om du vill expandera den klickar du på **Visa cellutdata** medan cellutdata är komprimerade.

   ![komprimera-cell-utdata](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Kör anteckningsböcker

Du kan köra kodcellerna i anteckningsboken individuellt eller på en gång. Status och förlopp för varje cell representeras i anteckningsboken.

### <a name="run-a-cell"></a>Köra en cell

Det finns flera sätt att köra koden i en cell.

1. Hovra på den cell som du vill köra och markera knappen **Kör cell** eller tryck på **Ctrl+Retur**.

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Om du vill komma åt menyn för ytterligare cellåtgärder längst till höger väljer du ellipserna (**...**). Välj sedan **Kör cell**.

   ![run-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Använd [Kortkommandon under kommandoläge](#shortcut-keys-under-command-mode). Tryck på **Skift+Retur** för att köra den aktuella cellen och markera cellen nedan. Tryck på **Alt+Retur** för att köra den aktuella cellen och infoga en ny cell nedan.


### <a name="run-all-cells"></a>Köra alla celler
Klicka på knappen **Kör alla** om du vill köra alla celler i den aktuella anteckningsboken i följd.

   ![run-all-celler](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Kör alla celler ovanför eller under

Om du vill komma åt menyn för ytterligare cellåtgärder längst till höger väljer du ellipserna (**...**). Välj sedan **Kör celler ovan** om du vill köra alla celler ovanför strömmen i följd. Välj **Kör celler nedan** om du vill köra alla celler under strömmen i följd.

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Indikator för cellstatus

En steg-för-steg cell körning status visas under cellen för att hjälpa dig att se dess aktuella framsteg. När cellkörningen är klar visas en körningssammanfattning med den totala varaktigheten och sluttiden och behålls där för framtida referens.

![cell-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indikator för gnistans förlopp

Azure Synapse Studio-anteckningsboken är enbart Spark-baserad. Kodceller körs på Spark-poolen på distans. En spark-jobbförloppsindikator med ett förloppsfält i realtid visas för att hjälpa dig att förstå jobbkörningsstatusen.


![spark-progress-indikator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark-session config

Du kan ange tidsgränsen, antalet och storleken på utförare som ska utföras till den aktuella Spark-sessionen i **Konfigurera session**. Starta om Spark-sessionen är att konfigurationsändringar ska börja gälla. Alla cachelagrade variabler för bärbara datorer rensas.

![session-mgmt](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Hämta data till en anteckningsbok

Du kan läsa in data från Azure Blob Storage, Azure Data Lake Store Gen 2 och SQL-pool som visas i kodexemplen nedan.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Läsa en CSV från Azure Data Lake Store Gen2 som en Spark DataFrame

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Läsa en CSV från Azure Blob Storage som spark dataframe

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

### <a name="read-data-from-the-primary-storage-account"></a>Läsa data från det primära lagringskontot

Du kan komma åt data i det primära lagringskontot direkt. Du behöver inte ange de hemliga nycklarna. Högerklicka på en fil i Data Explorer och välj **Ny anteckningsbok** för att se en ny anteckningsbok med dataextraherat dataextraherat.

![data-till-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Visualisera data i en anteckningsbok

### <a name="display"></a>Display()

En tabellvy med ett tabellresultat medföljer alternativet att skapa ett stapeldiagram, linjediagram, cirkeldiagram, punktdiagram och ytdiagram. Du kan visualisera dina data utan att behöva skriva kod. Diagrammen kan anpassas i **diagramalternativen**. 

Utdata för **%%sql** magiska kommandon visas i den renderade tabellvyn som standard. Du kan anropa **display(`<DataFrame name>`)** på Spark DataFrames eller Fröna distribuerade datauppsättningar (RDD) för att skapa den renderade tabellvyn.

   ![inbyggda diagram](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

Du kan återge HTML eller interaktiva bibliotek, till exempel **bokeh**, med hjälp av **displayHTML()**.

Följande bild är ett exempel på att rita tecken över en karta med hjälp av **bokeh**.

   ![bokeh-exempel](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Kör följande exempelkod för att rita bilden ovan.

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

## <a name="save-notebooks"></a>Spara anteckningsböcker

Du kan spara en enda anteckningsbok eller alla anteckningsböcker på arbetsytan.

1. Om du vill spara ändringar som du har gjort i en enda anteckningsbok markerar du knappen **Publicera** i kommandofältet för anteckningsbok.

   ![publicera-anteckningsbok](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Om du vill spara alla anteckningsböcker på arbetsytan markerar du knappen **Publicera alla** i kommandofältet på arbetsytan. 

   ![publicera-alla](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

I egenskaperna för anteckningsboken kan du konfigurera om du vill inkludera cellutdata när du sparar.

   ![egenskaper för anteckningsbok](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Magiska kommandon
Du kan använda dina välbekanta Jupyter-magiska kommandon i Azure Synapse Studio-anteckningsböcker. Kontrollera listan nedan som den aktuella tillgängliga magiska kommandon. Berätta för oss dina användningsfall på GitHub så att vi kan fortsätta att bygga ut mer magiska kommandon för att möta dina behov.

Tillgängliga radmagi: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%tid](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Tillgängliga cellmagi: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>Kortkommandon

I likhet med Jupyter-anteckningsböcker har Azure Synapse Studio-anteckningsböcker ett modalt användargränssnitt. Tangentbordet gör olika saker beroende på vilket läge anteckningsbokscellen är i. Synapse Studio-anteckningsböcker stöder följande två lägen för en viss kodcell: kommandoläge och redigeringsläge.

1. En cell är i kommandoläge när det inte finns någon textmarkör som uppmanar dig att skriva. När en cell är i kommandoläge kan du redigera anteckningsboken som helhet men inte skriva i enskilda celler. Ange kommandoläge `ESC` genom att trycka eller använda musen för att klicka utanför en cells redigeringsområde.

   ![kommandoläge](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. Redigeringsläget visas med en textmarkör som uppmanar dig att skriva i redigeringsområdet. När en cell är i redigeringsläge kan du inte skriva in i cellen. Ange redigeringsläge `Enter` genom att trycka eller använda musen för att klicka på en cells redigeringsområde.
   
   ![redigerings-läge](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Kortkommandon under kommandoläge

Med hjälp av följande kortkommandon för tangenttryckningar kan du lättare navigera och köra kod i Azure Synapse-anteckningsböcker.

| Åtgärd |Synapse Studio-genvägar för bärbara datorer  |
|--|--|
|Kör den aktuella cellen och välj nedan | Skift+Retur |
|Kör den aktuella cellen och infoga nedan | Alt+Retur |
|Markera cellen ovan| Upp |
|Markera cellen nedan| Ned |
|Infoga cell ovanför| A |
|Infoga cell under| B |
|Utöka markerade celler ovanför| Skift+Uppåt |
|Utöka markerade celler nedan| Skift+Nedåt|
|Flytta cellen uppåt| Ctrl+Alt+↑ |
|Flytta cellen nedåt| Ctrl+Alt+↓ |
|Ta bort markerade celler| D, D |
|Växla till redigeringsläge| Ange |

### <a name="shortcut-keys-under-edit-mode"></a>Kortkommandon under redigeringsläge

Med hjälp av följande kortkommandon för tangenttryckningar kan du lättare navigera och köra kod i Azure Synapse-anteckningsböcker när du är i redigeringsläge.

| Åtgärd |Kortkommandon för Synapse Studio-anteckningsbok  |
|--|--|
|Flytta markören uppåt | Upp |
|Flytta markören nedåt|Ned|
|Ångra|Ctrl + Z|
|Gör om|Ctrl + Y|
|Kommentar/Avkommentar|Ctrl + /|
|Ta bort ord före|Ctrl + Backsteg|
|Ta bort ord efter|Ctrl + Ta bort|
|Gå till cellstart|Ctrl + Start|
|Gå till cellslut |Ctrl +|
|Gå ett ord åt vänster|Ctrl + Vänster|
|Gå ett ord till höger|Ctrl + Höger|
|Markera alla|Ctrl + A|
|Strecksatsen| Ctrl + ]|
|Dedent (på andra)|Ctrl + [|
|Växla till kommandoläge| Esc |

## <a name="next-steps"></a>Nästa steg

- [.NET för Apache Spark-dokumentation](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)

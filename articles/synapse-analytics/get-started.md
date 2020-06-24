---
title: 'Självstudie: kom igång med Azure Synapse Analytics'
description: I den här självstudien får du lära dig de grundläggande stegen för att konfigurera och använda Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: daa8b594b06203c7de9a9b462be469dd71ed2e49
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791868"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Kom igång med Azure Synapse Analytics

Den här självstudien vägleder dig genom de grundläggande stegen för att konfigurera och använda Azure Synapse Analytics.

## <a name="prepare-a-storage-account"></a>Förbereda ett lagrings konto

1. Öppna [Azure Portal](https://portal.azure.com).
1. Skapa ett nytt lagrings konto med följande inställningar:

    |Flik|Inställningen | Föreslaget värde | Beskrivning |
    |---|---|---|---|
    |Grundläggande inställningar|**Lagrings konto namn**| Du kan ge den namnet.|I det här dokumentet kommer vi att se det som **contosolake**.|
    |Grundläggande inställningar|**Typ av konto**|Måste anges till **StorageV2**.||
    |Grundläggande inställningar|**Position**|Välj en plats.| Vi rekommenderar din Azure Synapse Analytics-arbetsyta och Azure Data Lake Storage Gen2 kontot befinner sig i samma region.|
    |Avancerat|**Data Lake Storage Gen2**|**Aktiverad**| Azure Synapse fungerar bara med lagrings konton där den här inställningen är aktive rad.|
    |||||

1. När du har skapat lagrings kontot väljer du **åtkomst kontroll (IAM)** i den vänstra rutan. Tilldela sedan följande roller eller kontrol lera att de redan har tilldelats:
    1. Tilldela rollen som **ägare** .
    1. Tilldela rollen som **ägare av Storage BLOB-data** .
1. I den vänstra rutan väljer du **behållare** och skapar en behållare.
1. Du kan ge behållaren ett valfritt namn. I det här dokumentet namnger vi behållar **användare**.
1. Godkänn standardinställningen **offentlig åtkomst nivå**och välj sedan **skapa**.

I följande steg ska du konfigurera din Azure Synapse-arbetsyta att använda det här lagrings kontot som primärt lagrings konto och behållare för att lagra data i arbets ytan. Arbets ytan lagrar data i Apache Spark tabeller. Programmet lagrar Spark-programloggarna under en mapp med namnet **/Synapse/workspacename**.

## <a name="create-a-synapse-workspace"></a>Skapa en Synapse-arbetsyta

1. Öppna [Azure Portal](https://portal.azure.com)och högst upp i Sök efter **Synapse**.
1. I Sök resultaten under **tjänster**väljer du **Azure Synapse Analytics (för hands versioner av arbets ytor)**.
1. Välj **Lägg till** för att skapa en arbets yta med följande inställningar:

    |Flik|Inställningen | Föreslaget värde | Beskrivning |
    |---|---|---|---|
    |Grundläggande inställningar|**Namn på arbetsyta**|Du kan anropa det något.| I det här dokumentet använder vi min **arbets yta**.|
    |Grundläggande inställningar|**Region**|Matcha lagrings kontots region.|

1. Under **välj Data Lake Storage gen 2**väljer du det konto och den behållare som du skapade tidigare.
1. Välj **Granska + skapa**  >  **skapa**. Din arbets yta är klar på några minuter.

## <a name="verify-access-to-the-storage-account"></a>Verifiera åtkomst till lagrings kontot

Hanterade identiteter för din Azure Synapse-arbetsyta kanske redan har åtkomst till lagrings kontot. Följ dessa steg för att se till att:

1. Öppna [Azure Portal](https://portal.azure.com) och det primära lagrings kontot som valts för din arbets yta.
1. Välj **åtkomst kontroll (IAM)** i den vänstra rutan.
1. Tilldela följande roller eller kontrol lera att de redan har tilldelats. Vi använder samma namn för arbets ytans identitet och arbets ytans namn.
    1. För rollen **Storage BLOB data Contributor** på lagrings kontot tilldelar du min **arbets yta** som arbets ytans identitet.
    1. Tilldela min **arbets yta** som arbets ytans namn.

1. Välj **Spara**.

## <a name="open-synapse-studio"></a>Öppna Synapse Studio

När din Azure Synapse-arbetsyta har skapats kan du öppna Synapse Studio på två sätt:

* Öppna din Synapse-arbetsyta i [Azure Portal](https://portal.azure.com). Överst i **översikts** avsnittet väljer du **Starta Synapse Studio**.
* Gå till https://web.azuresynapse.net och logga in på din arbets yta.

## <a name="create-a-sql-pool"></a>Skapa en SQL-pool

1. I Synapse Studio väljer du **Hantera**  >  **SQL-pooler**i det vänstra fönstret.
1. Välj **ny** och ange följande inställningar:

    |Inställningen | Föreslaget värde | 
    |---|---|---|
    |**SQL-poolnamn**| **SQLDB1**|
    |**Prestanda nivå**|**DW100C**|
    |||

1. Välj **Granska + skapa**  >  **skapa**. SQL-poolen är klar om några minuter. SQL-poolen är kopplad till en SQL-adresspool som också kallas **SQLDB1**.

En SQL-pool förbrukar fakturerbara resurser så länge den är aktiv. Du kan pausa poolen senare för att minska kostnaderna.

## <a name="create-an-apache-spark-pool"></a>Skapa en Apache Spark pool

1. I Synapse Studio väljer du **Hantera**  >  **Apache Spark pooler**i det vänstra fönstret.
1. Välj **ny** och ange följande inställningar:

    |Inställningen | Föreslaget värde | 
    |---|---|---|
    |**Namn på Apache Spark bassäng**|**Spark1**
    |**Node-storlek**| **Liten**|
    |**Antal noder**| Ange minst 3 och maximalt 3|

1. Välj **Granska + skapa**  >  **skapa**. Apache Spark-poolen är klar efter några sekunder.

> [!NOTE]
> Trots namnet är en Apache Spark pool inte som en SQL-pool. Det är bara grundläggande metadata som du använder för att berätta för Azure Synapse-arbetsytan hur du interagerar med Spark.

Eftersom de är metadata kan inte Spark-pooler startas eller stoppas.

När du utför Spark-aktivitet i Azure Synapse anger du en spark-pool som ska användas. Poolen talar om för Azure Synapse hur många Spark-resurser som ska användas. Du betalar bara för de resurser du använder. När du aktivt slutar använda poolen är resurserna automatiskt utgångna och återvinns.

> [!NOTE]
> Spark-databaser skapas oberoende av Spark-pooler. En arbets yta har alltid en spark-databas som kallas **default**. Du kan skapa ytterligare Spark-databaser.

## <a name="the-sql-on-demand-pool"></a>SQL on-demand-poolen

Varje arbets yta levereras med en fördefinierad pool som kallas **SQL på begäran**. Det går inte att ta bort poolen. SQL on-demand-poolen gör att du kan arbeta med SQL utan att behöva skapa eller tänka på att hantera en SQL-pool i Azure Synapse.

Till skillnad från andra typer av pooler baseras faktureringen av SQL på begäran baserat på mängden data som genomsöks för att köra frågan, inte antalet resurser som används för att köra frågan.

* SQL på begäran har egna SQL-databaser på begäran som finns oberoende av en SQL-pool på begäran.
* En arbets yta har alltid exakt en SQL-pool på begäran som heter **SQL på begäran**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Läs in NYC taxi-exempelprogrammet i SQLDB1-databasen

1. På den översta blå menyn i Synapse Studio väljer du **?** -ikonen.
1. Välj **Kom igång**  >  **kom**igång-hubb.
1. På kortet med etiketten **fråga exempel data**väljer du SQL-poolen med namnet **SQLDB1**.
1. Välj **fråga efter data**. Ett meddelande om att läsa in exempel data visas kort. Ett ljust blått statusfält längst upp i Synapse Studio anger att data läses in i SQLDB1.
1. När statusfältet är grönt stänger du det.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Utforska NYC taxi-data i SQL-poolen

1. Gå till **datahubben** i Synapse Studio.
1. Gå till **SQLDB1**-  >  **tabeller**. Du ser att flera tabeller har lästs in.
1. Högerklicka på **dbo. Rese** tabell och välj **nytt SQL-skript**  >  **Markera de 100 översta raderna**.
1. Vänta medan ett nytt SQL-skript skapas och körs.
1. Observera att överst i SQL-skriptet **Connect to** anges automatiskt till SQL-poolen med namnet **SQLDB1**.
1. Ersätt texten i SQL-skriptet med den här koden och kör den.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Den här frågan visar hur det totala antalet rese avstånd och det genomsnittliga rese avståndet avser antalet passagerare.
1. I resultat fönstret för SQL-skript ändrar du **vyn** till **diagram** för att visa en visualisering av resultaten som ett linje diagram.

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Läs in NYC taxi-data till Spark nyctaxi-databasen

Det finns data som är tillgängliga i en tabell i **SQLDB1**. Läs in den i en spark-databas med namnet **nyctaxi**.

1. Gå till **utveckla** hubben i Synapse Studio.
1. Välj **+**  >  **antecknings bok**.
1. Ange värdet **koppla till** värde till **Spark1**överst i antecknings boken.
1. Välj **Lägg till kod** för att lägga till en kod cell i antecknings boken och klistra sedan in följande text:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Gå till **data** hubben, högerklicka på **databaser**och välj sedan **Uppdatera**. Du bör se dessa databaser:

    - **SQLDB1** (SQL-pool)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analysera NYC taxi-data med Spark och Notebooks

1. Återgå till din bärbara dator.
1. Skapa en ny kod cell och ange följande text. Kör sedan cellen för att visa de NYC taxi-data som vi läste in i **nyctaxi** Spark-databasen.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Kör följande kod för att utföra samma analys som vi gjorde tidigare med SQL-poolens **SQLDB1**. Den här koden sparar resultatet av analysen i en tabell med namnet **nyctaxi. passengercountstats** och visualiserar resultaten.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. I cell resultaten väljer du **diagram** för att visa data som visualiseras.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Anpassa data visualisering med Spark och Notebook

Du kan styra hur diagram ska återges med hjälp av antecknings böcker. Följande kod visar ett enkelt exempel. Den använder populära bibliotek **matplotlib** och **Seaborn**. Koden återger samma typ av linje diagram som de SQL-frågor vi körde tidigare.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```

## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Läsa in data från en spark-tabell till en SQL-adresspool

Tidigare kopierade data från SQL-adresspoolen **SQLDB1. dbo. resan** till Spark-tabellen **nyctaxi. resa**. Därefter aggregerade vi data till Spark-tabellen **nyctaxi. passengercountstats**med Spark. Nu ska vi kopiera data från **nyctaxi. passengercountstats** till en SQL-pool med namnet **SQLDB1. dbo. passengercountstats**.

Kör följande cell i antecknings boken. Den sammanställda Spark-tabellen kopieras tillbaka till tabellen SQL-pool.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analysera NYC taxi-data i Spark-databaser med SQL på begäran

Tabeller i Spark-databaser visas automatiskt och de kan frågas efter SQL på begäran.

1. Gå till **utveckla** hubben i Synapse Studio och skapa ett nytt SQL-skript.
1. Ange **Anslut till** **SQL på begäran**.
1. Klistra in följande text i skriptet och kör skriptet.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Första gången du kör en fråga som använder SQL på begäran tar det cirka 10 sekunder för SQL på begäran att samla in de SQL-resurser som krävs för att köra dina frågor. Efterföljande frågor blir mycket snabbare.
  
## <a name="orchestrate-activities-with-pipelines"></a>Dirigera aktiviteter med pipelines

Du kan dirigera en mängd olika uppgifter i Azure Synapse.

1. I Synapse Studio går du till **Orchestration** -hubben.
1. Välj **+**  >  **pipeline** för att skapa en ny pipeline.
1. Gå till **utveckla** hubben och leta upp den antecknings bok som du skapade tidigare.
1. Dra den bärbara datorn till pipelinen.
1. I pipelinen väljer du **Lägg till utlösare**  >  **ny/redigera**.
1. I **Välj utlösare**väljer du **ny**och sedan i **upprepning** anger du att utlösaren ska köras var 1 timme.
1. Välj **OK**.
1. Välj **Publicera alla**. Pipelinen körs varje timme.
1. Om du vill att pipelinen ska köras nu, utan att vänta i nästa timma, väljer du **Lägg till utlösare**  >  **ny/redigera**.

## <a name="work-with-data-in-a-storage-account"></a>Arbeta med data i ett lagrings konto

Hittills har vi täckt scenarier där data finns i databaser på arbets ytan. Nu ska vi visa hur du arbetar med filer i lagrings konton. I det här scenariot använder vi det primära lagrings kontot för arbets ytan och behållaren som vi angav när du skapade arbets ytan.

* Namnet på lagrings kontot: **contosolake**
* Namnet på behållaren i lagrings kontot: **användare**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Skapa CSV-och Parquet-filer i ditt lagrings konto

Kör följande kod i en bärbar dator. Den skapar en CSV-fil och en Parquet-fil i lagrings kontot.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>Analysera data i ett lagrings konto

1. I Synapse Studio går du till **data** hubben och väljer sedan **länkad**.
1. Gå till **Storage Accounts**-  >  **arbetsytan (Primary-contosolake)**.
1. Välj **användare (primär) "**. Du bör se mappen **NYCTaxi** . Inuti bör du se två mappar som heter **PassengerCountStats.csv** och **PassengerCountStats. Parquet**.
1. Öppna mappen **PassengerCountStats. Parquet** . Inuti ser du en Parquet-fil med ett namn som *del-200 000-2638e00c-0790-496b-a523-578da9a15019-C000. fästfunktionen. Parquet*.
1. Högerklicka på **. Parquet**och välj sedan **ny antecknings bok**. Den skapar en antecknings bok som har en cell som den här:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Kör cellen.
1. Högerklicka på Parquet-filen inuti och välj sedan **nytt SQL-skript**  >  **Markera de 100 översta raderna**. Det skapar ett SQL-skript som detta:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

     I skriptet anges fältet **bifoga till** **SQL på begäran**.

1. Kör skriptet.

## <a name="visualize-data-with-power-bi"></a>Visualisera data med Power BI

Från NYC taxi-data skapade vi sammanställda data uppsättningar i två tabeller:
- **nyctaxi.passengercountstats**
- **SQLDB1. dbo. PassengerCountStats**

Du kan länka en Power BI arbets yta till din Azure Synapse-arbetsyta. På så sätt kan du enkelt hämta data till din Power BI-arbetsyta. Du kan redigera dina Power BI rapporter direkt i din Azure Synapse-arbetsyta.

### <a name="create-a-power-bi-workspace"></a>Skapa en Power BI arbets yta

1. Logga in på [PowerBI.Microsoft.com](https://powerbi.microsoft.com/).
1. Skapa en ny Power BI arbets yta med namnet **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Länka din Azure Synapse-arbetsyta till din nya Power BI-arbetsyta

1. I Synapse Studio går du till **Hantera**  >  **länkade tjänster**.
1. Välj **ny**  >  **Anslut till Power BI**och ange sedan följande fält:

    |Inställningen | Föreslaget värde | 
    |---|---|
    |**Namn**|**NYCTaxiWorkspace1**|
    |**Namn på arbetsyta**|**NYCTaxiWorkspace1**|

1. Välj **Skapa**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Skapa en Power BI-datauppsättning som använder data i din Azure Synapse-arbetsyta

1. I Synapse Studio går du till **utveckla**  >  **Power BI**.
1. Gå till **NYCTaxiWorkspace1**  >  **Power BI data uppsättningar** och välj **ny Power BI data uppsättning**.
1. Hovra över **SQLDB1** -databasen och välj **Hämta. pbids-fil**.
1. Öppna den nedladdade **. pbids** -filen. Power BI Skriv bordet öppnas och ansluter automatiskt till **SQLDB1** i din Azure Synapse-arbetsyta.
1. Om en dialog ruta visas som kallas **SQL Server-databas**:
    1. Välj **Microsoft-konto**.
    1. Välj **Logga** in och logga in på ditt konto.
    1. Välj **Anslut**.
1. När dialog rutan **navigatör** öppnas kontrollerar du tabellen **PassengerCountStats** och väljer **load**.
1. När dialog rutan **anslutnings inställningar** visas väljer du **DirectQuery**  >  **OK**.
1. Välj **rapport** knappen på vänster sida.
1. Lägg till **linje diagram** i rapporten.
    1. Dra kolumnen **PassengerCount** till **visualiserings**  >  **axeln**.
    1. Dra kolumnerna **SumTripDistance** och **AvgTripDistance** till **visualiserings**  >  **värden**.
1. På fliken **Start** väljer du **publicera**.
1. Välj **Spara** för att spara ändringarna.
1. Välj fil namnet **PassengerAnalysis. pbix**och välj sedan **Spara**.
1. I **Välj ett mål**väljer du **NYCTaxiWorkspace1**och klickar sedan på **Välj**.
1. Vänta tills publiceringen har slutförts.

### <a name="configure-authentication-for-your-dataset"></a>Konfigurera autentisering för din data uppsättning

1. Öppna [PowerBI.Microsoft.com](https://powerbi.microsoft.com/) och **Logga**in.
1. På den vänstra sidan, under **arbets ytor**, väljer du arbets ytan **NYCTaxiWorkspace1** .
1. På arbets ytan letar du reda på en data uppsättning som kallas **passagerar analys** och en rapport som kallas **passagerar analys**.
1. Hovra över **PassengerAnalysis** -datauppsättningen, Välj knappen med tre punkter (...) och välj sedan **Inställningar**.
1. I **autentiseringsuppgifter för data källa**anger du **autentiseringsmetoden** till **OAuth2**och väljer sedan **Logga**in.

### <a name="edit-a-report-in-synapse-studio"></a>Redigera en rapport i Synapse Studio

1. Gå tillbaka till Synapse Studio och välj **Stäng och uppdatera**.
1. Gå till **utveckla** hubben.
1. Hovra över **Power BI** och välj noden uppdatera **Power BI rapporter** .
1. Under **Power BI** bör du se:
    1. Under **NYCTaxiWorkspace1**  >  **Power BI data uppsättningar**, en ny data uppsättning som kallas **PassengerAnalysis**.
    1. Under **NYCTaxiWorkspace1**  >  **Power BI rapporter**kallas en ny rapport som heter **PassengerAnalysis**.
1. Välj **PassengerAnalysis** -rapporten. Rapporten öppnas och du kan redigera den direkt i Synapse Studio.

## <a name="monitor-activities"></a>Övervaka aktiviteter

1. Gå till **Monitor** Hub i Synapse Studio.
1. På den här platsen kan du se en historik över alla aktiviteter som sker i arbets ytan och vilka som är aktiva nu.
1. Utforska **pipeline-körningar**, **Apache Spark program**och **SQL-begäranden** för att se vad du redan har gjort i arbets ytan.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure Synapse Analytics (för hands versioner av arbets ytor)](overview-what-is.md).


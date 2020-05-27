---
title: 'Självstudie: komma igång med Azure Synapse Analytics'
description: Steg för steg för att snabbt förstå grundläggande koncept i Azure Synapse
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: dcad90713227e55437523c91997175242078e9e4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836489"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Komma igång med Azure Synapse Analytics

Den här självstudien vägleder dig genom alla grundläggande steg som krävs för att konfigurera och använda Azure Synapse Analytics.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Förbereda ett lagrings konto för användning med en Synapse-arbetsyta

1. Öppna [Azure Portal](https://portal.azure.com)
1. Skapa ett nytt lagrings konto med följande inställningar:
    * På fliken **grundläggande**

    |Inställningen | Föreslaget värde | Description |
    |---|---|---|
    |**Lagrings konto namn**| Du kan ge den namnet.|I det här dokumentet kommer vi att se det som `contosolake` .
    |**Typ av konto**|Måste anges till`StorageV2`||
    |**Position**|Du kan välja valfri plats| Vi rekommenderar att ditt Synapse-konto för arbets yta och Azure Data Lake Storage (ADLS) är i samma region.|
    ||||
    
    * På fliken **Avancerat**
    
    |Inställningen | Föreslaget värde | Description |
    |---|---|---|
    |**Data Lake Storage Gen2**|`Enabled`| Azure Synapse fungerar bara med lagrings konton där den här inställningen är aktive rad.|
    ||||

1. När lagrings kontot har skapats väljer du **åtkomst kontroll (IAM)** i det vänstra navigerings fältet. Tilldela sedan följande roller eller se till att de redan har tilldelats. 
    a. * Tilldela dig själv **ägar** rollen på lagrings kontot b. * Tilldela dig själv rollen som **Storage BLOB data-ägare** på lagrings kontot
1. I det vänstra navigerings fältet väljer du **behållare** och skapar en behållare. Du kan ge den namnet. Godkänn standard **nivån för offentlig åtkomst**. I det här dokumentet kommer vi att anropa behållaren `users` . Välj **Skapa**. 

## <a name="create-a-synapse-workspace"></a>Skapa en Synapse-arbetsyta

1. Öppna [Azure Portal](https://portal.azure.com) och högst upp Sök efter `Synapse` .
1. I Sök resultaten under **tjänster**väljer du **Azure Synapse Analytics (för hands versioner av arbets ytor)**
1. Välj **+ Lägg till**
1. Fliken **grunder** :

    |Inställningen | Föreslaget värde | Description |
    |---|---|---|
    |**Namn på arbetsyta**|Du kan anropa det något.| I det här dokumentet kommer vi att använda`myworkspace`
    |**Region**|Matcha lagrings kontots region||
    |||

1. Under **välj Data Lake Storage gen 2**väljer du det konto och den behållare som du skapade tidigare.
    > [!NOTE]
    > Vi refererar till det lagrings konto som valts här som "primärt" lagrings konto för Synapse-arbetsytan. Det här kontot används för att lagra data i Apache Spark-tabeller och för loggar som skapas när Spark-pooler skapas eller Spark-program körs.

1. Välj **Granska + skapa**. Välj **Skapa**. Din arbets yta är klar om några minuter.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Kontrol lera att MSI-Synapse Workspace har åtkomst till lagrings kontot

Detta kanske redan har gjorts. I så fall bör du kontrol lera.

1. Öppna [Azure Portal](https://portal.azure.com) och öppna det primära lagrings kontot som valts för din arbets yta.
1. Välj **åtkomst kontroll (IAM)** från det vänstra navigerings fältet. Tilldela sedan följande roller eller se till att de redan har tilldelats. 
    a. Tilldela arbets ytans identitet till **Storage BLOB data Contributor** -rollen på lagrings kontot. Arbets ytans identitet har samma namn som arbets ytan. I det här dokumentet är arbets ytans namn `myworkspace` så att arbets ytans identitet är`myworkspaced`
1. Välj **Spara**.
    
## <a name="launch-synapse-studio"></a>Starta Synapse Studio

När din Synapse-arbetsyta har skapats kan du öppna Synapse Studio på två sätt:
* Öppna din Synapse-arbetsyta i [Azure Portal](https://portal.azure.com) och överst i **översikts** avsnittet väljer du **Starta Synapse Studio**
* Gå direkt till https://web.azuresynapse.net och logga in på din arbets yta.

## <a name="create-a-sql-pool"></a>Skapa en SQL-pool

1. I Synapse Studio väljer du **hantera > SQL-pooler** på vänster sida.

    > [!NOTE] 
    > Alla Synapse-arbetsytor levereras med en förskapad pool som kallas **SQL på begäran**.

1. Välj **+ ny** och ange följande inställningar:

    |Inställningen | Föreslaget värde | 
    |---|---|---|
    |**SQL-poolnamn**| `SQLDB1`|
    |**Prestanda nivå**|`DW100C`|
    |||

1. Välj **Granska + skapa** och välj sedan **skapa**.
1. SQL-poolen är klar om några minuter.

    > [!NOTE]
    > En Synapse SQL-pool motsvarar det som används för att kallas "Azure SQL Data Warehouse"

En SQL-pool förbrukar fakturerbara resurser så länge den körs. Så du kan pausa poolen vid behov för att minska kostnaderna.

När SQL-poolen skapas kommer den att associeras med en SQL-adresspool som också kallas **SQLDB1**.

## <a name="create-an-apache-spark-pool"></a>Skapa en Apache Spark pool

1. I Synapse Studio väljer du **hantera > Apache Spark pooler** på vänster sida
1. Välj **+ ny** och ange följande inställningar:

    |Inställningen | Föreslaget värde | 
    |---|---|---|
    |**Namn på Apache Spark bassäng**|`Spark1`
    |**Node-storlek**| `Small`|
    |**Antal noder**| Ange minst 3 och maximalt 3|
    |||

1. Välj **Granska + skapa** och välj sedan **skapa**.
1. Apache Spark-poolen är klar efter några sekunder.

> [!NOTE]
> Trots namnet är en Apache Spark pool inte som en SQL-pool. Det är bara grundläggande metadata som du använder för att informera Synapse-arbetsytan så att du kan interagera med Spark. 

Eftersom de är metadata kan inte Spark-pooler startas eller stoppas. 

När du utför en spark-aktivitet i Synapse anger du en spark-pool som ska användas. Poolen informerar Synapse hur många Spark-resurser som ska användas. Du betalar bara för de resurser som Thar används. När du aktivt slutar använda poolen upphör resurserna automatiskt att fungera och återvinns.

> [!NOTE]
> Spark-databaser skapas oberoende av Spark-pooler. En arbets yta har alltid en spark DB som kallas **standard** och du kan skapa ytterligare Spark-databaser.

## <a name="the-sql-on-demand-pool"></a>SQL on-demand-poolen

Varje arbets yta levereras med en fördefinierad och en pool med namnet **SQL på begäran**som inte går att ta bort. SQL på begäran-poolen gör att du kan arbeta med SQL utan att behöva skapa eller tänka på att hantera en Synapse SQL-pool. Till skillnad från andra typer av pooler baseras faktureringen av SQL på begäran baserat på mängden data som genomsöks för att köra frågan och inte antalet resurser som används för att köra frågan.

* SQL på begäran har också egna SQL-databaser på begäran som finns oberoende av en SQL-pool på begäran.
* För närvarande har en arbets yta alltid exakt en SQL-pool på begäran som heter **SQL på begäran**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Läs in NYC taxi-exempelprogrammet i SQLDB1-databasen

1. I Synapse Studio, på den översta blå menyn, väljer du **?** .
1. Välj **komma igång > kom igång-hubb**
1. I kortet med etiketten **fråga exempel data**väljer du SQL-poolen med namnet`SQLDB1`
1. Välj **fråga efter data**. Du ser ett meddelande som säger "läser in exempel data" som visas och försvinner.
1. Du ser ett ljust blått meddelande fält längst upp i Synapse Studio som anger att data läses in i SQLDB1. Vänta tills den blir grön och stäng sedan den.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Utforska NYC taxi-data i SQL-poolen

1. I Synapse Studio navigerar du till **data** hubben
1. Navigera till **SQLDB1 > tabeller**. Du ser att flera tabeller har lästs in.
1. Högerklicka på **dbo. Rese** tabell och välj **nytt SQL-skript > markera de 100 översta raderna**
1. Ett nytt SQL-skript kommer att skapas och köras automatiskt.
1. Observera att överst i SQL-skriptet **Connect to** anges automatiskt till SQL-poolen med namnet SQLDB1.
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

1. Den här frågan visar hur det totala antalet rese avstånd och det genomsnittliga rese avståndet avser antalet passagerare
1. I resultat fönstret för SQL-skript ändrar du **vyn** till **diagram** för att visa en visualisering av resultaten som ett linje diagram

## <a name="load-the-nyc-taxi-sample-data-into-the-spark-nyctaxi-database"></a>Läs in NYC taxi-exempelprogrammet i Spark nyctaxi-databasen

Det finns data som är tillgängliga i en tabell i `SQLDB1` . Nu läser vi in det i en spark-databas med namnet ' nyctaxi '.

1. I Synapse Studio navigerar du till **utveckla** hubben
1. Välj **+** och välj **Notebook**
1. Längst upp i antecknings boken ställer du in värdet för **koppla till**`Spark1`
1. Välj **Lägg till kod** för att lägga till en kod cell i antecknings boken och klistra in texten nedan:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Navigera till **data** hubben, högerklicka på **databaser** och välj **Uppdatera**.
1. Nu bör du se dessa databaser:
    - SQLDB (SQL-pool)
    - nyctaxi (Spark)
      
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analysera NYC taxi-data med Spark och Notebooks

1. Återgå till din bärbara dator
1. Skapa en ny Code-cell, ange texten nedan och kör cellen för att till exempel NYC taxi-data som vi läste in i `nyctaxi` Spark-databasen.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Kör följande kod för att utföra samma analys som vi gjorde tidigare med SQL-poolen `SQLDB1` . I den här koden sparas även resultatet av analysen i en tabell med namnet `nyctaxi.passengercountstats` och visualiserar resultaten.

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

1. I cell resultaten väljer du **diagram** för att se de visualiserade data
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Anpassa data visualiserings data med Spark och Notebook

Med antecknings böcker kan du styra hur diagram återges. Följande kod visar ett enkelt exempel som använder populära bibliotek `matplotlib` och `seaborn` . Den kommer att återge samma typ av linje diagram som du såg när du körde SQL-frågorna tidigare.

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

Tidigare kopierade data från en SQL-adresspool `SQLDB1.dbo.Trip` till en spark-tabell `nyctaxi.trip` . Därefter aggregerade vi data till Spark-tabellen med Spark `nyctaxi.passengercountstats` . Nu ska vi kopiera data från `nyctaxi.passengercountstats` en SQL-adresspool som kallas `SQLDB1.dbo.PassengerCountStats` . 

Kör cellen nedan i din bärbara dator. Den sammanställda Spark-tabellen kopieras tillbaka till tabellen SQL-pool.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analysera NYC taxi-data i Spark-databaser med SQL på begäran 

Tabeller i Spark-databaser visas automatiskt och kan efter frågas av SQL på begäran.

1. I Synapse Studio navigerar du till **utveckla** hubben och skapar ett nytt SQL-skript
1. Ange **Anslut till** **SQL på begäran** 
1. Klistra in följande text i skriptet och kör skriptet.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```
    > [!NOTE]
    > Första gången du kör en fråga som använder SQL på begäran tar det cirka 10 sekunder för SQL på begäran att samla in de SQL-resurser som krävs för att köra dina frågor. Efterföljande frågor kräver inte den här tiden och går mycket snabbare.
  
## <a name="orchestrate-activities-with-pipelines"></a>Dirigera aktiviteter med pipelines

Du kan dirigera en mängd olika uppgifter i Azure Synapse. I det här avsnittet ser du hur enkelt det är.

1. I Synapse Studio navigerar du till **Orchestration** -hubben.
1. Välj **+** och välj **pipeline**. En ny pipeline kommer att skapas.
1. Navigera till utveckla hubben och leta upp den antecknings bok som du skapade tidigare.
1. Dra den bärbara datorn till pipelinen.
1. I pipelinen väljer du **Lägg till utlösare > ny/redigera**.
1. I **Välj utlösare** väljer du **ny**och sedan i upprepning anger du att utlösaren ska köras var 1 timme.
1. Välj **OK**.
1. Välj **publicera alla** och pipelinen körs varje timme.
1. Om du vill att pipelinen ska köras nu utan att vänta i nästa timma väljer du **Lägg till utlösare > ny/redigera**.

## <a name="working-with-data-in-a-storage-account"></a>Arbeta med data i ett lagrings konto

Hittills har vi i så fall varit data som finns i databaserna på arbets ytan. Nu ska vi visa hur du arbetar med filer i lagrings konton. I det här scenariot använder vi det primära lagrings kontot för arbets ytan och behållaren som vi angav när du skapade arbets ytan.

* Namnet på lagrings kontot:`contosolake`
* Namnet på behållaren i lagrings kontot:`users`

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>Skapa CSV-och Parquet-filer i ditt lagrings konto

Kör följande kod i en bärbar dator. Den skapar en CSV-fil och en Parquet-fil i lagrings kontot

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>Analysera data i ett lagrings konto

1. I Synapse Studio navigerar du till **data** hubben
1. Välj **länkad**
1. Gå till **lagrings konton > min arbets yta (Primary-contosolake)**
1. Välj **användare (primär) "**
1. Du bör se en mapp med namnet ' NYCTaxi '. Inuti bör du se två mappar ' PassengerCountStats. csv ' och ' PassengerCountStats. Parquet '.
1. Navigera till mappen "PassengerCountStats. Parquet".
1. Högerklicka på Parquet-filen i och välj **ny antecknings bok**så skapas en antecknings bok med en cell som detta:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Kör cellen.
1. Högerklicka på Parquet-filen inuti och välj **nytt SQL-skript > Markera de 100 översta raderna**så skapas ett SQL-skript som detta:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
1. I skriptet anges fältet **bifoga till** **SQL på begäran**.
1. Kör skriptet.

## <a name="visualize-data-with-power-bi"></a>Visualisera data med Power BI

Från NYX taxi-data skapade vi sammanställda data uppsättningar i två tabeller:
* `nyctaxi.passengercountstats`
* `SQLDB1.dbo.PassengerCountStats`

Du kan länka en Power BI arbets yta till din Synapse-arbetsyta. På så sätt kan du enkelt hämta data till din Power BI-arbetsyta och du kan redigera dina Power BI rapporter direkt på din Synapse-arbetsyta.

### <a name="create-a-power-bi-workspace"></a>Skapa en Power BI arbets yta

1. Logga in på [PowerBI.Microsoft.com](https://powerbi.microsoft.com/).
1. Skapa en ny Power BI-arbetsyta med namnet `NYCTaxiWorkspace1` .

### <a name="link-your-synapse-workspace-to-your-new-power-bi-workspace"></a>Länka din Synapse-arbetsyta till din nya Power BI-arbetsyta

1. I Synapse Studio navigerar du till **hanterade > länkade tjänster**.
1. Välj **+ nytt** och välj **Anslut till Power BI** och ange följande fält:

    |Inställningen | Föreslaget värde | 
    |---|---|---|
    |**Namn**|`NYCTaxiWorkspace1`|
    |**Namn på arbetsyta**|`NYCTaxiWorkspace1`|
    |||
    
1. Välj **Skapa**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>Skapa en Power BI-datauppsättning som använder data i din Synapse-arbetsyta

1. Navigera till **utveckla >s Power BI**i Synapse Studio.
1. Gå till **NYCTaxiWorkspace1 > Power BI data uppsättningar** och välj **ny Power BI data uppsättning**.
1. Hovra över `SQLDB1` databasen och välj **Hämta. pbids-fil**.
1. Öppna den nedladdade `.pbids` filen. 
1. Detta startar Power BI Desktop och ansluter det automatiskt till `SQLDB1` i din Synapse-arbetsyta.
1. Om en dialog ruta visas som kallas **SQL Server-databas**: a. Välj **Microsoft-konto**. 
    b. Välj **Logga** in och logga in.
    c. Välj **Anslut**.
1. **Navigerings** dialog rutan öppnas. När det sker kontrollerar du **PassengerCountStats** -tabellen och väljer **load**.
1. Dialog rutan **anslutnings inställningar** visas. Välj **DirectQuery** och välj **OK**
1. Välj **rapport** knappen till vänster.
1. Lägg till **linje diagram** i rapporten.
    a. Dra kolumnen **PasssengerCount** till **visualiseringar > axel** b. Dra kolumnerna **SumTripDistance** och **AvgTripDistance** till **visualiseringar > värden**.
1. På fliken **Start** väljer du **publicera**.
1. Du blir tillfrågad om du vill spara ändringarna. Välj **Spara**.
1. Du uppmanas att välja ett fil namn. Välj `PassengerAnalysis.pbix` och välj **Spara**.
1. Du uppmanas att **välja ett mål** val `NYCTaxiWorkspace1` och välja **Välj**.
1. Vänta tills publiceringen har slutförts.

### <a name="configure-authentication-for-your-dataset"></a>Konfigurera autentisering för din data uppsättning

1. Öppna [PowerBI.Microsoft.com](https://powerbi.microsoft.com/) och **Logga** in
1. Till vänster väljer du arbets ytan under **arbets ytor** `NYCTaxiWorkspace1` .
1. Inuti arbets ytan bör du se en data uppsättning `Passenger Analysis` som kallas och en rapport som kallas `Passenger Analysis` .
1. Hovra över `PassengerAnalysis` data uppsättningen och välj ikonen med tre punkter och välj **Inställningar**.
1. I **autentiseringsuppgifter för data källa**anger du **autentiseringsmetoden** till **OAuth2** och väljer **Logga**in.

### <a name="edit-a-report-in-synapse-studio"></a>Redigera en rapport i Synapse Studio

1. Gå tillbaka till Synapse Studio och välj **Stäng och uppdatera** 
1. Navigera till **utveckla** hubben 
1. Hovra över **Power BI** och klicka på noden uppdatera **Power BI rapporter** .
1. Nu under **Power BI** bör du se: a. * Under **NYCTaxiWorkspace1 > Power BI data uppsättningar**, en ny data uppsättning som heter **PassengerAnalysis**.
    b. * Under **NYCTaxiWorkspace1 > Power BI rapporter**skapas en ny rapport med namnet **PassengerAnalysis**.
1. Välj **PassengerAnalysis** -rapporten. 
1. Rapporten öppnas och nu kan du redigera rapporten direkt i Synapse Studio.

## <a name="monitor-activities"></a>Övervaka aktiviteter

1. Navigera till Monitor Hub i Synapse Studio.
1. På den här platsen kan du se en historik över alla aktiviteter som sker i arbets ytan och vilka som är aktiva nu.
1. Utforska **pipeline-körningar**, **Apache Spark program**och **SQL-begäranden** och du kan se vad du redan har gjort i arbets ytan.

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Synapse Analytics (för hands version)](overview-what-is.md)


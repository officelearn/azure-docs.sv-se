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
ms.openlocfilehash: e826075d2f0032f796ebe6d2c8648130e5b453b7
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800897"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Komma igång med Azure Synapse Analytics

Den här självstudien vägleder dig genom alla grundläggande steg som krävs för att konfigurera och använda Azure Synapse Analytics.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Förbereda ett lagrings konto för användning med en Synapse-arbetsyta

* Öppna [Azure Portal](https://portal.azure.com)
* Skapa ett nytt lagrings konto med följande inställningar:
    * På fliken **grundläggande**

    |Inställningen | Föreslaget värde | Beskrivning |
    |---|---|---|
    |**Lagrings konto namn**| Du kan ge den namnet.|I det här dokumentet kommer vi att se det som `contosolake` .
    |**Typ av konto**|Måste anges till`StorageV2`||
    |**Position**|Du kan välja valfri plats| Vi rekommenderar att ditt Synapse-konto för arbets yta och Azure Data Lake Storage (ADLS) är i samma region.|
    ||||

    * På fliken **Avancerat**

    |Inställningen | Föreslaget värde | Beskrivning |
    |---|---|---|
    |**Data Lake Storage Gen2**|`Enabled`| Azure Synapse fungerar bara med lagrings konton där den här inställningen är aktive rad.|
    ||||

* När lagrings kontot har skapats ska du göra roll tilldelningarna eller se till att de redan har tilldelats. Välj **åtkomst kontroll (IAM)** i lagrings kontot i det vänstra navigerings fältet.
    * Tilldela till **ägar** rollen på lagrings kontot
    * Tilldela dig själv rollen som **Storage BLOB data-ägare** på lagrings kontot
* I det vänstra navigerings fältet väljer du **behållare** och skapar en behållare. Du kan ge den namnet. Godkänn standard **nivån för offentlig åtkomst**. I det här dokumentet kommer vi att anropa behållaren `users` . Välj **Skapa**. 

## <a name="create-a-synapse-workspace"></a>Skapa en Synapse-arbetsyta

* Öppna [Azure Portal](https://portal.azure.com) och högst upp Sök efter `Synapse` .
* I Sök resultaten under **tjänster**väljer du **Azure Synapse Analytics (för hands versioner av arbets ytor)**
* Välj **+ Lägg till**
* Fliken **grunder** :

    |Inställningen | Föreslaget värde | Beskrivning |
    |---|---|---|
    |**Namn på arbetsyta**|Du kan anropa det något.| I det här dokumentet kommer vi att använda`myworkspace`
    |**Region**|Matcha lagrings kontots region||
    |||

* Under **välj Data Lake Storage gen 2** väljer du det konto och den behållare som du skapade tidigare

> [!NOTE]
> Det lagrings konto som väljs här kallas "primärt" lagrings konto för Synapse-arbetsytan

* Välj **Granska + skapa**. Välj **Skapa**. Din arbets yta är klar om några minuter.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Kontrol lera att MSI-Synapse Workspace har åtkomst till lagrings kontot

Detta kanske redan har gjorts. I så fall bör du kontrol lera.

* Öppna [Azure Portal](https://portal.azure.com) öppna det primära lagrings kontot som valts för din arbets yta.
* Se till att följande tilldelning finns eller skapa den om den inte
    * Rollen Storage BLOB data Contributor på lagrings kontot till din arbets yta.
    * Om du vill tilldela den här rollen till arbets ytan väljer du rollen Storage BLOB data Contributor, lämnar standard **behörigheten tilldela** och i rutan **Välj** skriver du namnet på din arbets yta. Välj **Spara**.
    
## <a name="launch-synapse-studio"></a>Starta Synapse Studio

När din Synapse-arbetsyta har skapats kan du öppna Synapse Studio på två sätt:
* Öppna din Synapse-arbetsyta i [Azure Portal](https://portal.azure.com) och överst i **översikts** avsnittet väljer du **Starta Synapse Studio**
* Gå direkt till https://web.azuresynapse.net och logga in på din arbets yta.

## <a name="create-a-sql-pool"></a>Skapa en SQL-pool

* I Synapse Studio navigerar du till **hantera > SQL-pooler** på vänster sida
* Obs! alla Synapse-arbetsytor levereras med en förskapad pool som kallas **SQL på begäran**.
* Välj **+ ny** och ange följande inställningar:

    |Inställningen | Föreslaget värde | 
    |---|---|---|
    |**SQL-poolnamn**| `SQLDB1`|
    |**Prestanda nivå**|`DW100C`|
* Välj **Granska + skapa** och välj sedan **skapa**.
* Poolen är klar om några minuter.

> [!NOTE]
> En Synapse SQL-pool motsvarar det som används för att kallas "Azure SQL Data Warehouse"

* En SQL-pool förbrukar fakturerbara resurser så länge den körs. Så du kan pausa poolen vid behov för att minska kostnaderna.
* När SQL-poolen skapas kommer den att associeras med en SQL-adresspool som också kallas **SQLDB1**.

## <a name="create-an-apache-spark-pool-for-azure-synapse-analytics"></a>Skapa en Apache Spark pool för Azure Synapse Analytics

* I Synapse Studio väljer du **hantera > Apache Spark pooler** på vänster sida
* Välj **+ ny** och ange följande inställningar:

    |Inställningen | Föreslaget värde | 
    |---|---|---|
    |**Namn på Apache Spark bassäng**|`Spark1`
    |**Node-storlek**| `Small`|
    |**Antal noder**| Ange minst 3 och maximalt 3|
    |||

* Välj **Granska + skapa** och välj sedan **skapa**.
* Apache Spark-poolen är klar efter några sekunder.

> [!NOTE]
> Trots namnet är en Apache Spark pool inte som en SQL-pool. Det är bara grundläggande metadata som du använder för att informera Synapse-arbetsytan så att du kan interagera med Spark. 

* Eftersom det finns metadata Spark-pooler kan inte startas eller stoppas. 
* När du utför en spark-aktivitet i Synapse anger du en spark-pool som ska användas. Poolen informerar Synapse hur många Spark-resurser som ska användas. Du betalar bara för de resurser som Thar används. När du aktivt slutar använda poolen upphör resurserna automatiskt att fungera och återvinns.
> [!NOTE]
> Spark-databaser skapas oberoende av Spark-pooler. En arbets yta har alltid en spark DB som kallas **standard** och du kan skapa ytterligare Spark-databaser.

## <a name="sql-on-demand-pools"></a>SQL-pooler på begäran

SQL på begäran är en särskild typ av SQL-pool som alltid är tillgänglig med en Synapse-arbetsyta. Det gör att du kan arbeta med SQL utan att behöva skapa eller tänka på att hantera en Synapse SQL-pool.

> [!NOTE]
> Till skillnad från andra typer av pooler baseras faktureringen av SQL på begäran baserat på mängden data som genomsöks för att köra frågan och inte antalet resurser som används för att köra frågan.

* SQL på begäran har också en egen typ av SQL-databaser på begäran som finns oberoende av en SQL-pool på begäran.
* För närvarande har en arbets yta alltid exakt en SQL-pool på begäran som heter **SQL på begäran**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Läs in NYC taxi-exempelprogrammet i SQLDB1-databasen

* I Synapse Studio, på den översta blå menyn, väljer du **?** .
* Välj **komma igång > kom igång-hubb**
* I kortet med etiketten **fråga exempel data** väljer du SQL-poolen med namnet`SQLDB1`
* Välj **fråga efter data**. Ett meddelande visas som säger "läser in exempel data" som visas och försvinner.
* Du ser ett ljust blått meddelande fält längst upp i Synapse Studio som anger att data läses in i SQLDB1. Vänta tills den blir grön och stäng sedan den.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Utforska NYC taxi-data i SQL-poolen

* I Synapse Studio navigerar du till **data** hubben
* Navigera till **SQLDB1 > tabeller**. Du ser att flera tabeller har lästs in.
* Högerklicka på **dbo. Rese** tabell och välj **nytt SQL-skript > markera de 100 översta raderna**
* Ett nytt SQL-skript kommer att skapas och köras automatiskt.
* Observera att överst i SQL-skriptet **Connect to** anges automatiskt till SQL-poolen med namnet SQLDB1.
* Ersätt texten i SQL-skriptet med den här koden och kör den.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

* Den här frågan visar hur det totala antalet rese avstånd och det genomsnittliga rese avståndet avser antalet passagerare
* I resultat fönstret för SQL-skript ändra **vyn** till **diagram** för att visa en visualisering av resultaten som ett linje diagram

## <a name="create-a-spark-database-and-load-the-nyc-taxi-data-into-it"></a>Skapa en spark-databas och Läs in NYC taxi-data till den

Vi har tillgängliga data i en SQL-adresspool. Nu läser vi in det i en spark-databas.

* I Synapse Studio navigerar du till * * utveckla hubben "
* Välj **+** och välj **Notebook**
* Längst upp i antecknings boken ställer du in värdet för **koppla till**`Spark1`
* Välj **Lägg till kod** för att lägga till en kod cell i antecknings boken och klistra in texten nedan:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

 * Navigera till data hubben, högerklicka på databaser och välj **Uppdatera**
 * Nu bör du se dessa databaser:
     * SQLDB (SQL-pool)
     * nyctaxi (Spark)
      
 ## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analysera NYC taxi-data med Spark och Notebooks

 * Återgå till din bärbara dator
 * Skapa en ny kod cell, ange texten nedan och kör cellen

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

 * Kör den här koden för att utföra samma analys som tidigare tidigare med SQL-poolen

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

 * I cell resultaten väljer du **diagram** för att se de visualiserade data
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Anpassa data visualiserings data med Spark och Notebook

Med Spark-anteckningsböcker kan du styra exakt hur återge diagram. Följande kod visar ett enkelt exempel som använder de populära biblioteken matplotlib och Sea-föddes. Det kommer att återges i samma diagram som du såg när du körde SQL-frågorna tidigare.

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

Tidigare kopierade data från en SQL-adresspool till en spark-databas. Med Spark sammanställde vi data i nyctaxi. passengercountstats. Nu ska du köra cellen nedan i en antecknings bok så kopieras den sammanställda tabellen till SQL-adresspoolen.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analysera NYC taxi-data i Spark-databaser med SQL-on-demand 

* Tabeller i Spark-databaser visas automatiskt och kan frågas av SQL på begäran
* Gå till utveckla hubben i Synapse Studio och skapa ett nytt SQL-skript
* Ange **Anslut till** **SQL på begäran** 
* Klistra in följande text i skriptet:

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

* Välj **Kör**
* Obs: första gången du kör det tar det cirka 10 sekunder för SQL på begäran att samla in de SQL-resurser som krävs för att köra dina frågor. Efterföljande frågor kommer inte att kräva den här tiden.
  
## <a name="use-pipelines-to-orchestrate-activities"></a>Använda pipelines för att dirigera aktiviteter

Du kan dirigera en mängd olika uppgifter i Azure Synapse. I det här avsnittet ser du hur enkelt det är.

* I Synapse Studio navigerar du till Orchestration-hubben.
* Välj **+** och välj **pipeline**. En ny pipeline kommer att skapas.
* Navigera till utveckla hubben och hitta någon av de antecknings böcker som du skapade tidigare.
* Dra den bärbara datorn till pipelinen.
* I pipelinen väljer du **Lägg till utlösare > ny/redigera**.
* I **Välj utlösare** väljer du **ny**och sedan i upprepning anger du att utlösaren ska köras var 1 timme.
* Välj **OK**.
* Välj **publicera alla** och pipelinen körs varje timme.
* Om du vill att pipelinen ska köras nu utan att vänta nästa timma väljer du **Lägg till utlösare > ny/redigera**.

## <a name="working-with-data-in-a-storage-account"></a>Arbeta med data i ett lagrings konto

Hittills har vi i så fall sett till att data finns i databaserna. Nu ska vi visa hur Azure-Synapse kan analysera enkla filer i ett lagrings konto. I det här scenariot använder vi det lagrings konto och den behållare som vi kopplade arbets ytan till.

Namnet på lagrings kontot: contosolake namnet på behållaren i lagrings kontot: användare

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>Skapa CSV-och Parquet-filer i ditt lagrings konto

Kör följande kod i en bärbar dator. Den skapar en CSV-och Parquet-data i lagrings kontot

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>Analysera data i ett lagrings konto

* I Synapse Studio navigerar du till **data** hubben
* Välj **länkad**
* Gå till **lagrings konton > workspacename (Primary-contosolake)**
* Välj **användare (primär) "**
* Du bör se en mapp med namnet ' NYCTaxi '. Inuti bör du se två mappar ' PassengerCountStats. csv ' och ' PassengerCountStats. Parquet '.
* Navigera till mappen "PassengerCountStats. Parquet".
* Högerklicka på Parquet-filen i och välj ny antecknings bok så skapas en antecknings bok med en cell som detta:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

* Kör cellen för att analysera Parquet-filen med Spark.
* Högerklicka på Parquet-filen inuti och välj nytt **SQL-skript > väljer de 100 översta raderna**så skapas en antecknings bok med en cell så här:

    ```py
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
* Skriptet bifogas till **SQL på begäran** för att köra skriptet. Observera att det härleder schemat från Parquet-filen.

## <a name="visualize-data-with-power-bi"></a>Visualisera data med Power BI

Dina data kan nu enkelt analyseras och visualiseras i Power BI. Synapse erbjuder en unik integrering som gör att du kan länka en Power BI arbets yta till Synapse-arbetsytan. Innan du börjar ska du först följa stegen i den här [snabb](quickstart-power-bi.md) starten för att länka din Power BI-arbetsyta.

### <a name="create-a-power-bi-workspace-and-link-it-to-your-synapse-workspace"></a>Skapa en Power BI arbets yta och länka den till din Synapse-arbetsyta

* Logga in på [PowerBI.Microsoft.com](https://powerbi.microsoft.com/).
* Skapa en ny Power BI-arbetsyta med namnet `NYCTaxiWorkspace1` .
* I Synapse Studio navigerar du till **hanterade > länkade tjänster**.
* Välj **+ nytt** och välj **Anslut till Power BI** och ange följande fält:

    |Inställningen | Föreslaget värde | 
    |---|---|---|
    |**Namn**|`NYCTaxiWorkspace1`|
    |**Namn på arbetsyta**|`NYCTaxiWorkspace1`|
    |||
    
* Välj **Skapa**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>Skapa en Power BI-datauppsättning som använder data i din Synapse-arbetsyta

* Navigera till **utveckla >s Power BI**i Synapse Studio.
* Gå till **NYCTaxiWorkspace1 > Power BI data uppsättningar** och välj **ny Power BI data uppsättning**.
* Hovra över SQLDB1-databasen och välj **Hämta. pbids-fil**.
* Öppna den nedladdade `.pbids` filen. Detta startar Power BI Desktop och ansluter det automatiskt till SQLDB1 i din Synapse-arbetsyta.
* Om en dialog ruta visas som kallas **SQL Server-databas**:
    * Välj **Microsoft-konto**. 
    * Välj **Logga** in och logga in.
    * Välj **Anslut**.
* **Navigerings** dialog rutan öppnas. Kontrol lera tabellen **PassengerCountStats** och välj **load**.
* Dialog rutan **anslutnings inställningar** visas. Välj **DirectQuery** och välj **OK**
* Välj **rapport** knappen till vänster.
* Lägg till **linje diagram** i rapporten.
    * Dra kolumnen **PasssengerCount** till **visualiseringar > axeln**
    * Dra kolumnerna **SumTripDistance** och **AvgTripDistance** till **visualiseringar > värden**.
* På fliken **Start** väljer du **publicera**.
* Du blir tillfrågad om du vill spara ändringarna. Välj **Spara**.
* Du uppmanas att välja ett fil namn. Välj `PassengerAnalysis.pbix` och välj **Spara**.
* Du uppmanas att **välja ett mål** val `NYCTaxiWorkspace1` och välja **Välj**.
* Vänta tills publiceringen har slutförts.

### <a name="configure-authentication-for-your-dataset"></a>Konfigurera autentisering för din data uppsättning

* Öppna [PowerBI.Microsoft.com](https://powerbi.microsoft.com/) och **Logga** in
* Till vänster, under **arbets ytor** , väljer du den `NYCTaxiWorkspace1` arbets yta som du publicerade till.
* Inuti arbets ytan bör du se en data uppsättning `Passenger Analysis` som kallas och en rapport som kallas `Passenger Analysis` .
* Hovra över `PassengerAnalysis` data uppsättningen och välj ikonen med tre punkter och välj **Inställningar**.
* I **autentiseringsuppgifter för data källa** anger du autentiseringsmetoden till **OAuth2** och väljer **Logga**in.

### <a name="edit-a-report-report-in-synapse-studio"></a>Redigera en rapport rapport i Synapse Studio

* Gå tillbaka till Synapse Studio och välj **Stäng och uppdatera** nu. du bör se:
    * Under **Power BI data uppsättningar**, en ny data uppsättning som kallas **PassengerAnalysis**.
    * Under **Power BI data uppsättningar**, en ny rapport som heter **PassengerAnalysis**.
* Klicka på **PassengerAnalysis** -rapporten. 
    * Det visar ingenting eftersom du fortfarande måste konfigurera autentisering för data uppsättningen.
* I SynapseStudio navigerar du till **utveckla > Power BI > arbets ytans namn > Power BI rapporter**.
* Stäng alla fönster som visar Power BI rapporten.
* Uppdatera noden **Power BI rapporter** .
* Välj rapporten och nu kan du redigera rapporten direkt i Synapse Studio.

## <a name="monitor-activities"></a>Övervaka aktiviteter

* Navigera till Monitor Hub i Synapse Studio.
* På den här platsen kan du se en historik över alla aktiviteter som sker i arbets ytan och vilka som är aktiva nu.
* Utforska **pipeline-körningar**, **Apache Spark program**och **SQL-begäranden** och du kan se vad du redan har gjort i arbets ytan.

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Synapse Analytics (för hands version)](overview-what-is.md)


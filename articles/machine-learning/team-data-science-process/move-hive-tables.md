---
title: Skapa Hive-tabeller och läsa in data från Blob storage - Team Data Science Process
description: Använd Hive-frågor för att skapa Hive-tabeller och läsa in data från Azure blob storage. Partition Hive-tabeller och använd ORC-formateringen (Optimized Row Columnar) för att förbättra frågeprestanda.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 625d9d5c5ecf095d4acbff625754b2065f184536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251666"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Skapa Hive-tabeller och läsa in data från Azure Blob Storage

Den här artikeln innehåller allmänna Hive-frågor som skapar Hive-tabeller och läser in data från Azure blob storage. Viss vägledning ges också om partitionering av Hive-tabeller och om hur du använder ORC-formateringen (Optimized Row Columnar) för att förbättra frågeprestanda.

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du har:

* Skapade ett Azure Storage-konto. Om du behöver instruktioner läser du [Om Azure Storage-konton](../../storage/common/storage-introduction.md).
* Etablerat ett anpassat Hadoop-kluster med HDInsight-tjänsten.  Om du behöver instruktioner läser du [Konfigurera kluster i HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Aktiverad fjärråtkomst till klustret, inloggad och öppnade Hadoop Command-Line-konsolen. Om du behöver instruktioner läser du [Hantera Apache Hadoop-kluster](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure-bloblagring
Om du skapade en virtuell Azure-dator genom att följa instruktionerna i [Konfigurera en virtuell Azure-dator för avancerad analys,](../../machine-learning/data-science-virtual-machine/overview.md)borde den här skriptfilen ha hämtats till katalogen *C:\\\\\<Users-användarnamn\>\\dokumentdatavetenskapsskript\\* på den virtuella datorn. Dessa Hive-frågor kräver bara att du tillhandahåller ett dataschema och Azure blob-lagringskonfiguration i lämpliga fält för att vara redo för inlämning.

Vi förutsätter att data för Hive-tabeller är i ett **okomprimerat** tabellformat och att data har överförts till standardbehållaren (eller till en ytterligare) behållare för lagringskontot som används av Hadoop-klustret.

Om du vill öva på **NYC Taxi Trip Data**måste du:

* **ladda ner** 24 [NYC Taxi Trip Data](https://www.andresmh.com/nyctaxitrips) filer (12 Trip filer och 12 Fare filer),
* **packa upp** alla filer i CSV-filer och sedan
* **ladda upp** dem till standard -(eller lämplig behållare) för Azure Storage-kontot. alternativ för ett sådant konto visas på [Använd Azure Storage med Azure HDInsight-kluster.](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) Processen att ladda upp CSV-filerna till standardbehållaren på lagringskontot finns på den här [sidan](hive-walkthrough.md#upload).

## <a name="how-to-submit-hive-queries"></a><a name="submit"></a>Så här skickar du Hive-frågor
Hive-frågor kan skickas med hjälp av:

* [Skicka Hive-frågor via Hadoop Command Line i headnod av Hadoop-kluster](#headnode)
* [Skicka Hive-frågor till Hive Editor](#hive-editor)
* [Skicka Hive-frågor med Azure PowerShell-kommandon](#ps)

Hive-frågor är SQL-liknande. Om du är bekant med SQL, Du kan hitta [Hive för SQL-användare lathund](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) användbar.

När du skickar en Hive-fråga kan du också styra resultatets mål från Hive-frågor, oavsett om det är på skärmen eller till en lokal fil på huvudnoden eller till en Azure-blob.

### <a name="submit-hive-queries-through-hadoop-command-line-in-headnode-of-hadoop-cluster"></a><a name="headnode"></a>Skicka Hive-frågor via Hadoop Command Line i headnod av Hadoop-kluster
Om Hive-frågan är komplex, skickar du den direkt i huvudnoden i Hadoop-klustret leder vanligtvis till snabbare vändning än att skicka den med ett Hive Editor- eller Azure PowerShell-skript.

Logga in på huvudnoden för Hadoop-klustret, öppna Hadoop-kommandoraden på `cd %hive_home%\bin`skrivbordet i huvudnoden och kommandot Enter .

Du kan skicka in Hive-frågor på Hadoop-kommandoraden:

* Direkt
* använda ".hql" filer
* med befallningskonsolen Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Skicka Hive-frågor direkt i Hadoop-kommandoraden.
Du kan köra `hive -e "<your hive query>;` kommandot som att skicka enkla Hive-frågor direkt i Hadoop Command Line. Här är ett exempel där den röda rutan beskriver kommandot som skickar Hive-frågan och den gröna rutan beskriver utdata från Hive-frågan.

![Kommando för att skicka Hive-fråga med utdata från Hive-fråga](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Skicka Hive-frågor i '.hql'-filer
När Hive-frågan är mer komplicerad och har flera rader är det inte praktiskt att redigera frågor på kommandoraden eller hive-kommandokonsolen. Ett alternativ är att använda en textredigerare i huvudnoden i Hadoop-klustret för att spara Hive-frågorna i en '.hql-fil i en lokal katalog över huvudnoden. Då Hive frågan i ".hql" fil kan `-f` skickas med hjälp av argumentet på följande sätt:

    hive -f "<path to the '.hql' file>"

![Hive-fråga i en '.hql-fil](./media/move-hive-tables/run-hive-queries-3.png)

**Undertrycka utskrift av status för status för Hive-frågor**

När Hive-frågan har skickats i Hadoop-kommandoraden skrivs som standard ut förloppet för jobbet Karta/Minska på skärmen. Om du vill dölja skärmutskriften för jobbförloppet `-S` för karta/minska kan du använda ett argument ("S" i versaler) på kommandoraden enligt följande:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Skicka Hive-frågor i Hive-kommandokonsolen.
Du kan också först ange kommandotasolen Hive genom att köra kommandot `hive` i Hadoop Command Line och sedan skicka Hive-frågor i Hive-kommandokonsolen. Här är ett exempel. I det här exemplet markerar de två röda rutorna de kommandon som används för att ange hive-kommandokonsolen och Hive-frågan som skickades i Hive-kommandokonsolen. Den gröna rutan markerar utdata från Hive-frågan.

![Öppna Hive kommandokonsol och ange kommando, visa Hive fråga utdata](./media/move-hive-tables/run-hive-queries-2.png)

De tidigare exemplen matar direkt ut Hive-frågeresultatet på skärmen. Du kan också skriva utdata till en lokal fil på huvudnoden eller till en Azure-blob. Sedan kan du använda andra verktyg för att ytterligare analysera utdata från Hive-frågor.

**Utdata Hive-frågeresultat till en lokal fil.**
Om du vill mata ut Hive-frågeresultat till en lokal katalog på huvudnoden måste du skicka Hive-frågan på Hadoop-kommandoraden enligt följande:

    hive -e "<hive query>" > <local path in the head node>

I följande exempel skrivs utdata från Hive-frågan till en fil `hivequeryoutput.txt` i katalogen `C:\apps\temp`.

![Utdata från Hive-fråga](./media/move-hive-tables/output-hive-results-1.png)

**Utdata Hive-frågeresultat till en Azure-blob**

Du kan också mata ut Hive-frågeresultaten till en Azure-blob, i standardbehållaren för Hadoop-klustret. Hive-frågan för detta är följande:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

I följande exempel skrivs utdata från Hive-frågan `queryoutputdir` till en blob-katalog i standardbehållaren för Hadoop-klustret. Här behöver du bara ange katalognamnet, utan blob-namnet. Ett fel genereras om du anger både katalog- `wasb:///queryoutputdir/queryoutput.txt`och blobnamn, till exempel .

![Utdata från Hive-fråga](./media/move-hive-tables/output-hive-results-2.png)

Om du öppnar standardbehållaren för Hadoop-klustret med Azure Storage Explorer kan du se utdata för Hive-frågan som visas i följande bild. Du kan använda filtret (markerat med röd ruta) för att bara hämta blobben med angivna bokstäver med namn.

![Azure Storage Explorer som visar utdata från Hive-frågan](./media/move-hive-tables/output-hive-results-3.png)

### <a name="submit-hive-queries-with-the-hive-editor"></a><a name="hive-editor"></a>Skicka Hive-frågor till Hive Editor
Du kan också använda Frågekonsolen (Hive Editor) genom att ange en URL för formuläret *https:\//\<Hadoop-klusternamn>.azurehdinsight.net/Home/HiveEditor* i en webbläsare. Du måste vara inloggad på den här konsolen så du behöver dina Hadoop-klusterautentiseringsuppgifter här.

### <a name="submit-hive-queries-with-azure-powershell-commands"></a><a name="ps"></a>Skicka Hive-frågor med Azure PowerShell-kommandon
Du kan också använda PowerShell för att skicka Hive-frågor. Instruktioner finns i [Skicka Hive-jobb med PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-hive-database-and-tables"></a><a name="create-tables"></a>Skapa Hive-databas och tabeller
Hive-frågorna delas i [GitHub-databasen](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) och kan hämtas därifrån.

Här är Hive-frågan som skapar en Hive-tabell.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Här är beskrivningarna av de fält som du behöver för att koppla in och andra konfigurationer:

* **databasnamn:\>namnet på den databas som du vill skapa. \<** Om du bara vill använda standarddatabasen kan frågan "*skapa databas...*" utelämnas.
* **tabellnamn:\>namnet på den tabell som du vill skapa i den angivna databasen. \<** Om du vill använda standarddatabasen kan tabellen refereras direkt \<efter\> * \<tabellnamn\> * utan databasnamn .
* **fältavgränsare:\>avgränsaren som avgränsar fält i datafilen som ska överföras till tabellen Hive. \<**
* **linjeavgränsare:\>avgränsaren som avgränsar rader i datafilen. \<**
* **lagringsplats:\>Azure Storage-platsen för att spara data från Hive-tabeller. \<** Om du inte anger * \<LOCATION-lagringsplats\>* lagras databasen och tabellerna i *registreringsdatafilen/lager-/katalogen* i standardbehållaren för Hive-klustret som standard. Om du vill ange lagringsplatsen måste lagringsplatsen finnas i standardbehållaren för databasen och tabellerna. Den här platsen måste kallas plats i förhållande till standardbehållaren för klustret i formatet *"wasb:///\<katalog 1>/"* eller *"wasb:///\<katalog 1>/\<katalog 2>/"* osv. När frågan har körts skapas de relativa katalogerna i standardbehållaren.
* **TBLPROPERTIES("skip.header.line.count"="1")**: Om datafilen har en rubrikrad måste du lägga till den här **egenskapen i slutet** av *tabellen.* Annars läses rubrikraden in som en post i tabellen. Om datafilen inte har någon rubrikrad kan den här konfigurationen utelämnas i frågan.

## <a name="load-data-to-hive-tables"></a><a name="load-data"></a>Läsa in data i Hive-tabeller
Här är Hive-frågan som läser in data i en Hive-tabell.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **sökväg till\>blob-data: Om blob-filen som ska överföras till Hive-tabellen finns i standardbehållaren för HDInsight Hadoop-klustret, bör sökvägen till blob-data vara i formatet "wasb:// katalog i det här>/ blob-filnamnet>". \<** * \<\> * *\<\<* Blob-filen kan också finnas i ytterligare en behållare i HDInsight Hadoop-klustret. I det här fallet bör * \<sökvägen till\> blob-data* vara i formatet *"wasb://\<behållarnamn>\<lagringskontonamn>.blob.core.windows.net/\<blob filnamn>"*.

  > [!NOTE]
  > Blob-data som ska överföras till Hive-tabellen måste finnas i standard- eller ytterligare behållaren för lagringskontot för Hadoop-klustret. Annars misslyckas *load data-frågan* med att klaga på att den inte kan komma åt data.
  >
  >

## <a name="advanced-topics-partitioned-table-and-store-hive-data-in-orc-format"></a><a name="partition-orc"></a>Avancerade ämnen: partitionerad tabell och lagra Hive-data i ORC-format
Om data är stora är partitionering av tabellen fördelaktigt för frågor som bara behöver söka igenom några partitioner i tabellen. Det är till exempel rimligt att partitionera loggdata för en webbplats efter datum.

Förutom att partitionera Hive-tabeller är det också fördelaktigt att lagra Hive-data i ORC-formatet (Optimized Row Columnar). Mer information om ORC-formatering finns i <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">Använda ORC-filer förbättrar prestanda när Hive läser, skriver och bearbetar data</a>.

### <a name="partitioned-table"></a>Partitionerad tabell
Här är Hive-frågan som skapar en partitionerad tabell och läser in data i den.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

När du frågar partitionerade tabeller rekommenderar vi att **beginning** du `where` lägger till partitionsvillkoret i början av satsen, vilket förbättrar sökeffektiviteten.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="store-hive-data-in-orc-format"></a><a name="orc"></a>Lagra Hive-data i ORC-format
Du kan inte direkt läsa in data från blob-lagring i Hive-tabeller som lagras i ORC-format. Här är de steg som du behöver vidta för att läsa in data från Azure-blobbar till Hive-tabeller som lagras i ORC-format.

Skapa en extern tabell **som lagras som TEXTFILE** och läs in data från bloblagring till tabellen.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Skapa en intern tabell med samma schema som den externa tabellen i steg 1, med samma fältavgränsare, och lagra Hive-data i ORC-format.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Markera data från den externa tabellen i steg 1 och infoga i ORC-tabellen

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Om * \<textfile-tabelldatabasens namn\>.\< externt textfiltabellnamn\> * har partitioner, i `SELECT * FROM <database name>.<external textfile table name>` STEG 3 väljer kommandot partitionsvariabeln som ett fält i den returnerade datauppsättningen. Infoga den i * \<\>databasnamnet\< . ORC-tabellnamn\> * misslyckas sedan * \<\>databasnamnet .\< ORC-tabellnamnet\> * har inte partitionsvariabeln som ett fält i tabellschemat. I det här fallet måste du specifikt välja de fält som ska infogas i * \<databasnamnet\>.\< ORC-tabellnamn\> * enligt följande:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Det är säkert att släppa det * \<externa\> textfilstabellnamnet* när du använder följande fråga efter att alla data har infogats i * \<databasnamnet\>.\< ORC-tabellnamn:\>*

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

När du har följt den här proceduren bör du ha en tabell med data i ORC-formatet redo att användas.  

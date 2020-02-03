---
title: Skapa Hive-tabeller och läsa in data från Blob storage - Team Data Science Process
description: Använda Hive-frågor för att skapa Hive-tabeller och läsa in data från Azure blob storage. Partitionera Hive-tabeller och använda den optimerade rad kolumner (ORC) formatering för att förbättra frågeprestanda.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722534"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Skapa Hive-tabeller och läsa in data från Azure Blob Storage

Den här artikeln beskriver vi allmän Hive-frågor som skapar Hive-tabeller och läsa in data från Azure blob storage. Vägledning finns även på partitionering Hive-tabeller och om hur du använder den optimerade rad kolumner (ORC) formatering för att förbättra frågeprestanda.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Ett Azure Storage-konto har skapats. Om du behöver instruktioner, se [om Azure Storage-konton](../../storage/common/storage-introduction.md).
* Etablerat en anpassade Hadoop-kluster med HDInsight-tjänsten.  Om du behöver instruktioner, se [installations kluster i HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Aktiverade fjärråtkomst till klustret, loggat in och öppnas Hadoop-Kommandotolken. Om du behöver instruktioner, se [hantera Apache Hadoop kluster](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure blob storage
Om du har skapat en virtuell Azure-dator genom att följa anvisningarna i [Konfigurera en virtuell Azure-dator för avancerad analys](../../machine-learning/data-science-virtual-machine/overview.md), bör den här skript filen ha hämtats till *C:\\användare\\\<användar namn\>\\Documents\\data science scripts* -katalogen på den virtuella datorn. Dessa Hive-frågor kräver bara att du anger ett data schema och Azure Blob Storage-konfigurationen i lämpliga fält för att kunna skickas.

Vi antar att data för Hive-tabeller är i ett **okomprimerat** tabell format och att data har överförts till standard (eller till en ytterligare) behållare för det lagrings konto som används av Hadoop-klustret.

Om du vill öva på **NYC taxi-rese data**måste du:

* **Hämta** 24 [NYC taxi](https://www.andresmh.com/nyctaxitrips) -datafiler (12-reseinformation och 12-pris per pris).
* **zippa** upp alla filer till CSV-filer och sedan
* **överför** dem till standardvärdet (eller lämplig container) för det Azure Storage kontot. alternativ för ett sådant konto visas i avsnittet [använda Azure Storage med Azure HDInsight-kluster](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) . Processen att ladda upp CSV-filerna till standard behållaren på lagrings kontot finns på den här [sidan](hive-walkthrough.md#upload).

## <a name="submit"></a>Så här skickar du Hive-frågor
Du kan skicka hive-frågor med hjälp av:

* [Skicka Hive-frågor via Hadoop-kommandoraden i huvudnoden för Hadoop-kluster](#headnode)
* [Skicka Hive-frågor med Hive-redigeraren](#hive-editor)
* [Skicka Hive-frågor med Azure PowerShell-kommandon](#ps)

Hive-frågor körs SQL-liknande. Om du är bekant med SQL kan du hitta [Hive för SQL-användare lathund-bladet](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) .

När du skickar en Hive-fråga, kan du också styra målet utdata från Hive-frågor, oavsett om det är på skärmen eller till en lokal fil på huvudnoden eller till en Azure-blob.

### <a name="headnode"></a>Skicka Hive-frågor via Hadoop-kommandoraden i huvudnoden för Hadoop-kluster
Om Hive-frågan är komplex kan leder skicka den direkt i klustrets huvudnod Hadoop kluster normalt till returtid snabbare än att skicka den med en Hive-redigeraren eller Azure PowerShell-skript.

Logga in på noden Head i Hadoop-klustret, öppna Hadoop-kommandoraden på Skriv bordet i head-noden och ange kommandot `cd %hive_home%\bin`.

Du har tre sätt att skicka Hive-frågor i Hadoop-kommandoraden:

* direkt
* använda. HQL-filer
* med kommandokonsolen Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Skicka Hive-frågor direkt i Hadoop-kommandoraden.
Du kan köra kommandot som `hive -e "<your hive query>;` för att skicka enkla Hive-frågor direkt i Hadoop-kommandoraden. Här är ett exempel där den röda rutan beskriver det kommando som skickar Hive-fråga, och den gröna rutan visar utdata från Hive-frågan.

![Kommando för att skicka Hive-fråga med utdata från Hive-fråga](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Skicka Hive-frågor i '. HQL '-filer
När Hive-frågan är mer komplicerad och har flera rader kan är redigera frågor i kommandoraden eller Hive kommandokonsolen inte praktiskt. Ett alternativ är att använda en text redigerare i noden Head i Hadoop-klustret för att spara Hive-frågor i en. HQL-fil i en lokal katalog i head-noden. Sedan kan Hive-frågan i filen ". HQL" skickas genom att använda argumentet `-f` enligt följande:

    hive -f "<path to the '.hql' file>"

![Hive-fråga i en. HQL-fil](./media/move-hive-tables/run-hive-queries-3.png)

**Ignorera förlopps status skärm utskrift av Hive-frågor**

Som standard när Hive-frågan har skickats i Hadoop kommandoraden skrivs förloppet för Map/Reduce-jobb på skärmen. Om du inte vill att skärmen ska skrivas ut eller minska jobbets förlopp, kan du använda ett argument `-S` ("S" i versaler) på kommando raden enligt följande:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Skicka Hive-frågor i kommandokonsolen för Hive.
Du kan också först ange Hive-kommandoraden genom att köra kommandot `hive` i Hadoop-kommandoraden och sedan skicka Hive-frågor i Hive-Kommandotolken. Här är ett exempel. I det här exemplet markerar du två röda rutor de kommandon som används för att ange Hive kommandokonsolen och Hive-fråga som skickats i kommandokonsolen Hive respektive. Den gröna rutan visar utdata från Hive-frågan.

![Öppna Hive kommandokonsolen och anger kommandot kan visa utdata för Hive-fråga](./media/move-hive-tables/run-hive-queries-2.png)

I föregående exempel utdata direkt Hive-frågeresultatet på skärmen. Du kan också skriva utdata till en lokal fil på huvudnoden eller till en Azure-blob. Du kan sedan använda andra verktyg för ytterligare analys utdata för Hive-frågor.

**Resulterande Hive-frågeresultat i en lokal fil.**
Om du vill spara resultatet av Hive-frågan till en lokal katalog på huvudnoden har att skicka Hive-frågan i Hadoop-kommandoraden enligt följande:

    hive -e "<hive query>" > <local path in the head node>

I följande exempel skrivs utdata från Hive-frågan till en fil `hivequeryoutput.txt` i katalog `C:\apps\temp`.

![Utdata för Hive-fråga](./media/move-hive-tables/output-hive-results-1.png)

**Returnera utdata för Hive-frågor till en Azure-Blob**

Du kan också spara resultatet av Hive-frågan till en Azure blob i standardbehållaren för Hadoop-kluster. Hive-frågan för det här är följande:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

I följande exempel skrivs utdata från Hive-frågan till en BLOB-katalog `queryoutputdir` i standard behållaren för Hadoop-klustret. Här kan behöver du bara ange katalognamnet utan blobnamnet. Ett fel genereras om du anger både katalog-och blob-namn, till exempel `wasb:///queryoutputdir/queryoutput.txt`.

![Utdata för Hive-fråga](./media/move-hive-tables/output-hive-results-2.png)

Om du öppnar standardbehållaren för Hadoop-kluster med Azure Storage Explorer kan du se utdata för Hive-fråga som visas i följande bild. Du kan använda filter (visas med röd ram) för att endast hämta blob med angivna bokstäverna i namn.

![Azure Storage Explorer visar utdata för Hive-fråga](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a>Skicka Hive-frågor med Hive-redigeraren
Du kan också använda fråga konsolen (Hive-redigeraren) genom att ange en URL i formatet *https:\//\<Hadoop-kluster namn >. azurehdinsight. net/Home/HiveEditor* i en webbläsare. Du måste vara inloggad i ser den här konsolen och du måste ha autentiseringsuppgifter här dina Hadoop-kluster.

### <a name="ps"></a>Skicka Hive-frågor med Azure PowerShell-kommandon
Du kan också använda PowerShell för att skicka Hive-frågor. Instruktioner finns i [Skicka Hive-jobb med PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Skapa Hive-databas och tabeller
Hive-frågorna delas i GitHub- [lagringsplatsen](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) och kan laddas ned därifrån.

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

Här följer beskrivningar av de fält som ska anslutas och andra konfigurationer:

* **\<databas namn\>** : namnet på den databas som du vill skapa. Om du bara vill använda standard databasen kan frågan "*skapa databas...* " utelämnas.
* **\<tabell namn\>** : namnet på den tabell som du vill skapa i den angivna databasen. Om du vill använda standard databasen kan tabellen direkt refereras av *\<tabell namn\>* utan \<databas namn\>.
* **\<fält avgränsare\>** : avgränsaren som avgränsar fält i data filen som ska överföras till Hive-tabellen.
* **\<rad avgränsare\>** : avgränsaren som avgränsar rader i data filen.
* **\<lagrings plats\>** : Azure Storages plats för att spara data för Hive-tabeller. Om du inte anger *plats \<lagrings plats\>* , lagras databasen och tabellerna i *Hive/lager/* katalog i standard behållaren för Hive-klustret som standard. Om du vill ange lagringsplatsen måste lagringsplatsen vara i standardbehållaren för databasen och tabeller. Den här platsen måste refereras till platsen i förhållande till standard behållaren för klustret i formatet *"wasb:///\<Directory 1 >/"* eller *"wasb:///\<Directory 1 >/\<Directory 2 >/"* osv. När frågan har körts skapas de relativa katalogerna i standard behållaren.
* **TBLPROPERTIES ("Skip. header. line. Count" = "1")** : om data filen har en rubrik rad måste du lägga till den här egenskapen **i slutet** av frågan *CREATE TABLE* . I annat fall rubrikraden har lästs in som en post i tabellen. Om filen inte har en rubrikrad kan du utelämna den här konfigurationen i frågan.

## <a name="load-data"></a>Läs in data till Hive-tabeller
Här är Hive-frågan som läser in data i en Hive-tabell.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **\<sökväg till BLOB-data\>** : om BLOB-filen som ska överföras till Hive-tabellen finns i standard behållaren för HDInsight Hadoop-klustret, ska *\<sökvägen till blob-data\>* vara i formatet *' wasb://\<directory i den här behållaren >/\<BLOB File name > '* . Blob-fil kan också vara i ytterligare en behållare för HDInsight Hadoop-kluster. I det här fallet ska *\<sökväg till BLOB-data\>* ha formatet *"wasb://\<container Name >\<lagrings kontots namn >. blob. Core. Windows. net/\<blob File name >"* .

  > [!NOTE]
  > Blob-data som ska överföras till Hive-tabell måste vara i standard eller ytterligare en behållare för storage-konto för Hadoop-kluster. I annat fall Miss lyckas *inläsnings data* frågan så att den inte kan komma åt data.
  >
  >

## <a name="partition-orc"></a>Avancerade ämnen: partitionerad tabell och lagra Hive-data i ORC-format
Om data är stor kan är partitionera tabellen bra för frågor som behöver bara skanna några partitioner i tabellen. Exempelvis är det rimligt att partitionera loggdata av en webbplats med datum.

Förutom partitionering Hive-tabeller, är det också bra att lagra Hive-data i formatet optimerade rad kolumner (ORC). Mer information om ORC-formatering finns i <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">använda Orc-filer ger bättre prestanda när Hive läser, skriver och bearbetar data</a>.

### <a name="partitioned-table"></a>Partitionerad tabell
Här är Hive-frågan som skapar en partitionerad tabell och läser in data till den.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

När du frågar efter partitionerade tabeller rekommenderar vi att du lägger till ett partitionsschema i **början** av `where`-satsen, vilket förbättrar Sök effektiviteten.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Lagra Hive-data i ORC-format
Du kan inte direkt läsa in data från blob storage till Hive-tabeller som lagras i ORC-format. Här är de steg som du behöver vidta för att läsa in data från Azure BLOB-objekt till Hive-tabeller som lagras i ORC-format.

Skapa en extern tabell **som lagras som textfile** och Läs in data från Blob Storage till tabellen.

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

Skapa en intern tabell med samma schema som den externa tabellen i steg 1, med samma fältavgränsaren och lagra Hive-data i ORC-format.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Välj data från den externa tabellen i steg 1 och infoga i ORC-tabell

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Om TEXTFILE-tabellen *\<databas namn\>.\<externa textfile-\>* har partitioner, i steg 3 `SELECT * FROM <database name>.<external textfile table name>`, väljer kommandot partition variabeln som ett fält i den returnerade data uppsättningen. Lägg till den i *\<databasens namn\>.\<Orc tabell namn\>* Miss lyckas eftersom *\<databas namn\>.\<ORC tabell namn\>* saknar variabeln partition som ett fält i Table-schemat. I så fall måste du särskilt Markera de fält som ska infogas i *\<databas namn\>.\<Orc tabell namn\>* enligt följande:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Det är säkert att ta bort *\<external text fils tabell namnet\>* när du använder följande fråga efter att alla data har infogats i *\<databas namn\>.\<ORC tabell namn\>* :

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Du bör ha en tabell med data i ORC-format kan användas när du har genomfört den här proceduren.  

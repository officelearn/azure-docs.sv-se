---
title: Skapa Hive-tabeller och läsa in data från Azure Blob Storage | Microsoft Docs
description: Skapa Hive-tabeller och läsa in data i blob till hive-tabeller
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: cff9280d-18ce-4b66-a54f-19f358d1ad90
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: c1cbd523b8c74bf1221dc47b832c35e9119493a9
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51346294"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Skapa Hive-tabeller och läsa in data från Azure Blob Storage

Den här artikeln beskriver vi allmän Hive-frågor som skapar Hive-tabeller och läsa in data från Azure blob storage. Vägledning finns även på partitionering Hive-tabeller och om hur du använder den optimerade rad kolumner (ORC) formatering för att förbättra frågeprestanda.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Skapa ett Azure storage-konto. Om du behöver mer information, se [om Azure storage-konton](../../storage/common/storage-create-storage-account.md).
* Etablerat en anpassade Hadoop-kluster med HDInsight-tjänsten.  Om du behöver mer information, se [anpassa Azure HDInsight Hadoop-kluster för avancerade analyser](customize-hadoop-cluster.md).
* Aktiverade fjärråtkomst till klustret, loggat in och öppnas Hadoop-Kommandotolken. Om du behöver mer information, se [åt Head noden av Hadoop-klustret](customize-hadoop-cluster.md).

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure blob storage
Om du har skapat en Azure virtuell dator genom att följa instruktionerna i [ställa in Azure-datorer för avancerade analyser](../data-science-virtual-machine/setup-virtual-machine.md), den här skriptfilen bör har hämtats till den *C:\\användare \\ \<användarnamn\>\\dokument\\Data Science skript* på den virtuella datorn. De här Hive-frågor behöver bara att du ansluter ditt eget dataschema och Azure blob storage-konfiguration i lämpliga fält är klar att skicka.

Vi antar att data för Hive-tabeller finns i en **okomprimerade** tabellformat och att data har överförts till standardvärdet (eller till en ytterligare) behållare för det lagringskonto som används av Hadoop-kluster.

Om du vill att öva på det **NYC Taxi Resedata**, måste du:

* **ladda ned** 24 [NYC Taxi Resedata](http://www.andresmh.com/nyctaxitrips) filer (12 resans filer och 12 avgiften-filer)
* **Packa upp** alla filer i CSV-filer, och sedan
* **ladda upp** dem till standard (eller att rätt behållare) för Azure storage-konto som har skapats med proceduren som beskrivs i den [anpassa Azure HDInsight Hadoop-kluster för Advanced Analytics Process and Technology](customize-hadoop-cluster.md)avsnittet. Processen för att ladda upp CSV-filer till standardbehållaren för lagringskontot finns på den här [sidan](hive-walkthrough.md#upload).

## <a name="submit"></a>Hur du skickar in Hive-frågor
Du kan skicka hive-frågor med hjälp av:

1. [Skicka Hive-frågor via Hadoop-kommandoraden i huvudnoden av Hadoop-kluster](#headnode)
2. [Skicka Hive-frågor med Hive-redigeraren](#hive-editor)
3. [Skicka Hive-frågor med Azure PowerShell-kommandon](#ps)

Hive-frågor körs SQL-liknande. Om du är bekant med SQL, kanske den [Hive för SQL-användare facit blad](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) användbart.

När du skickar en Hive-fråga, kan du också styra målet utdata från Hive-frågor, oavsett om det är på skärmen eller till en lokal fil på huvudnoden eller till en Azure-blob.

### <a name="headnode"></a> 1. Skicka Hive-frågor via Hadoop-kommandoraden i huvudnoden av Hadoop-kluster
Om Hive-frågan är komplex kan leder skicka den direkt i klustrets huvudnod Hadoop kluster normalt till returtid snabbare än att skicka den med en Hive-redigeraren eller Azure PowerShell-skript.

Logga in till huvudnoden för Hadoop-kluster, öppna Hadoop-kommandoraden på skrivbordet för huvudnoden och anger kommandot `cd %hive_home%\bin`.

Du har tre sätt att skicka Hive-frågor i Hadoop-kommandoraden:

* direkt
* med hjälp av .hql filer
* med kommandokonsolen Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Skicka Hive-frågor direkt i Hadoop-kommandoraden.
Du kan köra kommandot som `hive -e "<your hive query>;` att skicka enkla Hive-frågor direkt i Hadoop-kommandoraden. Här är ett exempel där den röda rutan beskriver det kommando som skickar Hive-fråga, och den gröna rutan visar utdata från Hive-frågan.

![Skapa arbetsyta](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Skicka Hive-frågor i .hql filer
När Hive-frågan är mer komplicerad och har flera rader kan är redigera frågor i kommandoraden eller Hive kommandokonsolen inte praktiskt. Ett alternativ är att använda en textredigerare i klustrets huvudnod Hadoop-kluster för att spara Hive-frågor i en .hql-fil i en lokal katalog för huvudnoden. Sedan kan skicka Hive-fråga i filen .hql med hjälp av den `-f` argumentet på följande sätt:

    hive -f "<path to the .hql file>"

![Skapa arbetsyta](./media/move-hive-tables/run-hive-queries-3.png)

**Ignorera förloppet status skärmen utskrift av Hive-frågor**

Som standard när Hive-frågan har skickats i Hadoop kommandoraden skrivs förloppet för Map/Reduce-jobb på skärmen. Om du inte skärmen utskrift av Map/Reduce-jobb pågår, kan du använda ett argument `-S` (”S” i versaler) i kommandot rad på följande sätt:

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Skicka Hive-frågor i kommandokonsolen för Hive.
Du kan också ange kommandokonsolen Hive genom att köra kommandot `hive` i Hadoop kommandoraden och skicka Hive-frågor i kommandokonsolen för Hive. Här är ett exempel. I det här exemplet markerar du två röda rutor de kommandon som används för att ange Hive kommandokonsolen och Hive-fråga som skickats i kommandokonsolen Hive respektive. Den gröna rutan visar utdata från Hive-frågan.

![Skapa arbetsyta](./media/move-hive-tables/run-hive-queries-2.png)

I föregående exempel utdata direkt Hive-frågeresultatet på skärmen. Du kan också skriva utdata till en lokal fil på huvudnoden eller till en Azure-blob. Du kan sedan använda andra verktyg för ytterligare analys utdata för Hive-frågor.

**Utdataresultat Hive-fråga till en lokal fil.**
Om du vill spara resultatet av Hive-frågan till en lokal katalog på huvudnoden har att skicka Hive-frågan i Hadoop-kommandoraden enligt följande:

    hive -e "<hive query>" > <local path in the head node>

I följande exempel skrivs utdata för Hive-fråga till en fil `hivequeryoutput.txt` i katalogen `C:\apps\temp`.

![Skapa arbetsyta](./media/move-hive-tables/output-hive-results-1.png)

**Hive-frågeresultat för utdata till en Azure-blob**

Du kan också spara resultatet av Hive-frågan till en Azure blob i standardbehållaren för Hadoop-kluster. Hive-frågan för det här är följande:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

I följande exempel skrivs utdata för Hive-fråga till en blob-katalog `queryoutputdir` i standardbehållaren för Hadoop-kluster. Här kan behöver du bara ange katalognamnet utan blobnamnet. Ett fel inträffar om du anger både directory och blob-namn, till exempel `wasb:///queryoutputdir/queryoutput.txt`.

![Skapa arbetsyta](./media/move-hive-tables/output-hive-results-2.png)

Om du öppnar standardbehållaren för Hadoop-kluster med Azure Storage Explorer kan du se utdata för Hive-fråga som visas i följande bild. Du kan använda filter (visas med röd ram) för att endast hämta blob med angivna bokstäverna i namn.

![Skapa arbetsyta](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2. Skicka Hive-frågor med Hive-redigeraren
Du kan också använda Frågekonsolen (Hive-redigeraren) genom att ange en URL i formatet *https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor* i en webbläsare. Du måste vara inloggad i ser den här konsolen och du måste ha autentiseringsuppgifter här dina Hadoop-kluster.

### <a name="ps"></a> 3. Skicka Hive-frågor med Azure PowerShell-kommandon
Du kan också använda PowerShell för att skicka Hive-frågor. Anvisningar finns i [skicka Hive-jobb med hjälp av PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Skapa Hive-databasen och tabeller
Hive-frågor som delas i den [GitHub-lagringsplatsen](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) och kan laddas ned därifrån.

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

* **<database name>**: namnet på databasen som du vill skapa. Om du bara vill använda standarddatabas, frågan *Skapa databas...*  kan utelämnas.
* **<table name>**: namnet på den tabell som du vill skapa inom den angivna databasen. Om du vill använda standarddatabasen tabellen kan refereras direkt av *<table name>* utan <database name>.
* **<field separator>**: avgränsaren som avgränsar fält i datafilen som ska överföras till Hive-tabell.
* **<line separator>**: avgränsaren som avgränsar rader i datafilen.
* **<storage location>**: Azure storage-plats att spara data i Hive-tabeller. Om du inte anger *plats <storage location>* , lagras i databasen och tabellerna i *hive/informationslager/* katalogen i standardbehållaren för Hive-kluster som standard. Om du vill ange lagringsplatsen måste lagringsplatsen vara i standardbehållaren för databasen och tabeller. Den här platsen måste vara kallas plats i förhållande till standardbehållaren för klustret i formatet *' wasb: / / / < katalogen 1 > / ”* eller *' wasb: / / / < katalogen 1 > / < katalogen 2 > /”* osv. När frågan körs, skapas de relativa katalogerna i standardbehållaren.
* **TBLPROPERTIES("Skip.Header.Line.Count"="1")**: datafilen har en rubrikrad, måste du lägga till den här egenskapen **i slutet** av den *Skapa tabell* fråga. I annat fall rubrikraden har lästs in som en post i tabellen. Om filen inte har en rubrikrad kan du utelämna den här konfigurationen i frågan.

## <a name="load-data"></a>Läsa in data till Hive-tabeller
Här är Hive-frågan som läser in data i en Hive-tabell.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **<path to blob data>**: Om blob-fil som ska överföras till Hive-tabell som är i standardbehållaren för HDInsight Hadoop-kluster i *<path to blob data>* ska vara i formatet *' wasb: / / /<directory in this container> / <blob file name>'*. Blob-fil kan också vara i ytterligare en behållare för HDInsight Hadoop-kluster. I det här fallet *<path to blob data>* ska vara i formatet *' wasb: / /<container name><storage account name>.blob.core.windows.net/<blob file name>'*.

  > [!NOTE]
  > Blob-data som ska överföras till Hive-tabell måste vara i standard eller ytterligare en behållare för storage-konto för Hadoop-kluster. I annat fall den *Läs in DATA* frågan inte kunde köras klagar på att den inte kan komma åt data.
  >
  >

## <a name="partition-orc"></a>Avancerade ämnen: partitionerad tabell och lagra Hive-data i ORC-format
Om data är stor kan är partitionera tabellen bra för frågor som behöver bara skanna några partitioner i tabellen. Exempelvis är det rimligt att partitionera loggdata av en webbplats med datum.

Förutom partitionering Hive-tabeller, är det också bra att lagra Hive-data i formatet optimerade rad kolumner (ORC). Läs mer om hur du formaterar ORC <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">med ORC-filer som förbättrar prestanda när Hive läsa, skriva och bearbetning av data</a>.

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

När du frågar efter partitionerade tabeller, rekommenderar vi att lägga till villkoret partition i den **början** av den `where` sats eftersom det förbättrar effektiviteten av söka avsevärt.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Store Hive-data i ORC-format
Du kan inte direkt läsa in data från blob storage till Hive-tabeller som lagras i ORC-format. Här är de steg som du behöver vidta för att läsa in data från Azure BLOB-objekt till Hive-tabeller som lagras i ORC-format.

Skapa en extern tabell **LAGRAS AS TEXTFILE** och läsa in data från blob storage i tabellen.

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
> Om tabellen TEXTFILE  *<database name>.<external textfile table name>* har partitioner, i steg3 i `SELECT * FROM <database name>.<external textfile table name>` väljer kommandot partition variabeln som ett fält i returnerade datauppsättningen. Lägga till dem i den  *<database name>.<ORC table name>* misslyckas eftersom  *<database name>.<ORC table name>* har inte partition variabeln som ett fält i tabellschemat. I det här fallet du måste uttryckligen välja de fält som ska infogas  *<database name>.<ORC table name>* enligt följande:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Det är säkert att släppa den *<external textfile table name>* när med följande fråga efter alla data har infogats i *<database name>.<ORC table name>*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Du bör ha en tabell med data i ORC-format kan användas när du har genomfört den här proceduren.  
